# CentOS 7 for el7 kernel
FROM centos:7

# Update base packages and install essential packages
RUN yum update -y && \
    yum install -y \
    gcc \
    gcc-c++ \
    make \
    yum-utils \
    zlib-devel \
    bzip2-devel \
    libffi-devel \
    wget \
    tar \
    libuuid-devel \
    glibc-langpack-en \
    pkgconfig \
    ncurses-devel \
    sqlite-devel \
    readline-devel \
    gdbm-devel \
    db4-devel \
    libpcap-devel \
    xz-devel \
    expat-devel \
    perl \
    coreutils \
    tcl-devel \
    tk-devel \
    openssl-devel \
    which \
    centos-release-scl && \
    yum install -y devtoolset-11

# Install Development Tools and additional groups
RUN yum groups mark install "Development Tools" && \
    yum groups install -y "Development Tools" && \
    yum groupinstall -y "Development Libraries" "Compatibility Libraries"

# Set locale to UTF-8
RUN localedef -i en_US -f UTF-8 en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Download, compile, and install the latest OpenSSL
WORKDIR /usr/src
RUN source /opt/rh/devtoolset-11/enable && \
    wget https://www.openssl.org/source/openssl-1.1.1u.tar.gz && \
    tar -xzf openssl-1.1.1u.tar.gz && \
    cd openssl-1.1.1u && \
    ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl && \
    make -j$(nproc) && \
    make install && \
    ln -sf /usr/local/openssl/bin/openssl /usr/bin/openssl

# Set environment variables for OpenSSL
ENV LD_LIBRARY_PATH=/usr/local/openssl/lib:$LD_LIBRARY_PATH
ENV CPATH=/usr/local/openssl/include:$CPATH
ENV PATH=/usr/local/openssl/bin:$PATH
ENV PKG_CONFIG_PATH=/usr/local/openssl/lib/pkgconfig:$PKG_CONFIG_PATH

# Ensure pkg-config can find OpenSSL
RUN echo "/usr/local/openssl/lib" > /etc/ld.so.conf.d/openssl.conf && ldconfig

# Download and compile Python 3.12 source code
WORKDIR /usr/src
RUN source /opt/rh/devtoolset-11/enable && \
    wget https://www.python.org/ftp/python/3.12.0/Python-3.12.0.tgz && \
    tar -xzf Python-3.12.0.tgz && \
    cd Python-3.12.0 && \
    CFLAGS="$(pkg-config --cflags openssl)" \
    LDFLAGS="$(pkg-config --libs openssl)" \
    ./configure --prefix=/usr/local/python3.12 --with-openssl=/usr/local/openssl --enable-optimizations --enable-shared --with-lto && \
    make -j$(nproc) && \
    make altinstall

# Create a portable Python package
RUN mkdir -p /portable-python && \
    cp -r /usr/local/python3.12 /portable-python/python3.12 && \
    cp -r /usr/local/openssl /portable-python/openssl && \
    cd /portable-python && \
    tar czf /python3.12-portable.tar.gz .

# Verify the installation
ENV LD_LIBRARY_PATH=/usr/local/python3.12/lib:$LD_LIBRARY_PATH
RUN source /opt/rh/devtoolset-11/enable && /usr/local/python3.12/bin/python3.12 --version

# Compress the Python 3.12 installation
RUN cd /portable-python && \
    tar czf /python3.12-portable.tar.gz .

# Copy the result to the host
CMD ["cp", "/python3.12-portable.tar.gz", "/host"]
