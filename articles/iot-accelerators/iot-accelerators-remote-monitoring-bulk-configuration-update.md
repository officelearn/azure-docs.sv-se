---
title: Hantera enheter som är anslutna till fjärrövervakning gruppvis – Azure | Microsoft Docs
description: I den här självstudien lär du dig att hantera de enheter som är anslutna till en fjärrövervakningslösning i grupp.
author: aditidugar
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: adugar
ms.openlocfilehash: 8a5c74c76662a089675fcbdcd8d5a7ea54b58fd1
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009675"
---
# <a name="tutorial-manage-your-connected-devices-in-bulk"></a>Självstudier: Hantera dina anslutna enheter i grupp

I den här självstudien använder du lösningsacceleratorn för fjärrövervakning för att hantera konfigurationen av dina anslutna enheter i grupp.

Som operatör på Contoso behöver du konfigurera en grupp med enheter med en ny version av inbyggd programvara. Du vill inte bli tvungen att uppdatera den inbyggda programvaran enskilt på varje enhet. Om du vill uppdatera den inbyggda programvaran på en grupp med enheter kan du använda enhetsgrupper och automatisk enhetshantering i lösningsacceleratorn för fjärrövervakning. Alla enheter som du lägger till i enhetsgruppen får den senaste inbyggda programvaran så snart enheten är online.

I den här kursen för du göra följande:

>[!div class="checklist"]
> * Skapa en enhetsgrupp.
> * Förbereda och hantera den inbyggda programvaran.
> * Skapa en enhetskonfiguration på Azure-portalen.
> * Importera en enhetskonfiguration till fjärrövervakningslösningen.
> * Distribuera konfigurationen till enheterna i enhetsgruppen.
> * Övervaka distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

<!--
If this is going to be a tutorial - we need to split this include into two so that we can accommodate the additional prerequisites:

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]
-->

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill följa den här självstudien behöver du en distribuerad instans av lösningsacceleratorn Fjärrövervakning i Azure-prenumerationen.

Om du inte har distribuerat lösningsacceleratorn Fjärrövervakning ännu bör du genomföra snabbstarten [Distribuera en molnbaserad fjärrövervakningslösning](quickstart-remote-monitoring-deploy.md).

Du behöver ett Azure-lagringskonto för att hantera filer för den inbyggda programvaran. Du kan använda ett befintligt lagringskonto eller [skapa ett nytt lagringskonto](../storage/common/storage-quickstart-create-account.md) i din prenumeration.

I självstudien används en [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)-enhet som exempelenhet.

Du behöver följande programvara installerad på den lokala datorn:

