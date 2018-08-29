---
title: IoT DevKit till molnet – ansluta IoT DevKit AZ3166 till Azure IoT Hub | Microsoft Docs
description: I de här självstudierna lär du dig hur du konfigurerar och ansluter IoT DevKit AZ3166 på Azure IoT Hub så att den kan skicka data till Azure-molnplattformen.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 1839f3efee53caa070690fed569a61c1d2b3c965
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127733"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ansluta IoT DevKit AZ3166 till Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda den [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) att utveckla och prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster. Den innehåller en Arduino-kompatibelt tavla med omfattande kringutrustning och sensorer, ett paket med öppen källkod tavla och en växande [projekt catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-do"></a>Vad du gör

Anslut den [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) till Azure IoT hub som du skapar, samla in temperatur och fuktighet data från sensorer och skicka data till IoT hub.

Har inte en DevKit ännu? Prova den [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) eller [köpa en DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du ansluter IoT DevKit till en trådlös åtkomstpunkt och Förbered din utvecklingsmiljö.
* Hur du skapar en IoT-hubb och registrera en enhet för MXChip IoT DevKit.
* Så här att samla in sensordata genom att köra ett exempelprogram på MXChip IoT DevKit.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

* En MXChip IoT DevKit tavla med Micro USB-kabel. [Hämta nu](https://aka.ms/iot-devkit-purchase).
* En dator som kör Windows 10- eller macOS 10.10 +.
* En aktiv Azure-prenumeration. [Aktivera en kostnadsfri 30-dagars utvärderingsversion Microsoft Azure-konto](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Förbered maskinvaran

Koppla samman följande maskinvara till datorn:

* DevKit tavla
* Micro USB-kabel

![Maskinvara som krävs](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Följ dessa steg om du vill ansluta DevKit till datorn:

1. Anslut USB slutet på din dator.

2. Anslut Micro USB slutet till DevKit.

3. Grön Indikator för kraft bekräftar anslutningen.

   ![Maskinvaruanslutningar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Konfigurera Wi-Fi

IoT-projekt är beroende av internet-anslutning. Använd följande instruktioner för att konfigurera DevKit att ansluta till Wi-Fi.

### <a name="enter-ap-mode"></a>Ingå läget för Asien och Stillahavsområdet

Håll ned knappen B, push och släpp återställningsknappen och sedan knappen B. Din DevKit försätts i Asien och Stillahavsområdet läge för att konfigurera Wi-Fi. På skärmen visas nätverksnamn (SSID) för DevKit och IP-adressen för configuration-portalen.

![Återställ knappen, knappen B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Ansluta till DevKit Asien och Stillahavsområdet

Nu kan du använda en annan Wi-Fi-aktiverad enhet (dator eller mobiltelefon) för att ansluta till DevKit SSID (markerat i föregående bild). Lämna lösenordet tomt.

![Nätverksinformation och knappen Anslut](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Konfigurera Wi-Fi för DevKit

Öppna IP-adressen som visas på skärmen DevKit på din dator eller en mobiltelefon webbläsare, markera Wi-Fi-nätverk som du vill DevKit att ansluta till och sedan ange lösenordet. Välj **Anslut**.

![Lösenord och knappen Anslut](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

När anslutningen lyckas, startar om DevKit på några sekunder. Då visas Wi-Fi-namn och IP-adress på skärmen:

![Wi-Fi-namn och IP-adress](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> IP-adressen som visas i fotot kanske inte överensstämmer med själva IP-adressen tilldelas och visas på skärmen DevKit. Detta är normalt, eftersom Wi-Fi använder DHCP för att dynamiskt tilldela IP-adresser.

När Wi-Fi har konfigurerats, behålls dina autentiseringsuppgifter på enheten för anslutningen, även om enheten är inte ansluten. Om du konfigurerar DevKit för Wi-Fi hemma och sedan vidta DevKit till office, behöver du exempelvis konfigurera om AP-läge (med början i steg i avsnittet ”Ange AP läget”) för att ansluta DevKit till kontoret Wi-Fi. 

## <a name="start-using-the-devkit"></a>Börja använda DevKit

Standardapp som körs på DevKit kontrollerar den senaste versionen av den inbyggda programvaran och visar vissa diagnos sensordata åt dig.

### <a name="upgrade-to-the-latest-firmware"></a>Uppgradera till den senaste inbyggda programvaran

> [!NOTE]
> Sedan v1.1 gör DevKit ST SAFE i startprogrammet. Du måste uppgradera den inbyggda programvaran om du kör en tidigare version än version 1.1.

Om du behöver en uppgradering av inbyggd programvara, visar skärmen de aktuella och de senaste firmware-versionerna. Om du vill uppgradera, följer du de [uppgradera firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) guide.

![Visning av versioner av inbyggd programvara med aktuella och de senaste](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Det här är en enstaka ansträngning. När du börja utveckla på DevKit och ladda upp din app, kommer den senaste inbyggda programvaran med din app.

### <a name="test-various-sensors"></a>Testa olika sensorer

Tryck på knappen B för att testa sensorerna. Fortsätt att trycka på och nedtryckt B för att gå igenom varje sensor.

![Visning av knappen B och sensorn](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

### <a name="install-azure-iot-workbench"></a>Installera Azure IoT-arbetsstationen

Vi rekommenderar att [Azure IoT Workbench](https://aka.ms/iot-workbench) tillägget för Visual Studio Code för att utveckla på DevKit.

Azure IoT-arbetsstationen ger en integrerad upplevelse för att utveckla IoT-lösningar. Det hjälper dig både på enheten och molnet utveckling med hjälp av Azure IoT och andra tjänster. Du kan den här videon Channel 9 har en översikt över hur det fungerar.

Följ dessa steg för att förbereda utvecklingsmiljön för DevKit:

1. Ladda ned och installera [Arduino IDE](https://www.arduino.cc/en/Main/Software). Den innehåller nödvändiga verktygskedjan för kompilering och ladda upp Arduino-kod.
    * **Windows**: Använd Windows Installer-version.
    * **macOS**: dra och släpp den extraherade **Arduino.app** till `/Applications` mapp.
    * **Ubuntu**: packa upp det till mappen som `$HOME/Downloads/arduino-1.8.5`

1. Installera [Visual Studio Code](https://code.visualstudio.com/), plattformsoberoende Kodredigerare för källan med kraftfulla developer verktyg som IntelliSense kodifyllning och felsökning.

1. Leta efter **Azure IoT Workbench** i tillägget marketplace och installera den.
    ![Installera Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) tillsammans med IoT-Workbench andra beroende tillägg kommer att installeras.

1. Öppna **fil > Inställningar > Inställningar** och Lägg till följande rad för att konfigurera Arduino.
    * **Windows**:
    ```json
    "arduino.path": "C:\\Program Files (x86)\\Arduino",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **macOS**:
    ```json
    "arduino.path": "/Applications",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```
    * **Ubuntu**:
    ```json
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **Arduino: tavla Manager**. Sök efter **AZ3166** och installera den senaste versionen.
    ![Installera DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Installera ST-Link-drivrutiner

[ST-länk eller V2](http://www.st.com/en/development-tools/st-link-v2.html) är USB-gränssnitt som IoT DevKit använder för att kommunicera med din utvecklingsdator. Följ stegen operativsystemspecifika så att får datoråtkomst till din enhet.

* **Windows**: hämta och installera USB-drivrutin från [STMicroelectronics webbplats](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: det krävs ingen drivrutin för macOS.
* **Ubuntu**: kör du följande i terminalen och logga ut och logga in för att gruppändringen ska börja gälla som gäller:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu konfigureras du alla med förbereder och konfigurerar din utvecklingsmiljö. Låt oss skapa ”Hello World”-exemplet för IoT: skicka temperatur telemetridata till Azure IoT Hub.

## <a name="build-your-first-project"></a>Skapa ditt första projekt

1. Kontrollera att din IoT DevKit är **inte ansluten** till datorn. Starta VS Code först och sedan ansluta DevKit till din dator.

1. I det nedre högra status-fältet, kontrollera den **MXCHIP AZ3166** visas som markerade board och seriell port med **STMicroelectronics** används.
    ![Välj panel och COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: exempel**. Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench exempel hitta **börjar** och klicka på **öppna exemplet**. Sedan väljer standardsökvägen för att hämta exempelkoden.
    ![Öppna exemplet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. I fönstret Öppna projekt, klickar du på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: molnet**och välj sedan **Azure etablera**. Följ steg-för-steg-instruktioner för att slutföra etableringen av Azure IoT Hub och skapa enheten.
    ![Etablera i molnet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Klicka på `F1` att öppna kommandopaletten, typ och väljer **IoT Workbench: enheten**och välj sedan **Config Enhetsinställningar > Välj IoT Hub enhetens anslutningssträng**.

1. På DevKit, håll ned **knappen A**, push och släpp den **återställa** knappen och släpp **knappen A**. Din DevKit anger konfigurationsläge och sparar anslutningssträngen.
    ![Anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klicka på `F1` igen, ange och välj **IoT Workbench: enheten**och välj sedan **enheten ladda upp**.
    ![Ladda upp Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE]
> Om det finns fel eller avbrott i verksamheten, kan du alltid återställa genom att köra kommandot igen.

## <a name="test-the-project"></a>Testa projektet

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visa telemetri som skickas till Azure IoT Hub

Klicka på ikonen power plugin i statusfältet för att öppna den seriella Monitor: ![seriell Övervakare](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Exempelprogrammet körs när du ser följande resultat:

* Seriell övervakaren visar meddelandet som skickas till IoT Hub.
* LED på MXChip IoT DevKit blinkar.

![Seriell övervakaren utdata](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visa telemetri som tagits emot av Azure IoT Hub

Du kan använda [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) att övervaka meddelanden från enheten till molnet (D2C) i IoT Hub.

1. I Visual Studio Code, leta efter **Azure IoT Toolkit** i tillägget marketplace och installera den.

1. Logga in [Azure-portalen](https://portal.azure.com/), hitta IoT-hubben som du skapade.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. I den **principer för delad åtkomst** fönstret klickar du på den **iothubowner princip**, och anteckna anslutningssträngen för IoT-hubben.
    ![Azure IoT Hub-anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. I Visual Studio Code, expanderar **AZURE IOT HUB-enheter** i det nedre vänstra hörnet, klickar du på **ange anslutningssträngen för IoT Hub**.
    ![Ange Azure IoT Hub-anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Klicka på **IoT: börja övervaka D2C-meddelande** i snabbmenyn.

1. I **utdata** fönstret ser du inkommande D2C-meddelanden till IoT Hub.
    ![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan du söka efter en lösning i den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Du kan också ge oss feedback genom att lämna en kommentar på den här sidan.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en MXChip IoT DevKit till IoT-hubben och du har skickat det avbildade sensordata till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
