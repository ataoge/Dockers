FROM postgres:10.5
MAINTAINER Huang Tao <loveht@aliyun.com>

#full seach
RUN apt-get update && apt-get install -y unzip wget gcc make postgresql-server-dev-$PG_MAJOR \
    && wget http://www.xunsearch.com/scws/down/scws-1.2.3.tar.bz2 \
    && tar xvjf scws-1.2.3.tar.bz2 \
    && wget https://github.com/amutu/zhparser/archive/master.zip -O zhparser.zip \
    && unzip zhparser.zip

RUN cd scws-1.2.3 && ./configure --prefix=/usr/local/lib/scws-1.2.3 && make && make install
RUN cd zhparser-master && SCWS_HOME=/usr/local/lib/scws-1.2.3 make && make install

RUN apt-get remove -y unzip wget gcc make postgresql-server-dev-$PG_MAJOR

#postgis
ENV POSTGIS_MAJOR 2.5
ENV POSTGIS_VERSION 2.5.0+dfsg-2.pgdg90+1

RUN apt-get update \
      && apt-cache showpkg postgresql-$PG_MAJOR-postgis-$POSTGIS_MAJOR \
      && apt-get install -y --no-install-recommends \
           postgresql-$PG_MAJOR-postgis-$POSTGIS_MAJOR=$POSTGIS_VERSION \
           postgresql-$PG_MAJOR-postgis-$POSTGIS_MAJOR-scripts=$POSTGIS_VERSION \
           postgis=$POSTGIS_VERSION \
      && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /docker-entrypoint-initdb.d
COPY ./initdb-postgis.sh /docker-entrypoint-initdb.d/postgis.sh
COPY ./update-postgis.sh /usr/local/bin