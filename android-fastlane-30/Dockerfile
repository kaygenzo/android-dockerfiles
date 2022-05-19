FROM openjdk:11-jdk-slim

RUN apt-get update \
  && mkdir -p /usr/share/man/man1 \
  && apt-get install -y \
    git mercurial xvfb apt \
    locales sudo openssh-client ca-certificates tar gzip parallel \
    net-tools netcat unzip zip bzip2 gnupg curl wget make

# Set timezone to UTC by default
RUN ln -sf /usr/share/zoneinfo/Etc/UTC /etc/localtime

# Use unicode
RUN locale-gen C.UTF-8 || true
ENV LANG=C.UTF-8

CMD ["/bin/sh"]

ENV HOME /home/project

ARG cmdline_tools=https://dl.google.com/android/repository/commandlinetools-linux-6609375_latest.zip
ARG android_home=/opt/android/sdk
ARG ruby_version=3.1.1

# SHA-256 92ffee5a1d98d856634e8b71132e8a95d96c83a63fde1099be3d86df3106def9

RUN sudo apt-get update && \
    sudo apt-get install --yes \
        xvfb lib32z1 lib32stdc++6 build-essential \
        libcurl4-openssl-dev libglu1-mesa libxi-dev libxmu-dev \
        libglu1-mesa-dev && \
    sudo rm -rf /var/lib/apt/lists/*

# Install Rbenv
RUN sudo apt-get update && \
    sudo apt install --yes \
	git curl autoconf bison build-essential \
	libssl-dev libyaml-dev libreadline6-dev \
	zlib1g-dev libncurses5-dev libffi-dev \
	libgdbm6 libgdbm-dev libdb-dev && \
    curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash && \
    sudo rm -rf /var/lib/apt/lists/*

ENV PATH ${HOME}/.rbenv/bin:${HOME}/.rbenv/shims:${PATH}

# Install Ruby
RUN rbenv install ${ruby_version} && \
    rbenv global ${ruby_version}

RUN gem install bundler && \
    gem install bundle

# Download and install Android Commandline Tools
RUN sudo mkdir -p ${android_home}/cmdline-tools && \
    wget -O /tmp/cmdline-tools.zip -t 5 "${cmdline_tools}" && \
    unzip -q /tmp/cmdline-tools.zip -d ${android_home}/cmdline-tools && \
    rm /tmp/cmdline-tools.zip

# Set environmental variables
ENV ANDROID_HOME ${android_home}
ENV ANDROID_SDK_ROOT ${android_home}
ENV ADB_INSTALL_TIMEOUT 120
ENV PATH ${ANDROID_SDK_ROOT}/emulator:${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin:${ANDROID_SDK_ROOT}/tools:${ANDROID_SDK_ROOT}/tools/bin:${ANDROID_SDK_ROOT}/platform-tools:${PATH}

RUN yes | sdkmanager --licenses && yes | sdkmanager --update

# Update SDK manager and install system image, platform and build tools
RUN sdkmanager \
  "tools" \
  "platform-tools" \
  "emulator"

RUN sdkmanager \
  "build-tools;30.0.0" \
  "build-tools;30.0.1" \
  "build-tools;30.0.2" \
  "build-tools;30.0.3" \
  "build-tools;31.0.0" \
  "build-tools;32.0.0"

RUN sdkmanager "platforms;android-30"

WORKDIR ${HOME}
