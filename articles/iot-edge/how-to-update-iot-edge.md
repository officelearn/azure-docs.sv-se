---
title: IoT Edge Uppdateringsversion på enheter – Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge-enheter för att köra de senaste versionerna av security-daemon och IoT Edge-körningen
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186514"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge security daemon och runtime

När IoT Edge service släpper nya versioner vill du uppdatera dina IoT Edge enheter för de senaste funktionerna och säkerhets förbättringarna. Den här artikeln innehåller information om hur du uppdaterar din IoT Edge-enheter när en ny version är tillgänglig.

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Det första är Security daemon, som körs på enheten och startar körnings moduler när enheten startar. Daemonen säkerhet kan för närvarande kan bara uppdateras från själva enheten. Den andra komponenten är körning, som består av IoT Edge hubb och IoT Edge agent-moduler. Beroende på hur du strukturera distributionen av kan körningen uppdateras från enheten eller på distans.

För att hitta den senaste versionen av Azure IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Uppdatera daemonen säkerhet

Daemon för IoT Edge-security är en intern komponent som måste uppdateras via Pakethanteraren på IoT Edge-enheten.

Kontrol lera vilken version av säkerhets-daemon som körs på enheten med hjälp av kommandot `iotedge version`.

### <a name="linux-devices"></a>Linux-enheter

