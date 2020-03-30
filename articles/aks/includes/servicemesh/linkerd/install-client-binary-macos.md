---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593754"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Ladda ner och installera Linkerd linkerd-klienten binär

I ett bash-baserat skal på `curl` MacOS kan du använda för att ladda ned Linkerd-versionen enligt följande:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Klientbinären `linkerd` körs på klientmaskinen och låter dig interagera med Linkerd-tjänstnätet. Använd följande kommandon för att installera `linkerd` Linkerd-klientbinären i ett bash-baserat skal på MacOS. Dessa kommandon kopierar klienten `linkerd` binär till standardanvändarprogramplatsen i . `PATH`

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
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
