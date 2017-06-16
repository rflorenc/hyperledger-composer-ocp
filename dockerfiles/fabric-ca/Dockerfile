# Dockerfile for Hyperledger fabric-ca image.

FROM centos
LABEL maintainer "Ricardo Lourenco <rlourenc@redhat.com>"

ARG GOLANG_VERSION=1.8
ARG GIMME_URL=raw.githubusercontent.com/travis-ci/gimme/master/gimme


RUN eval "$(curl -sL https://$GIMME_URL | \
    GIMME_GO_VERSION="$GOLANG_VERSION" \
    bash)"

ENV FABRIC_CA_HOME /etc/hyperledger/fabric-ca-server
ENV FABRIC_CA_SERVER_HOME /etc/hyperledger/fabric-ca-server
ENV CA_CFG_PATH /etc/hyperledger/fabric-ca
ENV FABRIC_CA_CLIENT_HOME $HOME/.fabric-ca-client
ENV GOPATH /go/src/github.com
ENV FABRIC_CA_ROOT $GOPATH/hyperledger/fabric-ca
ENV PATH="$HOME"/bin:/root/.gimme/versions/go"$GOLANG_VERSION".linux.amd64/bin:/usr/local/go/bin:$PATH


RUN mkdir -p $GOPATH $HOME/bin/ /usr/local/go/bin $GOPATH/hyperledger \
	$FABRIC_CA_SERVER_HOME \
	$FABRIC_CA_CLIENT_HOME \
	$CA_CFG_PATH \
	/var/hyperledger/fabric-ca-server && \
	echo $PATH && \
	go version && \
	yum install -y git libtool-ltdl make pkgconfig gcc gcc-c++ && \
	yum clean all

RUN cd $GOPATH/hyperledger \
    && git clone --single-branch -b master --depth 1 https://github.com/hyperledger/fabric-ca \
    && go get -v -u github.com/hyperledger/fabric-ca/cmd/... \
    && cp -v $FABRIC_CA_ROOT/images/fabric-ca/payload/*.pem $FABRIC_CA_HOME/

EXPOSE 7054

VOLUME $FABRIC_CA_SERVER_HOME
VOLUME $FABRIC_CA_CLIENT_HOME

WORKDIR $FABRIC_CA_ROOT

CMD ["bash", "-c", "fabric-ca-server start -b admin:adminpw"]
