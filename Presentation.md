name: inverse
layout: true
class: center, middle, inverse

---
<!-- markdownlint-disable MD041 MD001 MD024 MD003 MD033-->

# PSPHDC'24

### New SSL support with cURL

#### Presented by

[@KorigamiK](https://github.com/korigamik)
&
[@Bucanero](https://github.com/bucanero)

---

layout: false

# Introduction

## cURL

`libcurl` is a free and easy-to-use client-side URL transfer library,
supporting many protocols like FTP, TFTP, GOPHER, HTTP, HTTPS, and more.

It is highly portable, it builds and works identically on numerous platforms,
and supports SSL secure connections through different SSL libraries like OpenSSL,
mbedTLS, PolarSSL, wolfSSL, and others.

---

# Implementation

## PolarSSL / mbedTLS

PolarSSL offers an SSL library with an intuitive API and readable
source code in portable C language.

> Note 1: [polarSSL](https://polarssl.org/) was later acquired by ARM and is now
known as [mbedTLS](https://www.trustedfirmware.org/projects/mbed-tls/).

> Note 2: the PSP has internal http/ssl libraries, but those libs are old,
and you can't connect to current web servers due to outdated ciphers and protocols.

---

# Implementation

## Why PolarSSL / mbedTLS?

I have been building homebrew apps on other Sony platforms,
including PS3, PS4, and PS Vita.

The open-source PS3 PSL1GHT SDK had an old
[libcurl 7.31.0](https://github.com/ps3dev/ps3libraries/blob/master/scripts/016-libcurl-7.31.0.sh) +
[polarSSL 1.2.8](https://github.com/ps3dev/ps3libraries/blob/master/scripts/015-polarssl.sh)
implementation working. So for compatibility reasons,
when I was working on the PS3 I decided to update that build to a more
recent one that had TLS 1.2 support, while keeping polarSSL before it
changed to mbedTLS.

Since at some point PolarSSL was replaced by mbedTLS, I decided to use:

- libcurl 7.64.1
- PolarSSL 1.3.9 [(Relase notes)](https://polarssl.org/tech-updates/releases/polarssl-1.3.9-released/)

Since then, this combination has been working with great results on PS3
([PSL1GHT SDK](https://github.com/bucanero/ps3libraries/blob/master/scripts/016-libcurl-7.64.1.sh)),
and also on PS4 ([OpenOrbis SDK](https://github.com/bucanero/oosdk_libraries/tree/master/curl-7.64.1)).

---

# Implementation

## PSP - from polarSSL to mbedTLS

I recently started building [my first PSP homebrew](https://github.com/bucanero/apollo-psp),
and found that while the PSP-SDK had a libcurl build, it was quite old (7.15.1),
and it didn't had any SSL support, so you could only use it with unsecure HTTP
or FTP connections.

Based on my previous experience (PS3/PS4), I went ahead and decided to port
[polarSSL to the PSP](https://github.com/pspdev/psp-packages/pull/84),
and update [libcurl to 7.64.1](https://github.com/pspdev/psp-packages/pull/85)
to have a fully working TLS 1.2 solution to connect to secure HTTPS servers.

During my tests, cURL had a few issues when connecting to some HTTPS servers,
depending on the supported SSL ciphers. To solve it, I ported and patched 
[mbedTLS 2.16.6](https://github.com/pspdev/psp-packages/tree/master/mbedtls) to the PSP,
and switched libcurl to use mbedTLS.

---

# Demo

## How?

Let's take a look at what we can build using these libraries,
which allows for maximum cross-platform compatibility.

| | | |
| :--- | :----: | ---: |
| ![c/c++](https://ms-vscode.gallerycdn.vsassets.io/extensions/ms-vscode/cpptools/1.15.1/1679529633699/Microsoft.VisualStudio.Services.Icons.Default) | ![cmake](https://twxs.gallerycdn.vsassets.io/extensions/twxs/cmake/0.0.17/1488841920286/Microsoft.VisualStudio.Services.Icons.Default) | ![sdl2](https://www.libsdl.org/media/SDL_logo.png) |

## Example Projects

- The original HTTPS test app on [GitHub](https://github.com/KorigamiK/PSP-Net-libcurl)
  
- Jokr source code and binaries on [GitHub](https://github.com/KorigamiK/PSPHDC-23/tree/main/Jokr)

---

# Demo

## Jokr

Jokr, built by [@KorigamiK](https://github.com/korigamik), is a simple app that fetches a random joke
from [https://icanhazdadjoke.com](https://icanhazdadjoke.com/),
and shows it on the screen.

<div style="text-align:center">
    <img src="Jokr/res/joke.png" width="60%" alt="Jokr" />
</div>

---

# Demo - Jokr

## Features

- Cross-platform builds for Linux, Windows, and PSP
- SDL2 for graphics and input
- [Open Source](https://github.com/bucanero/PSPHDC-24/tree/main/Jokr)

### See it in action

<div style="text-align:center">
  <video width="80%" controls loop muted>
    <source
      src="https://user-images.githubusercontent.com/72932688/228461499-6844f137-06b4-4e73-82e5-32a2fa38a97e.MOV"
      type="video/mp4"
    >
  </video>
</div>

---

# Future Implications & Ideas

- It reopens the PSP for the internet age, and opens the door for a lot of new possibilities.

- Now you can create PSP homebrew apps with TLS 1.2 networking support:
  - you can download files,
  - connect to APIs,
  - and upload data, from any HTTPS web server

- Patch old homebrew to a working state, and make them work again.

- More Cross-platform homebrew apps, and more open-source projects.

---

class: middle, inverse

# Resources / References

- [PSP-SDK Net Resolver sample](https://github.com/pspdev/pspsdk/tree/master/src/samples/net/resolver)
- [PSP-Maps source code](https://github.com/PSP-Archive/PSP-Maps)
- [libcurl sample](https://github.com/KorigamiK/PSP-Net-libcurl) (with sceGu)
- [Jokr source code](https://github.com/bucanero/PSPHDC-24/tree/demo-app/Jokr) (with auto access point connection)
