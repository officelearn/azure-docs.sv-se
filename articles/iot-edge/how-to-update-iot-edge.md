---
title: IoT Edge Uppdateringsversion på enheter – Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge-enheter för att köra de senaste versionerna av security-daemon och IoT Edge-körningen
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0c461da44d3d9075d66a68fe8994a4e970288fca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543753"
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

Använd apt-get eller chefen lämpligt paket för att uppdatera daemonen säkerhet på Linux x64 enheter. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

På Linux ARM32 enheter kan använda stegen i [installera Azure IoT Edge-körningen på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) att installera den senaste versionen av daemonen säkerhet. 

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter kan du använda PowerShell-skriptet för att uppdatera daemonen säkerhet. Skriptet hämtar automatiskt den senaste versionen av daemonen säkerhet. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Kör kommandot Update IoTEdge tar bort daemonen säkerhet från din enhet, tillsammans med två runtime-behållaravbildningar. Config.yaml filen sparas på enheten, samt data från Moby container-motorn (om du använder Windows-behållare). Att hålla den konfigurationen information innebär att du inte behöver ange anslutningssträngen eller Device Provisioning-tjänsten information för enheten igen under uppdateringen. 

Om du vill installera en specifik version av daemonen security kan hämta Microsoft-Azure-IoTEdge.cab filen från [IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases). Använd sedan den `-OfflineInstallationPath` parametern så att den pekar till filens plats. Mer information finns i [offlineinstallation](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar IoT Edge-agenten och IoT Edge hub behållare beror på om du använder löpande taggar (till exempel 1.0) eller särskilda taggar (till exempel 1.0.7) i distributionen. 

Kontrollera versionen av IoT Edge-agenten och IoT Edge hub-moduler för närvarande på din enhet med hjälp av kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub`. 

  ![Hitta versionen för behållare i loggarna](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

IoT Edge-agenten och IoT Edge hub-avbildningar är märkta med IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med runtime-avbildningar: 

* **Löpande taggar** -använder de två första värdena versionsnummer för att hämta den senaste avbildningen som matchar dessa siffror. Till exempel uppdateras 1.0 när det finns en ny version så att den pekar till den senaste versionen av 1.0.x. Om container runtime på din IoT Edge-enhet hämtar avbildningen igen, har runtime-moduler uppdaterats till den senaste versionen. Den här metoden rekommenderas för utveckling. Distributioner från Azure portal standard till rullande taggar. 
* **Särskilda taggar** -använda alla tre värdena för det lägre versionsnumret för att uttryckligen ställa in versionsnumret för avbildningen. Exempel: 1.0.7 ändras inte när den första versionen. Du kan deklarera ett nytt versionsnummer i manifestet distribution när du är redo att uppdatera. Den här metoden rekommenderas för produktion.

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

Om du använder specifika taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0.7**) så är allt du behöver göra är att uppdatera taggen i ditt manifest för distributionen och sparar ändringarna till din enhet. 

I Azure-portalen distributionsavbildningar runtime deklareras i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. 

![Konfigurera avancerade edge-körningsinställningar](./media/how-to-update-iot-edge/configure-runtime.png)

I manifestet för en JSON-distribution, uppdatera modulen avbildningar i den **systemModules** avsnittet. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Uppdatera till en release candidate-version

Azure IoT Edge släpper regelbundet nya versioner av IoT Edge-tjänsten. Innan varje stabil version finns en eller flera release candidate (RC) versioner. RC versioner innehåller alla planerade funktioner för versionen, men är fortfarande går igenom testning och validering processer som krävs för en stabil version. Om du vill testa en ny funktion tidigt, kan du installera RC-versionen och ge feedback via GitHub. 

Förhandsversionerna följer samma regler för numrering av versioner, men har **-rc** plus ett inkrementellt nummer läggas till i slutet. Du kan se versionen kandidater i samma lista över [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases) som stabil versioner. Till exempel hitta **1.0.7-rc1** och **1.0.7-rc2**, två viktig kandidater som kommer före **1.0.7**. Du kan också se att RC versioner är markerade med **förhandsversioner** etiketter. 

Som förhandsversionerna av förhandsversionerna inkluderas inte som den senaste versionen som installationsprogram för vanliga mål. I stället måste du manuellt rikta in tillgångar för RC-versionen som du vill testa. Beroende på operativsystemet IoT Edge-enhet att använda följande avsnitt för att uppdatera IoT Edge till en viss version:

* [Linux X64](how-to-install-iot-edge-linux.md#install-a-specific-version)
* [Linux ARM32](how-to-install-iot-edge-linux-arm.md#install-a-specific-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Nästa steg

Visa senast [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med de senaste uppdateringarna och meddelandet i den [Sakernas Internet-bloggen](https://azure.microsoft.com/blog/topics/internet-of-things/) 