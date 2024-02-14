
# Personal Package Archive for R-hub

## Current packages

* `protobuf-clang`: protobuf compiled with clang, linking against
  libc++. It needs the libc++-16 libs (see `Dockerfile`).
  [Dockerfile for Ubuntu 22.04](https://github.com/r-hub/containers/blob/main/dependencies/protobuf/Dockerfile)
* `protobuf-clang17`: similar to the previous one, with clang 17.
* `protobuf-clang18`: similar to the previous ones, with clang 18.
* `skopeo`: a newer version, to be able to push packages to GHCR.
  [Dockerfile for Ubuntu 22.04](https://github.com/r-hub/containers/blob/main/dependencies/skopeo/Dockerfile)

## Using this repository

```sh
echo "deb https://ppa.r-pkg.org jammy main" \
    > /etc/apt/sources.list.d/rhub.list
curl -L https://raw.githubusercontent.com/r-hub/ppa/main/rhub.gpg.key |
    apt-key add -
apt-get update -y
apt-get install -y ...
```

## Adding new packages

0. You need an Ubuntu or Debian machine, e.g.
   ```
   docker run -ti -v `pwd`:/root/ppa ubuntu:22.04 bash
   ```
   and a couple of packages:
   ```
   apt-get update && apt-get install -y git dpkg-dev gpg
   ```
1. Copy the DEB files into `pool/main`.
2. Create `Packages*` files:
   ```
   dpkg-scanpackages --arch amd64 pool/ > dists/jammy/main/binary-amd64/Packages
   dpkg-scanpackages --arch arm64 pool/ > dists/jammy/main/binary-arm64/Packages
   gzip -kf dists/jammy/main/binary-amd64/Packages
   gzip -kf dists/jammy/main/binary-arm64/Packages
   ```
3. Generate `Release` file
   ```
   (cd dists/jammy && ../../generate-release.sh > Release)
   ```
4. Import private key for signing
   ```
   gpg --armor --import pgp-key.private
   ```
5. Sign the `Release` file
   ```
   export GPG_TTY=$(tty)
   cat dists/jammy/Release |
     gpg --default-key csardi.gabor@gmail.com -abs \
     > dists/jammy/Release.gpg
   cat dists/jammy/Release |
     gpg --default-key csardi.gabor@gmail.com -abs --clearsign \
     > dists/jammy/InRelease
   ```
6. Test
   ```
   echo "deb http://127.0.0.1:8000/ jammy main" \
      > /etc/apt/sources.list.d/rhub.list
   apt-get install -y python3 curl
   python3 -m http.server
   ```
   From another terminal:
   ```
   curl -L http://127.0.0.1:8000/rhub.gpg.key | apt-key add -
   apt-get update
   apt-get install skopeo
   skopeo --version
   ```

## Thanks

[Creating and hosting your own deb packages and apt repo](https://earthly.dev/blog/creating-and-hosting-your-own-deb-packages-and-apt-repo/) by Alex Couture-Beil @ [Earthly](https://earthly.dev/).
