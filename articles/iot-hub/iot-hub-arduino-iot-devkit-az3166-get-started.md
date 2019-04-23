---
title: IoT DevKit till molnet – ansluta IoT DevKit AZ3166 till Azure IoT Hub | Microsoft Docs
description: I de här självstudierna lär du dig hur du konfigurerar och ansluter IoT DevKit AZ3166 på Azure IoT Hub så att den kan skicka data till Azure-molnplattformen.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 7a2cb110d5be74e23e8e782fc02873786e8813e9
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149724"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Ansluta IoT DevKit AZ3166 till Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda den [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) att utveckla och prototyp Internet of Things (IoT) lösningar som utnyttjar Microsoft Azure-tjänster. Den innehåller en Arduino-kompatibelt tavla med omfattande kringutrustning och sensorer, ett paket med öppen källkod tavla och en omfattande [exempelgalleri](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för MXChip IoT DevKit.
* Hur du ansluter IoT DevKit till Wi-Fi och konfigurera IoT Hub-anslutningssträngen.
* Så här skickar telemetri sensordata DevKit till IoT hub.
* Så här att förbereda utvecklingsmiljön och utveckla program för IoT DevKit.

Har inte en DevKit ännu? Prova den [DevKit simulator](https://azure-samples.github.io/iot-devkit-web-simulator/) eller [köpa en DevKit](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-need"></a>Vad du behöver

* En MXChip IoT DevKit tavla med Micro USB-kabel. [Hämta nu](https://aka.ms/iot-devkit-purchase).
* En dator som kör Windows 10, macOS 10.10 + eller Ubuntu 18.04 +.
* En aktiv Azure-prenumeration. [Aktivera en kostnadsfri 30-dagars utvärderingsversion Microsoft Azure-konto](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Snabbstart: Skicka telemetri från DevKit till IoT Hub

Snabbstarten använder förkompilerad DevKit inbyggd programvara för att skicka telemetri till IoT Hub. Innan du kör det du skapar en IoT-hubb och registrera en enhet med hubben.

### <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyNodeDevice**: Namnet på den enhet som du registrerar. Använd **MyNodeDevice** såsom det visas. Om du väljer ett annat namn för enheten behöver du använda det namnet i hela artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

1. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

### <a name="send-devkit-telemetry"></a>Skicka DevKit telemetri

DevKit ansluter till en enhetsspecifik slutpunkt på din IoT hub och skickar telemetri om temperatur och fuktighet.

1. Ladda ned den senaste versionen av [GetStarted firmware](https://aka.ms/devkit/prod/getstarted/latest) för IoT DevKit.

1. Se till att IoT DevKit ansluta till datorn via USB. Öppna Utforskaren finns på en USB-masslagringsenhet kallas **AZ3166**.
    ![Öppna Windows Explorer](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Dra och släpp den inbyggda programvaran precis har laddat ned i masslagringsenhet och det blinkar automatiskt.
    ![Kopiera inbyggd programvara](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. På DevKit, håll ned knappen **B**, push och släpp den **återställa** knappen och sedan på knappen versionen **B**. Din DevKit försätts i Asien och Stillahavsområdet läge. För att bekräfta, visas på skärmen nätverksnamn (SSID) för DevKit och IP-adressen för configuration-portalen.
    ![Återställ knappen, knappen B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Inställning för Asien och Stillahavsområdet](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Använd en webbläsare på en annan WiFi-aktiverad enhet (dator eller mobiltelefon) att ansluta till IoT DevKit SSID: T visas i föregående steg. Om du uppmanas du att ange ett lösenord, lämna det tomt.
    ![Ansluta SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Öppna **192.168.0.1** i webbläsaren. Välj den Wi-Fi som du vill IoT DevKit ansluta till, skriver du Wi-Fi-lösenordet och sedan klistra in anslutningssträngen som du gjort Observera av tidigare. Klicka sedan på spara.
    ![Konfigurationsgränssnitt](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit stöder endast 2,4 GHz-nätverk. Kontrollera [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) för mer information.

1. Wi-Fi information och enheten anslutningssträngen lagras i IoT DevKit när du ser resultatsidan.
    ![Konfiguration av resultat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > När Wi-Fi har konfigurerats, behålls dina autentiseringsuppgifter på enheten för anslutningen, även om enheten är inte ansluten.

1. IoT DevKit startar om några sekunder. På skärmen DevKit se du IP-adressen för DevKit som följer av dessa data, inklusive temperatur och fuktighet värdet med meddelandeantalet skicka till Azure IoT Hub.
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Skicka data](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Följ dessa steg för att förbereda utvecklingsmiljön för DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installera Visual Studio Code med Azure IoT-tilläggspaket

1. Installera [Arduino IDE](https://www.arduino.cc/en/Main/Software). Den innehåller nödvändiga verktygskedjan för kompilering och ladda upp Arduino-kod.
    * **Windows**: Använda Windows Installer-version. Installera inte från App Store.
    * **macOS**: Dra och släpp den extraherade **Arduino.app** till `/Applications` mapp.
    * **Ubuntu**: Packa upp det till mappen som `$HOME/Downloads/arduino-1.8.8`

2. Installera [Visual Studio Code](https://code.visualstudio.com/), plattformsoberoende Kodredigerare för källan med kraftfulla intellisense code slutförande och felsökning stöd samt omfattande tillägg kan installeras från marketplace.

3. Starta VS Code, leta efter **Arduino** i tillägget marketplace och installera den. Det här tillägget ger bättre upplevelser för att utveckla på Arduino-plattformen.
    ![Installera Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Leta efter [Azure IoT Tools](https://aka.ms/azure-iot-tools) i tillägget marketplace och installera den.
    ![Installera verktyg för Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Eller Använd den här Direktlänk:
    > [!div class="nextstepaction"]
    > [Installera paketet för verktyg för Azure IoT-tillägget](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Verktyg för Azure IoT-tilläggspaketet innehåller den [Azure IoT Device Workbench](https://aka.ms/iot-workbench) som används för att utveckla och felsöka på olika IoT devkit-enheter. Den [Azure IoT Hub Toolkit](https://aka.ms/iot-toolkit), också ingår verktyg för Azure IoT-tilläggspaketet, används för att hantera och interagera med Azure IoT-hubbar.

5. Konfigurera VS Code med Arduino inställningar.

    I Visual Studio Code, klickar du på **fil > Inställningar > Inställningar**. Klicka sedan på den **...**  och **öppna settings.json**.
    ![Installera verktyg för Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Lägg till följande rad för att konfigurera Arduino beroende på din plattform: 

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
    
        Ersätt den **{username}** platshållaren nedan med ditt användarnamn.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Arduino: Tavla Manager**. Sök efter **AZ3166** och installera den senaste versionen.
    ![Installera DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installera ST-Link-drivrutiner

[ST-länk eller V2](https://www.st.com/en/development-tools/st-link-v2.html) är USB-gränssnitt som IoT DevKit använder för att kommunicera med din utvecklingsdator. Du måste installera den på Windows till flash kompilerade enheten kod till DevKit. Följ stegen operativsystemspecifika så att får datoråtkomst till din enhet.

* **Windows**: Ladda ned och installera USB-drivrutin från [STMicroelectronics webbplats](https://www.st.com/en/development-tools/stsw-link009.html) eller [Direktlänk](https://aka.ms/stlink-v2-windows).
* **macOS**: Det krävs ingen drivrutin för macOS.
* **Ubuntu**: Kör kommandon i terminalen och logga ut och logga in för att gruppändringen ska börja gälla som gäller:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu konfigureras du alla med förbereder och konfigurerar din utvecklingsmiljö. Låt oss bygga GetStarted-exemplet som du körde.

## <a name="build-your-first-project"></a>Skapa ditt första projekt

### <a name="open-sample-code-from-sample-gallery"></a>Öppna exempelkoden från exempelgalleri

IoT DevKit innehåller ett rikt galleri med exempel som du kan använda för att lära dig ansluta DevKit till olika Azure-tjänster.

1. Kontrollera att din IoT DevKit är **inte ansluten** till datorn. Starta VS Code först och sedan ansluta DevKit till din dator.

1. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Öppna exempel...** . Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench exempel hitta **börjar** och klicka på **öppna exemplet**. Sedan väljer standardsökvägen för att hämta exempelkoden.
    ![Öppna exemplet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Etablera Azure IoT Hub och enhet

I stället för etablering Azure IoT Hub och enhet från Azure-portalen, kan du göra det i VS Code utan att lämna utvecklingsmiljön.

1. I fönstret Öppna projekt, klickar du på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Etablera Azure-tjänster...** . Följ steg-för-steg-instruktioner för att slutföra etableringen av Azure IoT Hub och skapa en IoT Hub-enhet.
    ![Etablera kommando](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Om du inte har registrerat i Azure. Följ popup-meddelandet för att logga in.

1. Ange den prenumeration som du vill använda.
    ![Välj sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Välj sedan eller skapa en ny [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Välj resursgrupp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. Följ guiden för att välja eller skapa en ny Azure IoT Hub i den resursgrupp som du angav.
    ![Välj IoT Hub-steg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Välj IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Den valda IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. I utdatafönstret visas Azure IoT Hub etablerats ![etableras för IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Välj eller skapa en ny enhet i Azure IoT Hub som du etablerade.
    ![Välj IoT Device steg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Välj IoT-enhet som etablerats](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nu har du Azure IoT Hub etablerats och en enhet har skapats i den. Även sparas enhetens anslutningssträng i VS Code för att konfigurera IoT DevKit senare.
    ![Etablera klar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurera och kompilera koden för enheten

1. I statusfältet längst ned till höger, kontrollera den **MXCHIP AZ3166** visas som markerade board och seriell port med **STMicroelectronics** används.
    ![Välj panel och COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klicka på `F1` för att öppna kommandopaletten, skriver du och väljer **Azure IoT Device Workbench: Konfigurera Enhetsinställningar...** och välj sedan **Config enhetens anslutningssträng > Välj IoT Hub enhetens anslutningssträng**.

1. På DevKit, håll ned **knappen A**, push och släpp den **återställa** knappen och släpp **knappen A**. Din DevKit anger konfigurationsläge och sparar anslutningssträngen.
    ![Anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klicka på `F1` igen, ange och välj **Azure IoT Device Workbench: Ladda upp enheten**. Den börjar kompilera och ladda upp koden till DevKit.
    ![Ladda upp Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit startar om och börjat köra koden.

> [!NOTE]
> Om det finns några fel eller avbrott i verksamheten, kan du alltid återställa genom att köra kommandot igen.

## <a name="test-the-project"></a>Testa projektet

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visa telemetri som skickas till Azure IoT Hub

Klicka på ikonen power plugin i statusfältet för att öppna den seriella Monitor: ![Seriell Övervakare](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Exempelprogrammet körs när du ser följande resultat:

* Seriell övervakaren visar meddelandet som skickas till IoT Hub.
* LED på MXChip IoT DevKit blinkar.

![Seriell övervakaren utdata](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visa telemetri som tagits emot av Azure IoT Hub

Du kan använda [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) att övervaka meddelanden från enheten till molnet (D2C) i IoT Hub.

1. Logga in [Azure-portalen](https://portal.azure.com/), hitta IoT-hubben som du skapade.
    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. I den **principer för delad åtkomst** fönstret klickar du på den **iothubowner princip**, och anteckna anslutningssträngen för IoT-hubben.
    ![Azure IoT Hub-anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. I VS Code, klickar du på `F1`skriver du och väljer **Azure IoT Hub: Ange IoT Hub-anslutningssträngen**. Kopiera anslutningssträngen till den.
    ![Ange Azure IoT Hub-anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expandera den **AZURE IOT HUB-enheter** rutan till höger, högerklickar du på namnet på enheten du har skapat och välj **börja övervaka D2C-meddelande**.
    ![Övervaka D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. I **utdata** fönstret ser du inkommande D2C-meddelanden till IoT Hub.
    ![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Granska koden

Den `GetStarted.ino` är den huvudsakliga Arduino skiss filen.

![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Om du vill se hur enhetstelemetri skickas till Azure IoT Hub, öppna den `utility.cpp` fil i samma mapp. Visa [API-referens](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) att lära dig hur du använder sensorer och kringutrustning på IoT DevKit.

Den `DevKitMQTTClient` används är en Omslutning för det **iothub_client** från den [Microsoft Azure IoT SDK: er och bibliotek för C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) att interagera med Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan du söka efter en lösning i den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss från [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Du kan också ge oss feedback genom att lämna en kommentar på den här sidan.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en MXChip IoT DevKit till IoT-hubben och du har skickat det avbildade sensordata till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
