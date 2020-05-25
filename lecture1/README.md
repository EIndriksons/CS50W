# Lecture 1 - HTML, CSS

## More on Git
‘Branching’ is a feature of Git that allows a project to move in multiple different directions simultaneously. There is one `master` branch that is always usable, but any number of new branches can be created to develop new features. Once ready, these branches can then be merged back into `master`.

When working in a Git repository, `HEAD` refers to the current branch being worked on. When a different branch is ‘checked out’, the `HEAD` changes to indicate the new working branch.

When merging a branch back into `master`, there is the possibility for merge conflicts to arise. These can be resolved in the same way discussed in Lecture 0.

Some Git commands related to branching:
- `git branch` : list all the branches currently in a repository
- `git branch <name>` : create a new branch called `name`
- `git checkout <name>` : switch current working branch to `name`
- `git merge <name>` : merge branch `name` into current working branch (normally `master`)

Any version of a repository that is not stored locally on a device is called a ‘remote’. ‘Origin’ is used to refer to the remote from which the local repository was originally downloaded from.

Some Git commands related to remotes:
- `git fetch` : download all of the latest commits from a remote to a local device
- `git merge origin/master` : merge `origin/master`, which is the remote version of a repository normally downloaded with `git fetch`, into the local, preexesiting `master` branch
    * Note that `git pull` is equivalent to running `git fetch` and then `git merge origin/master`

A ‘fork’ of a repository is an entirely separate repository which is copy of the original repository. A forked repository can be managed and modified like any other, all without affecting the original copy.

Open source projects are often developed using forks. There will be one central version of the software which contributors will fork and improve on, and when they want these changes to be merged into the central repository, they submit a ‘pull request’.

A pull request can be made to merge a branch of a repository with another branch of the same repository or even a different repository. Pull requests are a good way to get feedback on changes from collaborators on the same project.

Note that forks and pull requests are both GitHub specific features.

## More on HTML
More useful HTML tags:
- `<a href="path/to/hello.html">Click here!</a>` : link to `hello.html`, some URL, or some other content marked by `id` by passing `#id` to `href`
- `<input type="radio"> Option 1` : radio-button option for a form, where only 1 out of all the options may be selected ``` html

There are lots of new useful tags with HTML5, but not all browsers, especially older browsers, will support these new features. Nonetheless, these new features can be used with increasing confidence that they will be rendered appropriately for a significant portion of users.

## More on CSS
CSS selectors are used to select different parts of a website to style in particular ways.

Some common CSS selectors:

1. Select `h1` and `h2`

```css
h1, h2 {
    color: red;
}
```

2. Select all `li` that are descendants of `ol` (not necessarily immediate descendants)

```css
ol li {
    color: red;
}
```

3. Select all `li` that are immediate children of `ol`

```css
ol > li {
    color: red;
}
```

4. Select all `input` fields with the attribute `type=text`

```css
input[type=text] {
    background-color: red;
}
```

5. Select all `button`s with the pseudoclass `hover`

```css
button:hover {
    background-color: orange;
}
```

**Note:** A ‘pseudoclass’ is a special state of an HTML element. In this example, the state is whether or not the cursor is hovering over a button.

6. Select all `before` pseudoelements of the element `a`

```css
a::before {
    content: "\21d2 Click here: ";
    font-weight: bold;
}
```

**Note:** A ‘pseudoelement’ is a way to affect certain parts of an HTML element. In this example, the before selector applies content with its included styling before the contents of all a elements.

Select all `selection` pseudoelements of the element `p`

```css
p::selection {
    color: red;
    background-color: yellow;
}
```