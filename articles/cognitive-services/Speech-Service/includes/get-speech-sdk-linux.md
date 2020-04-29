---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399985"
---
:::row:::
    :::column span="3":::
        Tal-SDK: n stöder endast **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**och **CentOS 7/8** i följande mål arkitekturer när de används med Linux:
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
> När du använder Linux-ARM64 och använder C# – .NET Core 3. x (dotNet-SDK-3. x-paketet) krävs. Om du är mål för ARM32 eller ARM64 stöds inte python.

> [!NOTE]
> X86-arkitekturerna för Ubuntu 16,04, Ubuntu 18,04 och Debian 9 stöder bara C++-utveckling med talet SDK.

### <a name="system-requirements"></a>Systemkrav

För ett inbyggt program är tal-SDK: n beroende `libMicrosoft.CognitiveServices.Speech.core.so`av. Kontrol lera att mål arkitekturen (x86, x64) matchar programmet. Beroende på Linux-versionen kan det krävas ytterligare beroenden.

- De delade biblioteken för GNU C-biblioteket (inklusive programmerings biblioteket för POSIX `libpthreads`-trådar)
- OpenSSL-biblioteket (`libssl.so.1.0.0` eller `libssl.so.1.0.2`)
- Det delade biblioteket för ALSA-program`libasound.so.2`()

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
> Följ anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
