# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
ARG BUILD_ARCH
FROM $BUILD_FROM

LABEL org.opencontainers.image.source=https://github.com/Oddiesea/homebox-ha-addon

# Download and install Homebox from GitHub releases
RUN \
    HOMEBOX_VERSION="latest" \
    && if [ "$BUILD_ARCH" = "aarch64" ]; then \
        ARCH="arm64"; \
    elif [ "$BUILD_ARCH" = "amd64" ]; then \
        ARCH="amd64"; \
    elif [ "$BUILD_ARCH" = "armv7" ]; then \
        ARCH="armv7"; \
    fi \
    && curl -L -o /tmp/homebox.tar.gz \
        "https://github.com/homesystems/homebox/releases/${HOMEBOX_VERSION}/download/homebox-linux-${ARCH}.tar.gz" \
    && mkdir -p /app \
    && tar -xzf /tmp/homebox.tar.gz -C /app \
    && chmod +x /app/api \
    && rm /tmp/homebox.tar.gz

# Copy service scripts for s6-overlay
COPY rootfs/ /

# Make service scripts executable
RUN chmod +x /etc/services.d/homebox/run /etc/services.d/homebox/finish || true

# s6-overlay will automatically start services in /etc/services.d/ as PID 1
