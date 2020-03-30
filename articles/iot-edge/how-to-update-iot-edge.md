---
title: Uppdatera IoT Edge-versionen på enheter – Azure IoT Edge | Microsoft-dokument
description: Så här uppdaterar du IoT Edge-enheter för att köra de senaste versionerna av säkerhetsdemonen och IoT Edge-körningen
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186514"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge-säkerhetsdaemon och runtime

När IoT Edge-tjänsten släpper nya versioner bör du uppdatera dina IoT Edge-enheter för de senaste funktionerna och säkerhetsförbättringarna. Den här artikeln innehåller information om hur du uppdaterar dina IoT Edge-enheter när en ny version är tillgänglig.

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Den första är säkerhetsdemonen, som körs på enheten och startar runtime-modulerna när enheten startar. För närvarande kan säkerhetsdemonen bara uppdateras från själva enheten. Den andra komponenten är körningen, som består av IoT Edge-hubben och IoT Edge-agentmodulerna. Beroende på hur du strukturerar distributionen kan körningen uppdateras från enheten eller på distans.

Information om hur du hittar den senaste versionen av Azure IoT Edge finns i [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Uppdatera säkerhetsdemonen

Säkerhetsdemonen IoT Edge är en inbyggd komponent som måste uppdateras med pakethanteraren på IoT Edge-enheten.

Kontrollera vilken version av säkerhetsdemonen som körs `iotedge version`på enheten med kommandot .

### <a name="linux-devices"></a>Linux-enheter

På Linux x64-enheter använder du apt-get eller din lämpliga pakethanterare för att uppdatera säkerhetsdemonen till den senaste versionen.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Om du vill uppdatera till en viss version av säkerhetsdemonen hittar du den version du vill rikta från [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen letar du reda på lämpliga **libiothsm-std-** och **iotedge-filer** för din enhet. För varje fil högerklickar du på fillänken och kopierar länkadressen. Använd länkadressen för att installera specifika versioner av dessa komponenter:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter använder du PowerShell-skriptet för att uppdatera säkerhetsdemonen. Skriptet drar automatiskt den senaste versionen av säkerhetsdemonen.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Om du kör kommandot Update-IoTEdge tas säkerhetsdemon bort och uppdateras säkerhetsdemonen från enheten tillsammans med de två körningsbehållareavbildningarna. Filen config.yaml lagras på enheten, samt data från Moby-behållarmotorn (om du använder Windows-behållare). Om du behåller konfigurationsinformationen behöver du inte ange information om anslutningssträngen eller enhetsetableringstjänsten för enheten igen under uppdateringsprocessen.

Om du vill uppdatera till en viss version av säkerhetsdemonen hittar du den version du vill rikta från [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen laddar du ned **filen Microsoft-Azure-IoTEdge.cab.** Använd sedan `-OfflineInstallationPath` parametern för att peka på den lokala filplatsen. Ett exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Parametern `-OfflineInstallationPath` söker efter en fil med namnet **Microsoft-Azure-IoTEdge.cab** i katalogen som tillhandahålls. Från och med IoT Edge version 1.0.9-rc4 finns det två .cab-filer tillgängliga att använda, en för AMD64-enheter och en för ARM32. Hämta rätt fil för enheten och byt sedan namn på filen för att ta bort arkitektursuffixet.

Om du vill ha mer `Get-Help Update-IoTEdge -full` information om uppdateringsalternativ använder du kommandot eller hänvisar till [alla installationsparametrar](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar IoT Edge-agenten och IoT Edge-hubbbehållarna beror på om du använder rullande taggar (som 1.0) eller specifika taggar (som 1.0.7) i distributionen.

Kontrollera versionen av IoT Edge-agenten och IoT Edge-hubbmodulerna som för närvarande finns på enheten med hjälp av kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub`.

  ![Hitta behållarversion i loggar](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

IoT Edge-agenten och IoT Edge-hubbavbildningarna är taggade med IoT Edge-versionen som de är associerade med. Det finns två olika sätt att använda taggar med körningsavbildningar:

* **Rullande taggar** - Använd bara de två första värdena i versionsnumret för att få den senaste bilden som matchar dessa siffror. Till exempel uppdateras 1.0 när det finns en ny version som pekar på den senaste 1.0.x-versionen. Om behållarkörningen på IoT Edge-enheten hämtar avbildningen igen uppdateras körningsmodulerna till den senaste versionen. Detta tillvägagångssätt föreslås för utvecklingsändamål. Distributioner från Azure-portalen som standard till rullande taggar.

* **Specifika taggar** - Använd alla tre värdena i versionsnumret för att uttryckligen ange bildversionen. Till exempel ändras inte 1.0.7 efter den första versionen. Du kan deklarera ett nytt versionsnummer i distributionsmanifestet när du är redo att uppdatera. Detta tillvägagångssätt föreslås för produktionsändamål.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en rullande taggbild

Om du använder rullande taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0)** måste du tvinga behållaren att köras på enheten för att hämta den senaste versionen av avbildningen.

Ta bort den lokala versionen av avbildningen från IoT Edge-enheten. På Windows-datorer tar avinstallationen av säkerhetsdemonen också bort körningsavbildningarna, så du behöver inte ta det här steget igen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda `-f` kraftflaggan för att ta bort bilderna.

IoT Edge-tjänsten hämtar de senaste versionerna av körningsavbildningarna och startar dem automatiskt på enheten igen.

### <a name="update-a-specific-tag-image"></a>Uppdatera en viss taggbild

Om du använder specifika taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1.0.8)** behöver du bara uppdatera taggen i distributionsmanifestet och tillämpa ändringarna på enheten.

1. I IoT-hubben i Azure-portalen väljer du din IoT Edge-enhet och väljer **Ange moduler**.

1. I avsnittet **IoT-kantmoduler** väljer du **Körtidsinställningar**.

   ![Konfigurera körningsinställningar](./media/how-to-update-iot-edge/configure-runtime.png)

1. Uppdatera **bildvärdet** för **Edge Hub** i **Runtime-inställningar**med önskad version. Välj inte **Spara** ännu.

   ![Version av uppdaterings edge hub-bild](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Komprimera **Edge** Hub-inställningarna eller rulla nedåt och uppdatera **bildvärdet** för **Edge Agent** med samma önskade version.

   ![Version av Update Edge Hub Agent](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Välj **Spara**.

1. Välj **Granska + skapa,** granska distributionen och välj **Skapa**.

## <a name="update-to-a-release-candidate-version"></a>Uppdatera till en version av utgivningskandidaten

Azure IoT Edge släpper regelbundet nya versioner av IoT Edge-tjänsten. Före varje stabil utgåva finns det en eller flera release candidate (RC) versioner. RC-versioner innehåller alla planerade funktioner för versionen, men går fortfarande igenom testning och validering. Om du vill testa en ny funktion tidigt kan du installera en RC-version och ge feedback via GitHub.

Släpp kandidatversioner följer samma numreringskonvention av utgåvor, men har **-rc** plus ett inkrementellt tal som läggs till i slutet. Du kan se utgivningskandidaterna i samma lista över [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases) som stabila versioner. Till exempel, hitta **1.0.7-rc1** och **1.0.7-rc2**, de två release kandidater som kom före **1.0.7**. Du kan också se att **RC-versioner** är markerade med förutgivna etiketter.

IoT Edge-agenten och hubbmodulerna har RC-versioner som är taggade med samma konvention. Till exempel **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2**.

Som förhandsgranskningar ingår inte utgivningskandidatersversioner som den senaste versionen som de vanliga installationsprogrammet riktar in sig på. I stället måste du manuellt rikta in dig på tillgångarna för rc-versionen som du vill testa. För det mesta är installation eller uppdatering till en RC-version detsamma som att inrikta sig på någon annan specifik version av IoT Edge förutom en skillnad för Windows-enheter. 

I en versionskandidat kan PowerShell-skriptet som låter dig installera och hantera IoT Edge-säkerhetsdemonen på en Windows-enhet ha andra funktioner än den senaste allmänt tillgängliga versionen. Förutom att ladda ner IoT Edge .cab-filen för RC, också ladda ner **IotEdgeSecurityDaemon.ps1** skriptet. Använd [punktkällor](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) för att köra det nedladdade skriptet i den aktuella källan. Ett exempel: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Använd avsnitten i den här artikeln om du vill lära dig hur du uppdaterar en IoT Edge-enhet till en viss version av säkerhetsdemon- eller runtime-modulerna.

Om du installerar IoT Edge på en ny dator använder du följande länkar för att lära dig hur du installerar en viss version beroende på enhetens operativsystem:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Nästa steg

Visa de senaste [Azure IoT Edge-versionerna](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med de senaste uppdateringarna och tillkännagivandet i [Internet of Things blogg](https://azure.microsoft.com/blog/topics/internet-of-things/)
