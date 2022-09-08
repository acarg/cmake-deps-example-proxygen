[![Build](https://github.com/acarg/cmake-deps-example-proxygen/actions/workflows/main.yml/badge.svg)](https://github.com/acarg/cmake-deps-example-proxygen/actions/workflows/main.yml)

# cmake-deps-example-proxygen
An example showing how to handle dependencies with CMake, here using Proxygen.
It goes with [this blog post](https://www.acarg.ch/posts/cmake-deps/).

> Warning: This is not trying to do anything useful with Proxygen! I just
chose it as an example because it is not trivial to build its dependencies.

## How to build

This project depends on Proxygen, which has a few dependencies (listed
[here](dependencies/)).

### With a package manager

It turns out that Proxygen is available in the Arch AUR repo.
That means that it can be installed e.g. using `yay`.

It can be done like this:

1. Install the dependencies:

    ```
    yay -S proxygen
    ```

2. Build the project

    ```
    cmake -Bbuild -S.
    cmake --build build
    ```

The limitation here is that your package manager may not have Proxygen.
For instance, it was outdated when I first tried it, and therefore
unavailable.

### With the helper script

1. Build the dependencies and install them into `./dependencies/install`:

    ```
    cmake -DCMAKE_INSTALL_PREFIX=dependencies/install -DCMAKE_PREFIX_PATH=$(pwd)/dependencies/install -Bdependencies/build -Sdependencies
    cmake --build dependencies/build
    ```

Have a look into `dependencies/install`, and see that they were installed
_locally_. Now we can tell CMake to look there when using `find_package`,
by providing the path: `-DCMAKE_PREFIX_PATH=/path/to/dependencies/install`.

2. Build the project using the dependencies installed in step 1:

    ```
    cmake -DCMAKE_PREFIX_PATH=$(pwd)/dependencies/install -Bbuild -S.
    cmake --build build
    ```

## About the CI

The CI runs the helper script. That's very convenient because as a result,
it does not result in a custom package manager. Also it gets very useful
for cross-compiling.

Finally, because the dependencies are installed locally, it's extremely
easy to [cache them](.github/workflows/main.yml#L24-L28):

```
- uses: actions/cache@v2
  id: cache
  with:
    path: ./install
    key: ${{ github.job }}-${{ hashFiles('./dependencies/**') }}
```

Here we tell the CI to cache all the files that are in `./install` at the
end of a successful build (note that `./install` is where the CI installs
them, instead of `./dependencies/install` above).

[Then](.github/workflows/main.yml#L30)
we can just tell the CI to not build the dependencies if they are
already in the cache:

```
if: steps.cache.outputs.cache-hit != 'true'
```
