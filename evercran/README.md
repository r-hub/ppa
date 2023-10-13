
# Evercran PPA

## Usage

### Sarge

```sh
echo 'deb http://ppa.r-pkg.org/evercran sarge main' \
    >> /etc/apt/sources.list
apt-get update -y
apt-get install -y r-1.0.0
```

### Etch

```sh
echo 'deb http://ppa.r-pkg.org/evercran etch main' \
    >> /etc/apt/sources.list
apt-get update -y
apt-get install -y r-2.6.0
```

### Lenny

```sh
echo 'deb http://ppa.r-pkg.org/evercran lenny main' \
    >> /etc/apt/sources.list
apt-get update -y
apt-get install -y r-2.9.0
```

### Squeeze

```sh
echo 'deb http://ppa.r-pkg.org/evercran squeeze main' \
    >> /etc/apt/sources.list
apt-get update -y
apt-get install -y r-2.13.0
```

See <https://github.com/r-hub/evercran> for more about evercran.

## Updating

0. You need an Ubuntu or Debian machine, e.g.
   ```
   docker run -ti -v `pwd`:/root/ppa ubuntu:22.04 bash
   ```
   and a couple of packages:
   ```
   apt-get update && apt-get install -y git dpkg-dev gpg
   ```

1. Create directories
   ```
   cd ~/ppa
   mkdir -p pool/sarge pool/etch pool/lenny pool/squeeze
   mkdir -p dists/sarge/main/binary-i386
   mkdir -p dists/etch/main/binary-i386
   mkdir -p dists/lenny/main/binary-i386
   mkdir -p dists/squeeze/main/binary-i386
   ```

2. Copy the DEB files into `pool/`. (In the container or on the host.)

3. Create `Packages*` files:
   ```
   dpkg-scanpackages --arch i386 pool/sarge   > dists/sarge/main/binary-i386/Packages
   dpkg-scanpackages --arch i386 pool/etch    > dists/etch/main/binary-i386/Packages
   dpkg-scanpackages --arch i386 pool/lenny   > dists/lenny/main/binary-i386/Packages
   dpkg-scanpackages --arch i386 pool/squeeze > dists/squeeze/main/binary-i386/Packages
   gzip -kf dists/sarge/main/binary-i386/Packages
   gzip -kf dists/etch/main/binary-i386/Packages
   gzip -kf dists/lenny/main/binary-i386/Packages
   gzip -kf dists/squeeze/main/binary-i386/Packages
   ```

4. Generate `Release` file
   ```
   (cd dists/sarge   && ./sarge-release.sh   > Release)
   (cd dists/etch    && ./etch-release.sh    > Release)
   (cd dists/lenny   && ./lenny-release.sh   > Release)
   (cd dists/squeeze && ./squeeze-release.sh > Release)
   ```

5. Import private key for signing
   ```
   gpg --armor --import pgp-key.private
   ```
