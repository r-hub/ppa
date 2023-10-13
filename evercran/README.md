
# Evercran PPA

## Usage

```sh
echo 'deb http://ppa.r-pkg.org/evercran sarge main' \
    >> /etc/apt/sources.list
apt-get update -y
apt-get install -y r-1.0.0
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
1. Copy the DEB files into `pool/`. (In the container or on the host.)
   ```
   for rver in `cat r-versions.txt`; do
   (
     cd pool/sarge
     curl -LO https://github.com/r-hub/R/releases/download/v${rver}/r-evercran-debian-3.1-${rver}_1-1_i386.deb
   )
   done
   ```
2. Create `Packages*` files:
   ```
   dpkg-scanpackages --arch i386 pool/sarge > dists/sarge/main/binary-i386/Packages
   dpkg-scanpackages --arch i386 pool/lenny > dists/lenny/main/binary-i386/Packages
   gzip -kf dists/sarge/main/binary-i386/Packages
   gzip -kf dists/lenny/main/binary-i386/Packages
   ```
3. Generate `Release` file
   ```
   (cd dists/sarge && ./sarge-release.sh > Release)
   (cd dists/lenny && ./lenny-release.sh > Release)
   ```
