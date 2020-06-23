---
layout: post
title: Rails migration
tags: rails sql
cover_image: /assets/images/rails-migration.png
excerpt_separator: <!--more-->
---

Mid June of 2020.

Many times when I talk about MVC in Rails, people will ask then what is the migration.
I think I could write a blog thats some how explain the Rails Migration using my words instead of the officical Rails
guide. The blog isn't for a replicating the Rails Guide, it covers main concepts and go deeper on some of
important one.

<!--more-->

Let's start by understanding what is Rails Migration.


# What is Rails migration

  Rails Migration is for changing the database structure.

  It is different from the Model.

  There are 2 types of language used to interact with the database, SQL and DDL.

  SQL is used to CRUD data from/to database, DDL is to change the database design.

  Rails Model (Active Record) works with SQL, and Rails Migration works with DDL.

  Rails Model supports ways to interact to with the database, while Rails Migration changes the database structure.

  A migration can change the name of a column in `books` table.

  A migration can remove the table `book_covers`.

  A migration can rename column `sent_at` to `delivered_at` in the table `mail_histories`.

  A migration can add contraint to ensure field `name` in `babies` table isn't null.

  Those are some examples of what a migration can do.

  "Select those users who have more than 2 roles in the database" is done by Rails Model.

  "Create a book with input fields, validate the isbn to be uniq before save" is the Model responsibility.

  I hope this explanation works.

# Rails migration supporting features

## Change or up/down

Sometimes we see the method name is `up` and `down` and sometimes we see the method name is
`change` in the migration file.

You can run `rails db:migrate` to run all pending migrations, and `rails db:rollback` to revert 1 migration.

When the `rails db:migrate` is executed, Rails will triggered the `up` or `change` method. When the command `rails
db:rollback` method is executed, Rails will try to run the reversed `change` or `down` method.

There are behaviors that aren't reversible, such as `change_column`, `change_table` and manual SQL triggering.

If you are using those reversible behaviors, you can just use the `change` method.

And if so, you can define your own `down` migration to rollback the change. The other option is using the keyword
`reversible` to define the `up` and `down` behaviors. Take a look at the example below.

```ruby
  def change
    reversible do |dir|
      dir.up  { }
      dir.down { }
    end
  end
```

The information here you can find in the [Rails guide][1]. Please note that the migration can be reversed, but the data
can't. When you perform one of those behaviors below:

- Remove a table
- Remove a column
- Change the column type

The change will affect to existing data. This operation can't be reversed.

I want to ensure the column isn't accept null value. Hence I go ahead and add the constraint

```ruby
  def change
    change_column_null :reminders, :message, false
  end
```

The migration fails in the first try. There is null value in the database. A common solution is adding a default value
for the column, then adding the null check contraint. It may end up with 2 migrations instead one. It isn't a strict
rule, you can write a script (a rake task) to set default value, which is totally fine. After adding the defalt value
contraint, you can rerun the migration again

Rails has a better way to do so. The `change_column_null` actually has the fourth argument to set the default value for 
those rows with the column to add the null constraint are holding NULL value. This is the most elegant way I found so far to
deal with this situation.


```ruby
  def change
    change_column_null :reminders, :message, false, "default reminder message"
  end
```

Note that this won't add the `default_value` constraint to the column.

A similar situation, I want to add a unique constraint to a column, which may already contain duplication data, how
should we deal with?

Unfortunately, there is no straight forward way to clean up duplication data, we have to clean it up separately before
adding the index. Here is a blog post talking about a safe strategy to add unique index, check it out [Rails Migration:
When you can’t add a uniqueness constraint because you already have
duplicates][2]

The race condition is also an annoying things while perform database migration.

While you try to run 2 steps migrations, for instance adding default value to a set of data, new data with null value
comming in, and the migration to add the `not_null` constraint still remains failing. The same case can happen when the
team are removing duplication data, more duplicated data is comming in.

Solving the race condition isn't easy too, hence for those risky operations, I think running the migration after working
hours. Although we are looking at a zero time deployment, there are still places we have to take a risk assessment.

You may ask why don't add those constraint from the beginning. And the answer I can come up with: *Well,...* if we
know all this, we can write a perfect software all the time, cheers.

I will talk about some Rails migration features in next sections.

## Reference

```ruby
  add_reference :books, :author

  # or

  create_table :books do |t|
    t.references :author
  end
```

This method adds a column name `author_id` to the books table.

However, it doesn't make `author_id` a foreign key to the authors table.

In fact, when I started with Rails, there is no `add_reference`. I thought the `belongs_to :author, foreign_key: 'user_id'`
added the constraint to the source model. It turned out that the key word `user_id` here just mean the reference. It
tells Rails (ActiveRecord) which column to join to the `authors` table.

