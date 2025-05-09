###############################################################################
# manylinux2014 + Clang-14 via CentOS SCL + full C++ headers
###############################################################################
ARG ARCH=x86_64                     # overridden by build-arg for arm64
FROM quay.io/pypa/manylinux2014_${ARCH}:2025.04.19-1

# 1) System C/C++ dev headers so <cstdint> & STL exist
RUN yum install -y glibc-devel libstdc++-devel curl ca-certificates \
 && yum clean all

# 2) Download the static micromamba binary for this architecture
#    (no tar, no compression errors)
ARG MAMBA_ARCH=linux-64             # or linux-aarch64 for arm64
RUN curl -L \
    https://github.com/mamba-org/micromamba-releases/releases/latest/download/micromamba-${MAMBA_ARCH} \
  -o /usr/local/bin/micromamba \
  && chmod +x /usr/local/bin/micromamba

# 3) Create /opt/conda env with Clang-14 + libc++ runtime & headers
RUN micromamba create -y -p /opt/conda -c conda-forge \
        clang=14.* clangxx=14.* lld=14.* \
        libcxx=14.* libcxxabi=14.* \
    && micromamba clean -a -y

# 4) Expose the toolchain
ENV PATH=/opt/conda/bin:$PATH \
    CC=clang \
    CXX=clang++

# 5) Sanity check: clang works and <cstdint> is found
RUN clang --version \
 && printf '#include <cstdint>\nint main(){}' \
    | clang++ -std=c++20 -stdlib=libc++ -x c++ - -fsyntax-only \
 && echo "✅ C++ headers OK"

CMD ["bash"]
