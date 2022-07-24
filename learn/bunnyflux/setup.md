
---
layout: book
title: Setting Up Bunny Flux
permalink: /learn/bunnyflux/setup.md
---

# Setup

Getting started is always the hardest part. Thankfully, this is made easier
with the Fortran Package Manager. We can create our new project with

```sh
$ fpm new bunnyflux
```

fpm creates a new `bunnyflux` directory that looks like this:

```
bunnyflux/
  app/
    main.f90
  src/
    bunnyflux.f90
  test/
    check.f90
  fpm.toml
  README.md
```

A fpm package (which we just created) is comprised of a *library* and an
*application*. The package also bundles any *tests* that ensure the correctness
of the program.

A *library* is a bundle of code that can be executed by the *application*, or by
someone else who wants to use your package in their own project. Libraries can
be static or dynamic. For this project we will be using the default, which is a
static library. This has a `.a` extension on Linux and a `.lib` extension on
Windows.

Each directory that fpm created has a specific purpose. `src/` contains source
files for the library. `app/` contains source files for the application, and
`test/` holds source files for any tests.

The `fpm.toml` file is used to describe the package to fpm so it can build it
properly. 

`README.md` is a markdown file, used to describe the package to the world. Some
version control hosting sites, like Github or Gitlab, will display the contents
of this file on their website in a nicely formatted way.

fpm has also automatically initialized a git repository in the package directory.

First things first, let's add a brief description to the `README.md` file:

```markdown
# Bunny Flux

In which I make up a numerical method to solve a problem that no one cares
about to demonstrate features of Modern Fortran! (Also, Bunnies!)
```
That's good enough for now. Now on to `fpm.toml`.  Let's fill in all the header information:

```toml
name = "Bunny Flux"
version = "0.0.0"
license = "mit"
author = "Dalon Work"
maintainer = "dwwork@gmail.com"
copyright = "Copyright 2022, Dalon Work
```

fpm also lets us specify other packages that we want to use in our package. A
dedicated team of individuals has created many excellent fpm packages that we
can use, most importantly the 'fortran-stdlib' project. Bunny Flux will lean on this project, but first we have to tell fpm that we want to do that. At the bottom of 'fpm.toml' we can add the following lines:

```toml
[dependencies]
stdlib.git = "https://github.com/fortran-lang/stdlib
stdlib.branch = "stdlib-fpm"

[dev-dependencies]
stdlib.git = "https://github.com/fortran-lang/stdlib"
stdlib.branch = "stdlib-fpm"
```

The first paragraph tells fpm that the Bunny Flux library and application will
rely on stdlib, and the second paragraph does the same for the tests.  The
first time we try to build, fpm will automatically download and build the
stdlib package for us. If you've ever experienced the brain-fogging tedium of
getting someone else's code to compile, this killer fpm feature is amazing.

Now let's try a test build:

```sh
fpm build
```

Oh dear -- apparently it's not all rainbows and butterflies like I promised. I got an error while building stdlib:

```
build/dependencies/stdlib/src/stdlib_hash_64bit.f90:66:29:

   66 |         pow64_over_phi = int(z'9E3779B97F4A7C15', int64)
      |                             1
Error: Arithmetic overflow converting INTEGER(16) to INTEGER(8) at (1). This check can be disabled with the option ‘-fno-range-check’
compilation terminated due to -fmax-errors=1.
<ERROR> Compilation failed for object " build_dependencies_stdlib_src_stdlib_hash_64bit.f90.o "
<ERROR>stopping due to failed compilation
STOP 1
```

After some investigation, turns out this is a gfortran bug in my version of
gfortran on my Linux Mint system. (compilers aren't perfect!), and we can
overcome it by adding the compile flag as suggested to our compile line.
Let's try again:

```sh
fpm build --flag -fno-range-check
```

That worked! It downloaded and built everything. This is now a good point to create our first git commit.








