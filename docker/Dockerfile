ARG BASE_IMAGE=debian:stretch

FROM debian:stretch

ARG SERGE_TAG=1.4

RUN apt-get -qq -y update && apt-get -qq -y install git
RUN git clone https://github.com/evernote/serge.git /serge \
    && cd /serge \
    && git checkout tags/${SERGE_TAG}

# Copied from https://github.com/evernote/serge
FROM ${BASE_IMAGE}

ARG INSTALL_PREREQUISITES="apt-get -qq -y update && apt-get -qq -y install make cpanminus libscalar-list-utils-perl libxml-parser-perl libxml-xpath-perl libxml-libxslt-perl libtext-diff-perl libyaml-perl libmime-lite-perl libfile-copy-recursive-perl libauthen-sasl-perl libxml-twig-perl libtext-csv-xs-perl libjson-perl libjson-xs-perl libnet-smtp-ssl-perl libcpan-sqlite-perl libio-string-perl"
ARG CLEAN_PREREQUISITES="apt-get -qq -y purge make cpanminus"
ARG GIT_SHA1="CUSTOM BUILD"

LABEL maintainers="Dragos Varovici <dvarovici.work@gmail.com>"
LABEL git_sha1="${GIT_SHA1}"

COPY --from=0 /serge /serge

# The simplest way to handle the escaping contortions is to echo everything into a file and source it.
RUN set -ex \
    && echo ${INSTALL_PREREQUISITES} > /tmp/prereq \
    && . /tmp/prereq \
    && rm /tmp/prereq \
    && cpanm --no-wget --installdeps /serge \
    && rm -rf /root/.cpan* /usr/local/share/man \
    && mkdir -p /data \
    && cp /serge/doc/sample.serge /data \
    && echo ${CLEAN_PREREQUISITES} > /tmp/prereq \
    && . /tmp/prereq \
    && rm /tmp/prereq

ENV PATH="/serge/bin:${PATH}"
ENV PERL5LIB="/serge/lib${PERL5LIB:+:}${PERL5LIB}"
ENV SERGE_DATA_DIR=/data
VOLUME /data
WORKDIR /data
ENTRYPOINT ["/serge/bin/serge"]
