FROM golang:1.20-alpine as dev
COPY . /src/
WORKDIR /src
RUN CGO_ENABLED=0 go build -a -ldflags '-w -extldflags "-static"' -o /hook-docker

FROM docker:24.0.4-dind
RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update && apk add linux-headers alpine-sdk
ADD https://www.kernel.org/pub/linux/utils/kernel/kexec/kexec-tools-2.0.11.tar.gz .

RUN zcat kexec-tools-2.0.27.tar.gz | tar xvf - && \
  cd kexec-tools-2.0.27 && \
  sed 's/loff_t/off_t/g' -i vmcore-dmesg/vmcore-dmesg.c && \
  ./configure && \
  make && \
  make install

COPY --from=dev /hook-docker .
ENTRYPOINT ["/hook-docker"]
