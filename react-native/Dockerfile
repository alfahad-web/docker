# Use an official Ubuntu base image
FROM ubuntu:20.04

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive \
    ANDROID_HOME=/opt/android-sdk \
    PATH=$PATH:/opt/android-sdk/tools:/opt/android-sdk/platform-tools:/opt/android-sdk/cmdline-tools/latest/bin

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
    && rm -rf /var/lib/apt/lists/*

# Packages for checking display compatibility
RUN apt-get update && apt-get install -y pcmanfm featherpad lxtask xterm \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

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
        "build-tools;33.0.2"
RUN /opt/android-sdk/cmdline-tools/latest/bin/sdkmanager \
        "system-images;android-33;google_apis;x86_64"
RUN /opt/android-sdk/cmdline-tools/latest/bin/sdkmanager \
        "emulator"

# Install Watchman and dependencies
RUN apt-get update && apt-get install -y \
    automake \
    build-essential \
    libtool \
    pkg-config \
    autoconf \
    openssl \
    libssl-dev
RUN git clone https://github.com/facebook/watchman.git /tmp/watchman && \
    cd /tmp/watchman
RUN cd /tmp/watchman && ./autogen.sh
RUN cd /tmp/watchman && ./configure
RUN cd /tmp/watchman && make
RUN cd /tmp/watchman && make install
RUN rm -rf /tmp/watchman

# Install Android Emulator and necessary libraries (including Qt and software rendering support)
RUN apt-get update && apt-get install -y \
    qemu-kvm \
    libvirt-daemon-system \
    libvirt-clients \
    libxkbfile1 \
    libx11-xcb1 \
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

# Additional X11 libraries for software rendering
RUN apt-get update && apt-get install -y \
    libx11-xcb1 \
    libxcb1 \
    libxcb-glx0 \
    libxcb-icccm4 \
    libxcb-image0 \
    libxcb-keysyms1 \
    libxcb-randr0 \
    libxcb-render0 \
    libxcb-render-util0 \
    libxcb-shape0 \
    libxcb-shm0 \
    libxcb-sync1 \
    libxcb-xfixes0 \
    libxcb-xinerama0 \
    libxcb-xkb1 \
    libxcb-cursor0 \
    && apt-get clean

RUN apt-get update && apt-get install -y net-tools iputils-ping usbutils

# Create an Android Virtual Device (AVD)
RUN echo "no" | /opt/android-sdk/cmdline-tools/latest/bin/avdmanager create avd -n test_emulator -k "system-images;android-33;google_apis;x86_64" --device "pixel_4" && \
    mkdir -p ~/.android/ && touch ~/.android/repositories.cfg

# Set up a working directory
WORKDIR /usr/src/app

# Copy project files to the working directory, excluding Dockerfile
# COPY . /usr/src/app

# Expose necessary ports
EXPOSE 8081

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

ENV DISPLAY=host.docker.internal:0.0

# Default command
CMD ["/bin/bash"]
