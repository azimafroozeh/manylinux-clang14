ARG ARCH=x86_64
FROM quay.io/pypa/manylinux_2_28_${ARCH}

# 1) enable powertools (for llvm-libs) and install Clang 16
RUN yum install -y dnf-plugins-core \
 && yum config-manager --set-enabled powertools \
 && yum install -y clang lld llvm-libs \
                  glibc-devel libstdc++-devel \
 && yum clean all

ENV CC=clang CXX=clang++

# sanity check
RUN clang --version \
 && printf '#include <cstdint>\nint main(){}' \
      | clang++ -std=c++20 -x c++ - -fsyntax-only \
 && echo '✅ headers found (Clang + libstdc++)'

CMD ["bash"]
