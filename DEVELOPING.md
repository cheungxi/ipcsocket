Developing ipcsocket requires the use of the
[sbt](https://www.scala-sbt.org/1.x/docs/index.html) build tool. To generate a
new version of the library for testing, run
```
sbt publishLocal
```
(or run `publishLocal` in the sbt command shell).  This will generate a snapshot
version that you can add to another sbt project with:
```
libraryDependencies += "org.scalasbt" % "ipcsocket" % "1.0.1-SNAPSHOT"
```
Project tests can be run with the `test` command.

#### JNI library

There are two implementations of both the `UnixDomain*Socket*` and the
`Win32Named*Socket*` classes. One implementation uses
[jna](https://en.wikipedia.org/wiki/Java_Native_Access#External_links) while the
other uses [jni](https://en.wikipedia.org/wiki/Java_Native_Interface). In order
to test the latter implementation, it is necessary to compile native code. This
requires a working installation of [gcc](https://gcc.gnu.org) (for posix
systems) and/or [mingw-w64](http://mingw-w64.org/doku.php) (for windows) in the
sbt library path. MingW is used to build windows native libraries and is
available for mac and linux as part of the standard package management systems.
Install on mac with homebrew using:
```
brew install mingw-w64
```
Install on ubuntu/debian with:
```
sudo apt install mingw-w64
```
There is a [chocolatey](https://chocolatey.org) package available for windows,
but it requires more work to get the toolchain to work correctly with the sbt
build.

Once mingw-w64 is set up, run:

```
sbt buildNativeArtifacts
```

to build the native artifacts. This is done automatically for the CI on Github Actions.

#### Releasing

Each release should include the latest binaries. The binaries are built during
each CI run on Github Actions. To include them, go to the latest passing Github
Actions at <https://github.com/sbt/ipcsocket/actions>. The required
artifacts can be found under the Artifacts section as
"dist-Linux.zip" and "dist-macOS.zip" links.

The dist-Linux.zip provides:
* src/main/resources/linux/x86_64/libsbtipcsocket.so
* src/main/resources/linux/aarch64/libsbtipcsocket.so
* src/main/resources/linux/loongarch64/libsbtipcsocket.so
* src/main/resources/win32/x86_64/sbtipcsocket.dll

while the dist-macOS.zip provides:
* src/main/resources/darwin/x86_64/libsbtipcsocket.dylib

Extract each of these files and overwrite the existing library in the project's
`src/main/resources` directory. Check in the overwritten libraries and push the
changes in a new PR that updates the binaries. Once that PR is merged, it is
safe to make a release off of that commit.

```sh
mv $HOME/Downloads/dist-Linux/win32/x86_64/sbtipcsocket.dll src/main/resources/win32/x86_64/sbtipcsocket.dll
mv $HOME/Downloads/dist-Linux/linux/x86_64/libsbtipcsocket.so src/main/resources/linux/x86_64/libsbtipcsocket.so
mv $HOME/Downloads/dist-Linux/linux/aarch64/libsbtipcsocket.so src/main/resources/linux/aarch64/libsbtipcsocket.so
mv $HOME/Downloads/libsbtipcsocket.dylib src/main/resources/darwin/x86_64/libsbtipcsocket.dylib
```
