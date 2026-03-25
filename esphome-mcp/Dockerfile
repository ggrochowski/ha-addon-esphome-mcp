ARG BUILD_FROM
FROM ${BUILD_FROM}

# Match ESPHome's musl wheel index for pre-compiled Alpine packages
ENV UV_EXTRA_INDEX_URL=https://wheels.home-assistant.io/musllinux-index/
ENV UV_SYSTEM_PYTHON=true
ENV UV_INDEX_STRATEGY=unsafe-best-match
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Install build dependencies for ESPHome native compilation
# libc6-compat: provides glibc compatibility layer so PlatformIO's pre-compiled
#   glibc-linked tools (cmake, ninja, xtensa toolchain) can run on Alpine/musl
RUN apk add --no-cache \
    build-base \
    libffi-dev \
    openssl-dev \
    rust \
    cargo \
    git \
    curl \
    bash \
    patch \
    libmagic \
    cairo \
    libc6-compat

# Install uv (same version as ESPHome official image) and upgrade pip
RUN pip3 install --no-cache-dir -U pip uv==0.10.1

# Install Python dependencies and ESPHome
COPY requirements.txt /tmp/
RUN uv pip install --no-cache-dir -r /tmp/requirements.txt \
    && uv pip install --no-cache-dir esphome \
    && rm /tmp/requirements.txt

# Pre-configure PlatformIO to avoid first-run setup at compile time
RUN platformio settings set enable_telemetry No \
    && platformio settings set check_platformio_interval 1000000 \
    && mkdir -p /piolibs

# Copy server code
COPY server/ /opt/server/
COPY run.sh /opt/

RUN chmod a+x /opt/run.sh

WORKDIR /opt

CMD ["/opt/run.sh"]
