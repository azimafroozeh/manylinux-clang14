# `manylinux-clang14` Docker Image  
**Multi-arch (`amd64` + `arm64`) · Clang 14**

[![Docker – latest](https://img.shields.io/badge/ghcr.io%2Fazimafroozeh-manylinux--clang14-0A7ACA?logo=docker&label=latest)](https://github.com/users/azimafroozeh/packages/container/manylinux-clang14)  
[![Build & Push CI](https://github.com/azimafroozeh/manylinux-clang14/actions/workflows/docker-push.yml/badge.svg)](https://github.com/azimafroozeh/manylinux-clang14/actions)

A **minimal manylinux2014-compatible** container bundling **Clang 14** via Conda-Forge for reproducible Python-wheel (and C/C++) builds.

---

## Purpose

Projects that publish Python wheels with compiled C/C++ extensions often target the [manylinux2014] standard to maximize Linux compatibility.  
However, the official manylinux images ship only GCC. If you need **Clang 14**—to match upstream toolchains, test ABI compatibility, or leverage sanitizers—this image delivers:

- A **manylinux2014** environment (glibc 2.17)  
- Pre-built **Clang 14.0.x** binaries from Conda-Forge  
- Fast startup (~30 s build, no LLVM compilation)  
- Multi-arch support for both **amd64** and **arm64**

---

## Quick start

```bash
# 1 · Pull
docker pull ghcr.io/azimafroozeh/manylinux-clang14:14   # or :latest

# 2 · Verify
docker run --rm ghcr.io/azimafroozeh/manylinux-clang14:14 clang --version
````

> **Repo-scoped tag** (alternate namespace):
> `ghcr.io/azimafroozeh/manylinux-clang14/manylinux-clang14:14`

---

## Typical workflows

<details>
<summary><strong>Make targets</strong></summary>

```makefile
IMAGE = ghcr.io/azimafroozeh/manylinux-clang14:14

build-wheel:
	docker run --rm \
	  -v "$(PWD)":/io -w /io \
	  $(IMAGE) \
	  /opt/python/cp310-cp310/bin/python -m pip wheel . \
	  --no-deps --wheel-dir /io/dist
```

</details>

<details>
<summary><strong>GitHub Actions build</strong></summary>

```yaml
jobs:
  build-wheels:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build manylinux wheels
        run: |
          docker run --rm \
            -v "$PWD":/io -w /io \
            ghcr.io/azimafroozeh/manylinux-clang14:14 \
            /opt/python/cp39-cp39/bin/python -m pip wheel . \
            --no-deps -w dist
```

</details>

---

## Image details

* **Base**: `condaforge/miniforge3:latest`
  (Conda-Forge’s multi-arch, manylinux2014-compatible Miniforge)
* **Clang**: installed via
  `mamba install -y -c conda-forge 'clang=14.*' 'clangxx=14.*' 'lld=14.*'`
* **Final image size**: ≈ 500 MB (Miniforge + Clang)
* **Symlinks**:
  `/usr/local/bin/clang → /opt/conda/bin/clang`
  `/usr/local/bin/clang++ → /opt/conda/bin/clang++`
* Built & published by [docker-push.yml](.github/workflows/docker-push.yml)

### Supported tags

| Tag            | Purpose                                    |
| -------------- | ------------------------------------------ |
| `14`, `latest` | identical; rebuilt on every push to `main` |

---

## Building locally

```bash
# 1 · Ensure buildx is set up
docker buildx create --use --name multiarch || true

# 2 · Build for host arch only
docker build \
  -t manylinux-clang14:dev \
  -f Dockerfile .

# 3 · Multi-arch push
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t ghcr.io/azimafroozeh/manylinux-clang14:14 \
  -t ghcr.io/azimafroozeh/manylinux-clang14:latest \
  --push \
  -f Dockerfile .
```

---

## Contributing

1. Edit **`Dockerfile`** (e.g. bump Clang version, add tools).
2. Push to `main` — CI rebuilds & publishes multi-arch tags.
3. PRs & issues welcome!

---

## License

Code in this repo is MIT-licensed.
`clang`/`clang++` binaries remain under the [LLVM License](https://llvm.org/LICENSE.txt).

[manylinux2014]: https://www.python.org/dev/peps/pep-0599/```
