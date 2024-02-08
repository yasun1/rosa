FROM registry.ci.openshift.org/ocp/builder:rhel-8-golang-1.21-openshift-4.16 as builder
WORKDIR /go/src/github.com/openshift/rosa
RUN go install ./cmd/rosa
RUN rosa verify openshift-client

FROM registry.ci.openshift.org/ci/cli-ocm:latest as ocmcli

FROM registry.ci.openshift.org/origin/4.16:cli
COPY --from=builder /go/bin/rosa /usr/bin/rosa
COPY --from=ocmcli /usr/bin/ocm /usr/bin/ocm
RUN yum -y install --setopt=skip_missing_names_on_install=False \
    openssl \
    jq \
    unzip \
    which
RUN yum clean all
RUN curl -s "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip -q awscliv2.zip && \
    ./aws/install && \
    rm -rf awscliv2.zip aws &&\
    aws --version
RUN rosa verify openshift-client