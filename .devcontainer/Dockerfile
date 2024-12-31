# Use the latest TeXLive image as the base image
FROM texlive/texlive:latest

# Switch to Tsinghua mirror with complete configuration
# Switch to Tsinghua mirror with complete configuration
RUN cp /etc/apt/sources.list.d/debian.sources /etc/apt/sources.list.d/debian.sources.bak && \
    echo 'Types: deb\n\
URIs: http://mirrors.tuna.tsinghua.edu.cn/debian\n\
Suites: testing testing-updates testing-backports\n\
Components: main contrib non-free non-free-firmware\n\
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg\n\
\n\
Types: deb-src\n\
URIs: http://mirrors.tuna.tsinghua.edu.cn/debian\n\
Suites: testing testing-updates testing-backports\n\
Components: main contrib non-free non-free-firmware\n\
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg\n\
\n\
Types: deb\n\
URIs: https://security.debian.org/debian-security\n\
Suites: testing-security\n\
Components: main contrib non-free non-free-firmware\n\
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg\n\
\n\
Types: deb-src\n\
URIs: https://security.debian.org/debian-security\n\
Suites: testing-security\n\
Components: main contrib non-free non-free-firmware\n\
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg' > /etc/apt/sources.list.d/debian.sources

# Install required packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    make \
    zsh \
    locales \
    tzdata \
    curl \
    wget \
    vim \
    unzip \
    fontconfig \
    openssh-client \
    && rm -rf /var/lib/apt/lists/*

# Set timezone and locale
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && \
    sed -i '/zh_CN.UTF-8/s/^# //g' /etc/locale.gen && \
    locale-gen
ENV LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8

# Install Oh My Zsh and plugins
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && \
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Configure zsh
RUN echo 'export ZSH="/root/.oh-my-zsh"' > ~/.zshrc && \
    echo 'ZSH_THEME="robbyrussell"' >> ~/.zshrc && \
    echo 'plugins=(git zsh-autosuggestions zsh-syntax-highlighting)' >> ~/.zshrc && \
    echo 'source $ZSH/oh-my-zsh.sh' >> ~/.zshrc && \
    echo 'export LANG=en_US.UTF-8' >> ~/.zshrc && \
    echo 'alias ll="ls -alh"' >> ~/.zshrc
# 创建用户字体目录
RUN mkdir -p ~/.fonts

# 下载并解压字体到用户字体目录
RUN cd ~/.fonts \
    && wget --tries=3 --timeout=15 https://gitlab.com/Xmagus/fonts/-/archive/thesis/fonts-thesis.zip \
    && unzip fonts-thesis.zip \
    && cp fonts-thesis/*.ttf ./ \
    && rm -rf fonts-thesis.zip fonts-thesis \
    && fc-cache -fv
# Set up zsh as default shell
RUN chsh -s /bin/zsh root

# Create a working directory
WORKDIR /workspace