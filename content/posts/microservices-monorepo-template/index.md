---
title: "A Modern Approach to Microservices with Monorepos, Devbox, and Skaffold"
date: 2025-10-28T14:03:03-04:00
draft: false
---

As software systems grow, many teams adopt the microservice architecture to break down a large monolith into smaller, manageable services. This approach offers benefits in scalability and separation of concerns, it introduces new challenges, especially around code sharing and development environment consistency.

This post explores a modern workflow for developing microservices within a monorepo, using a powerful combination of Devbox, Skaffold, and Make to create a seamless and production-like development experience. We'll be referencing the setup from this sample project: [https://github.com/duykhoa/microservice_playground](https://github.com/duykhoa/microservice_playground).

### Why a Monorepo for Microservices?

Traditionally, microservices are often managed in separate repositories (a "poly-repo" approach). However, a monorepo is a single repository containing multiple services—offers compelling advantages:

*   **Simplified Code Sharing:** In a poly-repo world, sharing code (e.g., common libraries, data models) requires a formal versioning and packaging process. You have to compile, tag, and publish the shared library to an artifact repository (like GitHub Packages or a private Maven repository). Then, each service that depends on it must update its dependencies. In a monorepo, you can simply import the code directly. This eliminates versioning overhead and ensures all services are always using the latest shared logic.

*   **Atomic Commits and Reviews:** A single feature or bug fix might require changes across multiple services. In a monorepo, you can commit all these changes in a single commit. This makes the git history easier to understand. Furthermore, a single pull request can contain both the change to a shared library and the necessary adjustments in the services that consume it. This makes code reviews more holistic and efficient.

*   **Simpler Git Workflow:** Managing dependencies and versions across dozens of repositories can become a nightmare. A monorepo simplifies the workflow by having a single source of truth. There's no need to coordinate commits across different repos to keep them in sync.

### The Development Environment Challenge

Developing a single microservice requires more than just the service's code. You need a complete environment, which often includes:

*   **Compilation Tools:** The Go compiler, `protoc` for gRPC, a JDK for Java services, etc.
*   **Databases:** PostgreSQL, MongoDB, Redis, etc.
*   **Message Queues:** RabbitMQ, Kafka.
*   **Mock Services:** Stubs for external dependencies.
*   **Orchestration:** A way to run all these components together.

Docker and `docker-compose` have made this much easier by allowing you to define your environment in code. However, to get one step closer to a production Kubernetes environment, we can use a combination of Minikube, Skaffold, and Devbox. This stack ensures that all developers have the exact same tools and that their environments are consistent and compatible.

**Devbox** ensures you have the right versions of all your command-line tools (like `kubectl`, `skaffold`, `minikube`). **Skaffold** automates the process of building container images and deploying them to Kubernetes. **Minikube** provides a local Kubernetes cluster.

Here’s how a `skaffold.yaml` file might look, defining the build and deployment process for multiple services:

```yaml
apiVersion: skaffold/v4beta1
kind: Config
metadata:
  name: microservice-playground
build:
  artifacts:
    - image: products-service
      context: services/products
      docker:
        dockerfile: Dockerfile
    - image: order-service
      context: services/order
      docker:
        dockerfile: Dockerfile
    - image: web-service
      context: web
      docker:
        dockerfile: Dockerfile
manifests:
  kustomize:
    paths:
      - ./infra/k8s/
profiles:
  - name: dev
    build:
      artifacts:
        - image: products-service
          context: services/products
          docker:
            dockerfile: Dockerfile
          sync:
            manual:
              - src: '**/*.go'
                dest: .
        - image: order-service
          context: services/order
          docker:
            dockerfile: Dockerfile
          sync:
            manual:
              - src: '**/*.go'
                dest: .
    manifests:
      kustomize:
        paths:
          - ./infra/k8s/
```

This configuration tells Skaffold how to build the Docker image for each service and where to find the Kubernetes manifests (in this case, managed by Kustomize). The `sync` block enables hot-reloading for Go files, so changes are automatically synced to the running container without a full rebuild.

### How to Set It Up