På linux x64-enheter använder du apt-get eller lämplig Package Manager för att uppdatera säkerhets daemonen till den senaste versionen.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Om du vill uppdatera till en speciell version av Security daemon, letar du upp den version som du vill använda för [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen letar du reda på lämpliga **libiothsm-STD-** och **iotedge** -filer för enheten. För varje fil högerklickar du på fil länken och kopierar länk adressen. Använd länk adressen för att installera de olika versionerna av dessa komponenter:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter använder du PowerShell-skriptet för att uppdatera Security daemon. Skriptet hämtar automatiskt den senaste versionen av Security daemon.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Om du kör kommandot Update-IoTEdge tas och uppdateras säkerhets daemonen från enheten, tillsammans med de två behållar avbildningarna. Config. yaml-filen sparas på enheten, samt data från Moby container Engine (om du använder Windows-behållare). Att behålla konfigurations informationen innebär att du inte behöver ange anslutnings strängen eller enhets etablerings tjänstens information för enheten igen under uppdaterings processen.

Om du vill uppdatera till en speciell version av Security daemon, letar du upp den version som du vill använda för [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen laddar du ned filen **Microsoft-Azure-IoTEdge. cab** . Använd sedan `-OfflineInstallationPath`-parametern för att peka på den lokala fil platsen. Exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Parametern `-OfflineInstallationPath` söker efter en fil med namnet **Microsoft-Azure-IoTEdge. cab** i den angivna katalogen. Från och med IoT Edge version 1.0.9-RC4 finns det två CAB-filer som är tillgängliga för användning, en för AMD64-enheter och en för ARM32. Hämta rätt fil för enheten och byt sedan namn på filen för att ta bort det.

Om du vill ha mer information om uppdaterings alternativ använder du kommandot `Get-Help Update-IoTEdge -full` eller läser [alla installations parametrar](how-to-install-iot-edge-windows.md#all-installation-parameters).

## <a name="update-the-runtime-containers"></a>Uppdatera körningsbehållarna

Hur du uppdaterar IoT Edge agenten och IoT Edge Hub-behållare beror på om du använder rullande Taggar (t. ex. 1,0) eller vissa taggar (t. ex. 1.0.7) i distributionen.

Kontrol lera versionen av IoT Edge agent och IoT Edge Hub-moduler som finns på enheten med kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub`.

  ![Hitta versionen för behållare i loggarna](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge-taggar

IoT Edge-agenten och IoT Edge Hub-avbildningarna är taggade med den IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med runtime-avbildningar:

* **Rullande Taggar** – Använd bara de första två värdena i versions numret för att hämta den senaste bilden som matchar de siffrorna. Till exempel uppdateras 1.0 när det finns en ny version så att den pekar till den senaste versionen av 1.0.x. Om container runtime på din IoT Edge-enhet hämtar avbildningen igen, har runtime-moduler uppdaterats till den senaste versionen. Den här metoden rekommenderas för utveckling. Distributioner från Azure portal standard till rullande taggar.

* **Vissa Taggar** -Använd alla tre värdena i versions numret för att uttryckligen ange avbildnings versionen. 1\.0.7 ändras till exempel inte efter den första versionen. Du kan deklarera ett nytt versionsnummer i manifestet distribution när du är redo att uppdatera. Den här metoden rekommenderas för produktion.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en löpande tagg-avbildning

Om du använder rullande Taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1,0**) måste du tvinga behållar körning på enheten att hämta den senaste versionen av avbildningen.

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. När du avinstallerar Security daemon på Windows-datorer tar du även bort körnings avbildningarna, så du behöver inte göra det här steget igen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda flaggan Force `-f` för att ta bort avbildningarna.

IoT Edge-tjänsten använder de senaste versionerna av runtime-avbildningar och startar automatiskt på din enhet dem igen.

### <a name="update-a-specific-tag-image"></a>Uppdatera en specifik tagg-avbildning

Om du använder vissa taggar i din distribution (t. ex. mcr.microsoft.com/azureiotedge-hub:**1.0.8**) måste du uppdatera taggen i distributions manifestet och tillämpa ändringarna på enheten.

1. I IoT Hub i Azure Portal väljer du din IoT Edge enhet och väljer **Ange moduler**.

1. I avsnittet **IoT Edge moduler** väljer du **körnings inställningar**.

   ![Konfigurera körnings inställningar](./media/how-to-update-iot-edge/configure-runtime.png)

1. Uppdatera **avbildning** svärdet för **Edge Hub** med önskad version i **körnings inställningarna**. Välj inte **Spara** bara än.

   ![Uppdatera bild version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Minimera inställningarna för **Edge Hub** eller rulla nedåt och uppdatera **avbildning** svärdet för **Edge-agenten** med samma önskade version.

   ![Uppdatera agent version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Välj **Spara**.

1. Välj **Granska + skapa**, granska distributionen och välj **skapa**.

## <a name="update-to-a-release-candidate-version"></a>Uppdatera till en version av Release Candidate

Azure IoT Edge regelbundet frigör nya versioner av tjänsten IoT Edge. Innan varje stabil utgåva finns det en eller flera versioner av Release Candidate (RC). RC-versioner innehåller alla planerade funktioner för versionen, men fortsätter att testa och verifiera. Om du vill testa en ny funktion tidigt kan du installera en RC-version och ge feedback via GitHub.

Release Candidate-versioner följer samma siffer konvention, men har **-RC** plus ett stegvist nummer som läggs till i slutet. Du kan se versions kandidaterna i samma lista med [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases) som stabila versioner. Du kan till exempel hitta **1.0.7-RC1** och **1.0.7-rc2**, de två versions kandidater som kom före **1.0.7**. Du kan också se att RC-versionerna är markerade med **för hands versions** etiketter.

IoT Edge agent-och hubb-moduler har RC-versioner som är taggade med samma konvention. Till exempel **MCR.Microsoft.com/azureiotedge-Hub:1.0.7-rc2**.

Som för hands versioner ingår inte versions kandidat versioner som den senaste versionen som används som mål för vanliga installationer. I stället måste du manuellt rikta in till gångarna för RC-versionen som du vill testa. För det mesta är att installera eller uppdatera till en RC-version som riktar sig mot en annan speciell version av IoT Edge, förutom en skillnad för Windows-enheter. 

I en versions kandidat kan PowerShell-skriptet som låter dig installera och hantera IoT Edge Security daemon på en Windows-enhet ha andra funktioner än den senaste allmänt tillgängliga versionen. Förutom att ladda ned IoT Edge. cab-filen för RC kan du också hämta skriptet **IotEdgeSecurityDaemon. ps1** . Använd [punkt källa](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) för att köra det nedladdade skriptet i den aktuella källan. Exempel: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Använd avsnitten i den här artikeln för att lära dig hur du uppdaterar en IoT Edge-enhet till en viss version av daemon-eller körnings moduler.

Om du installerar IoT Edge på en ny dator kan du använda följande länkar för att lära dig hur du installerar en speciell version beroende på enhetens operativ system:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Nästa steg

Visa de senaste [Azure IoT Edge versionerna](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med senaste uppdateringar och meddelande i [Sakernas Internet blogg](https://azure.microsoft.com/blog/topics/internet-of-things/)
