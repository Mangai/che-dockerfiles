# Copyright (c) 2012-2016 Codenvy, S.A.
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html
# Contributors:
# Codenvy, S.A. - initial API and implementation

FROM fedora:24
ENV CC arm-linux-gnueabi-gcc
ENV CXX arm-linux-gnueabi-g++
ENV JAVA_HOME /usr/lib/jvm/java-1.8.0-openjdk
ENV PATH $JAVA_HOME/bin:$PATH
ENV CPATH=/usr/arm-linux-gnueabi/sys-root/usr/include/artik
ENV NODE_PATH=/usr/local/lib/node_modules
ENV CROSS_COMPILE=arm-linux-gnueabi- \
    SYSROOT=/usr/arm-linux-gnueabi/sys-root

RUN dnf install dnf-plugins-core copr-cli java-1.8.0-openjdk-devel.x86_64 -y && \
    dnf copr enable lantw44/arm-linux-gnueabi-toolchain -y && \
    dnf --enablerepo='*debug*' install arm-linux-gnueabi-{binutils,gcc,glibc} sudo usbutils openssh-server procps wget unzip nc mc git curl openssl bash passwd tar gdb sshpass cpio subversion -y && \
    dnf clean all && \
    mkdir /var/run/sshd && \
    sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd && \
    echo "%wheel ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    sed -i 's/requiretty/!requiretty/g' /etc/sudoers && \
    echo -e "#! /bin/bash\n set -e\nsudo /usr/bin/ssh-keygen -A\n sudo /usr/sbin/sshd -D &\n exec \"\$@\"" > /root/entrypoint.sh && chmod a+x /root/entrypoint.sh && \
    echo -e "export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk\n export CC=arm-linux-gnueabi-gcc\n export CXX=arm-linux-gnueabi-g++\nexport PATH=$JAVA_HOME/bin:$PATH" >> /root/.bashrc
RUN cd / && wget https://install.codenvycorp.com/artik/artik-libs-deps.zip
RUN unzip -q artik-libs-deps.zip -d /usr/arm-linux-gnueabi/sys-root && rm artik-libs-deps.zip
ADD artik.repo /etc/yum.repos.d/artik.repo
ADD RPM-GPG-KEY-ARTIK /etc/pki/rpm-gpg/RPM-GPG-KEY-ARTIK
RUN rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-ARTIK
RUN dnf makecache && dnf install --disablerepo=updates --disablerepo=lantw44-arm-linux-gnueabi-toolchain --disablerepo=fedora libartik-sdk-sysroot libartik-sdk-doc -y
EXPOSE 22 4403
ADD rsync.sh /usr/local/bin/rsync.sh
RUN chmod +x /usr/local/bin/rsync.sh
ENTRYPOINT ["/root/entrypoint.sh"]
RUN mkdir -p /home/user/.m2 && \
    mkdir -p /home/user/jdtls/data && \
    chown -R 0 /home/user && \
    chmod -R g+w /home/user
WORKDIR /projects
CMD tail -f /dev/null
