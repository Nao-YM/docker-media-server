FROM debian:trixie-slim AS builder

WORKDIR /opt
# ビルド環境構築
RUN rm -f /etc/apt/apt.conf.d/docker-clean; \
    echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' \
      > /etc/apt/apt.conf.d/keep-cache
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
	--mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt-get update && apt-get install --no-install-recommends -y \
		wget \
    && wget --no-check-certificate https://download.serviio.org/releases/serviio-2.5-linux.tar.gz -O serviio.tar.gz  \
    && tar xf serviio.tar.gz \
    && rm serviio.tar.gz 

FROM debian:trixie-slim AS runtime

WORKDIR /opt/Serviio

ENV LANG=ja_JP.UTF-8
ENV LC_ALL=ja_JP.UTF-8
ENV LC_CTYPE=ja_JP.UTF-8

COPY --from=builder /opt/serviio-2.5 /opt/Serviio
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
	--mount=type=cache,target=/var/lib/apt,sharing=locked \
	apt-get update && apt-get install --no-install-recommends -y \
		locales-all \
		openjdk-25-jre \
		default-jre \
		ffmpeg \
		dcraw \
	&& chmod +x bin/serviio.sh \
# Cleanup	
	&& apt-get -y autoremove \
	&& apt-get -y clean \
	&& apt-get -y autopurge

CMD [ "bin/serviio.sh" ]