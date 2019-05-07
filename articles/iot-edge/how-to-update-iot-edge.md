---
title: IoT Edge Uppdateringsversion på enheter – Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge-enheter för att köra de senaste versionerna av security-daemon och IoT Edge-körningen
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3b6327b9e05b039696cc1743fc2d16c5e945e26
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152622"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge security daemon och runtime

När IoT Edge-tjänsten släpper nya versioner, bör du uppdatera dina IoT Edge-enheter för de senaste funktionerna och säkerhetsförbättringar. Den här artikeln innehåller information om hur du uppdaterar din IoT Edge-enheter när en ny version är tillgänglig. 

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Först är daemonen säkerhet som kan köras på enheten och startar moduler för körning när enheten startas. Daemonen säkerhet kan för närvarande kan bara uppdateras från själva enheten. Den andra komponenten är körning, består av IoT Edge hub och IoT Edge-moduler för agenten. Beroende på hur du strukturera distributionen av kan körningen uppdateras från enheten eller på distans. 

Du hittar den senaste versionen av Azure IoT Edge [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

>[!IMPORTANT]
>Om du kör Azure IoT Edge på en Windows-enhet, uppdateras inte till version 1.0.5 om något av följande gäller för din enhet: 
>* Du har inte uppgraderat din enhet till Windows build 17763. IoT Edge-version 1.0.5 inte har stöd för Windows-versioner äldre än 17763.
>* Du kan köra Java eller Node.js-moduler på din Windows-enhet. Hoppa över version 1.0.5 även om du har uppdaterat din Windows-enhet till den senaste versionen. 
>
>Läs mer om IoT Edge version 1.0.5 [1.0.5 viktig](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5). Läs mer om hur du förhindrar att uppdatera till den senaste versionen av utvecklingsverktyg, [IoT developer-bloggen](https://devblogs.microsoft.com/iotdev/).


## <a name="update-the-security-daemon"></a>Uppdatera daemonen säkerhet

Daemon för IoT Edge-security är en intern komponent som måste uppdateras via Pakethanteraren på IoT Edge-enheten. 

Kontrollera vilken version av daemonen security som körs på din enhet med hjälp av kommandot `iotedge version`. 

### <a name="linux-devices"></a>Linux-enheter

Använd apt-get eller chefen lämpligt paket för att uppdatera daemonen säkerhet på Linux-enheter. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter kan du använda PowerShell-skriptet för att uppdatera daemonen säkerhet. Skriptet hämtar automatiskt den senaste versionen av daemonen säkerhet. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Kör kommandot Update IoTEdge tar bort daemonen säkerhet från din enhet, tillsammans med två runtime-behållaravbildningar. Config.yaml filen sparas på enheten, samt data från Moby container-motorn (om du använder Windows-behållare). Att hålla den konfigurationen information innebär att du inte behöver ange anslutningssträngen eller Device Provisioning-tjänsten information för enheten igen under uppdateringen. 

Om du vill installera en specifik version av daemonen security kan hämta Microsoft-Azure-IoTEdge.cab filen från [IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases). Använd sedan den `-OfflineInstallationPath` parametern så att den pekar till filens plats. Mer information finns i [offlineinstallation](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar IoT Edge-agenten och IoT Edge hub behållare beror på om du använder löpande taggar (till exempel 1.0) eller särskilda taggar (till exempel 1.0.2) i distributionen. 

Kontrollera versionen av IoT Edge-agenten och IoT Edge hub-moduler för närvarande på din enhet med hjälp av kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub`. 

  ![Hitta versionen för behållare i loggarna](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

IoT Edge-agenten och IoT Edge hub-avbildningar är märkta med IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med runtime-avbildningar: 

* **Löpande taggar** -använder de två första värdena versionsnummer för att hämta den senaste avbildningen som matchar dessa siffror. Till exempel uppdateras 1.0 när det finns en ny version så att den pekar till den senaste versionen av 1.0.x. Om container runtime på din IoT Edge-enhet hämtar avbildningen igen, har runtime-moduler uppdaterats till den senaste versionen. Den här metoden rekommenderas för utveckling. Distributioner från Azure portal standard till rullande taggar. 
* **Särskilda taggar** -använda alla tre värdena för det lägre versionsnumret för att uttryckligen ställa in versionsnumret för avbildningen. Till exempel ändras 1.0.2 inte när den första versionen. Du kan deklarera ett nytt versionsnummer i manifestet distribution när du är redo att uppdatera. Den här metoden rekommenderas för produktion.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en löpande tagg-avbildning

Om du använder löpande taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0**) måste du tvinga behållaren runtime på enheten för att hämta den senaste versionen av avbildningen. 

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. På Windows-datorer, avinstallera daemonen security tar också bort runtime-avbildningar, så du inte behöver vidta åtgärden igen. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda kraften `-f` flagga för att ta bort bilderna. 

IoT Edge-tjänsten använder de senaste versionerna av runtime-avbildningar och startar automatiskt på din enhet dem igen. 

### <a name="update-a-specific-tag-image"></a>Uppdatera en specifik tagg-avbildning

Om du använder specifika taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0.2**) så är allt du behöver göra är att uppdatera taggen i ditt manifest för distributionen och sparar ändringarna till din enhet. 

I Azure-portalen distributionsavbildningar runtime deklareras i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. 

![Konfigurera avancerade edge-körningsinställningar](./media/how-to-update-iot-edge/configure-runtime.png)

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

Håll dig uppdaterad med de senaste uppdateringarna och meddelandet i den [Sakernas Internet-bloggen](https://azure.microsoft.com/blog/topics/internet-of-things/) 