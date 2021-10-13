The Dockerfile in this directory sets up an Ubuntu Focal image ready to build
GrapheneOS 11 or better.

First, build the image (use `docker` or `podman`):
```
# Copy your host gitconfig, or create a stripped down version
$ cp ~/.gitconfig gitconfig
$ docker build --build-arg userid=$(id -u) --build-arg groupid=$(id -g) --build-arg username=$(id -un) -t android-build-focal .
```

Then you can start up new instances with:
```
$ docker run -it --rm -v $ANDROID_BUILD_TOP:/src android-buildf-focal
> repo init -u https://github.com/GrapheneOS/platform_manifest.git -b 12
> repo sync -j$(nproc)
> source script/envsetup.sh
> choosecombo release redfin user 
```
If using `podman` unprivileged containers, also add SELinux relabeling by appending `:Z` to the
volume map, i.e., `-v foo:bar:Z`.
