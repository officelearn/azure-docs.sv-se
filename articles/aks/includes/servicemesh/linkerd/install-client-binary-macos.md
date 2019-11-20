---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a4090172e926f21db01a8e374d8c4bb1c80402c7
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74197379"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Hämta och installera Linkerd Linkerd-klientens binärfil

I ett bash gränssnitt på MacOS använder `curl` för att hämta Linkerd-versionen på följande sätt:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

`linkerd`-klientens binärfil körs på klient datorn och gör att du kan interagera med Linkerd-tjänstens nät. Använd följande kommandon för att installera Linkerd-`linkerd` klientens binärfil i ett bash-baserat gränssnitt på MacOS. Dessa kommandon kopierar `linkerd`-klientens binärfil till standard platsen för användar program i `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Om du vill att kommando tolken ska slutföras för Linkerd `linkerd`-klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
