# Hygen

<img src="https://travis-ci.org/jondot/hygen.svg?branch=master">

A simple, fast, and flexible code generator and generator builder.

![](media/hygen.gif)

## Quick Start

Install `hygen`:

```
$ npm i -g hygen
```

And generate:

```
# generate all required worker files
$ hygen worker new --name reporter

# generate one specific file
$ hygen worker new:init --name reporter

# generate all files that correspond to a regular expression
$ hygen worker 'new:set.*' --name reporter
```

## Templates

`hygen` comes with prepacked templates for node.js workers and mailers, which you probably don't
need if you're not using a `hyperstack`. To see them run `hygen`.

For your own use, let's create a folder named `_templates` in your project root. In it, build the following template layout:

```
_templates/
  worker/
    new/
      index.ejs.t
```

Here's how:

```
$ mkdir -p _templates/worker/new
$ touch _templates/worker/new/index.ejs.t
```

Next, `index.ejs.t` should contain this (just copy and paste it):

```javascript
---
to: app/workers/<%=name%>.js
---
class <%= h.capitalize(name) %> {
    work(){
        // your code here!
    }
}
```

Finally, you can generate a worker like this:

```
$ hygen worker new --name cleaner

Loaded templates: _templates
       added: app/workers/foo.js
```

## hygen: a New Template Engine

`hygen` was built to have a good developer ergonomics; to avoid
cluttered template projects which are hard to reason about, and
to simplify overly complex generator workflows.

Ultimately, it cuts the time from having an itch for a template in your current
project to code generated with it and others benefiting from it.

Let's go over why `hygen` is different. Here's our example from before:

```
_templates/
  worker/
    new/
      index.ejs.t
```

### Template Locality

`hygen` picks up a local `_templates` folder
at any folder level of your project you're working from (or an environment variable, or built-in
templates by building a new library with it).

### Folder Structure is Command Structure

Opinionated towards simplifying making generators; the folder structure _maps directly_ to the command structure:

```
$ hygen worker new --name jobrunner
```

Template parameters are given with `--flag VALUE`, as many as you'd like. In this example we've set a parameter named `name` to the value `jobrunner`.

### You can run a subcommand

A subcomman is a file inside a your folder structure. So if the structure is this:

```
_templates/
    worker/
      new/
        index.html.ejs
        setup.html.ejs
```

And you only want `setup`, you can run:

```
$ hygen worker new:setup
```

You can also use the subcommand as a regular expression so, these will do the same:

```
$ hygen worker new:se
```

```
$ hygen worker new:se.*
```

### Frontmatter for Decluttering

Finally, here's how a template looks like (in our example, `index.ejs.t`). Templates are [ejs](https://github.com/tj/ejs):

```javascript
---
to: app/workers/<%=name%>.js
---

class <%= h.capitalize(name) %> {
    work(){
        // your code here!
    }
}
```

The first part of the template is a [front matter](https://jekyllrb.com/docs/frontmatter/), stolen from Markdown, this is now part of a `hygen` template and is part of the reason of why your templates will feel more lightweight and flexible.

All frontmatter metadata _are also run through the template engine_ so feel free to use variables as you wish.

There's one required meta variable: `to`.
`to` points to where this file will be placed (folders are created as needed).

### Addition or Injection

By default templates are 'added' to your project as a new target file, but you can also choose to inject a template _into_ an existing target file.

For this to work, you need to use `inject: true` with the accompanied inject-specific props.

```yaml
---
to: package.json
inject: true
after: dependencies
skip_if: react-native-fs
---
"react-native-fs",
```

This template will add the `react-native-fs` dependency into a `package.json` file, but it will not add it twice (see `skip_if`).

Here are the available exclusive options for where to inject at:

* `before | after` - a regular expression / text to locate. The inject line will appear before or after the located line.
* `prepend | append` - add a line to start or end of file respectively.
* `line_at` - add a line at this exact line number.

You can guard against double injection:

* `skip_if` - a regular expression / text. If exists injection is skipped.

### Build Your Own

`hygen` is highly embeddable. You should be able to use it by simply listing it
as a dependency and having [this kind of workflow](src/index.js) in your binary.

```javascript
const { runner } = require('hygen')
const path = require('path')
const defaultTemplates = path.join(__dirname, 'templates')
runner(defaultTemplates)
```

# Development

The Hygen codebase has a functional style where possible. This is because naturally, it
feeds parameters and spits out files. Try to keep it that way.

Running `hygen` locally, rigged to your current codebase (note the additional `--` to allow passing flags)

```
$ yarn hygen -- mailer new --name foobar
```

Running tests in watch mode:

```
$ yarn watch
```

Note [here be dragons](src/__tests__/readme.md).

# Contributing

Fork, implement, add tests, pull request, get my everlasting thanks and a respectable place here :).

### Thanks:

To all [Contributors](https://github.com/jondot/hygen/graphs/contributors) - you make this happen, thanks!

# Copyright

Copyright (c) 2017 [Dotan Nahum](http://gplus.to/dotan) [@jondot](http://twitter.com/jondot). See [LICENSE](LICENSE.txt) for further details.