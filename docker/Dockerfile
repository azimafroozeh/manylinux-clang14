###############################################################################
# A manylinux2014 image with Clang 14 + lld from conda-forge and libc++ headers
# Tags to build (examples):
#   linux/amd64 → quay.io/pypa/manylinux2014_x86_64
#   linux/arm64 → quay.io/pypa/manylinux2014_aarch64
###############################################################################
# ----- 1) choose base according to build arg (default: x86_64) --------------
ARG BASE_IMAGE=quay.io/pypa/manylinux2014_x86_64:2025.04.19-1
FROM ${BASE_IMAGE}

# ----- 2) system dev headers for <cstdint>, <vector>, … ---------------------
RUN yum install -y glibc-devel libstdc++-devel curl && yum clean all

# ----- 3) micromamba bootstrap ----------------------------------------------
RUN curl -Ls https://micromamba.snakepit.net/api/micromamba/linux-64/latest \
    | tar -xvj -C /usr/local/bin --strip-components=1 bin/micromamba

# ----- 4) create /opt/conda env with Clang 14 + libc++ headers --------------
RUN micromamba create --yes --prefix /opt/conda -c conda-forge \
        clang=14.* clangxx=14.* lld=14.* \
        libcxx=14.* libcxxabi=14.* \
    && micromamba clean --all --yes

# ----- 5) expose toolchain ----------------------------------------------------
ENV PATH=/opt/conda/bin:$PATH \
    CC=clang \
    CXX=clang++

# ----- 6) sanity check: clang & <cstdint> ------------------------------------
RUN clang --version && \
    printf '#include <cstdint>\nint main(){}' \
      | clang++ -std=c++20 -stdlib=libc++ -x c++ - -fsyntax-only

CMD ["bash"]
