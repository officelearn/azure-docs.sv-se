---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 14f9d0936a4d1949cf8d7fc69bbb782ee447bdba
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668761"
---
:::row:::
    :::column span="3":::
        Speech SDK stöder endast **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**och **CentOS 7/8** på följande målarkitekturer när de används med Linux:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> När du riktar in dig på Linux ARM64 och använder C# - krävs .NET Core 3.x (dotnet-sdk-3.x-paketet). Om du riktar in dig på ARM32 eller ARM64 stöds inte Python.

> [!NOTE]
> X86-arkitekturerna i Ubuntu 16.04, Ubuntu 18.04 och Debian 9 stöder endast C++-utveckling med Speech SDK.

### <a name="system-requirements"></a>Systemkrav

För ett inbyggt program är Tal-SDK beroende av `libMicrosoft.CognitiveServices.Speech.core.so`. Kontrollera att målarkitekturen (x86, x64) matchar programmet. Beroende på Linux-versionen kan ytterligare beroenden krävas.

- De delade biblioteken i GNU C-biblioteket (inklusive POSIX-trådar programmeringsbiblioteket, `libpthreads`)
- OpenSSL-biblioteket`libssl.so.1.0.0` ( `libssl.so.1.0.2`eller )
- Det delade biblioteket för ALSA-applikationer (`libasound.so.2`)

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 och CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Följ instruktionerna om [hur du konfigurerar RHEL/CentOS 7 för Tal SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> På RHEL/CentOS 8 följer du instruktionerna för [hur du konfigurerar OpenSSL för Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
