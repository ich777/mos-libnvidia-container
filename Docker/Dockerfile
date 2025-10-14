FROM debian:buster-slim

RUN echo "deb http://archive.debian.org/debian/ buster main contrib non-free" > /etc/apt/sources.list && \
    echo "deb http://archive.debian.org/debian-security/ buster/updates main contrib non-free" >> /etc/apt/sources.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until

RUN apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get -y install --no-install-recommends apt-utils bmake build-essential bzip2 ca-certificates \
  curl devscripts dh-make fakeroot git libcap-dev libelf-dev libseccomp-dev lintian lsb-release m4 pkg-config xz-utils && \
  rm -rf /var/lib/apt/lists/*

ENV OS_ARCH="amd64"
ENV GOLANG_VERSION="1.22.12"
ENV BUILD_DIR=/tmp
ENV GOPATH=$BUILD_DIR/go
ENV PATH=/opt/go/bin:$PATH
ENV GPG_TTY=/dev/console
ENV PACKAGE_DIR=$BUILD_DIR/package

COPY daemon.json /opt/daemon.json

RUN curl https://storage.googleapis.com/golang/go${GOLANG_VERSION}.linux-${OS_ARCH}.tar.gz | tar -C /usr/local -xz
RUN chmod -R 777 /opt

CMD bash -c "cd $BUILD_DIR && \
  git clone --depth 1 --branch v${LIBNVIDIA_VERSION} https://github.com/NVIDIA/libnvidia-container.git && \
  cd $BUILD_DIR/libnvidia-container && \
  git checkout v${LIBNVIDIA_VERSION} && \
  sed -i '/if (syscall(SYS_pivot_root, \".\", \".\") < 0)/,+1 d' $BUILD_DIR/libnvidia-container/src/nvc_ldcache.c && \
  sed -i '/if (umount2(\".\", MNT_DETACH) < 0)/,+1 d' $BUILD_DIR/libnvidia-container/src/nvc_ldcache.c && \
  DESTDIR=$PACKAGE_DIR make LIB_VERSION=${LIBNVIDIA_VERSION} LIB_TAG=${LIBNVIDIA_VERSION} install prefix=/usr GO111MODULE=auto && \
  mkdir -p $PACKAGE_DIR/etc/docker $PACKAGE_DIR/usr/share/doc/libnvidia-container && \
  cp /opt/daemon.json $PACKAGE_DIR/etc/docker/daemon.json && \
  cat $BUILD_DIR/libnvidia-container/LICENSE* >> $PACKAGE_DIR/usr/share/doc/libnvidia-container/LICENSE && \
  cd $BUILD_DIR && \
  echo \"Package: libnvidia-container\nVersion: $LIBNVIDIA_VERSION\nArchitecture: amd64\nMaintainer: ich777\nDescription: libnvidia-container for MOS\" > $PACKAGE_DIR/DEBIAN/control && \
  cd $BUILD_DIR && \
  dpkg-deb --build package $BUILD_DIR/libnvidia-container_${LIBNVIDIA_VERSION}-1+mos_amd64.deb && \
  md5sum $BUILD_DIR/libnvidia-container_${LIBNVIDIA_VERSION}-1+mos_amd64.deb | awk '{print $1}' > $BUILD_DIR/libnvidia-container_${LIBNVIDIA_VERSION}-1+mos_amd64.deb.md5"
