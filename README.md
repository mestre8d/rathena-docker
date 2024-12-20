# rAthena - Docker

A container image that can compile and run rAthena emulator.

## How it works

The image only clones the rAthena official repository from GitHub and builds the server executables.

If you need any modifications the quick path is point a public repository that could be cloned.

## Build arguments

Pass those arguments to change the behavior of the build phase: 

#### CONFIGURE_FLAGS

Allows customizing flags to the [configure](https://github.com/rathena/rathena/blob/master/configure) file:

```
CONFIGURE_FLAGS='CXXFLAGS=-Werror -Wno-error=builtin-declaration-mismatch'
```

#### MAKE_FLAGS

Allows customizing flags to the make executable:
```
MAKE_FLAGS="--jobs=6"
```
#### RATHENA_SOURCE_REPO

Allows customizing the source repository of the rAthena source to be compiled:
```
RATHENA_SOURCE_REPO=https://github.com/rathena/rathena.git
```


