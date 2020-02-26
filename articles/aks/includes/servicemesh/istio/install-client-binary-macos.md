---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594024"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Hämta och installera Istio istioctl-klientens binärfil

Använd `curl` för att hämta Istio-versionen i ett bash-baserat gränssnitt på MacOS och extrahera sedan med `tar` enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl`-klientens binärfil körs på klient datorn och gör att du kan interagera med Istio-tjänstens nät. Använd följande kommandon för att installera Istio-`istioctl` klientens binärfil i ett bash-baserat gränssnitt på MacOS. Dessa kommandon kopierar `istioctl`-klientens binärfil till standard platsen för användar program i `PATH`.

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