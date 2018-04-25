FROM cross-compiler:linux-x64

RUN mkdir -p /build
WORKDIR /build

#RUN apt -y install libboost-system-dev libboost-python-dev libboost-chrono-dev libboost-random-dev libssl1.0-dev
RUN apt update -y && apt upgrade -y && apt dist-upgrade -y && apt install -y libpython-all-dev grep coreutils
    
# Install Boost
ENV BOOST_VERSION 1.64.0
RUN curl -L https://dl.bintray.com/boostorg/release/1.64.0/source/boost_1_64_0.tar.gz | tar xvz && \
    cd boost* && \
    ./bootstrap.sh --with-python-root=/usr/local/lib/python2.7 && \
    echo "using gcc : linux : ${CROSS_TRIPLE}-c++ ;" > ${HOME}/user-config.jam && \
    ./b2 --with-date_time --with-system --with-chrono --with-random --with-system --with-python cxxflags=-I/usr/include/python2.7 cflags=-I/usr/include/python2.7 toolset=gcc-linux link=static threading=multi install && \
    rm -rf ${HOME}/user-config.jam && \
    rm -rf `pwd`













# Install OpenSSL
ENV OPENSSL_VERSION 1.0.2l
RUN curl -L https://dl.dropboxusercontent.com/s/vsw6ehrmdwagmy5/openssl-1.0.2l-OE.tar.xz | tar xvJ && \
    cd openssl* && \
    sed -i -e '/^"linux-x86_64"/ s/-m64 -DL_ENDIAN -O3 -Wall/-march=native -O2 -pipe -fstack-protector-strong -m64 -DL_ENDIAN -Wall/' Configure && \
    ./Configure no-shared threads no-rc5 no-zlib enable-tlsext no-ssl2 no-ssl3 enable-unit-test enable-ec_nistp_64_gcc_128 linux-x86_64} && \
    make -j4 && make install_sw && \
    rm -rf `pwd`
    
# Install libtorrent
ENV LIBTORRENT_VERSION libtorrent-1.1.4
RUN curl -L https://github.com/arvidn/libtorrent/archive/`echo ${LIBTORRENT_VERSION} | sed 's/\\./_/g'`.tar.gz | tar xz && \
    cd libtorren* && \
    ./autotool.sh && \
    \
    sed -i 's/$PKG_CONFIG openssl --libs-only-/$PKG_CONFIG openssl --static --libs-only-/' ./configure && \
    \
    CFLAGS="${CFLAGS} -march=native -O2 -pipe -fno-plt" \
    CXXFLAGS="${CXXFLAGS} ${CFLAGS}" \
    ./configure \
        --enable-static \
        --disable-shared \
        --with-pic \
        --enable-python-binding \
        --without-libgeoip \
        --without-libiconv \
        --disable-deprecated-functions && \
    make -j $(cat /proc/cpuinfo | grep processor | wc -l) && make install && \
    rm -rf `pwd`

#RUN ldd -v /usr/.go/bin/torrent2http
#RUN mv /usr/.go/bin/torrent2http /usr/.go/bin/torrent2http-RC1_1-95

WORKDIR /
