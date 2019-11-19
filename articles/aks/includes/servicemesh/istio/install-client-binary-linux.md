---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e2dd45f778bd5e596b5bcc91c63984742237ad4c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170861"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Hämta och installera Istio istioctl-klientens binärfil

I ett bash gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]använder du `curl` för att hämta Istio-versionen och extrahera sedan med `tar` enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl`-klientens binärfil körs på klient datorn och gör att du kan interagera med Istio-tjänstens nät. Använd följande kommandon för att installera Istio-`istioctl` klientens binärfil i ett bash-baserat gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]. Dessa kommandon kopierar `istioctl`-klientens binärfil till standard platsen för användar program i `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill att kommando tolken ska slutföras för Istio `istioctl`-klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10