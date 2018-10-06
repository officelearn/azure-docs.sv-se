---
title: Uppdatera enheter till den senaste versionen av Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge-enheter för att köra de senaste versionerna av security-daemon och IoT Edge-körningen
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88ea4b2eab57684bc5455c0d8eb23a5d62f9dd77
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48817522"
---
# <a name="update-the-iot-edge-runtime"></a>Uppdatera IoT Edge-körningen

När IoT Edge-tjänsten släpper nya versioner, bör du uppdatera dina IoT Edge-enheter om du vill ha de senaste funktionerna och säkerhetsförbättringar. Den här artikeln innehåller information om hur du uppdaterar din IoT Edge-enheter när en ny version är tillgänglig. 

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Först är daemonen säkerhet som kan köras på enheten och startar körningen när enheten startas. Daemonen säkerhet kan för närvarande kan bara uppdateras från själva enheten. Den andra komponenten är körning, består av Edge hub och Edge-moduler för agenten. Beroende på hur du strukturera distributionen av kan körningen uppdateras från enheten eller på distans. 

Du hittar den senaste versionen av Azure IoT Edge [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).


## <a name="update-the-security-daemon"></a>Uppdatera daemonen säkerhet

Daemon för IoT Edge-security är en intern komponent som måste uppdateras via Pakethanteraren på IoT Edge-enheten. 

### <a name="linux-devices"></a>Linux-enheter

Använd apt-get eller chefen lämpligt paket för att uppdatera daemonen säkerhet på Linux-enheter. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter kan du använda PowerShell-skriptet avinstallera och installera sedan om daemonen säkerhet. Installationsskriptet hämtar automatiskt den senaste versionen av daemonen säkerhet. Du måste ange anslutningssträngen för din enhet igen under installationen. 

Avinstallera daemonen säkerhet i en PowerShell-administratörssession. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
UnInstall-SecurityDaemon
```

Installera om daemonen security beroende på om din IoT Edge-enhet använder behållare i Windows eller Linux-behållare. Ersätt frasen **\<Windows eller Linux\>** med något av operativsystemen behållare. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOS <Windows or Linux>
```

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar Edge-agent och Edge hub behållare beror på om du använder löpande taggar (till exempel 1.0) eller särskilda taggar (till exempel 1.0.2) i distributionen. 

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

Edge-agent och Edge hub avbildningarna är märkta med IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med runtime-avbildningar: 

* **Löpande taggar** -använder de två första värdena versionsnummer för att hämta den senaste avbildningen som matchar dessa siffror. Till exempel uppdateras 1.0 när det finns en ny version så att den pekar till den senaste versionen av 1.0.x. Om container runtime på din IoT Edge-enhet hämtar avbildningen igen, har runtime-moduler uppdaterats till den senaste versionen. Den här metoden rekommenderas för utveckling. Distributioner från Azure portal standard till rullande taggar. 
* **Särskilda taggar** -använda alla tre värdena för det lägre versionsnumret för att uttryckligen ställa in versionsnumret för avbildningen. Till exempel ändras 1.0.2 inte när den första versionen. Du kan deklarera ett nytt versionsnummer i manifestet distribution när du är redo att uppdatera. Den här metoden rekommenderas för produktion.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en löpande tagg-avbildning

Om du använder löpande taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0**) måste du tvinga behållaren runtime på enheten för att hämta den senaste versionen av avbildningen. 

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. 

```cmd/sh
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda kraften `-f` flagga för att ta bort bilderna. 

IoT Edge-tjänsten använder de senaste versionerna av runtime-avbildningar och startar automatiskt på din enhet dem igen. 

### <a name="update-a-specific-tag-image"></a>Uppdatera en specifik tagg-avbildning

Om du använder specifika taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0.2**) så är allt du behöver göra är att uppdatera taggen i ditt manifest för distributionen och sparar ändringarna till din enhet. 

I Azure-portalen distributionsavbildningar runtime deklareras i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. 

[Konfigurera avancerade edge-körningsinställningar](./media/how-to-update-iot-edge/configure-runtime.png)

I manifestet för en JSON-distribution, uppdatera modulen avbildningar i den **systemModules** avsnittet. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>Nästa steg

Visa senast [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med de senaste uppdateringarna och meddelandet i den [Sakernas Internet-bloggen](https://azure.microsoft.com/blog/topics/internet-of-things/
) 