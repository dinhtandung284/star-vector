FROM pytorch/pytorch:2.5.1-cuda12.4-cudnn9-devel

# Install necessary dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    git \
    vim \
    build-essential \
    libcairo2 \
    cuda-compiler-12-4 \
    libaio-dev \
    && rm -rf /var/lib/apt/lists/*


# Install Jupyter Notebook, clone and install star-vector and dependencies.
# flash_attn's setup.py imports torch, so it must be installed with
# --no-build-isolation (pip's isolated build env has no torch). It needs
# packaging/ninja/psutil present beforehand; setup.py first tries to download a
# prebuilt wheel matching torch 2.5 / CUDA 12 / cp311, and only compiles if none fits.
ENV MAX_JOBS=4
RUN pip install --upgrade pip && pip install jupyter deepspeed packaging ninja psutil wheel \
    && pip install --no-build-isolation flash_attn==2.7.3 \
    && git clone https://github.com/joanrod/star-vector.git /tmp/star-vector \
    && pip install /tmp/star-vector \
    && rm -rf /tmp/star-vector

# Cleanup unneeded packages
RUN apt-get purge -y --auto-remove \
    git \
    build-essential \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Expose Jupyter Notebook port
EXPOSE 8888

WORKDIR /workspace

# Launch Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
