---
title: Så här konfigurerar du RHEL/CentOS 7-tal service
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar RHEL/CentOS 7 så att du kan använda tal-SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639167"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurera RHEL/CentOS 7 för tal-SDK

Red Hat Enterprise Linux (RHEL) 8 x64 och CentOS 8 x64 stöds officiellt av tal SDK-versionen 1.10.0 och senare. Du kan också använda tal-SDK: n på RHEL/CentOS 7 x64, men det kräver att C++-kompilatorn uppdateras (för C++-utveckling) och det delade C++ runtime-biblioteket i systemet.

Kör följande för att kontrol lera C++-kompilator versionen:

```bash
g++ --version
```

Om kompilatorn är installerad bör utdata se ut så här:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

I det här meddelandet kan du se att GCC Major version 4 är installerad. Den här versionen har inte fullständigt stöd för C++ 11 standard, vilket används i tal-SDK. Om du försöker kompilera ett C++-program med den här GCC-versionen och talet SDK-huvuden kommer att leda till kompileringsfel.

Det är också viktigt att kontrol lera versionen av det delade C++ runtime library (libstdc + +). De flesta tal-SDK: n implementeras som inbyggda C++-bibliotek, vilket innebär att det är beroende av libstdc + + oavsett vilket språk du använder för att utveckla program.

Du hittar platsen för libstdc + + i systemet genom att köra:

```bash
ldconfig -p | grep libstdc++
```

Utdata på vanilj RHEL/CentOS 7 (x64) är:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Utifrån det här meddelandet ska du kontrol lera versions definitionerna med följande kommando:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Utdata ska vara:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Talet SDK kräver **CXXABI_1.3.9** och **GLIBCXX_3.4.21**. Du kan hitta den här informationen genom `ldd libMicrosoft.CognitiveServices.Speech.core.so` att köra i tal SDK-biblioteken från Linux-paketet.

> [!NOTE]
> Vi rekommenderar att versionen av GCC som är installerad på systemet är minst **5.4.0**, med matchande körnings bibliotek.

## <a name="example"></a>Exempel

Detta är ett exempel kommando som illustrerar hur du konfigurerar RHEL/CentOS 7 x64 för utveckling (C++, C#, Java, python) med talet SDK 1.10.0 eller senare:

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
