FROM ubuntu:22.04

ENV DEBIAN_FRONTEND noninteractive 
ENV container docker 
ENV LC_ALL C

# renovate: datasource=repology depName=ubuntu_22_04/sudo versioning=loose
ENV SUDO_PACKAGE_VERSION="1.9.9-1ubuntu2.4"
# renovate: datasource=repology depName=ubuntu_22_04/systemd versioning=loose
ENV SYSTEMD_PACKAGE_VERSION="249.11-0ubuntu3.9"
# renovate: datasource=repology depName=ubuntu_22_04/systemd versioning=loose
ENV SYSTEMD_SYSV_PACKAGE_VERSION="249.11-0ubuntu3.9"
# renovate: datasource=repology depName=ubuntu_22_04/systemd-cron versioning=loose
ENV SYSTEMD_CRON_PACKAGE_VERSION="1.15.18-1"
# renovate: datasource=repology depName=ubuntu_22_04/rsyslog versioning=loose
ENV RSYSLOG_PACKAGE_VERSION="8.2112.0-2ubuntu2.2"
# renovate: datasource=repology depName=ubuntu_22_04/dbus versioning=loose
ENV DBUS_PACKAGE_VERSION="1.12.20-2ubuntu4.1"
# renovate: datasource=repology depName=ubuntu_22_04/iproute2 versioning=loose
ENV IPROUTE2_PACKAGE_VERSION="5.15.0-1ubuntu2"
# renovate: datasource=repology depName=ubuntu_22_04/ansible versioning=loose
ENV ANSIBLE_PACKAGE_VERSION="2.10.7+merged+base+2.10.8+dfsg-1"
# renovate: datasource=repology depName=ubuntu_22_04/apt versioning=loose
ENV APT_UTILS_PACKAGE_VERSION="2.4.9"
# renovate: datasource=repology depName=ubuntu_22_04/glibc versioning=loose
ENV LOCALES_PACKAGE_VERSION="2.35-0ubuntu3.1"
# renovate: datasource=repology depName=ubuntu_22_04/python-setuptools versioning=loose
ENV PYTHON_SETUPTOOLS_PACKAGE_VERSION="44.1.1-1.2ubuntu0.22.04.1"
# renovate: datasource=repology depName=ubuntu_22_04/python-pip versioning=loose
ENV PYTHON3_PIP_PACKAGE_VERSION="22.0.2+dfsg-1ubuntu0.2"
# renovate: datasource=repology depName=ubuntu_22_04/software-properties versioning=loose
ENV SOFTWARE_PROPERTIES_COMMON_PACKAGE_VERSION="0.99.22.6"

# Update and install necessary packages
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
      sudo=${SUDO_PACKAGE_VERSION} \
      systemd=${SYSTEMD_PACKAGE_VERSION} \ 
      systemd-sysv=${SYSTEMD_SYSV_PACKAGE_VERSION} \
      systemd-cron=${SYSTEMD_CRON_PACKAGE_VERSION} \ 
      rsyslog=${RSYSLOG_PACKAGE_VERSION} \
      dbus=${DBUS_PACKAGE_VERSION} \
      iproute2=${IPROUTE2_PACKAGE_VERSION} \
      ansible=${ANSIBLE_PACKAGE_VERSION} \
      apt-utils=${APT_UTILS_PACKAGE_VERSION} \
      locales=${LOCALES_PACKAGE_VERSION} \
      python-setuptools=${PYTHON_SETUPTOOLS_PACKAGE_VERSION} \
      python3-pip=${PYTHON3_PIP_PACKAGE_VERSION} \
      software-properties-common=${SOFTWARE_PROPERTIES_COMMON_PACKAGE_VERSION} && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Clean up 
RUN find . -type f -name '*systemd-tmpfiles-setup*' -exec rm {} \; && \
rm -f /lib/systemd/system/multi-user.target.wants/*; \
rm -f /etc/systemd/system/*.wants/*; \
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*; \
rm -f /lib/systemd/system/plymouth*; \
rm -f /lib/systemd/system/systemd-update-utmp*;
RUN systemctl set-default multi-user.target

ENV ANSIBLE_USER ansible 
ENV DEPLOY_GROUP deployer 
ENV SUDO_GROUP sudo

RUN set -xe \
  && groupadd -r ${ANSIBLE_USER} \
  && groupadd -r ${DEPLOY_GROUP} \
  && useradd -m -g ${ANSIBLE_USER} ${ANSIBLE_USER} \
  && usermod -aG ${SUDO_GROUP} ${ANSIBLE_USER} \
  && usermod -aG ${DEPLOY_GROUP} ${ANSIBLE_USER} \
  && sed -i "/^%${SUDO_GROUP}/s/ALL\$/NOPASSWD:ALL/g" /etc/sudoers

ENV init /lib/systemd/systemd

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]

WORKDIR /home/${ANSIBLE_USER}
ENTRYPOINT ["/lib/systemd/systemd"]