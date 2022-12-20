FROM python:3.9-slim as compilers

RUN apt-get update && apt-get install -y curl wget

RUN mkdir -p "/ido5.3" && \
    curl -L "https://github.com/decompals/ido-static-recomp/releases/download/v0.2/ido-5.3-recomp-ubuntu-latest.tar.gz" | \
    tar zx -C "/ido5.3"

RUN mkdir -p "/ido7.1" && \
    curl -L "https://github.com/decompals/ido-static-recomp/releases/download/v0.2/ido-7.1-recomp-ubuntu-latest.tar.gz" | \
    tar zx -C "/ido7.1"

RUN mkdir -p "/gcc2.8.1" && \
    curl -L "https://github.com/pmret/gcc-papermario/releases/download/master/linux.tar.gz" | \
    tar zx -C "/gcc2.8.1" && \
    curl -L "https://github.com/pmret/binutils-papermario/releases/download/master/linux.tar.gz" | \
    tar zx -C "gcc2.8.1"

# STAGE 2

FROM python:3.9-slim

RUN apt-get update && \
    apt-get install -y \
        binutils-mips-linux-gnu \
        cpp

RUN python3 -m pip install --upgrade \
    pycparser \
    pynacl \
    toml

RUN useradd user

COPY --from=compilers /ido5.3/usr/bin/ /tools/ido_recomp/linux/5.3
COPY --from=compilers /ido5.3/usr/lib/ /tools/ido_recomp/linux/5.3

COPY --from=compilers /ido7.1/usr/bin/ /tools/ido_recomp/linux/7.1
COPY --from=compilers /ido7.1/usr/lib/ /tools/ido_recomp/linux/7.1

COPY --from=compilers /gcc2.8.1 /tools/cc/gcc

COPY tools/nu64 /tools/linux

# symlinks to handle alternative compiler paths
RUN cd /tools && ln -nsf . build
RUN cd /tools && ln -nsf ido_recomp/linux/5.3 ido5.3_recomp
RUN cd /tools && ln -nsf ido_recomp/linux/7.1 ido7.1_recomp
RUN cd /tools && ln -nsf linux mac

USER user
