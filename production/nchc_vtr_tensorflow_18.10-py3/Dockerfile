FROM nvcr.io/nvidia/tensorflow:18.10-py3 

COPY requirement.txt /root/
RUN pip install -r /root/requirement.txt

COPY try.py /root/

COPY init.sh /etc/profile.d/

RUN set -xe; \
    apt-get update; \
    apt-get install -y sudo; \
    DEBIAN_FRONTEND=noninteractive apt-get -y install libnss-ldap libpam-ldap ldap-utils nscd ;\
    DEBIAN_FRONTEND=noninteractive apt-get install -y libpam-ldapd ;\
    apt-get -y install openssh-server openssh-client

RUN echo "%slurm  ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers    

RUN set -xe; \
    echo "session required pam_mkhomedir.so skel=/etc/skel umask=077" >> /etc/pam.d/common-session; \
    mkdir -p /var/run/sshd
COPY ldap-auth-config /etc/auth-client-config/profile.d/ldap-auth-config
COPY ldap.conf /etc/ldap.conf
COPY ldap.secret /etc/ldap.secret
RUN chmod 600 /etc/ldap.secret

COPY pam.d /etc/pam.d
COPY nslcd.conf /etc/nslcd.conf

RUN /usr/sbin/auth-client-config -t nss -p lac_ldap

RUN update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3.5 2

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT /bin/sh /entrypoint.sh

EXPOSE 5000
EXPOSE 22
