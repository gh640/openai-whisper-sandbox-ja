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

COPY . ./
