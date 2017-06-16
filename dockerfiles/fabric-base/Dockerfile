# Dockerfile for Hyperledger fabric base image.
# Data is stored under /var/hyperledger/db and /var/hyperledger/production

FROM centos
LABEL maintainer "Ricardo Lourenco <rlourenc@redhat.com>"

ARG GOLANG_VERSION=1.8
ARG GIMME_URL=raw.githubusercontent.com/travis-ci/gimme/master/gimme


RUN eval "$(curl -sL https://$GIMME_URL | \
    GIMME_GO_VERSION="$GOLANG_VERSION" \
    bash)"

ENV PATH="$GOPATH":"$HOME"/bin:/root/.gimme/versions/go"$GOLANG_VERSION".linux.amd64/bin:/usr/local/go/bin:$PATH


ENV FABRIC_CFG_PATH /etc/hyperledger/fabric

ENV FABRIC_ROOT $GOPATH/src/github.com/hyperledger/fabric
ENV ARCH x86_64
ENV BASEIMAGE_RELEASE 0.3.1
ENV BASE_VERSION 1.0.0
ENV PROJECT_VERSION 1.0.0-preview
ENV DOCKER_NS hyperledger
ENV BASE_DOCKER_NS hyperledger
ENV LD_FLAGS="-X github.com/hyperledger/fabric/common/metadata.Version=${PROJECT_VERSION} \
             -X github.com/hyperledger/fabric/common/metadata.BaseVersion=${BASEIMAGE_RELEASE} \
             -X github.com/hyperledger/fabric/common/metadata.BaseDockerLabel=org.hyperledger.fabric \
             -X github.com/hyperledger/fabric/common/metadata.DockerNamespace=hyperledger \
             -X github.com/hyperledger/fabric/common/metadata.BaseDockerNamespace=hyperledger"

RUN mkdir -p /var/hyperledger/db \
        /var/hyperledger/production \
        /chaincode/input \
        /chaincode/output \
        $FABRIC_CFG_PATH

RUN yum update -y\
        && yum install -y snappy-devel zlib-devel git libtool-ltdl make pkgconfig gcc gcc-c++ bzip2-devel bzip2-libs \
        && yum clean all


# install chaintool
RUN curl -L https://github.com/hyperledger/fabric-chaintool/releases/download/v0.10.3/chaintool > /usr/local/bin/chaintool \
        && chmod a+x /usr/local/bin/chaintool

# install gotools
RUN go get github.com/golang/protobuf/protoc-gen-go \
        && go get github.com/kardianos/govendor \
        && go get github.com/golang/lint/golint \
        && go get golang.org/x/tools/cmd/goimports \
        && go get github.com/onsi/ginkgo/ginkgo \
        && go get github.com/axw/gocov/... \
        && go get github.com/client9/misspell/cmd/misspell \
        && go get github.com/AlekSi/gocov-xml

RUN mkdir -p $GOPATH/src/github.com/hyperledger \
        && cd $GOPATH/src/github.com/hyperledger \
        && git clone --single-branch -b master --depth 1 http://gerrit.hyperledger.org/r/fabric \
        && cp $FABRIC_ROOT/devenv/limits.conf /etc/security/limits.conf \
        && cp -r $FABRIC_ROOT/sampleconfig/* $FABRIC_CFG_PATH

RUN cd $FABRIC_ROOT/ \
        && CGO_CFLAGS=" " go install -tags "nopkcs11" -ldflags "-X github.com/hyperledger/fabric/common/configtx/tool/configtxgen/metadata.Version=${PROJECT_VERSION}" github.com/hyperledger/fabric/common/configtx/tool/configtxgen \
        && CGO_CFLAGS=" " go install -tags "" -ldflags "-X github.com/hyperledger/fabric/common/tools/cryptogen/metadata.Version=${PROJECT_VERSION}" github.com/hyperledger/fabric/common/tools/cryptogen \
        && CGO_CFLAGS=" " go install -tags "" -ldflags "-X github.com/hyperledger/fabric/common/tools/configtxlator/metadata.Version=${PROJECT_VERSION}" github.com/hyperledger/fabric/common/tools/configtxlator

# Install block-listener
RUN cd $FABRIC_ROOT/examples/events/block-listener \
        && go build \
        && mv block-listener $GOPATH/bin


VOLUME /var/hyperledger
#VOLUME /etc/hyperledger/fabric

RUN ln -s $GOPATH /opt/gopath

ENV GOROOT=/usr/local/go

WORKDIR $FABRIC_ROOT

LABEL org.hyperledger.fabric.version=${PROJECT_VERSION} \
      org.hyperledger.fabric.base.version=${BASEIMAGE_RELEASE}
