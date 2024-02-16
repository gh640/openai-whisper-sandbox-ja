# syntax=docker/dockerfile:1
FROM python:3.12

WORKDIR /app

RUN apt-get update && \
  apt-get install -y --no-install-recommends \
    ffmpeg \
    > /dev/null \
  && \
  rm -rf /var/lib/apt/lists/*

COPY ./requirements.txt ./

RUN python -m pip install -r requirements.txt

# `models` で指定されたモデルをダウンロードする:
ARG models=''
RUN <<EOF
for model in ${models}; do
  whisper --model="${model}" dummy-file
done
EOF

COPY . ./
