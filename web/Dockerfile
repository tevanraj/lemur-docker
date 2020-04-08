FROM ubuntu:18.04
LABEL maintainer="Netflix Open Source Development <talent@netflix.com>"

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
	apt-get install -y software-properties-common && \
	add-apt-repository ppa:deadsnakes/ppa -y && \
	apt-get update && \
	apt-get install -y curl git build-essential sudo \
	python3.6 python3-pip python3.6-dev

RUN apt-get install -y \
	postgresql \
	postgresql-contrib \
	libpq-dev \
	libssl-dev \
	libffi-dev \
	libsasl2-dev \
	libldap2-dev

RUN apt-get update && \
	apt-get install -y \
	nodejs-dev \
	node-gyp \
	libssl1.0-dev

RUN apt-get install -y npm

RUN apt-get install -y nodejs

RUN ln -sf /usr/bin/python3.6 /usr/local/bin/python3 && \
	update-alternatives --install /usr/bin/python python /usr/bin/python3 1 && \
	update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1 && \
	update-alternatives --install /usr/bin/node node /usr/bin/nodejs 1 && \
	apt-get clean -y && \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN locale-gen en_US.UTF-8

ENV LC_ALL=en_US.UTF-8

ENV LEMUR_VERSION=master LEMUR_TARGET=develop

# Install Lemur
RUN git config --global url."https://".insteadOf git:// && \
	cd /usr/local/src && \
	git clone https://github.com/netflix/lemur.git && \
	cd lemur && \
	git checkout ${LEMUR_VERSION} && \
	pip install --upgrade pip virtualenv && \
	export PATH=/usr/local/src/lemur/venv/bin:${PATH} && \
	virtualenv -p python3 venv && \
	. venv/bin/activate && \
	make ${LEMUR_TARGET}

WORKDIR /usr/local/src/lemur

# Create static files
RUN npm install --unsafe-perm && node_modules/.bin/gulp build && \
	node_modules/.bin/gulp package && \
	rm -r bower_components node_modules

ADD lemur.conf.py /usr/local/src/lemur/lemur.conf.py
ADD api-start.sh /usr/local/src/lemur/scripts/api-start.sh
RUN chmod +x /usr/local/src/lemur/scripts/api-start.sh

CMD ["/usr/local/src/lemur/scripts/api-start.sh"]