#### Why so?

Check out this [Hacker news thread][3] and this [Github issue][4]

Because of some trade offs of the foreign key approach, such as performance issue, hard to combine data from many sources
and extracting a subset of data, microservice approach, hence many team has decided to not go with foreign key approach
to manage the relationship between models and rely on the ORM (app level) to handle this responsibility.

What are the disadvantages using the ORM to manage the integrity and relationship of the database? Compare to the
foreign key approach, you don't have a reliable way to ensure the integrity on the database level. Most of the database
is located in separate server. Usually there will be multiple instance of the web app which writting and reading to the
same database simultaneously. From time to time, the race condition and network issue can create polution to the database.

However, if you need to use foreign_key, you can do that with an additional parameter `foreign_key: true` in the
`add_reference` method.

```ruby
  add_reference :books, :author, foreign_key: true
```

If you are wondering why need the `add_reference` method while what it does is adding a new column to table.

Both `add_column` and `add_reference` are both adding column to the table, plus `add_reference` does a bit
more than that. Beside adding column, `add_reference` provides a more friendly syntax, and secondly it maps the model to
column (reference from x to y mean adding column y_id to table x).
It supports polymorphic association too. Thirdly, it adds an index for new column by default, unless you know what you
are doing, just leave it. And lastly, you can add a foreign key constraint by specifying `foreign_key: true`.

## Index

Database index is a big topic. In this post, I just cover an extra thin iceberg layer.

Generally database index speeds up database data retrieval operation.

SQL query checks if index for the lookup column exist then use it instead of sequently scanning through all rows of
data.

If your project use email to find a user, it is definitely a good idea to have an index for the email column.

You can have index for multiple columns.

To add an index in Rails, you can use `add_index` method

```ruby
  add_index :users, [:gender, :age], name: "user_index_by_gender_and_age"
```

I hope you are curious enough to ask why do we need multiple columns index, and I am happy to bring the question to the
next stage: when the multiple columns index doesn't work, and what is difference between multiple column index and
multiple indexes?

[This article][5] may answer your question.

Another common usecase of index is to ensure a column is unique.

The validation from model level can't ensure 100% as above discussion.

To add the unique index, adding the `unique: true` argument when create index.

I think it is an interesting topic, let me know if you feel the same way, then I will go more details in a separate post.

## Postgresql extra data types

PostgreSQL Database supports additional data types.
By using these data type, we will depend on a specific database and the cost to migrate to different database is increased

In this section, I share the 2 most common data types from Postgres.

- Array
  The array is useful to store an array of data like tags, scores, etc.
  With array support, we don't have to create extra table to store those data.

- Json & Jsonb
  Extremely useful to store a complicated data while providing sufficient operation to retrieve and update the json data.
  PostgreSQL support operators to directly access and update a key path.

- Range
  Define a range of data, for example age 0-18. With range type, you can perform some checking to see if ranges are 
  overlap.

You don't have to design a complicated database models or adding a NoSQL Database while you can make it a column type in
postgres.

For an example is a todo list application. There is no need to have different models for list and item, a todos table
with a list of items, each contains detail, current state and complete date that is represent as a column with jsonb
type.

Jsonb is faster when retrieving as well as it supports indexing, while json type is faster when writting to
the database.

When use jsonb with Rails, the data you are getting back is just a hash. To convert the data from
json column to Ruby object, we have to handle the mapping ourselves.

While writting this blog post, I find out Rails support `serialize` and `store_accessor` methods, which allows the model
to access json attributes.

This [blog post][6] provides excellent explanation on how to use these 2 methods.

*For the full list of postgres data type supported by Rails, check out [the guide][7].*

## Multiple databases connection

After many years, Rails has supported multiple database connection.

This feature opens opportunity in many cases. Almost every medium project written on Rails are using more than 1 database.

What we can do with multiple database connection?

- Switching connection from primary database and replica.
  Reading operators are using replica while writing's are using primary.

  Depends on the nature of the app, some project needs heavy reading operations, By using replicas for reading, it is
  more ideal. The reason is because there is only 1 primary node while there can be many replicas node that copy the
  data from primary node and are used for reading purpose.

- Distributing data to multiple databases

  For example, user and profile can be stored separately. The app may not need to know about user profile when user
  login to the application. Another example is the product and orders. While the product data is used on the front page
  of the ecommerce, the order data is interests of the internal departments such as Sales, Finance and Delivery.

  Of course, the `foreign_key` doesn't work with this database design approach.
  
