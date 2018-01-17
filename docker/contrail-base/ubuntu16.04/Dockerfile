ARG BASE_IMAGE
FROM ${BASE_IMAGE}
#FROM contrail-repo-ubuntu16.04:4.0.0.0-3017
MAINTAINER Juniper Contrail <contrail@juniper.net>
ARG CONTRAIL_REPO_URL
ARG CONTRAIL_ANSIBLE_TAR
ARG http_proxy
ARG https_proxy
ARG no_proxy
ARG OS=ubuntu16.04
ARG CONTRAIL_VERSION
ENV CONTRAIL_VERSION $CONTRAIL_VERSION
ENV OS=$OS
ARG DEBIAN_FRONTEND=noninteractive
ARG apt_install="apt-get install -yq --force-yes --no-install-recommends --no-install-suggests "
ENV ANSIBLE_INVENTORY="all-in-one"
ARG PACKAGES_ANSIBLE="iproute2 python-configparser vim ssh-client iputils-ping less sudo python-setuptools"
ARG CONTRAIL_REPO_MIRROR_SNAPSHOT=04042017
COPY contrail-ubuntu-mirror.key /
RUN apt-key add /contrail-ubuntu-mirror.key;\
    echo "deb $CONTRAIL_REPO_URL ./" > /etc/apt/sources.list.d/contrail-local.list;
RUN var1=$(echo $CONTRAIL_REPO_URL | sed -r 's#http[s]?://([[:digit:]\.]+):.*#\1#') ; \
    echo "Package: *\nPin: origin \"$var1\"\nPin-Priority: 1001" > /etc/apt/preferences
RUN echo "APT::Get::AllowUnauthenticated \"true\";" > /etc/apt/apt.conf.d/99allowunauth
RUN apt-get update && \
    apt-get install -y python-pip  && \
    pip install 'ansible>=2.3.0,<2.4.0'
RUN apt-get update -qy && \
    $apt_install $PACKAGES_ANSIBLE && \
    apt-get autoremove -yq  &&\
    rm -fr /usr/share/doc/* /usr/share/man/*
RUN cd /lib/systemd/system/sysinit.target.wants/; ls | grep -v systemd-tmpfiles-setup | xargs rm -f $1 \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*; \
rm -f /lib/systemd/system/plymouth*; \
rm -f /lib/systemd/system/systemd-update-utmp*;
RUN systemctl set-default multi-user.target
ENV init /lib/systemd/systemd
COPY python-contrailctl /python-contrailctl
RUN cd /python-contrailctl/; python setup.py install
ADD $CONTRAIL_ANSIBLE_TAR /
ENTRYPOINT ["/lib/systemd/systemd"]
CMD ["systemd.unit=multi-user.target"]
