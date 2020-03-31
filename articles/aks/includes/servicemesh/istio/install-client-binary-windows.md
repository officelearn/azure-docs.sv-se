---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594003"
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
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```