---
layout: post
title: A Vim post
excerpt_separator: <!--more-->
---

This post is about a successful software - Vim, how I use it and why everyone should try it.

<!--more-->

# Introduction

While writing this post, I'm having a good coffee made by me.

<img src='https://lh3.googleusercontent.com/FA0Jrrw03hM0pbVliOORCAAMCXcUzwciJUya1RUHQvs1k7hjXo0bfJIekSX86Tywi-5ADodE-fAdZJ81SVYP5R_5bDvLCHLdyXeDmBuQQIGJhPALVrVllPGplTfv0hVz1KJ2zLeL2gLikneGNd3xi7m-5hyfdm5PKfwm1suzPJBzW6pvzfX7ral2y3vU0KjTP8x-axOLHKZNVLA6FTb662F3Dtakr-Gx9Tl2LPVqqQhnZ9nC6eRw6DAf4lpYI92jaiJIHfFmKte6YU-96-4vOwiSdYKTI5nPaKl9_zUA-zlF7GgVDhMZaiHn9bi3GhXS9HHb1fPAlBu1UjuaT4bS5PLjHJCfSmf0bFB-pgjBNQn71sbdv0trRdgaq_QqRq04hlK0kHtpifG77nSQcJXA4i3UvWcM2UZjpTgnIu8LMEcCrzZDphOMMpbQzRAHQUB_kK-8GKNqa9YXg3jKUniGd9PWIVjQwGD7nP42ae8_DhiPC8ts5UY0WdMsvCeIAXlT5Oe5B1qKwNF7InkM-7R8_ZckoLvPmvS76PpEXpiHAnr9B7FaCFH3bxL7nxULlFf4GFZOk5qZvOplnYCsS1DoiNKC307vJdIOyBZQOcjh5BZdWpOX4IuiYSBAjfP2cVtEXx3bTEffXlVqJmGk9zs27ylSEw=w1302-h1734-no' height='300'/>

Vim is the editor of productivity.

When I know how to use vim, I regret that I haven't known it earlier.

It changes the way I work. It changes the way I use the computer. It changes the way I think, the way I learn new thing.

---

I will explain more about these points, but first, I want to make a short introduction about Vim.

Vim is the most successful opensource software I've known.

The earliest release is in 2nd November, 1991, and from that time,
there are massive number of releases (current version is 8.1).

**Bram Moolenaar** is the one untiringly maintain Vim for 20 years.

Vim means "vi improved", an improved version of the classic vi editor, which is developed by Bill Joy (co-founded of Sun Microsystem).

Vim isn't the oldest ever known editor as you may think. There are some older editor like ex, ed, em, they share several
key elements of a general line editor.

Some fun facts:

- `vi` is pronouced as "vi:ai", 2 words, while `vim` is pronounced as one word "Vim"

> Vim is pronounced as one word, like Jim, not vi-ai-em. It's written with a capital, since it's a name, again like Jim.

