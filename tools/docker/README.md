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

You can start up new instances of the interactive environment with
`docker run` or `podman run`.
This may or may not require root privileges, read below for more information.

Note: Replace `$ANDROID_BUILD_TOP` with where the Android source directory resides.
```
$ docker run -it --rm -v $ANDROID_BUILD_TOP:/src android-build-focal
> repo init -u https://github.com/GrapheneOS/platform_manifest.git -b 12
> repo sync -j$(nproc)
> source script/envsetup.sh
> choosecombo release redfin user 
```

## Rootless Container Engine

By now, both, Docker and Podman support rootless containers, that is, running
the Docker/Podman daemon without requiring root permissions.
This is described in
https://docs.docker.com/engine/security/rootless/
for Docker and here
https://docs.podman.io/en/latest/markdown/podman.1.html?highlight=rootless#rootless-mode
for Podman.

Note that in this case you may also need SELinux relabeling by appending `:Z` to the
volume map, i.e., `-v foo:bar:Z`.

## Rootfull Container Engine

If you do not want to setup or have reasons against running rootless Docker/Podman,
you need to run the above command using `sudo` or add yourself to the `docker`
or `podman` group, otherwise you will get the error
`Got permission denied while trying to connect to the Docker daemon socket`

Note that using sudo to run this image may cause problems,
such as the `root` users `gitconfig` being used instead of your own users.

```
# Create the docker group, if it doesn't already exist
$ sudo groupadd docker

# Add your user to this group.
# If you are not currently logged in to your user account, replace '$USER' with your username
$ sudo usermod -aG docker $USER
```
Now logout and log back in again, and you should be able to use `docker`/`podman`
without `sudo`.

## Emulator

If you want to run the emulator, you need to use the rootfull container engine
and also pass the `--privileged` flag to `docker run`/`podman run` in order
for the container to be allowed to access the kernel (KVM).
