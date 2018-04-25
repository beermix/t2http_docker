FROM cross-compiler:linux-x64

RUN mkdir -p /build
WORKDIR /build

# Install miniupnpc
RUN apt-get -y install cmake
RUN curl -L http://miniupnp.free.fr/files/download.php?file=miniupnpc-2.0.20170509.tar.gz | tar xvz && \
    cd miniupnp* && \
    cmake -DCMAKE_BUILD_TYPE=MinSizeRel -DUPNPC_BUILD_SHARED=0 -DUPNPC_BUILD_STATIC=1 -DCMAKE_INSTALL_PREFIX=/usr && \
    make && make install && \
    rm -rf `pwd`

# Install OpenSSL
ENV OPENSSL_VERSION 1.0.2l
RUN curl -L https://dl.dropboxusercontent.com/s/vsw6ehrmdwagmy5/openssl-1.0.2l-OE.tar.xz | tar xvJ && \
    cd openssl* && \
    sed -i -e '/^"linux-x86_64"/ s/-m64 -DL_ENDIAN -O3 -Wall/-march=native -O2 -m64 -DL_ENDIAN -Wall/' Configure && \
    CROSS_COMPILE=${CROSS_TRIPLE}- ./Configure no-shared threads no-rc5 no-zlib enable-tlsext no-ssl2 no-ssl3 enable-unit-test enable-ec_nistp_64_gcc_128 linux-x86_64 --prefix=${CROSS_ROOT} && \
    make -j $(cat /proc/cpuinfo | grep processor | wc -l) && make install_sw && \
    rm -rf `pwd`

# Install Boost
ENV BOOST_VERSION 1.64.0
RUN curl -L https://dl.bintray.com/boostorg/release/1.64.0/source/boost_1_64_0.tar.gz | tar xvz && \
    cd boost* && \
    ./bootstrap.sh --prefix=/usr && \
    echo "using gcc : linux : ${CROSS_TRIPLE}-c++ ;" > ${HOME}/user-config.jam && \
    ./b2 --with-date_time --with-system --with-filesystem --with-program_options --with-date_time --prefix=/usr cxxflags=-fPIC cflags=-fPIC toolset=gcc-linux link=static variant=release threading=multi target-os=linux install && \
    rm -rf ${HOME}/user-config.jam && \
    rm -rf `pwd`
    
RUN apt-get -y install zlib1g-dev
# Install libtorrent
ENV i2pd_VERSION 9739e67
RUN curl -L https://github.com/PurpleI2P/i2pd/archive/`echo ${i2pd_VERSION} | sed 's/\\./_/g'`.tar.gz | tar xvz && \
    cd i2pd* && \
    \
    sed -i 's/USE_STATIC	:= no/USE_STATIC	:= yes/' ./Makefile && \
    sed -i 's/USE_UPNP	:= no/USE_UPNP	:= yes/' ./Makefile && \
    \
    CC=${CROSS_TRIPLE}-gcc CXX=${CROSS_TRIPLE}-g++ \
    CFLAGS="${CFLAGS} -march=native -O3 -pipe" \
    CXXFLAGS="${CXXFLAGS} ${CFLAGS}" \
    make -j $(cat /proc/cpuinfo | grep processor | wc -l) && \
    rm -rf `pwd`



WORKDIR /
