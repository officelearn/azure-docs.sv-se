---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593752"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Ladda ner och installera Linkerd linkerd-klienten binär

I ett bash-baserat skal på Linux eller [Windows Subsystem för Linux][install-wsl], använd `curl` för att ladda ner Linkerd release enligt följande:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Klientbinären `linkerd` körs på klientmaskinen och låter dig interagera med Linkerd-tjänstnätet. Använd följande kommandon för att installera `linkerd` Linkerd-klientens binära i ett bash-baserat skal på Linux eller [Windows-undersystemet för Linux][install-wsl]. Dessa kommandon kopierar klienten `linkerd` binär till standardanvändarprogramplatsen i . `PATH`

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Om du vill ha slutförad kommandorad `linkerd` för binära Linkerd-klienten ställer du in den på följande sätt:

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