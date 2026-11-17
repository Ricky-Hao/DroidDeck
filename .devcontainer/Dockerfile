FROM mcr.microsoft.com/devcontainers/base:1-bookworm@sha256:4bd6635f8df3a451a8fa8ef6f4d96d83baddeffcd4afcde66e45be3e51b2f331

USER root
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN set -eux; \
    test "$(dpkg --print-architecture)" = amd64; \
    apt-get update; \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      openjdk-17-jdk-headless python3 python3-venv python3-pip \
      git openssh-client ca-certificates curl tar xz-utils zstd unzip zip \
      build-essential cmake ninja-build pkg-config libssl-dev libvulkan-dev \
      autoconf automake libtool patch file rsync jq ripgrep shellcheck gh; \
    rm -rf /var/lib/apt/lists/*; \
    ln -s "$(dirname "$(dirname "$(readlink -f "$(command -v javac)")")")" /opt/java

ENV JAVA_HOME=/opt/java \
    ANDROID_HOME=/opt/android-sdk \
    ANDROID_SDK_ROOT=/opt/android-sdk \
    ANDROID_USER_HOME=/home/vscode/.android \
    ANDROID_NDK_HOME=/opt/android-sdk/ndk/27.3.13750724 \
    ANDROID_NDK_ROOT=/opt/android-sdk/ndk/27.3.13750724 \
    NDK=/opt/android-sdk/ndk/27.3.13750724
ENV PATH="${JAVA_HOME}/bin:${ANDROID_HOME}/cmdline-tools/12.0/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/cmake/3.22.1/bin:${PATH}"

# Pin Java 17-compatible tools; the checksum is from Google's SDK repository metadata.
RUN set -eux; \
    sdk_tmp="$(mktemp -d)"; \
    curl -fsSL https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip \
      -o "${sdk_tmp}/tools.zip"; \
    printf '%s  %s\n' d313adb7aedccf6cf0cfca51ec180f0059f5f8f8 "${sdk_tmp}/tools.zip" | sha1sum -c -; \
    mkdir -p "${ANDROID_HOME}/cmdline-tools"; \
    unzip -q "${sdk_tmp}/tools.zip" -d "${sdk_tmp}/unpacked"; \
    mv "${sdk_tmp}/unpacked/cmdline-tools" "${ANDROID_HOME}/cmdline-tools/12.0"; \
    rm -rf "${sdk_tmp}"; \
    printf 'y\n%.0s' {1..100} | sdkmanager --sdk_root="${ANDROID_HOME}" --licenses >/dev/null; \
    sdkmanager --sdk_root="${ANDROID_HOME}" --channel=0 --install \
      "platform-tools" "platforms;android-34" \
      "build-tools;34.0.0" "build-tools;35.0.0" \
      "ndk;27.3.13750724" "cmake;3.22.1"; \
    install -d -o vscode -g vscode /home/vscode/.android /home/vscode/.cache; \
    chown -R vscode:vscode "${ANDROID_HOME}"

USER vscode
