ARG PYTHON_BASE_IMAGE=3-slim-buster

FROM python:${PYTHON_BASE_IMAGE} AS build

ARG octoprint_ref 
ENV octoprint_ref ${octoprint_ref:-master}

RUN apt-get update && apt-get install -y \
  build-essential \
  curl \
  ffmpeg

RUN groupadd --gid 1000 octoprint \
  && useradd --uid 1000 --gid octoprint -G dialout --shell /bin/bash -m octoprint \
  && mkdir -p /octoprint/octoprint /octoprint/plugins \
  && chown -R octoprint:octoprint /octoprint

COPY docker-entrypoint.sh /usr/local/bin

USER octoprint

WORKDIR /home/octoprint

ENV PYTHONUSERBASE /octoprint/plugins
ENV PIP_USER true
ENV PATH "${PYTHONUSERBASE}/bin:${PATH}"

RUN	mkdir tmp && curl -fsSLO --compressed --retry 3 --retry-delay 10 \
  https://github.com/OctoPrint/OctoPrint/archive/${octoprint_ref}.tar.gz \
  && tar xzf ${octoprint_ref}.tar.gz --strip-components 1 -C tmp \
  && cd /home/octoprint/tmp \
  && pip install . \
  && rm -rf /home/octoprint/tmp

WORKDIR /octoprint
COPY config.yaml /octoprint/octoprint

VOLUME /octoprint
EXPOSE 5000

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["octoprint", "--basedir", "/octoprint/octoprint", "serve"]