- Integrate with legacy application

  Rails doesn't limit to use a database for only one reading or writting purpose. While working with legacy system, we
  may come up with new application for new features development while putting the old app in the maintaining mode. The
  legacy app is still in used without new feature instroduced. From the newer application, there are some data that the
  new app needs to retrieve from the legacy app's database.

  Another approach is to use API to retrieve the data, it is quite depends on the current stage of the legacy app to decide
  which approach works better.

  The complete guide to setup multiple database connection with Rails app is [Rails guides - Multiple Databases with Active Record][8]

## Command line options
  
  From Rails 5, you can call `rails db:migrate` and `rails db:rollback` instead of using `rake db:migrate` and `rake
  db:rollback`, they are still Rake tasks.

  Rails makes it simple to just run the migrations. It will compare the last record of the table `schema_migrations` in
  your database and the version in `db/schema.rb` file to see if it is up-to-date. If the timestamp from the
  `schema_migrations` table is lower than the one in db/schema.rb, Rails will execute all migration files with the version
  after the version of the `db/schema.rb`.

  The version is a timestamp follow format **yyyymmddhhmmss**, e.g 20200406022729.

  Because it is easier to learn with example, here are some Rails migration commands I use often

  ```sh
    rails db:migrate
    rails db:rollback STEP=2                   # rollback by 2 steps, useful during development, not push yet.
    rails db:schema:load                       # load databse from schema file instead.
    rails db:seed                              # create seed data
    rails db:migrate:up VERSION=20200616035428 # run a migration file, quite useful when you need it 😉
  ```

  I used to add this task to perform a migration way of resetting database. We can create a custom rake task that
  triggers multiple existing rake task like below

  `lib/tasks/db/rebuild_dev.rake`

  ```ruby
    namespace :db do
      desc "This task rebuild the db for development environment"
      task rebuild_dev: %i(drop create migrate seed)
    end
  ```

  You can run it by `rails db:rebuild_dev`. It drops the databases (ensure the rails console and rails server are
  quited), recreate the databases, run migration and create seed data for you, all in once.

  The built in `rake db:reset` does almost the same thing, except it loads the schema file to database instead of
  running migration.

  However, you don't need this custom `db:rebuild_dev` task because you can use the built-in `rails db:migrate:reset`
  instead.

## Sql Structure schema

  It is generally same with exporting the Data definition language (DDL) from a database to a SQL file.
  You can use the file to load into the a database to do some works without installing Rails just to run the migration.

  In the Rails application, Rails allows us to choose between Ruby DSL and SQL to store the snapshot of the database.

  Each time the migration command is executed, the `db/schema.rb` is updated as a snapshot of your database. It is
  written in Ruby, it is like a aggregation of all migration files. Some migrations you may add a new column, some
  migration you change the column name and drop the table, but the latest state is stored in the db/schema.

  By running rails `db:schema:load`, all of the existing table are wiped out and the entire database is loaded up again.

  **Remember to not use this command on the production database.**

  Knowing the Database snapshot is written in Ruby, we know that Rails has to maintain the DSL of the schema to make it
  compatible with all supported Database. That requires a lot of efforts and definitely there are some lags to catch up,
  for instance the data type jsonb in Postgresql isn't reflected correctly for a long time.

  When using those cutting edge features from DB or you prefered to use SQL schema since it is more natural to you, or
  you have to import the sql schema to the DB to perform some works and those servers aren't accessible from outside,
  the SQL Structure schema may be the only choice.

# Conclusion

  Rails migration helps interacting with the Database easier.

  It is a thin layer but handle an extremely important component used by the rest of the app - the database.

  By taking advantages of this layer, the app can perform very fast, hence we may consider to understand Rails migration
  and Database well.

  The real database design is usually different from what we have taught in school. We should open mind to understand
  the rationale behind.

  NoSQL Database and reactive programming also open new ways to fix some missing features of SQL database. With new
  technologies, it may outdate? the migration layer in Rails. Please let me know your thoughts.


  [1]: https://guides.rubyonrails.org/active_record_migrations.html#using-reversible
  [2]: https://medium.com/@josh_works/rails-migration-when-you-cant-add-a-uniqueness-constraint-because-you-already-have-duplicates-352a370e4b54
  [3]: https://news.ycombinator.com/item?id=21486494
  [4]: https://github.com/github/gh-ost/issues/331#issuecomment-266027731
  [5]: https://www.cybertec-postgresql.com/en/combined-indexes-vs-separate-indexes-in-postgresql/
  [6]: https://nandovieira.com/using-postgresql-and-jsonb-with-ruby-on-rails
  [7]: https://edgeguides.rubyonrails.org/active_record_postgresql.html
  [8]: https://guides.rubyonrails.org/active_record_multiple_databases.html
