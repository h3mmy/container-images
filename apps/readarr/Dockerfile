# hadolint ignore=DL3007
FROM ghcr.io/k8s-at-home/ubuntu:latest

ARG TARGETPLATFORM
ARG BUILDPLATFORM
ARG VERSION

USER root

# hadolint ignore=DL3008,DL3015,SC2086
RUN \
  apt-get -qq update \
  && \
  apt-get -qq install -y \
    libchromaprint-tools \
    libicu66 \
    sqlite3 \
  && \
  case "${TARGETPLATFORM}" in \
    'linux/amd64') export ARCH='x64' ;; \
    'linux/arm64') export ARCH='arm64' ;; \
  esac \
  && \
  curl -fsSL -o /tmp/readarr.tar.gz \
    "https://readarr.servarr.com/v1/update/nightly/updatefile?version=${VERSION}&os=linux&runtime=netcore&arch=${ARCH}" \
  && tar ixzf /tmp/readarr.tar.gz -C /app --strip-components 1 \
  && \
  rm -rf \
    /app/readarr.Update* \
  && \
  printf "UpdateMethod=docker\nBranch=nightly\nPackageVersion=%s\nPackageAuthor=[Team k8s-at-home](https://github.com/k8s-at-home)" "${VERSION}" > /app/package_info \
  && \
  apt-get autoremove -y \
  && apt-get clean \
  && \
  rm -rf \
    /tmp/* \
    /var/lib/apt/lists/* \
    /var/tmp/ \
  && chown -R kah:kah /app \
  && chmod -R u=rwX,go=rX /app \
  && printf "umask %d" "${UMASK}" >> /etc/bash.bashrc \
  && update-ca-certificates

USER kah

EXPOSE 8787

COPY ./apps/readarr/entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]

LABEL \
  org.opencontainers.image.title="Readarr" \
  org.opencontainers.image.source="https://github.com/Readarr/Readarr" \
  org.opencontainers.image.version="${VERSION}"
