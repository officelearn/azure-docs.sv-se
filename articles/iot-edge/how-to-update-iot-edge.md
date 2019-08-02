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
ms.openlocfilehash: 27f0ebab1fd87eb1870c5a8be21c4f80be4132f1
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698594"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge security daemon och runtime

När IoT Edge service släpper nya versioner vill du uppdatera dina IoT Edge enheter för de senaste funktionerna och säkerhets förbättringarna. Den här artikeln innehåller information om hur du uppdaterar din IoT Edge-enheter när en ny version är tillgänglig. 

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Det första är Security daemon, som körs på enheten och startar körnings moduler när enheten startar. Daemonen säkerhet kan för närvarande kan bara uppdateras från själva enheten. Den andra komponenten är körning, som består av IoT Edge hubb och IoT Edge agent-moduler. Beroende på hur du strukturera distributionen av kan körningen uppdateras från enheten eller på distans. 

Du hittar den senaste versionen av Azure IoT Edge [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Uppdatera daemonen säkerhet

Daemon för IoT Edge-security är en intern komponent som måste uppdateras via Pakethanteraren på IoT Edge-enheten. 

Kontrollera vilken version av daemonen security som körs på din enhet med hjälp av kommandot `iotedge version`. 

### <a name="linux-devices"></a>Linux-enheter

På linux x64-enheter använder du apt-get eller lämplig Package Manager för att uppdatera säkerhets daemonen. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

På Linux ARM32-enheter använder du stegen i [installera Azure IoT Edge runtime på Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) för att installera den senaste versionen av Security daemon. 

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter använder du PowerShell-skriptet för att uppdatera Security daemon. Skriptet hämtar automatiskt den senaste versionen av Security daemon. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Om du kör kommandot Update-IoTEdge tas säkerhets daemonen bort från enheten, tillsammans med de två behållar avbildningarna. Config. yaml-filen sparas på enheten, samt data från Moby container Engine (om du använder Windows-behållare). Att behålla konfigurations informationen innebär att du inte behöver ange anslutnings strängen eller enhets etablerings tjänstens information för enheten igen under uppdaterings processen. 

Om du vill installera en version av Security daemon laddar du ned lämplig Microsoft-Azure-IoTEdge. cab-fil från IoT Edge- [versioner](https://github.com/Azure/azure-iotedge/releases). Använd `-OfflineInstallationPath` sedan parametern för att peka på filens plats. Mer information finns i [Offline-installation](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar IoT Edge agenten och IoT Edge Hub-behållare beror på om du använder rullande Taggar (t. ex. 1,0) eller vissa taggar (t. ex. 1.0.7) i distributionen. 

Kontrol lera versionen av IoT Edge agent och IoT Edge Hub-moduler som finns på enheten med hjälp av `iotedge logs edgeAgent` kommandona eller `iotedge logs edgeHub`. 

  ![Hitta versionen för behållare i loggarna](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

IoT Edge-agenten och IoT Edge Hub-avbildningarna är taggade med den IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med runtime-avbildningar: 

* **Löpande taggar** -använder de två första värdena versionsnummer för att hämta den senaste avbildningen som matchar dessa siffror. Till exempel uppdateras 1.0 när det finns en ny version så att den pekar till den senaste versionen av 1.0.x. Om container runtime på din IoT Edge-enhet hämtar avbildningen igen, har runtime-moduler uppdaterats till den senaste versionen. Den här metoden rekommenderas för utveckling. Distributioner från Azure portal standard till rullande taggar. 
* **Särskilda taggar** -använda alla tre värdena för det lägre versionsnumret för att uttryckligen ställa in versionsnumret för avbildningen. 1\.0.7 ändras till exempel inte efter den första versionen. Du kan deklarera ett nytt versionsnummer i manifestet distribution när du är redo att uppdatera. Den här metoden rekommenderas för produktion.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en löpande tagg-avbildning

Om du använder löpande taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0**) måste du tvinga behållaren runtime på enheten för att hämta den senaste versionen av avbildningen. 

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. När du avinstallerar Security daemon på Windows-datorer tar du även bort körnings avbildningarna, så du behöver inte göra det här steget igen. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda kraften `-f` flagga för att ta bort bilderna. 

IoT Edge-tjänsten använder de senaste versionerna av runtime-avbildningar och startar automatiskt på din enhet dem igen. 

### <a name="update-a-specific-tag-image"></a>Uppdatera en specifik tagg-avbildning

Om du använder vissa taggar i din distribution (t. ex. mcr.microsoft.com/azureiotedge-hub:**1.0.7**) måste du uppdatera taggen i distributions manifestet och tillämpa ändringarna på enheten. 

I Azure-portalen distributionsavbildningar runtime deklareras i den **konfigurera avancerade Edge-körningsinställningar** avsnittet. 

![Konfigurera avancerade inställningar för Edge-körning](./media/how-to-update-iot-edge/configure-runtime.png)

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

## <a name="update-to-a-release-candidate-version"></a>Uppdatera till en version av Release Candidate

Azure IoT Edge regelbundet frigör nya versioner av tjänsten IoT Edge. Innan varje stabil utgåva finns det en eller flera versioner av Release Candidate (RC). RC-versioner innehåller alla planerade funktioner för versionen, men fortsätter att utföra de testnings-och verifierings processer som krävs för en stabil version. Om du vill testa en ny funktion tidigt kan du installera RC-versionen och ge feedback via GitHub. 

Release Candidate-versioner följer samma siffer konvention, men har **-RC** plus ett stegvist nummer som läggs till i slutet. Du kan se versions kandidaterna i samma lista med [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases) som stabila versioner. Du kan till exempel hitta **1.0.7-RC1** och **1.0.7-rc2**, de två versions kandidater som kom före **1.0.7**. Du kan också se att RC-versionerna är markerade med **för hands versions** etiketter. 

Som för hands versioner ingår inte versions kandidat versioner som den senaste versionen som används som mål för vanliga installationer. I stället måste du manuellt rikta in till gångarna för RC-versionen som du vill testa. Använd följande avsnitt för att uppdatera IoT Edge till en angiven version, beroende på IoT Edge enhetens operativ system:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Nästa steg

Visa senast [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med senaste uppdateringar och meddelande i [Sakernas Internet blogg](https://azure.microsoft.com/blog/topics/internet-of-things/) 