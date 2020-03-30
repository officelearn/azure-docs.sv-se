---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594015"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Ladda ner och installera Istio istioctl-klienten binär

I ett bash-baserat skal på Linux eller [Windows Subsystem för Linux][install-wsl], använd `curl` för att ladda ner Istio release och sedan extrahera med `tar` följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Klientbinären `istioctl` körs på klientmaskinen och låter dig interagera med Istio-servicenätet. Använd följande kommandon för att installera `istioctl` Istio-klientbinären i ett bash-baserat skal på Linux eller [Windows-undersystemet för Linux][install-wsl]. Dessa kommandon kopierar klienten `istioctl` binär till standardanvändarprogramplatsen i . `PATH`

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill ha slutförad kommandorad `istioctl` för binära Istio-klienter ställer du in den på följande sätt:

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