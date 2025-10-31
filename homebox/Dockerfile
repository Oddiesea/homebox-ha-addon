# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

# Install Homebox - copy from the homebox image
ARG HOMEBOX_IMAGE=ghcr.io/sysadminsmedia/homebox:latest
COPY --from=${HOMEBOX_IMAGE} /app /app

LABEL org.opencontainers.image.source=https://github.com/Oddiesea/homebox-ha-addon

# Copy service scripts for s6-overlay
COPY rootfs/ /

# Make service scripts executable
RUN chmod +x /etc/services.d/homebox/run /etc/services.d/homebox/finish || true

# s6-overlay will automatically start services in /etc/services.d/ as PID 1