The [microservice_playground](https://github.com/duykhoa/microservice_playground) repository provides a working example.

1.  **Tooling with `devbox.json`**: This file defines all the necessary developer tools.

    ```json
    {
      "packages": [
        "go@1.21",
        "minikube@1.31",
        "skaffold@2.9",
        "kubectl@1.28",
        "make@4.4"
      ],
      "shell": {
        "init_hook": [
          "echo 'Welcome to the dev environment!'"
        ]
      }
    }
    ```
    By running `devbox shell`, a developer instantly gets access to all the required tools at the correct versions.

2.  **Service Code**: Inside the `services` directory, each service is a standard Go application. For example, the `order-service` might look like this:

    ```go
    // services/order/main.go
    package main

    import (
        "fmt"
        "net/http"
        // Direct import from another module in the monorepo
        "github.com/duykhoa/microservice_playground/services/products/client"
    )

    func main() {
        productClient := client.New()

        http.HandleFunc("/orders", func(w http.ResponseWriter, r *http.Request) {
            // Logic to create an order, potentially calling the product service
            fmt.Fprintf(w, "Order created!")
        })

        http.ListenAndServe(":8080", nil)
    }
    ```

3.  **Optimizing with `go.work`**: A key tool for Go monorepos is the `go.work` file. It tells the Go toolchain that you're working in a multi-module workspace. This allows you to have separate `go.mod` files for each service while still enabling cross-service code navigation and dependency resolution in your IDE as if it were a single module.

    A `go.work` file at the root of the repository would look like this:

    ```
    go 1.21

    use (
        ./services/products
        ./services/order
        ./web
    )
    ```

### Focusing on Specific Services with Skaffold

Running all services at once is great for integration testing, but it consumes a lot of resources. Often, you only want to work on one or two services at a time. Skaffold supports this through **modules**.

You can target specific services (or groups of services) by using the `--module` (or `-m`) flag in your commands. The module names are derived from the `image` names defined in the `build.artifacts` section of your `skaffold.yaml`.

For example, if you only want to run the `order-service` and the `web` frontend, you would run:

```bash
skaffold dev -m order-service -m web-service
```

This command tells Skaffold to only build, deploy, and watch those two specific modules, ignoring the `products-service`. This significantly speeds up your development loop and reduces resource consumption.

### A Deeper Dive into the `go work` Workflow

The `go.work` file is a game-changer for Go development in a monorepo. Let's clarify the workflow.

**The Problem Before `go.work`:**

Without a workspace, if `order-service` depended on a library in `products-service`, its `go.mod` file would point to a specific version of the `products-service` repository. To work on them together locally, you would have to add a `replace` directive in `order-service/go.mod`:

```
// TEMPORARY and easily committed by mistake!
replace github.com/duykhoa/microservice_playground/services/products => ../products
```

This is clumsy and highly error-prone, as these temporary `replace` directives can easily be committed to version control, breaking builds for other developers.

**The `go work` Solution:**

The `go.work` file at the root of the project tells the Go toolchain that this is a multi-module workspace. This file is meant to be checked into source control.

Here’s how you work on a specific service:

1.  Navigate to the service's directory: `cd services/order`.
2.  Open the code in your editor. The Go language server (`gopls`) will automatically detect the `go.work` file in the parent directory and understand the entire workspace.
3.  Now, if you make a change in a dependency that is also in the workspace (e.g., you edit a function in `services/products/client`), the change is instantly available to `order-service`. There is no need to modify any `go.mod` files.
4.  You can run standard Go commands from within the `services/order` directory, and they will just work. For example, `go test` will use the local `services/products` code instead of a version from the internet.

This creates a seamless development experience where you can treat your monorepo as a single, cohesive unit while still maintaining separation between your service modules.

### Conclusion

Combining a monorepo with tools like Devbox, Skaffold, and `go.work` creates a powerful and efficient development workflow for microservices. It solves the key challenges of code sharing and environment consistency, allowing developers to focus on building features rather than fighting their tools. This setup not only improves the developer experience but also brings your local environment one step closer to the production reality of Kubernetes.
