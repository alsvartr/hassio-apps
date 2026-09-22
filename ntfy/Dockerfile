ARG BUILD_FROM
ARG NTFY_VERSION=2.28.0

# Stage 1: Get ntfy binary from official image
FROM binwiederhier/ntfy:v${NTFY_VERSION} AS ntfy-source

# Stage 2: Build the add-on image on the HA Debian base
FROM ${BUILD_FROM}

# Labels
LABEL \
    io.hass.name="ntfy" \
    io.hass.description="Send push notifications to your phone or desktop using PUT/POST" \
    io.hass.type="addon" \
    io.hass.version="${BUILD_VERSION}" \
    maintainer="Alsvartr"

# Copy ntfy binary from official image
COPY --from=ntfy-source /usr/bin/ntfy /usr/bin/ntfy

# Copy add-on rootfs
COPY rootfs /

# Create required directories
RUN mkdir -p \
    /var/cache/ntfy \
    /var/lib/ntfy \
    /etc/ntfy \
    /data/ntfy

# Set script permissions
RUN chmod a+x /etc/cont-init.d/*.sh \
    && chmod a+x /etc/s6-overlay/s6-rc.d/*/run

# Expose ports: 2586=ingress HTTP, 443=external HTTPS (optional, requires SSL certs)
EXPOSE 2586 443
