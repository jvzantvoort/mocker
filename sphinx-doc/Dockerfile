FROM centos:7.3.1611
MAINTAINER John van Zantvoort "John@vanzantvoort.org"
ENV LANG C

# gpg: key 18ADD4FF: public key "Benjamin Peterson <benjamin@python.org>" imported
RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys C01E1CAD5EA2C4F0B8E3571504C367C218ADD4FF

ENV PYTHON_VERSION 2.7.10
ENV APPDIR /opt/sphinxdoc

# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 9.0.1

RUN yum -y -q install libxml2-devel
RUN yum -y -q install bzip2-devel
RUN yum -y -q install sqlite-devel
RUN yum -y -q install openssl-devel
RUN yum -y -q install zlib-devel
RUN yum -y -q install readline-devel
RUN yum -y -q install ncurses-devel
RUN yum -y -q install libffi-devel
RUN yum -y -q groupinstall "Development Tools"


RUN set -x \
	&& mkdir -p /usr/src/python \
	&& curl -SL "https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz" -o python.tar.xz \
	&& curl -SL "https://www.python.org/ftp/python/$PYTHON_VERSION/Python-$PYTHON_VERSION.tar.xz.asc" -o python.tar.xz.asc \
	&& curl --location --show-error --output /usr/src/python/get-pip.py 'https://bootstrap.pypa.io/get-pip.py' \
	&& gpg --verify python.tar.xz.asc \
	&& tar -xJC /usr/src/python --strip-components=1 -f python.tar.xz \
	&& rm python.tar.xz* \
	&& cd /usr/src/python \
	&& ./configure --enable-shared --prefix ${APPDIR} --enable-unicode=ucs4 \
	&& make -j$(nproc) \
	&& make install \
	&& ldconfig

RUN set -x \
	&& ${APPDIR}/bin/python2 /usr/src/python/get-pip.py

RUN pip install --no-cache-dir --upgrade pip==$PYTHON_PIP_VERSION

#	&& rm -rf /usr/src/python
ADD requirements.txt /tmp/requirements.txt

# install "virtualenv", since the vast majority of users of this image will want it
RUN pip install -r /tmp/requirements.txt

RUN yum clean all

CMD ["/bin/bash"]
