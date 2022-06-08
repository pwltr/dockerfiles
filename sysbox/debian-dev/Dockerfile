FROM debian as systemd

# Install Systemd, Avahi
RUN apt-get update &&                            \
    apt-get install -y --no-install-recommends   \
            systemd                              \
            systemd-sysv                         \
            libpam-systemd                       \
            libsystemd0                          \
            parted                               \
            ca-certificates                      \
            dbus                                 \
            iptables                             \
            iproute2                             \
            avahi-daemon                         \
            avahi-discover                       \
            libnss-mdns                          \
            kmod                                 \
            locales                              \
            sudo                                 \
            udev &&                              \
                                                 \
    # Prevents journald from reading kernel messages from /dev/kmsg
    echo "ReadKMsg=no" >> /etc/systemd/journald.conf &&               \
                                                                      \
    # Housekeeping
    apt-get clean -y &&                                               \
    rm -rf                                                            \
       /var/cache/debconf/*                                           \
       /var/lib/apt/lists/*                                           \
       /var/log/*                                                     \
       /tmp/*                                                         \
       /var/tmp/*                                                     \
       /usr/share/doc/*                                               \
       /usr/share/man/*                                               \
       /usr/share/local/* &&                                          \
                                                                      \
    # Create default user
    useradd --create-home --shell /bin/bash dev &&                    \
    echo "dev:pass" | chpasswd && adduser dev sudo

# Install sshd
RUN apt-get update && apt-get install --no-install-recommends -y      \
            openssh-server &&                                         \
    mkdir /home/dev/.ssh &&                                           \
    chown dev:dev /home/dev/.ssh

FROM systemd as docker

# Install Docker
RUN apt-get update && apt-get install --no-install-recommends -y      \
       apt-transport-https                                            \
       ca-certificates                                                \
       curl                                                           \
       gnupg                                                          \
       gnupg-agent                                                    \
       software-properties-common &&                                  \
                                                                      \
    curl -fsSL https://download.docker.com/linux/debian/gpg           \
         | apt-key add - &&                                           \
	                                                                  \
    apt-key fingerprint 0EBFCD88 &&                                   \
                                                                      \
    add-apt-repository                                                \
       "deb [arch=amd64] https://download.docker.com/linux/debian     \
       $(lsb_release -cs)                                             \
       stable" &&                                                     \
                                                                      \
    apt-get update && apt-get install --no-install-recommends -y      \
       docker-ce docker-ce-cli containerd.io &&                       \
                                                                      \
    # Housekeeping
    apt-get clean -y &&                                               \
    rm -rf                                                            \
       /var/cache/debconf/*                                           \
       /var/lib/apt/lists/*                                           \
       /var/log/*                                                     \
       /tmp/*                                                         \
       /var/tmp/*                                                     \
       /usr/share/doc/*                                               \
       /usr/share/man/*                                               \
       /usr/share/local/* &&                                          \
                                                                      \
    # Add user "dev" to the Docker group
    usermod -a -G docker dev

# Install Docker Compose v2
RUN mkdir -p /usr/local/lib/docker/cli-plugins/ &&                                                                  \
	curl -SL "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)"    \ 
         -o /usr/local/lib/docker/cli-plugins/docker-compose &&                                                     \
	chmod +x /usr/local/lib/docker/cli-plugins/docker-compose


FROM docker as tooling

# Install tools for development
RUN apt-get update &&                                                          \
    apt-get install -y --no-install-recommends                                 \
            git                                                                \
            xxd                                                                \
            net-tools                                                          \
            curl                                                               \
            micro                                                              \
            vim                                                                \
            wget                                                               \
            rsync                                                              \
            jq &&                                                              \
                                                                               \
    # Housekeeping
    apt-get clean -y &&                                                        \
    rm -rf                                                                     \
       /var/cache/debconf/*                                                    \
       /var/lib/apt/lists/*                                                    \
       /var/log/*                                                              \
       /tmp/*                                                                  \
       /var/tmp/*                                                              \
       /usr/share/doc/*                                                        \
       /usr/share/man/*                                                        \
       /usr/share/local/*

# Expose SSH and HTTP ports
EXPOSE 22
EXPOSE 80

# Expose more common development ports
EXPOSE 3000
EXPOSE 4000
EXPOSE 5000
EXPOSE 8000
EXPOSE 8080

# Make use of stopsignal (instead of sigterm) to stop systemd containers.
STOPSIGNAL SIGRTMIN+3

# Set systemd as entrypoint.
ENTRYPOINT [ "/sbin/init", "--log-level=err" ]
