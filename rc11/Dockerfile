FROM cross-compiler:linux-x64
#FROM dockcross:linux-x64

RUN mkdir -p /build
WORKDIR /build

#RUN apt update -y && apt upgrade -y && apt dist-upgrade -y && echo oo

# Install Golang
ENV BOOTSTRAP_GO_VERSION release-branch.go1.4
ENV GO_VERSION release-branch.go1.10
RUN cd /usr/local && \
    curl -L https://github.com/golang/go/archive/${BOOTSTRAP_GO_VERSION}.tar.gz | tar xvz && \
    cd /usr/local/go-${BOOTSTRAP_GO_VERSION}/src && \
    GOOS=linux GOARCH=amd64 ./make.bash -v
RUN cd /usr/local && \
    curl -L https://github.com/golang/go/archive/${GO_VERSION}.tar.gz | tar xvz
RUN cd /usr/local/go-${GO_VERSION}/src && \
	echo ${GO_VERSION} > /usr/local/go-${GO_VERSION}/VERSION && \
    GOROOT_BOOTSTRAP=/usr/local/go-${BOOTSTRAP_GO_VERSION} GOOS=linux GOARCH=amd64 CGO_ENABLED=1 ./make.bash -v
ENV PATH ${PATH}:/usr/local/go-${GO_VERSION}/bin

# Install OpenSSL
ENV OPENSSL_VERSION 1.1.0h
RUN curl -L http://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz | tar xvz && \
    cd openssl-${OPENSSL_VERSION}/ && \
    sed -i -e '/^"linux-x86_64"/ s/-m64 -DL_ENDIAN -O3 -Wall/-march=westmere -O2 -pipe -m64 -DL_ENDIAN -Wall/' Configure && \
    CROSS_COMPILE=${CROSS_TRIPLE}- ./Configure threads no-shared no-zlib no-zlib-dynamic enable-ec_nistp_64_gcc_128 linux-x86_64 --prefix=${CROSS_ROOT} && \
    make && make install_sw && \
    rm -rf `pwd`

# Install Boost
ENV BOOST_VERSION 1_63_0
RUN curl -L https://dl.bintray.com/boostorg/release/1.63.0/source/boost_1_63_0.tar.gz | tar xvz && \
    cd boost* && \
    ./bootstrap.sh --prefix=${CROSS_ROOT} && \
    echo "using gcc : linux : ${CROSS_TRIPLE}-c++ ;" > ${HOME}/user-config.jam && \
    ./b2 --with-date_time --with-system --with-chrono --with-random --with-system --prefix=${CROSS_ROOT} cxxflags="-m64 -march=westmere -O3 -fPIC" cflags="-m64 -march=westmere -O3 -fPIC" toolset=gcc-linux link=static variant=release threading=multi target-os=linux install && \
    rm -rf ${HOME}/user-config.jam && \
    rm -rf `pwd`

# Install SWIG
ENV SWIG_VERSION rel-3.0.12
RUN curl -L https://github.com/swig/swig/archive/${SWIG_VERSION}.tar.gz | tar xvz && \
    cd swig* && \
    ./autogen.sh && \
    ./configure && \
    make -j $(cat /proc/cpuinfo | grep processor | wc -l) && make install && \
    rm -rf `pwd`

# Install libtorrent
ENV LIBTORRENT_VERSION libtorrent-1.1.7
RUN curl -L https://github.com/arvidn/libtorrent/archive/`echo ${LIBTORRENT_VERSION} | sed 's/\\./_/g'`.tar.gz | tar xz && \
    cd libtorren* && \
    ./autotool.sh && \
    \
    sed -i 's/$PKG_CONFIG openssl --libs-only-/$PKG_CONFIG openssl --static --libs-only-/' ./configure && \
    \
    CC=${CROSS_TRIPLE}-gcc CXX=${CROSS_TRIPLE}-g++ \
    CFLAGS="${CFLAGS} -march=westmere -O2" \
    CXXFLAGS="${CXXFLAGS} ${CFLAGS}" \
    ./configure \
        --enable-static \
        --disable-shared \
        --without-libgeoip \
        --without-libiconv \
        --disable-deprecated-functions \
        --host=${CROSS_TRIPLE} \
        --prefix=${CROSS_ROOT} \
        --with-boost=${CROSS_ROOT} --with-boost-libdir=${CROSS_ROOT}/lib && \
    \
    make -j $(cat /proc/cpuinfo | grep processor | wc -l) && make install && \
    rm -rf `pwd`

# Build
ENV GOPATH=/usr/.go
ENV GOOS=linux
ENV CGO_ENABLED=1
ENV CGO_NO_EMULATION=1
ENV HTTP_PROXY "http://192.168.1.2:999"
RUN go get -u -v -ldflags "-s -w" github.com/dimitriss/torrent2http
RUN ldd -v /usr/.go/bin/torrent2http
RUN mv /usr/.go/bin/torrent2http /usr/.go/bin/torrent2http-RC1_1-1_419
#VOLUME /usr/.go/bin /pkg

WORKDIR /
