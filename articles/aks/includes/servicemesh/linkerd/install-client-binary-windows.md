---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: cb24e2e1511c64024d4fefdc8accab53db3f2071
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530126"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Hämta och installera Linkerd Linkerd-klientens binärfil

I ett PowerShell-baserat gränssnitt i Windows använder du `Invoke-WebRequest` för att ladda ned Linkerd-versionen på följande sätt:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

@No__t_0-klientens binärfil körs på klient datorn och gör att du kan interagera med Linkerd-tjänstens nät. Använd följande kommandon för att installera Linkerd-`linkerd`-klientens binärfil i ett PowerShell-baserat gränssnitt i Windows. Dessa kommandon kopierar `linkerd`-klientens binärfil till en Linkerd-mapp och gör den tillgänglig både direkt (i aktuellt gränssnitt) och permanent (mellan omstarter av gränssnittet) via din `PATH`. Du behöver inte utökade privilegier (admin) för att köra dessa kommandon och du behöver inte starta om gränssnittet.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```