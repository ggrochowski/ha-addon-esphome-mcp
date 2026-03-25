# Use the official ESPHome HA add-on image as base — it already has ESPHome,
# PlatformIO, and all cross-compilation toolchains correctly configured.
# This avoids glibc/musl incompatibility issues with PlatformIO's pre-compiled
# binaries (cmake, ninja, xtensa toolchain) on Alpine.
ARG BUILD_FROM
FROM ${BUILD_FROM}

# Install MCP server Python dependencies
# ESPHome and its deps are already in the base image
COPY requirements.txt /tmp/
RUN pip3 install --no-cache-dir -r /tmp/requirements.txt \
    && rm /tmp/requirements.txt

# Copy server code
COPY server/ /opt/server/
COPY run.sh /opt/

RUN chmod a+x /opt/run.sh

WORKDIR /opt

CMD ["/opt/run.sh"]
