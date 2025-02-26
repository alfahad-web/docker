# Use an official Ubuntu base image
FROM ubuntu:20.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive \
    ANDROID_HOME=/opt/android-sdk \
    PATH=$PATH:/opt/android-sdk/tools:/opt/android-sdk/platform-tools:/opt/android-sdk/cmdline-tools/latest/bin:/flutter/bin

# Update and install basic dependencies
RUN apt-get update && apt-get install -y \
    curl \
    git \
    unzip \
    zip \
    build-essential \
    wget \
    lib32stdc++6 \
    lib32z1 \
    python3 \
    python3-pip \
    clang \
    cmake \
    ninja-build \
    libgtk-3-dev \
    pkg-config \
    xz-utils \
    && rm -rf /var/lib/apt/lists/*

# Install OpenJDK 19
RUN wget https://download.oracle.com/java/19/archive/jdk-19.0.2_linux-x64_bin.tar.gz -O /tmp/jdk19.tar.gz
RUN mkdir -p /usr/lib/jvm && \
    tar -xzf /tmp/jdk19.tar.gz -C /usr/lib/jvm && \
    mkdir /usr/lib/jvm/jdk19/ && \
    mv /usr/lib/jvm/jdk-19.0.2/* /usr/lib/jvm/jdk19/ && \
    rm /tmp/jdk19.tar.gz && rm -r /usr/lib/jvm/jdk-19.0.2

# Set JDK 19 as default
ENV JAVA_HOME=/usr/lib/jvm/jdk19
ENV PATH=$JAVA_HOME/bin:$PATH

# Install Node.js and npm
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - && \
    apt-get install -y nodejs

# Install Android SDK Command Line Tools
RUN mkdir -p /opt/android-sdk && cd /opt/android-sdk && \
    wget https://dl.google.com/android/repository/commandlinetools-linux-8512546_latest.zip -O cmdline-tools.zip
RUN cd /opt/android-sdk && \
    unzip cmdline-tools.zip && \
    mkdir cmdline-tools-tmp && \
    mv cmdline-tools/* cmdline-tools-tmp/ && \
    mkdir -p cmdline-tools/latest && \
    mv cmdline-tools-tmp/* cmdline-tools/latest/ && \
    rm cmdline-tools.zip && rm -r cmdline-tools-tmp

# Accept Android SDK licenses and install SDK tools
RUN yes | /opt/android-sdk/cmdline-tools/latest/bin/sdkmanager --licenses && \
    /opt/android-sdk/cmdline-tools/latest/bin/sdkmanager \
        "platform-tools" \
        "platforms;android-33" \
        "build-tools;33.0.2" \
        "system-images;android-33;google_apis;x86_64" \
        "emulator"

# Install Android Emulator and necessary libraries (including Qt and software rendering support)
RUN apt-get update && apt-get install -y \
    qemu-kvm \
    libvirt-daemon-system \
    libvirt-clients \
    libxkbfile1 \
    libfontconfig1 \
    libglu1-mesa \
    libxi6 \
    libxrender1 \
    libxrandr2 \
    libxtst6 \
    mesa-utils \
    libqt5widgets5 \
    libqt5gui5 \
    libqt5opengl5 \
    libqt5core5a \
    && ln -s /opt/android-sdk/emulator/emulator /usr/local/bin/emulator \
    && rm -rf /var/lib/apt/lists/*

# Install Flutter SDK
RUN mkdir /flutter && \
    git clone -b stable https://github.com/flutter/flutter.git /flutter && \
    flutter precache && \
    flutter doctor -v

# Set up a working directory
WORKDIR /usr/src/app

# Expose necessary ports
# EXPOSE 8081

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

ENV DISPLAY=host.docker.internal:0.0

# Default command
CMD ["/bin/bash"]