* [Visual Studio Code (VS Code)](https://code.visualstudio.com/).
* [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench)-tillägget för VS Code.

Innan du börjar:

* Kontrollera att [startprogrammet på IoT DevKit-enheten är version 1.4.0 eller senare](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Kontrollera att IoT DevKit SDK har samma version som startprogrammet. Du kan uppdatera IoT DevKit SDK med hjälp av Azure IoT Workbench i VS Code. Öppna kommandopaletten och ange **Arduino: Tavla Manager**. Mer information finns i avsnittet om att [förbereda utvecklingsmiljön](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).

Du behöver även ansluta minst en IoT DevKit-enhet till lösningsacceleratorn för fjärrövervakning. Om du inte har anslutit en IoT DevKit-enhet läser du avsnittet om att [ansluta MXChip IoT DevKit AZ3166 till lösningsacceleratorn för IoT-fjärrövervakning](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2.md).

## <a name="navigate-to-the-dashboard"></a>Gå till instrumentpanelen

Om du vill visa fjärrövervakningslösningens instrumentpanel i webbläsaren går du först till [Acceleratorer för Microsoft Azure IoT-lösning](https://www.azureiotsolutions.com/Accelerators#dashboard). Du kan bli ombedd att logga in med dina autentiseringsuppgifter för Azure-prenumeration.

Klicka sedan på **Starta** på panelen för den lösningsaccelerator för fjärrövervakning som du distribuerade i [Snabbstarten](quickstart-remote-monitoring-deploy.md).

## <a name="create-a-device-group"></a>Skapa en enhetsgrupp.

För att den inbyggda programvaran ska uppdateras automatiskt på en grupp med enheter måste enheterna vara medlemmar i en enhetsgrupp i fjärrövervakningslösningen:

1. På sidan **Enheter** markerar du alla de **IoT DevKit**-enheter som du har anslutit till lösningsacceleratorn. Klicka sedan på **Jobb**.

1. I panelen **Jobb** väljer du **Taggar**, anger jobbnamnet till **AddDevKitTag** och lägger sedan till en texttagg med namnet **IsDevKitDevice** och värdet **Y**. Klicka sedan på **Använd**.

1. Nu kan du använda taggvärdena till att skapa en enhetsgrupp. På sidan **Enheter** klickar du på **Hantera enhetsgrupper**.

1. Skapa ett textfilter som använder taggnamnet **IsDevKitDevice** och värdet **Y** i villkoret. Spara enhetsgruppen som **IoT DevKit devices**.

Senare i den här självstudien använder du den här enhetsgruppen för att tillämpa en enhetskonfiguration som uppdaterar den inbyggda programvaran för alla medlemmar.

## <a name="prepare-and-host-the-firmware"></a>Förbereda och hantera den inbyggda programvaran.

[Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench)-tillägget för VS Code innehåller exempelenhetskoden för uppdateringen av inbyggd programvara.

### <a name="open-the-firmware-ota-sample-in-vs-code"></a>Öppna Firmware OTA-exemplet i VS Code

1. Kontrollera att din IoT DevKit inte är ansluten till datorn. Starta VS Code och ansluta sedan DevKit till datorn.

1. Tryck på **F1** för att öppna kommandopaletten, skriver du och väljer **IoT Workbench: Exempel**. Välj sedan **IoT DevKit** som tavla.

1. Leta upp **Firmware OTA** och klicka på **Öppna exempel**. Ett nytt VS Code-fönster öppnas, och projektmappen **firmware_ota** visas:

    ![IoT Workbench, välj exemplet Firmware OTA](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-firmware-example.png)

### <a name="build-the-new-firmware"></a>Skapa den nya inbyggda programvaran

Den första versionen av enhetens inbyggda programvara är 1.0.0. Den nya inbyggda programvaran bör ha ett högre versionsnummer.

1. I VS Code öppnar du filen **FirmwareOTA.ino** och ändrar `currentFirmwareVersion` från `1.0.0` till `1.0.1`:

    ![Ändra version av inbyggd programvara](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Öppna kommandopaletten, Skriv och markera **IoT Workbench: Enheten**. Välj sedan **Device Compile** (Enhetskompilering) för att kompilera koden:

    ![Enhetskompilering](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-compile.png)

    VS Code sparar den kompilerade filen i mappen `.build` i projektet. Beroende på dina inställningar kan VS Code dölja mappen `.build` i utforskarfyn.

### <a name="generate-the-crc-value-and-calculate-the-firmware-file-size"></a>Generera CRC-värdet och beräkna filstorleken för den inbyggda programvaran

1. Öppna kommandopaletten, Skriv och markera **IoT Workbench: Enheten**. Välj sedan **Generera CRC**:

    ![Generera CRC](media/iot-accelerators-remote-monitoring-bulk-configuration-update/iot-workbench-device-crc.png)

1. VS Code genererar och skriver ut CRC-värdet, filnamnet och sökvägen för den inbyggda programvaran samt filstorleken i utdatafönstret. Anteckna dessa värden för senare bruk:

    ![CRC-info](media/iot-accelerators-remote-monitoring-bulk-configuration-update/crc-info.png)

### <a name="upload-the-firmware-to-the-cloud"></a>Ladda upp den inbyggda programvaran till molnet

Använd ditt Azure Storage-konto som värd för den nya filen för inbyggd programvara i molnet.

1. Navigera till ditt lagringskonto i Azure-portalen. I avsnittet Tjänster väljer du **Blobar**. Skapa en offentlig container med namnet **firmware** där filerna för inbyggd programvara ska lagras:

    ![Skapa mapp](media/iot-accelerators-remote-monitoring-bulk-configuration-update/blob-folder.png)

1. För att vill ladda upp fil för inbyggd programvara till containern väljer du containern **firmware** och klickar på **Ladda upp**.

1. Välj **FirmwareOTA.ino.bin**. Du antecknade den fullständiga sökvägen till den här filen i föregående avsnitt.

1. När filen för inbyggd programvara har laddats upp antecknar du filens URL.

### <a name="build-and-upload-the-original-firmware-to-the-iot-devkit-device"></a>Skapa och ladda upp den ursprungliga inbyggda programvaran till IoT DevKit-enheten

1. I VS Code öppnar du filen **FirmwareOTA.ino** och ändrar `currentFirmwareVersion` tillbaka till `1.0.0`:

    ![Version 1.0.0](media/iot-accelerators-remote-monitoring-bulk-configuration-update/version-1-0-1.png)

1. Öppna kommandopaletten, Skriv och markera **IoT Workbench: Enheten**. Välj sedan **Enhetsuppladdning**:

    ![Enhetsuppladdning](media/iot-accelerators-remote-monitoring-bulk-configuration-update/device-upload.png)

1. VS Code verifierar och laddar upp koden till din IoT DevKit-enhet.

1. När uppladdningen är klar startas IoT DevKit-enheten om. När omstarten är klar visar skärmen för IoT DevKit **VB-version: 1.0.0**, och att den söker efter nya inbyggda programvaran:

    ![ota-1](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-1.jpg)

## <a name="create-a-device-configuration"></a>Skapa en enhetskonfiguration

En enhetskonfiguration anger önskad status för dina enheter. Vanligtvis [skapar en utvecklare konfigurationen](../iot-hub/iot-hub-automatic-device-management.md#create-a-configuration) på sidan **IoT-enhetskonfiguration** på Azure-portalen. En enhetskonfiguration är ett JSON-dokument som anger det önskade tillståndet för dina enheter och en uppsättning mått.

Spara följande konfiguration som en fil med namnet **firmware-update.json** på den lokala datorn. Ersätt platshållarna `YOURSTRORAGEACCOUNTNAME`, `YOURCHECKSUM` och `YOURPACKAGESIZE` med de värden som du antecknade tidigare:

```json
{
  "id": "firmware-update",
  "schemaVersion": null,
  "labels": {
    "Version": "1.0.1",
    "Devices": "IoT DevKit"
  },
  "content": {
    "deviceContent": {
      "properties.desired.firmware": {
        "fwVersion": "1.0.1",
        "fwPackageURI": "https://YOURSTRORAGEACCOUNTNAME.blob.core.windows.net/firmware/FirmwareOTA.ino.bin",
        "fwPackageCheckValue": "YOURCHECKSUM",
        "fwSize": YOURPACKAGESIZE
      }
    }
  },
  "targetCondition": "",
  "priority": 10,
  "systemMetrics": {
    "results": {
      "targetedCount": 0,
      "appliedCount": 0
    },
    "queries": {
      "targetedCount": "",
      "appliedCount": "select deviceId from devices where configurations.[[firmware-update]].status = 'Applied'"
    }
  },
  "metrics": {
    "results": {
      "Current": 1
    },
    "queries": {
      "Current": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Current' AND properties.reported.firmware.type='IoTDevKit'",
      "Downloading": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Downloading' AND properties.reported.firmware.type='IoTDevKit'",
      "Verifying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Verifying' AND properties.reported.firmware.type='IoTDevKit'",
      "Applying": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Applying' AND properties.reported.firmware.type='IoTDevKit'",
      "Error": "SELECT deviceId FROM devices WHERE properties.reported.firmware.fwUpdateStatus='Error' AND properties.reported.firmware.type='IoTDevKit'"
    }
  }
}
```

Du använder den här konfigurationsfilen i följande avsnitt.

## <a name="import-a-configuration"></a>Importera en konfiguration

I det här avsnittet importerar du enhetskonfigurationen som ett paket i lösningsacceleratorn för fjärrövervakning. Vanligtvis slutför en operatör den här uppgiften.

1. I webbgränssnittet för fjärrövervakning går du till sidan **Paket** och klickar på **+ Nytt paket**:

    ![Nytt paket](media/iot-accelerators-remote-monitoring-bulk-configuration-update/packagepage.png)

1. I panelen **Nytt paket** väljer du **Enhetskonfiguration** som pakettyp och **Inbyggd programvara** som konfigurationstyp. Klicka på **Bläddra** och leta upp filen **firmware-update.json** på den lokala datorn. Klicka sedan på **Ladda upp**:

    ![Ladda upp paket](media/iot-accelerators-remote-monitoring-bulk-configuration-update/uploadpackage.png)

1. Listan över paket innehåller nu paketet **firmware-update**.

## <a name="deploy-the-configuration-to-your-devices"></a>Distribuera konfigurationen till dina enheter

I det här avsnittet skapar och kör du en distribution som tillämpar enhetskonfigurationen på dina IoT DevKit-enheter.

1. I webbgränssnittet för fjärrövervakning går du till sidan **Distributioner** och klickar på **+ Ny distribution**:

    ![Ny distribution](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentpage.png)

1. I panelen **Ny distribution** skapar du en distribution med följande inställningar:

    |Alternativ|Värde|
    |---|---|
    |Namn|Distribuera uppdatering av inbyggd programvara|
    |Pakettyp|Enhetskonfiguration|
    |Konfigurationstyp|Inbyggd programvara|
    |Paket|firmware-update.json|
    |Enhetsgrupp|IoT DevKit-enheter|
    |Prioritet|10|

    ![Skapa distribution](media/iot-accelerators-remote-monitoring-bulk-configuration-update/newdeployment.png)

    Klicka på **Verkställ**. Du ser en ny distribution på sidan **Distributioner** som visar följande mått:

    * **Riktade** visar antalet enheter i enhetsgruppen.
    * **Tillämpade** visar antalet enheter som uppdaterades med konfigurationsinnehållet.
    * **Lyckade** visar antalet enheter i distributionen som rapporterade att de lyckades.
    * **Misslyckade** visar antalet enheter i distributionen rapporterade att de misslyckades.

## <a name="monitor-the-deployment"></a>Övervaka distributionen

Efter några minuter hämtar IoT DevKit informationen om den nya inbyggda programvara och börjar ladda ned den till enheten:

![ota-2](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-2.jpg)

Beroende på nätverkets hastighet kan nedladdningen ta några minuter. När den inbyggda programvaran har laddats ned kontrollerar enheten filstorleken och CRC-värdet. Skärmen på MXChip visar **passed** (lyckades) om verifieringen lyckas.

![ota-3](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-3.jpg)

Om kontrollen lyckas startas enheten om. Du ser en nedräkning från **5** till **0** innan omstarten sker.

![ota-4](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-4.jpg)

Efter omstarten uppgraderar IoT DevKit-startprogrammet den inbyggda programvaran till den nya versionen. Uppgraderingen kan ta flera sekunder. I det här stadiet är RGB-LED i enheten röd och skärmen är tom.

![ota-5](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-5.jpg)

När omstarten är klar körs IoT DevKit-enheten nu version 1.0.1 av den inbyggda programvaran.

![ota-6](media/iot-accelerators-remote-monitoring-bulk-configuration-update/ota-6.jpg)

På sidan **Distributioner** klickar du på en distribution för att se status för dina enheter allt eftersom de uppdateras. Du kan se statusen för varje enhet i enhetsgruppen och de anpassade mått som du har definierat.

![Distributionsinformation](media/iot-accelerators-remote-monitoring-bulk-configuration-update/deploymentstatus.png)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Den här självstudien visade hur du uppdaterar den inbyggda programvaran för en grupp av enheter som är anslutna till din lösning. Lösningen använder automatisk enhetshantering för att uppdatera enheterna. Läs mer om funktionen för automatisk enhetshantering i din lösnings underliggande IoT-hubb i avsnittet om att [konfigurera och övervaka IoT-enheter i stor skala med hjälp av Azure-portalen](../iot-hub/iot-hub-auto-device-config.md).