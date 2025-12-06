# Cross-compile aria2 for Windows (MinGW-w64 in Docker with OpenSSL)

This project provides a Docker-based cross-compilation environment for building **aria2 (aria2c.exe)** for Windows using **MinGW-w64** on Linux, with **OpenSSL** enabled.

The build targets older Windows versions (e.g., Windows 7/8) that do not ship with native TLS 1.3 support, ensuring compatibility through the bundled OpenSSL.

## Features

* Cross-compile on Linux using Docker (no local MinGW setup required)

* Produces a 64-bit Windows build

* OpenSSL enabled (suitable for older Windows versions)

* Default CA bundle configured for [Git for Windows](https://gitforwindows.org)

  * `--with-ca-bundle='C:/Program Files/Git/mingw64/etc/ssl/certs/ca-bundle.crt'`

## 1. Install Docker (Debian 13)

```sh
sudo apt-get update
sudo apt-get install docker.io docker-cli --no-install-recommends -y
```

## 2. Get the Dockerfile

You may clone this repository or download `Dockerfile.mingw` directly.

## 3. Build with Dockerfile

From the directory containing `Dockerfile.mingw`:

```sh
sudo docker build -t aria2-mingw - < Dockerfile.mingw
```

This will download dependencies and compile aria2 inside a container.

## 4. Export the compiled aria2c.exe

```sh
sudo docker run --rm -it -v /path/to/dest:/out aria2-mingw \
    cp /aria2/src/aria2c.exe /out
```

The file `/path/to/dest/aria2c.exe` will be your final Windows binary.

## 5. CA Certificates

By default, `aria2c.exe` uses the **CA certificate bundle** provided by **Git for Windows**. The default path is:

```
C:/Program Files/Git/mingw64/etc/ssl/certs/ca-bundle.crt
```

This ensures HTTPS connections work correctly on Windows systems without native TLS 1.3 support.

### Custom CA Certificate Location

If Git for Windows is **not installed**, `aria2c.exe` will not have a default CA certificate bundle. In this case, you need to provide one manually. You can download a CA certificate bundle from sources such as [curl's CA bundle](https://curl.se/docs/caextract.html) and then tell aria2c where to use it.

The steps are:

1. **Download a CA certificate bundle**:

```sh
curl -o /path/to/ca-bundle.crt https://curl.se/ca/cacert.pem
```

2. **Specify the CA bundle for aria2c**

   You can do this either:

   * **Via command-line argument**:

     ```sh
     aria2c --ca-certificate=/path/to/ca-bundle.crt [other options]
     ```

   * **Or in the configuration file** (`aria2.conf`):

     ```
     ca-cert=/path/to/ca-bundle.crt
     ```

This ensures HTTPS connections are verified correctly even on systems without Git for Windows installed.

## License

Refer to the upstream [aria2](https://github.com/aria2/aria2) project for license information.

This repository contains only build scripts and configuration.
