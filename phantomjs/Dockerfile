FROM ubuntu:14.04 AS phantomjs-ubuntu-image
# IMAGE: local/node:8-alpine-phantomjs

ENV PHANTOMJS_VERSION 2.1.1
ENV DOCKERIZE_VERSION 0.2.2

RUN apt-get update \
    && apt-get install -y curl libfontconfig-dev python-pip rsync ttf-mscorefonts-installer xfonts-base \
    \
    && echo "Downloading the released pre-built phantomjs packages..." && sleep 1 \
    && curl -L https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-${PHANTOMJS_VERSION}-linux-x86_64.tar.bz2 \
       | tar -xvj \
    \
    && ln -s /phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs /usr/local/share/phantomjs \
    && ls -la phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs \
    && ln -s /phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs /usr/local/bin/phantomjs \
    && ln -s /phantomjs-${PHANTOMJS_VERSION}-linux-x86_64/bin/phantomjs /usr/bin/phantomjs \
    && ls -la /usr/bin/phantomjs \
    &&  /usr/bin/phantomjs -v \
    && echo $PATH \
    # Checking if phantom works
    && phantomjs -v \
    \
    && curl -L https://github.com/larsks/dockerize/archive/${DOCKERIZE_VERSION}.tar.gz \
       | tar xvz -C /tmp/ --strip-components=1 \
    && cd /tmp/ \
    && pip install . \
    \
    && dockerize --help \
    && dockerize -n -o /dockerized-phantomjs \
                 -e $(which phantomjs) \
                 -a /bin/dash /bin/sh \
                 -a /etc/fonts /etc  \
                 -a /etc/ssl /etc  \
                 -a /usr/share/fonts /usr/share  \
                 --verbose \
                 $(which phantomjs) \
                 /usr/bin/curl \
    \
    && cd /dockerized-phantomjs \
    && tar --numeric-owner -zcf ../dockerized-phantomjs.tar.gz * && cd / \
    && rm -rf dockerized-phantomjs \
    && chown $(stat -c %u .):$(stat -c %g .) /dockerized-phantomjs.tar.gz

# patch the dockerized phantomjs packge to node image
FROM local/node:8-alpine
COPY --from=phantomjs-ubuntu-image /dockerized-phantomjs.tar.gz /dockerized-phantomjs.tar.gz
RUN tar -xzvf /dockerized-phantomjs.tar.gz && rm -rf /dockerized-phantomjs.tar.gz && phantomjs -v
