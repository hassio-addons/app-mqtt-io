ARG BUILD_FROM=ghcr.io/hassio-addons/base:21.0.3
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Copy Python requirements file and patches
COPY requirements.txt /tmp/
COPY patches/ /tmp/patches/

# Setup base
# hadolint ignore=DL3003,DL3042
RUN \
    apk add --no-cache --virtual .build-dependencies \
        build-base=0.5-r4 \
        git=2.54.0-r0 \
        libffi-dev=3.5.2-r1 \
        py3-wheel=0.47.0-r0 \
        python3-dev=3.14.7-r1 \
    \
    && apk add --no-cache \
        py3-bcrypt=5.0.0-r1 \
        py3-cryptography=47.0.0-r0 \
        py3-packaging=26.2-r0 \
        py3-pip=26.1.2-r0 \
        python3=3.14.7-r1 \
    \
    && pip install -r /tmp/requirements.txt \
    \
    && patch -p1 -d "$(python3 -c 'import mqtt_io, pathlib; print(pathlib.Path(mqtt_io.__file__).parent.parent)')" \
        < /tmp/patches/0001-replace-pkg-resources-with-importlib-metadata.patch \
    \
    && find /usr \
        \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    \
    && apk del --no-cache --purge .build-dependencies

# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Franck Nijhof <opensource@frenck.dev>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Apps" \
    org.opencontainers.image.authors="Franck Nijhof <opensource@frenck.dev>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://frenck.dev/home-assistant-apps" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
