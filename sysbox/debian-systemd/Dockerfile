FROM debian as systemd

#
# Systemd installation
#
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
    useradd --create-home --shell /bin/bash dev &&                \
    echo "dev:pass" | chpasswd && adduser dev sudo


FROM systemd as docker

# Sshd install
RUN apt-get update && apt-get install --no-install-recommends -y      \
            openssh-server &&                                         \
    mkdir /home/dev/.ssh &&                                       \
    chown dev:dev /home/dev/.ssh


# Expose SSH and HTTP ports
EXPOSE 22
EXPOSE 80

# Make use of stopsignal (instead of sigterm) to stop systemd containers.
STOPSIGNAL SIGRTMIN+3

# Set systemd as entrypoint.
ENTRYPOINT [ "/sbin/init", "--log-level=err" ]
