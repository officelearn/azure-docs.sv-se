---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593752"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Hämta och installera Linkerd Linkerd-klientens binärfil

I ett bash gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]använder du `curl` för att hämta Linkerd-versionen på följande sätt:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

`linkerd`-klientens binärfil körs på klient datorn och gör att du kan interagera med Linkerd-tjänstens nät. Använd följande kommandon för att installera Linkerd-`linkerd` klientens binärfil i ett bash-baserat gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]. Dessa kommandon kopierar `linkerd`-klientens binärfil till standard platsen för användar program i `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
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

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10