# manylinux2014-compatible Clang 14 from Conda-Forge
FROM docker.io/condaforge/miniforge3:latest

# Install LLVM/Clang 14 toolchain
RUN mamba install -y -c conda-forge \
        'clang=14.*' 'clangxx=14.*' 'lld=14.*' \
        && mamba clean -afy

# Convenience symlinks
RUN ln -s /opt/conda/bin/clang   /usr/local/bin/clang  && \
    ln -s /opt/conda/bin/clang++ /usr/local/bin/clang++

CMD ["clang", "--version"]
