# 1) Base on the official manylinux2014_x86_64, so /opt/python/cp3*-cp3* exist
FROM quay.io/pypa/manylinux2014_x86_64:2025.04.19-1

# 2) Install micromamba (lightweight Conda installer)
RUN curl -Ls https://micromamba.snakepit.net/api/micromamba/linux-64/latest \
    | tar -xvj -C /usr/local/bin --strip-components=1 bin/micromamba

# 3) Create a Conda env at /opt/conda with Clang-14 from conda-forge
RUN micromamba create --yes --prefix /opt/conda \
        -c conda-forge clang=14.* clangxx=14.* lld=14.* \
    && micromamba clean --all --yes

# 4) Put Conda’s clang in PATH and set CC/CXX
ENV PATH=/opt/conda/bin:$PATH \
    CC=clang \
    CXX=clang++

# 5) (Optional) verify both clang and Python interpreters are present
RUN clang --version && ls /opt/python/cp312-cp312/bin/python

CMD ["bash"]
