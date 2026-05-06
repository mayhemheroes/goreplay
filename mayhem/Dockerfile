FROM golang:1.25 AS builder

RUN apt-get update && apt-get install -y clang

ADD . /goreplay
WORKDIR /goreplay

RUN go install github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
RUN go get github.com/dvyukov/go-fuzz/go-fuzz-dep
RUN cd proto && GO_ENABLED=0 go-fuzz-build -libfuzzer -o fuzz.a .
RUN cd proto && clang -fsanitize=fuzzer fuzz.a -o fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=builder /goreplay/proto/fuzz.libfuzzer /
