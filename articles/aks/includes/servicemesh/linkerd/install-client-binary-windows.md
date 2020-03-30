---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593753"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Ladda ner och installera Linkerd linkerd-klienten binär

I ett PowerShell-baserat skal `Invoke-WebRequest` på Windows kan du använda för att hämta Linkerd-versionen enligt följande:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Klientbinären `linkerd` körs på klientmaskinen och låter dig interagera med Linkerd-tjänstnätet. Använd följande kommandon för att installera `linkerd` Linkerd-klientens binära i ett PowerShell-baserat skal i Windows. Dessa kommandon kopierar klientbinären `linkerd` till en Linkerd-mapp och gör den sedan tillgänglig både omedelbart `PATH`(i aktuellt skal) och permanent (över omstarter skalet) via din . Du behöver inte förhöjda (Admin) privilegier för att köra dessa kommandon och du behöver inte starta om skalet.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```