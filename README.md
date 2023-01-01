Set up Cygwin
=============

[![Test](https://github.com/egor-tensin/setup-cygwin/actions/workflows/test.yml/badge.svg)](https://github.com/egor-tensin/setup-cygwin/actions/workflows/test.yml)

This GitHub action sets up Cygwin in your workflow run.

1. Installs Cygwin.
2. Installs any additional packages specified.

Use it in your workflow like this:

    - name: Set up Cygwin
      uses: egor-tensin/setup-cygwin@v3
      with:
        packages: cmake gcc-g++

    # Cygwin executables are added to PATH, so you can call them directly:
    - run: |
        ls.exe -Al
        pwd.exe

    # Alternatively, you can set Cygwin's bash as the shell to use:
    - run: |
        # This is a real bash script!
        basic() {
            ls -Al
            pwd
        }
        basic
      shell: C:\tools\cygwin\bin\bash.exe --login --norc -eo pipefail -o igncr '{0}'

* Specify any additional packages to be installed as a space-separated list in
the `packages` parameter.
* Set the installation directory using the `install-dir` parameter
(C:\tools\cygwin is the default).
* The `CYGWIN` environment variable is set to an empty string by default.
Provide a custom value using the `env` parameter.

API
---

| Input       | Value   | Default | Description
| ----------- | ------- | ------- | -----------
| install-dir | *empty* | ✓       | Install to C:\tools\cygwin.
|             | *any*   |         | Install to the specified directory.
| packages    | *empty* | ✓       | Don't install any additional packages.
|             | *any*   |         | Space-separated package names.
| env         | *empty* | ✓       | Set `%CYGWIN%` to an empty string.
|             | *any*   |         | Set `%CYGWIN%` to the specified value.
| hardlinks   | *any*   | ✓       | Don't convert any symlinks.
|             | 1       |         | Convert symlinks in /usr/bin to hardlinks.

The paths to the Cygwin binaries are added to the PATH variable.

Line terminators
----------------

When using Cygwin's `bash` as the shell to run the step script (or if you see
something like `\r': command not found` in logs), make sure that `igncr` is
present in the SHELLOPTS variable be either:

* passing `-o igncr` to bash.exe,

      job:
        name: Test job
        runs-on: windows-latest
        defaults:
          run:
            shell: C:\tools\cygwin\bin\bash.exe --login -o igncr '{0}'
        steps:
          - ...

* or setting the SHELLOPTS variable in the `env:` block.

      job:
        name: Test job
        runs-on: windows-latest
        env:
          SHELLOPTS: igncr
        defaults:
          run:
            shell: C:\tools\cygwin\bin\bash.exe --login '{0}'
        steps:
          - ...

Please avoid using \r\n as line separators in your shell scripts, as Linux
shells will choke on them.

Executable symlinks
-------------------

Some packages install symlinks in /usr/bin along with real executables.
An example is package "gcc-core", which (as of November 2021) installs symlink
`cc`, pointing to the real executable `gcc.exe`.
Calling Cygwin symlinks from Windows' command prompt is unsupported, but might
be convenient so there's an option to convert them to hardlinks instead (the
`hardlinks` parameter).

License
-------

Distributed under the MIT License.
See [LICENSE.txt] for details.

[LICENSE.txt]: LICENSE.txt
