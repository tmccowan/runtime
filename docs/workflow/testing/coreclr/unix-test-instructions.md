Building and running tests on Linux, macOS, and FreeBSD
======================================================

CoreCLR tests
-------------

## Building

Build CoreCLR on [Unix](../../building/coreclr/linux-instructions.md).

## Building the Tests

Dotnet CLI is required to build the tests. This can be done on any platform then copied over if the architecture or OS does not support Dotnet.

To build the tests on Unix:

```sh
./src/coreclr/build-test.sh
```

Please note that this builds the Priority 0 tests. To build priority 1:

```sh
./src/coreclr/build-test.sh -priority1
```

## Building Individual Tests

During development there are many instances where building an individual test is fast and necessary. All of the necessary tools to build are under `coreclr`. It is possible to use `~/runtime/dotnet.sh msbuild` as you would normally use MSBuild with a few caveats.

**!! Note !! -- Passing /p:TargetOS=[OSX|Linux] is required.**

## Building an Individual Test

```sh
./dotnet.sh msbuild src/coreclr/tests/src/path-to-proj-file /p:TargetOS=<TargetOS> /p:Configuration=<BuildType>
```

## Running Tests

The following instructions assume that on the Unix machine:
- The CoreCLR repo is cloned at `/mnt/coreclr`

`src/coreclr/build-test.sh` will have set up the `Core_Root` directory correctly after the test build.

```sh
./src/coreclr/tests/runtest.sh x64 checked
```

Please use the following command for help.

```sh
./src/coreclr/tests/runtest.sh -h
```

### Unsupported and temporarily disabled tests

To support building all tests for all targets on single target, we use
the conditional property

```xml
<CLRTestTargetUnsupported Condition="...">true</CLRTestTargetUnsupported>
```

This property disables building of a test in a default build. It also
disables running a test in the bash/batch wrapper scripts. It allows the
test to be built on any target in CI when the `allTargets` option is
passed to the `build-test.*` scripts.

Tests which never should be built or run are marked

```xml
<DisableProjectBuild>true</DisableProjectBuild>
```

This propoerty should not be conditioned on Target properties to allow
all tests to be built for `allTargets`.

PAL tests
---------

Build CoreCLR with PAL tests on the Unix machine:

```sh
./src/coreclr/build-runtime.sh -skipgenerateversion -nopgooptimize \
    -cmakeargs -DCLR_CMAKE_BUILD_TESTS=1
```

Run tests:

```sh
./src/coreclr/src/pal/tests/palsuite/runpaltests.sh $(pwd)/artifacts/obj/coreclr/$(uname).x64.Debug
```

Test results will go into: `/tmp/PalTestOutput/default/pal_tests.xml`
