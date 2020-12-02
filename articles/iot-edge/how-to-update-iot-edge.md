---
title: Uppdatera IoT Edge version på enheter – Azure IoT Edge | Microsoft Docs
description: Så här uppdaterar du IoT Edge enheter för att köra de senaste versionerna av Security daemon och IoT Edge runtime
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 797b5f569f081065eb950f7c10bf6449002f733b
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436988"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Uppdatera IoT Edge-säkerhetsdaemon och runtime

När IoT Edge service släpper nya versioner vill du uppdatera dina IoT Edge enheter för de senaste funktionerna och säkerhets förbättringarna. Den här artikeln innehåller information om hur du uppdaterar IoT Edge enheter när en ny version är tillgänglig.

Två komponenter i en IoT Edge-enhet måste uppdateras om du vill flytta till en nyare version. Det första är Security daemon, som körs på enheten och startar körnings moduler när enheten startar. Security daemon kan för närvarande bara uppdateras från själva enheten. Den andra komponenten är körning, som består av IoT Edge hubb och IoT Edge agent-moduler. Beroende på hur du strukturerar distributionen kan körningen uppdateras från enheten eller via fjärr anslutning.

För att hitta den senaste versionen av Azure IoT Edge, se [Azure IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Uppdatera Security daemon

IoT Edge Security daemon är en inbyggd komponent som behöver uppdateras med hjälp av paket hanteraren på den IoT Edge enheten.

Kontrol lera vilken version av säkerhets-daemon som körs på enheten med hjälp av kommandot `iotedge version` .

### <a name="linux-devices"></a>Linux-enheter

På linux x64-enheter använder du apt-get eller lämplig Package Manager för att uppdatera säkerhets daemonen till den senaste versionen.

Hämta den senaste databas konfigurationen från Microsoft:

* **Ubuntu Server 16,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS-storlek**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopiera den genererade listan.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installera Microsoft GPG offentlig nyckel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Uppdatera apt.

   ```bash
   sudo apt-get update
   ```

Kontrol lera vilka versioner av IoT Edge som är tillgängliga.

   ```bash
   apt list -a iotedge
   ```

Om du vill uppdatera till den senaste versionen av Security daemon använder du följande kommando som också uppdaterar **libiothsm-STD** till den senaste versionen:

   ```bash
   sudo apt-get install iotedge
   ```

Om du vill uppdatera till en speciell version av Security daemon, anger du versionen från apt lista utdata. När **iotedge** uppdateras försöker den automatiskt uppdatera **libiothsm-STD-** paketet till den senaste versionen, vilket kan orsaka en beroende konflikt. Om du inte kommer till den senaste versionen, måste du ange båda paketen för samma version. Följande kommando installerar till exempel en speciell version av 1.0.9-versionen:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Om den version som du vill installera inte är tillgänglig via apt-get kan du använda spiral för att rikta in dig på alla versioner från lagrings platsen för [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) . För den version som du vill installera letar du reda på lämpliga **libiothsm-STD-** och **iotedge** -filer för enheten. För varje fil högerklickar du på fil länken och kopierar länk adressen. Använd länk adressen för att installera de olika versionerna av dessa komponenter:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Windows-enheter

På Windows-enheter använder du PowerShell-skriptet för att uppdatera Security daemon. Skriptet hämtar automatiskt den senaste versionen av Security daemon.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Att köra kommandot Update-IoTEdge tar bort och uppdaterar säkerhets daemonen från enheten, tillsammans med de två behållar avbildningarna. Config. yaml-filen sparas på enheten, samt data från Moby container Engine (om du använder Windows-behållare). Att behålla konfigurations informationen innebär att du inte behöver ange anslutnings strängen eller enhets etablerings tjänstens information för enheten igen under uppdaterings processen.

Om du vill uppdatera till en speciell version av Security daemon, letar du upp den version som du vill använda för [IoT Edge-versioner](https://github.com/Azure/azure-iotedge/releases). I den versionen hämtar du **Microsoft-Azure-IoTEdge.cab** -filen. Använd sedan `-OfflineInstallationPath` parametern för att peka på den lokala fil platsen. Exempel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath`Parametern söker efter en fil med namnet **Microsoft-Azure-IoTEdge.cab** i den angivna katalogen. Från och med IoT Edge version 1.0.9-RC4 finns det två CAB-filer som är tillgängliga för användning, en för AMD64-enheter och en för ARM32. Hämta rätt fil för enheten och byt sedan namn på filen för att ta bort det.

Om du vill ha mer information om uppdaterings alternativ använder du kommandot `Get-Help Update-IoTEdge -full` eller läser [PowerShell-skript för IoT Edge i Windows](reference-windows-scripts.md).

## <a name="update-the-runtime-containers"></a>Uppdatera runtime-behållare

Hur du uppdaterar IoT Edge agenten och IoT Edge Hub-behållare beror på om du använder rullande Taggar (t. ex. 1,0) eller vissa taggar (t. ex. 1.0.7) i distributionen.

Kontrol lera versionen av IoT Edge agent och IoT Edge Hub-moduler som finns på enheten med hjälp av kommandona `iotedge logs edgeAgent` eller `iotedge logs edgeHub` .

  ![Hitta behållar version i loggar](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Förstå IoT Edge Taggar

IoT Edge-agenten och IoT Edge Hub-avbildningarna är taggade med den IoT Edge-version som de är associerade med. Det finns två olika sätt att använda taggar med körnings avbildningarna:

* **Rullande Taggar** – Använd bara de första två värdena i versions numret för att hämta den senaste bilden som matchar de siffrorna. 1,0 uppdateras till exempel när det finns en ny version som pekar på den senaste 1.0. x-versionen. Om behållar körningen på din IoT Edge enhet hämtar avbildningen igen, uppdateras runtime-modulerna till den senaste versionen. Den här metoden föreslås i utvecklings syfte. Distributioner från Azure Portal standardvärde till rullande taggar.

* **Vissa Taggar** -Använd alla tre värdena i versions numret för att uttryckligen ange avbildnings versionen. 1.0.7 ändras till exempel inte efter den första versionen. Du kan deklarera ett nytt versions nummer i distributions manifestet när du är redo att uppdatera. Den här metoden rekommenderas för produktions syfte.

### <a name="update-a-rolling-tag-image"></a>Uppdatera en rullande tag-bild

Om du använder rullande Taggar i distributionen (till exempel mcr.microsoft.com/azureiotedge-hub:**1,0**) måste du tvinga behållar körning på enheten att hämta den senaste versionen av avbildningen.

Ta bort den lokala versionen av avbildningen från din IoT Edge-enhet. När du avinstallerar Security daemon på Windows-datorer tar du även bort körnings avbildningarna, så du behöver inte göra det här steget igen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Du kan behöva använda Force- `-f` flaggan för att ta bort avbildningarna.

IoT Edge tjänsten hämtar de senaste versionerna av körnings avbildningarna och startar dem automatiskt på enheten igen.

### <a name="update-a-specific-tag-image"></a>Uppdatera en speciell tag-bild

Om du använder vissa taggar i din distribution (t. ex. mcr.microsoft.com/azureiotedge-hub:**1.0.8**) måste du uppdatera taggen i distributions manifestet och tillämpa ändringarna på enheten.

1. I IoT Hub i Azure Portal väljer du din IoT Edge enhet och väljer **Ange moduler**.

1. I avsnittet **IoT Edge moduler** väljer du **körnings inställningar**.

   ![Konfigurera körnings inställningar](./media/how-to-update-iot-edge/configure-runtime.png)

1. Uppdatera **avbildning** svärdet för **Edge Hub** med önskad version i **körnings inställningarna**. Välj inte **Spara** ännu.

   ![Uppdatera bild version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Minimera inställningarna för **Edge Hub** eller rulla nedåt och uppdatera **avbildning** svärdet för **Edge-agenten** med samma önskade version.

   ![Uppdatera agent version för Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Välj **Spara**.

1. Välj **Granska + skapa**, granska distributionen och välj **skapa**.

## <a name="update-offline-or-to-a-specific-version"></a>Uppdatera offline eller till en annan version

Om du vill uppdatera en enhet offline eller uppdatera till en speciell version av IoT Edge i stället för den senaste versionen kan du göra det med `-OfflineInstallationPath` parametern.

Två komponenter används för att uppdatera en IoT Edge enhet:

* Ett PowerShell-skript som innehåller installations anvisningarna
* Microsoft Azure IoT Edge CAB, som innehåller IoT Edge Security daemon (iotedged), Moby container Engine och Moby CLI

1. De senaste IoT Edge-installationsfilerna tillsammans med tidigare versioner finns i [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases).

2. Leta upp den version som du vill installera och ladda ned följande filer från avsnittet **till gångar** i viktig information på din IoT-enhet:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab från att lansera 1.0.9 eller senare, eller Microsoft-Azure-IoTEdge.cab från att lansera 1.0.8 och äldre.

   Microsoft-Azure-IotEdge-arm32.cab är även tillgängligt från och med 1.0.9 endast för testning. IoT Edge stöds för närvarande inte på Windows ARM32-enheter.

   Det är viktigt att använda PowerShell-skriptet från samma version som. cab-filen som du använder eftersom funktionen ändras till att stödja funktionerna i varje version.

3. Om CAB-filen som du laddade ned har ett arkitektur-suffix, byter du namn på filen till bara **Microsoft-Azure-IoTEdge.cab**.

4. Om du vill uppdatera med offline-komponenter kan du [punkt källa](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) den lokala kopian av PowerShell-skriptet. Använd sedan `-OfflineInstallationPath` parametern som en del av `Update-IoTEdge` kommandot och ange den absoluta sökvägen till fil katalogen. Exempel:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Uppdatera till en version av Release Candidate

Azure IoT Edge regelbundet frigör nya versioner av tjänsten IoT Edge. Innan varje stabil utgåva finns det en eller flera versioner av Release Candidate (RC). RC-versioner innehåller alla planerade funktioner för versionen, men fortsätter att testa och verifiera. Om du vill testa en ny funktion tidigt kan du installera en RC-version och ge feedback via GitHub.

Release Candidate-versioner följer samma siffer konvention, men har **-RC** plus ett stegvist nummer som läggs till i slutet. Du kan se versions kandidaterna i samma lista med [Azure IoT Edge versioner](https://github.com/Azure/azure-iotedge/releases) som stabila versioner. Du kan till exempel hitta **1.0.9-RC5** och **1.0.9-RC6**, två av de versions kandidater som kom före **1.0.9**. Du kan också se att RC-versionerna är markerade med **för hands versions** etiketter.

IoT Edge agent-och hubb-moduler har RC-versioner som är taggade med samma konvention. Till exempel **MCR.Microsoft.com/azureiotedge-Hub:1.0.9-RC6**.

Som för hands versioner ingår inte versions kandidat versioner som den senaste versionen som används som mål för vanliga installationer. I stället måste du manuellt rikta in till gångarna för RC-versionen som du vill testa. För det mesta är att installera eller uppdatera till en RC-version som riktar sig mot en annan speciell version av IoT Edge.

Använd avsnitten i den här artikeln för att lära dig hur du uppdaterar en IoT Edge-enhet till en viss version av daemon-eller körnings moduler.

Om du installerar IoT Edge, i stället för att uppgradera en befintlig installation, använder du stegen i [offline eller en speciell versions installation](how-to-install-iot-edge.md#offline-or-specific-version-installation).

## <a name="next-steps"></a>Nästa steg

Visa de senaste [Azure IoT Edge versionerna](https://github.com/Azure/azure-iotedge/releases).

Håll dig uppdaterad med de senaste uppdateringarna och meddelandena i [Sakernas Internet blogg](https://azure.microsoft.com/blog/topics/internet-of-things/)