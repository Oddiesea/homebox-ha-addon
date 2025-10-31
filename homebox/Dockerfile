# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

LABEL org.opencontainers.image.source=https://github.com/Oddiesea/homebox-ha-addon

# Copy service scripts (for potential future use with s6-overlay)
COPY rootfs/ /

# Make service scripts executable
RUN chmod +x /etc/services.d/homebox/run /etc/services.d/homebox/finish || true

# Use the original entrypoint that worked - Homebox API
ENTRYPOINT [ "/app/api" ]
CMD [ "/data/config.yml" ]