- Why vim uses `hjkl` as arrow keys? Check this out [https://catonmat.net/why-vim-uses-hjkl-as-arrow-keys](https://catonmat.net/why-vim-uses-hjkl-as-arrow-keys)

- See how people talk about Vim in [this link](http://vimdoc.sourceforge.net/htmldoc/quotes.html).

Vim is just an editor which works well in all linux system, windows, mackintos.
`vi` is usually included in the linux distribution, or git install package in windows.

`vi` is a terminal editor, it works well in the linux server without the GUI supported.

Differences between Vim and Vi? Go ahead, Open Vim, and type

  ```
    :help vi_diff
  ```

# What does Vim change me?

It is hard to convince people to learn Vim. I tried a few times and I can confirm that it didn't work.

My post, I still want people will start using Vim, but I just share what and how I use Vim.

6 years ago, I started using Vim.

It is really hard.

I like using mouse and arrow keys.

I can still use the arrow keys, but my mentor told me that moving my hand around the letter keys and arrow keys isn't
efficient. Later on I found that he was right.

At first, I only knew how to move up down, left right using `hjkl`.

I kept forgetting to use `hjkl`. I even think that I just need the Insert mode, so I press `i` right after open a file.
This way I feel like using other editors (e.g. Notepad).

Then I did pair programming with a few Vim developers. They use Vim so efficiently. They don't use mouse. They don't
repeat an action many times. They communicate what they want to do with me, then how to do it. They make me feel like
writing software is an art.

I got inspired. I started learning and practicing Vim. I started watching the online video, reading online how people
use Vim. I learned from my colleagues. I tried not to use arrow keys, and switching back to normal mode after inserting
text.

# The Vim adventure

## The first 2 months with basic movements

The first resource I learn Vim from is the `vimtutor`. It teaches basic features in Vim, included navigation, writing,
searching, replacement, saving file and [exiting](https://stackoverflow.com/questions/11828270/how-to-exit-the-vim-editor)

My colleagues gave me many online website to learn Vim. There are https://www.openvim.com/, https://vim-adventures.com/, http://vimgenius.com/

After 3 weeks, I am comfortable with some basic movements in Vim, writing, saving file, switching different mode.
I still use the arrow keys, but I am no longer able to do anything with it since I put the code below in my `~/.vimrc`

```
  nnoremap <up>    <Esc>:echoerr 'Please use k instead'<CR>
  nnoremap <down>  <Esc>:echoerr 'Please use j instead'<CR>
  nnoremap <left>  <Esc>:echoerr 'Please use h instead'<CR>
  nnoremap <right> <Esc>:echoerr 'Please use l instead'<CR>

  inoremap <up>    <Esc>:echoerr 'Please use k instead'<CR>
  inoremap <down>  <Esc>:echoerr 'Please use j instead'<CR>
  inoremap <left>  <Esc>:echoerr 'Please use h instead'<CR>
  inoremap <right> <Esc>:echoerr 'Please use l instead'<CR>
```

When I press arrow key in vim, Vim shows an error message in the status line "Please use <h|i|j|k> instead".

These thing belows are the list of operations I have learned after 2-3 months using Vim:

- Go to the end of line ($)
- Go to the first non-blank character of the line (`^` - I map it to 0)
- Go to the end of file (G)
- Go to the beginning of the file (gg)
- Go back a word with b, to the next one with w, and end of current word with e
- Select a few lines (Shift V, use hijk to select)
- Copy (yank - y), and copy a line (yy), to paste (p), and (P) to paste the content on one line above.
- Delete a line (dd) or d if the line is selected
- Open new line with o, and O to open new line above the current line.

I found these operations are the most important movements in Vim, it would be enough for us to use Vim already.

The more we learn, the more fluent we need to get with basic movements.

## Plugins and configurations

Vim comes with a huge list of configurations, which allows us to customize Vim as the way we want.

I have spent a lot of time reading and trying out each option and end up with a set of configurations that you can find
in my `dotfiles` github repo [.vimrc](https://github.com/duykhoa/dotfiles/blob/master/vimrc).

I like plugins, it helps to solve some problems easier. Some of those pluggins are very useful, like
Silver Searcher, CtrlP (fuzzy search), Nerd tree, Super Tab, Emmet, Table mode...

However, the plugin's downside is that we need to depend on the plugin. For other editors, that will never be a problem.
But Vim user also use vim in the server aka another machine, which adds extra step to install plugins in all servers
before doing the work, which isn't a good practice.

My advice after spending a huge chunk of time setting up my Vim is: just get one of `.vimrc` file in the list of
dotfile repositories in github [http://dotfiles.github.io/](http://dotfiles.github.io/) or [mine](https://github.com/duykhoa/dotfiles/blob/master/vimrc),
and use it for a while. It isn't necessary to customize your vim right from the beginning.

## Continue learning Vim instead of dig deeper to each pluggins and configuration

After spending so much time learning configuring Vim, I found it is a bit wasted.
However, I think it is a good point to know what should be the right focus.
I start learning 3 more important and advance features of vim:

- Motion and range
- The dot command
- Search and substitute

1. [Motion and range](http://vimdoc.sourceforge.net/htmldoc/motion.html)

    The link in the title describes what is motion, and there are some examples of what motion can do.

    Motion are commands that move the cursor, just like `hjkl`. However, using motion commands improve the efficiently.

    Before knowing motion command, I know `x` is used to delete a character in the cursor position. If there is a word
    with 5 letters, I can repeat the step by pressing `x` 5 times (`xxxxx`).

    Pressing 5 times is still fine, but say that I want to reduce the repeated action, Vim allows me to do that by
    pressing `5x` (press number 5 and then press x). To me, that's a big thing, and it is easy to remember as well.

    There is a better way to do it. By pressing `5x`, we need to count the total letters.
    Vim supports `w` which allows us to move the cursor to the next word. As you guess, `dw` is delete a word, this is the
    command we want.

    Take one more step further, we want to delete 5 words. We can press `dw` 5 times. But we can save a bit of work using
    the dot command aka the repeat commond. By pressing `.`, VIM will repeat the last command for the current cursor
    position. Let's see how it work with the example below:

    ```
      <CURSOR>Using welcoming and inclusive language
    ```

2. The dot command

    As the cursor is in the beginning of the line, after pressing `dw`, the word "Using" is deleted. Now, if we press `.`,
    the word "welcomming" is deleted because Vim will repeat the `dw` command for the next word (notes that `dw` command
    also deletes all the spaces before the next following word). We can press `.` 4 times to delete all the words in this
    sentence, or `4.` to get the same effect.

    It is just to demonstrate how the `.` works. In real life, to delete an entire line, we can press `dd` or `d$`.

    An overwhelming tips: If we want to delete a sentence (end by a dot, exclaimation mark or question mark), `d)` will
    definitely be the command we need.

    I have spent a little bit of time thinking how to replace all the words with the dot command. The most common way (and
    yet, very efficient way is to use substitute command), but beside that, if we haven't learn regex and substitute
    command yet, we can still make the quite efficiently by using the dot command.

    As we delete a word `dw` and insert a new word, the dot command will only repeat the insert command since `dw` and `i`
    are 2 commands. Vim supports a command called "change" - `c`. We can change a word using `cw` then type a new word for
    it. By using `cw`, Vim treats the entire replacement as 1 command, and we can use the dot command to repeat it.

    Given the sentence below, we want to change the word `young` to something else, for example `funny`.

    ```
      She was young the way an actual young person is young.
    ```

    First, do some kind of action to move the cursor to the word `young`, then press `cw` and type `funny` and press
    `<ESC>`. Then somehow we move the cursor to the next occurrence of the word `young` and press `.`, then repeat it one
    more time.

    To move the cursor to the word `young`, we can use the search command `/young`.
    The sequence of commands is `/young cw funny <ESC> n . n .`

    `n` command move the cursor to the next occurence of the searching result.

    Another overwhelming tips: the `*` command has the same effect, it will search all the occurrence of the current word
    under the cursor, e.g, if the cursor is on the word `young`, by pressing `*`, Vim makes a search for the keyword
    `young`.

    Another nice example, we want to wrap the text below in a open and close <li> tag.

    ```
      Using welcoming and inclusive language
      Being respectful of differing viewpoints and experiences
      Gracefully accepting constructive criticism
      Focusing on what is best for the community
      Showing empathy towards other community members
    ```

    An another 2 commands I haven't introduce are the `I` and `A` (Shift I and Shift A). Similar to `i` and `a`, they change Vim to `insert`
    mode. `I` opens the insert mode at the beginning of the line, and `A` opens the insert mode at the end of the line.

    To make it work, we can place the cursor at any where in the first line. Then we can press `I` to open the insert mode
    at the beginning of the line. We type `<li>` and press `<ESC>` (or Ctrol C) to switch back to the normal mode.

    The sequence of commands to insert `<li>` at the beginning of all of the lines is (the cursor is in the fist line, any
    position): `I <li> <ESC> j . j . j . j .` 

    The sequence of commands to insert `</li>` at the end of all of the lines is (the cursor is in the last line, as we
    finish the above sequence of command to insert the open `li` tag): `A </li> <ESC> k . k . k . k .` 

    It isn't the best way actually, since we need to press `k .` and `j .` a few times. To make it more automatically, we can
    use marco to make it more DRY (asahi). In this post, I don't cover the marco technique, because I haven't take
    the most value of marco in my daily work yet. To me, marco can be used in some limited cases only, the investment
    isn't worth for the return enough. I may find out how to use it properly from some better vim users than me in the
    future. I would love to learn from you also, please share with me.

3. Search and substitute 
  
    Search and replacement are common features of all editors.

    But substitute command in Vim is not just a feature, it is also an art.
    The art of regression.

    I rarely use search by regex, but for replacement, it is extremely useful for me.

    Let's go back to the example of adding the <li></li> surround each sentence.

    We can just use this substitute command: `:.,+4s/^\(\s\+\)\(.*\)$/\1<li>\2<\/li>/gc`

    Mind blown yet?

    To explain how it work, the subtititue command takes 4 parameters:

    - The range of text that the command will run on
    - The pattern to search
    - The replacement phrase (can be plain text or the matching groups)
    - Some optional (with/without confirm, applied for the first occurence only...)

    In this example, the range is `.,+4`, which means from the current line to the next 4 more lines.
    Instead of switching to the visual mode, we can specify a range in Vim to perform some operators, like the
    substitute command, copy and move command.

    The second parameter tells Vim to group each line from start to end to 2 groups: the first group contains all the
    spaces and the second group contain every character til the end.

    The third parameter tells Vim to keep the first group (spaces), adding the <li> and </li> around the second matching
    group.

    The last parameter `gc`, g stands for globals - which tells Vim to apply the command for all occurrences on a line,
    and c stand for `confirm`, which tells Vim to ask the user to press `y` before making the change.

This post can become even longer than this. I try to not share so many small details, but at the end it is still a lot
of information. Hope you enjoy reading.

What is next after knowing all of these? So far I can't answer this question myself. I know there are manythings I think
I know but I won't, and manything I know but haven't applied yet. For me, I just keep practicing every Vim techniques
I've learned, and when there is some difficulty while during some tasks that I feel like there should be a better way, I
just find out how other people do it. And most of the time, there are many better ways. That's how I learn Vim, this joy
of learning Vim can also be applied for everything in work and life.
