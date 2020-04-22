---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736630"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Ladda ner och installera Istio istioctl-klienten binär

I ett PowerShell-baserat skal `Invoke-WebRequest` på Windows kan du använda för `Expand-Archive` att hämta Istio-versionen och sedan extrahera med följande:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Klientbinären `istioctl` körs på klientmaskinen och låter dig interagera med Istio-servicenätet. Använd följande kommandon för att installera `istioctl` Istio-klientens binära i ett PowerShell-baserat skal i Windows. Dessa kommandon kopierar klientbinären `istioctl` till en Istio-mapp och gör den sedan tillgänglig både omedelbart `PATH`(i aktuellt skal) och permanent (över omstarter skalet) via din . Du behöver inte förhöjda (Admin) privilegier för att köra dessa kommandon och du behöver inte starta om skalet.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```