The Dockerfile in this directory sets up an Ubuntu Focal image ready to build
GrapheneOS 11 or better.

# 1. Build the image
- Use `docker` or `podman`
```
# Copy your host gitconfig, or create a stripped down version
$ cp ~/.gitconfig gitconfig
$ docker build --build-arg userid=$(id -u) --build-arg groupid=$(id -g) --build-arg username=$(id -un) -t android-build-focal .
```

## 2. Start new instances
- Replace `/path/to/saved/source` with where you want android source code to be saved to
- `--privileged` is required for `emulator`, enabling KVM to function

```
$ docker run --privileged -it --rm -v /path/to/saved/source:/src android-build-focal
> repo init -u https://github.com/GrapheneOS/platform_manifest.git -b 12
> repo sync -j$(nproc)
> source script/envsetup.sh
> choosecombo release redfin user 
```
If using `podman` unprivileged containers, also add SELinux relabeling by appending `:Z` to the
volume map, i.e., `-v foo:bar:Z`.

## Running docker commands without sudo
The error `Got permission denied while trying to connect to the Docker daemon socket` is commonly resolved by using `sudo`.

Using sudo to build this image may cause problems, such as your `gitconfig` not being included.

To use docker without sudo, you need to add yourself to the `docker` user group:

```
# Create the docker group, if it doesn't already exist
$ sudo groupadd docker

# Add your user to this group.
# If you are not currently logged in to your user account, replace '$USER' with your username
$ sudo usermod -aG docker $USER
```
Now logout and log back in again, and you should be able to use `docker` without `sudo`.
