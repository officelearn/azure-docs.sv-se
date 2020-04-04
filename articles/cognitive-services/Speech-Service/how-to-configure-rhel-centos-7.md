---
title: Konfigurera RHEL/CentOS 7 - Taltjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar RHEL/CentOS 7 så att Tal SDK kan användas.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639167"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurera RHEL/CentOS 7 för Tal SDK

Red Hat Enterprise Linux (RHEL) 8 x64 och CentOS 8 x64 stöds officiellt av Speech SDK version 1.10.0 och senare. Det är också möjligt att använda Tal SDK på RHEL/CentOS 7 x64, men detta kräver uppdatering av C++-kompilatorn (för C++-utveckling) och det delade C++-runtimebiblioteket på ditt system.

Om du vill kontrollera C++-kompilatorversionen kör du:

```bash
g++ --version
```

Om kompilatorn är installerad ska utdata se ut så här:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Detta meddelande låter dig veta att GCC större version 4 är installerad. Den här versionen har inte fullt stöd för C++ 11-standarden, som Tal-SDK använder. Om du försöker kompilera ett C++-program med den här GCC-versionen och Tal-SDK-huvudena resulterar det i kompileringsfel.

Det är också viktigt att kontrollera versionen av det delade C++-körningsbiblioteket (libstdc++). De flesta tal-SDK implementeras som inbyggda C++-bibliotek, vilket innebär att det beror på libstdc++ oavsett vilket språk du använder för att utveckla program.

Om du vill hitta platsen för libstdc++ på datorn kör du:

```bash
ldconfig -p | grep libstdc++
```

Produktionen på vanilj RHEL/CentOS 7 (x64) är:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Baserat på det här meddelandet bör du kontrollera versionsdefinitionerna med det här kommandot:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Utgången bör vara:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Tal-SDK kräver **CXXABI_1.3.9** och **GLIBCXX_3.4.21**. Du hittar den här `ldd libMicrosoft.CognitiveServices.Speech.core.so` informationen genom att köra på Speech SDK-biblioteken från Linux-paketet.

> [!NOTE]
> Vi rekommenderar att den version av GCC som är installerad på systemet är minst **5.4.0**, med matchande körningsbibliotek.

## <a name="example"></a>Exempel

Det här är ett exempelkommando som illustrerar hur du konfigurerar RHEL/CentOS 7 x64 för utveckling (C++, C#, Java, Python) med Tal SDK 1.10.0 eller senare:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om Speech SDK](speech-sdk.md)
