FROM cross-compiler:linux-x86

RUN mkdir -p /build
WORKDIR /build

RUN apt-get -y install zlib1g-dev autoconf automake autopoint autotools-dev debhelper dh-autoreconf dh-strip-nondeterminism gettext intltool-debian libarchive-zip-perl libbz2-dev libcurl4-gnutls-dev libfile-stripnondeterminism-perl libpcre16-3 libpcre3-dev libpcre32-3 libpcrecpp0v5 libreadline-dev libsqlite3-dev libtool m4 po-debconf python-all re2c libssh2-1-dev libgpm-dev

RUN apt-get -y install lib32ncursesw5-dev lib32ncursesw5-dev libcurl4-openssl-dev libssl-dev

# Install lnav
ENV LNAV_VERSION master
RUN curl -L https://github.com/tstack/lnav/archive/${LNAV_VERSION}.tar.gz | tar xz && \
    cd lnav-*/ && \
    ./autogen.sh && \
    CC=${CROSS_TRIPLE}-gcc CXX=${CROSS_TRIPLE}-g++ \
    CFLAGS="${CFLAGS} -march=native -O2" \
    CXXFLAGS="${CXXFLAGS} ${CFLAGS}" \
    ./configure --disable-shared && \
    make -j7 && \
    cd src && cp lnav lnav55425 && \
    rm -rf `pwd`

WORKDIR /
