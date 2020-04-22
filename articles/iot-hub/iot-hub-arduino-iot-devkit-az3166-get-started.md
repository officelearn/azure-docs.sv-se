---
title: Anslut IoT DevKit AZ3166 till en Azure IoT-hubb
description: I den här självstudien kan du lära dig hur du konfigurerar och ansluter IoT DevKit AZ3166 till Azure IoT Hub så att den kan skicka data till Azure-molnplattformen.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom: mqtt
ms.openlocfilehash: 1c6af4f44cd16405d1ef12ae909085220b0d072d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733584"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Anslut IoT DevKit AZ3166 till Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Du kan använda [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) för att utveckla och prototypen av Sakernas Internet (IoT) lösningar som utnyttjar Microsoft Azure-tjänster. Den innehåller en Arduino-kompatibel styrelse med omfattande kringutrustning och sensorer, en öppen källkod ombord paket och ett rikt [prov galleri](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en IoT-hubb och registrerar en enhet för MXChip IoT DevKit.
* Så här ansluter du IoT DevKit till Wi-Fi och konfigurerar anslutningssträngen för IoT Hub.
* Så här skickar du telemetridata för DevKit-sensorn till din IoT-hubb.
* Hur man förbereder utvecklingsmiljön och utvecklar applikation för IoT DevKit.

Har du ingen DevKit än? Prova [DevKit-simulatorn](https://azure-samples.github.io/iot-devkit-web-simulator/) eller [köp en DevKit](https://aka.ms/iot-devkit-purchase).

Du hittar källkoden för alla DevKit tutorials från [kodexempel galleri](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Vad du behöver

* En MXChip IoT DevKit styrelse med en Micro-USB-kabel. [Ta den nu.](https://aka.ms/iot-devkit-purchase)
* En dator med Windows 10, macOS 10.10+ eller Ubuntu 18.04+.
* En aktiv Azure-prenumeration. [Aktivera ett kostnadsfritt 30-dagars testversionskonto för Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Förbered maskinvaran

Anslut följande maskinvara till datorn:

* DevKit styrelse
* Mikro-USB-kabel

![Nödvändig maskinvara](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Så här ansluter du DevKit till datorn:

1. Anslut USB-änden till datorn.

2. Anslut Micro-USB-änden till DevKit.

3. Den gröna lysdioden för ström bekräftar anslutningen.

   ![Maskinvaruanslutningar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Snabbstart: Skicka telemetri från DevKit till en IoT-hubb

Snabbstarten använder förkompilerad DevKit firmware för att skicka telemetrin till IoT Hub. Innan du kör den skapar du en IoT-hubb och registrerar en enhet med navet.

### <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I den här snabbstarten använder du Azure Cloud Shell till att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetsidentiteten.

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

   **MyNodeDevice**: Namnet på enheten du registrerar. Använd **MyNodeDevice** såsom det visas. Om du väljer ett annat namn för enheten behöver du använda det namnet i hela artikeln och uppdatera enhetsnamnet i exempelprogrammen innan du kör dem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Om du får `device-identity`ett fel som körs installerar du [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i din Cloud Shell-instans. IoT-tillägget lägger till kommandon som är specifika för IoT Hub, IoT Edge och IoT Device Provisioning Service (DPS) i Azure CLI.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Kör följande kommandon i Azure Cloud Shell för att hämta _enhetsanslutningssträngen_ för enheten du just registrerade:

   **YourIoTHubName** : Ersätt platshållaren nedan med det namn du väljer för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

### <a name="send-devkit-telemetry"></a>Skicka DevKit-telemetri

DevKit ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar telemetri för temperatur och luftfuktighet.

1. Ladda ner den senaste versionen av [GetStarted firmware](https://aka.ms/devkit/prod/getstarted/latest) för IoT DevKit.

1. Kontrollera att IoT DevKit ansluter till datorn via USB. Öppna Utforskaren finns det en USB-masslagringsenhet som heter **AZ3166**.

    ![Öppna Utforskaren](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Dra och släpp firmware bara laddas ner till masslagring enheten och det kommer att blinka automatiskt.

    ![Kopiera inbyggd programvara](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. På DevKit håller du ned knapp **B,** trycker och släpper **återställningsknappen** och släpper sedan knappen **B**. DevKit går in i AP-läge. För att bekräfta visar skärmen tjänstuppsättningsidentifieraren (SSID) för DevKit och konfigurationsportalens IP-adress.

    ![Knappen Återställ, knapp B och SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Ställ in AP-läge](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Använd en webbläsare på en annan Wi-Fi-aktiverad enhet (dator eller mobiltelefon) för att ansluta till IoT DevKit SSID som visas i föregående steg. Om den ber om ett lösenord lämnar du det tomt.

    ![Anslut SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Öppna **192.168.0.1** i webbläsaren. Välj det Wi-Fi som du vill att IoT DevKit ska ansluta till, skriv Wi-Fi-lösenordet och klistra sedan in enhetsanslutningssträngen som du har noterat tidigare. Klicka sedan på spara.

    ![Konfigurationsgränssnitt](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit stöder endast 2,4 GHz-nätverk. Kolla [faq](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) för mer information.

1. WiFi-information och enhetsanslutningssträng lagras i IoT DevKit när du ser resultatsidan.

    ![Konfigurationsresultat](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > När Wi-Fi har konfigurerats finns dina autentiseringsuppgifter kvar på enheten för den anslutningen, även om enheten är frånkopplad.

1. IoT DevKit startar om några sekunder. På DevKit-skärmen ser du IP-adressen för DevKit följer av telemetridata, inklusive temperatur- och fuktighetsvärde med meddelandeantal som skickas till Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Skicka data](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Om du vill verifiera telemetridata som skickas till Azure kör du följande kommando i Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Följ dessa steg för att förbereda utvecklingsmiljön för DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Installera Visual Studio-kod med azure IoT Tools-tilläggspaket

1. Installera [Arduino IDE](https://www.arduino.cc/en/Main/Software). Det ger den nödvändiga verktygskedjan för att sammanställa och ladda upp Arduino-kod.
    * **Windows**: Använd Windows Installer-versionen. Installera inte från App Store.
    * **macOS**: Dra och släpp den extraherade `/Applications` **Arduino.app** i mappen.
    * **Ubuntu**: Packa upp den i mappen såsom`$HOME/Downloads/arduino-1.8.8`

2. Installera [Visual Studio Code](https://code.visualstudio.com/), en plattformsoberoende källkodsredigerare med kraftfull intellisense, kodkomplettering och felsökning stöd samt rika tillägg kan installeras från marketplace.

3. Starta VS Code, leta efter **Arduino** i förlängningen marknadsplats och installera den. Denna förlängning ger förbättrade upplevelser för utveckling på Arduino plattform.

    ![Installera Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Leta efter [Azure IoT Tools](https://aka.ms/azure-iot-tools) på tilläggsmarknadsplatsen och installera det.

    ![Installera Azure IoT-verktyg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Eller kopiera och klistra in webbadressen i ett webbläsarfönster:`vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Azure IoT Tools-tilläggspaketet innehåller [Azure IoT Device Workbench](https://aka.ms/iot-workbench) som används för att utveckla och felsöka på olika IoT-devkit-enheter. [Azure IoT Hub-tillägget](https://aka.ms/iot-toolkit), som också ingår i Azure IoT Tools-tilläggspaketet, används för att hantera och interagera med Azure IoT Hubs.

5. Konfigurera VS-kod med Arduino-inställningar.

    I Visual Studio-kod klickar du på **Inställningar för fil > > inställningar** (på macOS, Kod > inställningar > **Inställningar**). Klicka sedan på ikonen **Öppna inställningar (JSON)** i det övre högra hörnet på sidan *Inställningar.*

    ![Installera Azure IoT-verktyg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Lägg till följande rader för att konfigurera Arduino beroende på din plattform: 

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

        Ersätt **platshållaren {användarnamn}** nedan med ditt användarnamn.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Klicka `F1` här om du vill öppna kommandopaletten skriver du och väljer **Arduino: Board Manager**. Sök efter **AZ3166** och installera den senaste versionen.

    ![Installera DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Installera ST-Link-drivrutiner

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) är DET USB-gränssnitt som IoT DevKit använder för att kommunicera med din utvecklingsmaskin. Du måste installera den på Windows för att blinka den kompilerade enhetskoden till DevKit. Följ de OS-specifika stegen för att ge datorn åtkomst till enheten.

* **Windows**: Ladda ner och installera USB-drivrutin från [STMicroelectronics hemsida](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Ingen drivrutin krävs för macOS.
* **Ubuntu**: Kör kommandona i terminalen och logga ut och logga in för att gruppändringen ska börja gälla:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Nu är du redo med att förbereda och konfigurera din utvecklingsmiljö. Låt oss bygga GetStarted-provet som du just körde.

## <a name="build-your-first-project"></a>Bygg ditt första projekt

### <a name="open-sample-code-from-sample-gallery"></a>Öppna exempelkod från exempelgalleriet

IoT DevKit innehåller ett omfattande galleri med exempel som du kan använda för att lära dig ansluta DevKit till olika Azure-tjänster.

1. Kontrollera att din IoT DevKit inte är **ansluten** till datorn. Starta VS-koden först och anslut sedan DevKit till datorn.

1. Klicka `F1` här om du vill öppna kommandopaletten, skriv och välj **Azure IoT Device Workbench: Öppna exempel...**. Välj sedan **IoT DevKit** som anslagstavla.

1. Leta reda på **Kom igång** på sidan IoT Workbench-exempel och klicka på **Öppna exempel**. Väljer sedan standardsökvägen för att hämta exempelkoden.

    ![Öppna exempel](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Etablera Azure IoT-hubb och enhet

I stället för att etablera Azure IoT Hub och enhet från Azure-portalen kan du göra det i VS-koden utan att lämna utvecklingsmiljön.

1. I det nya öppnade `F1` projektfönstret klickar du för att öppna kommandopaletten, skriver och väljer **Azure IoT Device Workbench: Provision Azure Services...**. Följ steg för steg guide för att slutföra etableringen av din Azure IoT Hub och skapa IoT Hub-enheten.

    ![Kommandot Etablera](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Om du inte har loggat in i Azure. Följ popup-meddelandet för inloggning.

1. Ange den prenumeration som du vill använda.

    ![Välj under](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Välj eller skapa sedan en ny [resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Välj resursgrupp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. I den resursgrupp som du angav följer du guiden för att välja eller skapa en ny Azure IoT Hub.

    ![Välj IoT Hub-steg](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Välj IoT-hubb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Vald IoT-hubb](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. I utdatafönstret ser du Azure IoT Hub-etableringen.

    ![IoT Hub-etablerat](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Välj eller skapa en ny enhet i Azure IoT Hub som du har etablerat.

    ![Välj steg för IoT-enhet](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Välj IoT-enhet etablerad](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Nu har du Azure IoT Hub etablerat och enhet skapas i den. Även enheten anslutning strängen kommer att sparas i VS-kod för att konfigurera IoT DevKit senare.

    ![Avsättningen har gjorts](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurera och kompilera enhetskod

1. I det nedre högra statusfältet kontrollerar du att **MXCHIP AZ3166** visas som vald anslagstavla och serieport med **STMicroelectronics** används.

    ![Välj anslagstavla och COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Klicka `F1` här om du vill öppna kommandopaletten skriver du och väljer **Azure IoT Device Workbench: Konfigurera enhetsinställningar...** och välj sedan **Anslutningssträng för konfigurationsenhet > Välj IoT Hub Device Connection String**.

1. På DevKit håller du ned **knapp A,** trycker och släpper **återställningsknappen** och släpper sedan **knappen A**. DevKit går in i konfigurationsläge och sparar anslutningssträngen.

    ![Anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Klicka `F1` igen, skriv och välj **Azure IoT Device Workbench: Ladda upp enhetskod**. Det börjar kompilera och ladda upp koden till DevKit.

    ![Arduino ladda upp](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit startar om och börjar köra koden.

> [!NOTE]
> Om det finns några fel eller avbrott kan du alltid återställa genom att köra kommandot igen.

## <a name="test-the-project"></a>Testa projektet

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Visa telemetrin som skickas till Azure IoT Hub

Klicka på ikonen för stickkontakt i statusfältet för att öppna serieövervakaren:

![Seriell bildskärm](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Exempelprogrammet körs när följande resultat visas:

* Serieförvakaren visar meddelandet som skickas till IoT Hub.
* Lysdioden på MXChip IoT DevKit blinkar.

![Seriell bildskärmsutgång](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Du kan stöta på ett fel under testning där lysdioden inte blinkar, Azure-portalen visar inte inkommande data från enheten, men enhetens OLED-skärm visas som **Running...**. För att lösa problemet i Azure-portalen går du till enheten i IoT-hubben och skickar ett meddelande till enheten. Om du ser följande svar i den seriella bildskärmen i VS-kod är det möjligt att direkt kommunikation från enheten blockeras på routernivå. Kontrollera brandväggs- och routerregler som är konfigurerade för anslutningsenheterna. Se också till att utgående port 1833 är öppen.
> 
> FEL: mqtt_client.c (ln 454): Fel: fel vid öppnande av anslutningen till slutpunkten  
> INFO: >>>Anslutningsstatus: frånkopplad  
> FEL: tlsio_mbedtls.c (ln 604): Underliggande IO-öppen misslyckades  
> FEL: mqtt_client.c (ln 1042): Fel: io_open misslyckades  
> FEL: iothubtransport_mqtt_common.c (ln 2283): fel anslutning till adress atcsliothub.azure-devices.net.  
> INFO: >>>Anslut igen.  
> INFO: IoThub Version: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Visa telemetrin som tagits emot av Azure IoT Hub

Du kan använda [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) för att övervaka D2C-meddelanden (Device-to-cloud) i IoT Hub.

1. Logga in [Azure-portalen,](https://portal.azure.com/)hitta IoT Hub du skapade.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. I fönstret **Principer för delad åtkomst** klickar du på **iothubowner-principen**och skriver ned anslutningssträngen för IoT-hubben.

    ![Anslutningssträng för Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. I VS-kod `F1`klickar du på , skriver och väljer **Azure IoT Hub: Ange IoT Hub Connection String**. Kopiera anslutningssträngen till den.

    ![Ange Azure IoT Hub-anslutningssträng](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expandera fönstret **AZURE IOT HUB DEVICES** till höger, högerklicka på enhetsnamnet du skapade och välj Starta övervakning inbyggd **händelseslutpunkt**.

    ![Övervaka D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. I fönstret **UTDATA** kan du se inkommande D2C-meddelanden till IoT Hub.

    ![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Granska koden

Den `GetStarted.ino` är den viktigaste Arduino skiss fil.

![D2C-meddelande](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Om du vill se hur enhetstelemetri skickas till `utility.cpp` Azure IoT Hub öppnar du filen i samma mapp. Visa [API-referens](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) om du vill lära dig hur du använder sensorer och kringutrustning på IoT DevKit.

Den `DevKitMQTTClient` använda är ett omslag av **iothub_client** från [Microsoft Azure IoT SDK:er och bibliotek för C för](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) att interagera med Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du söka efter en lösning i [IoT DevKit FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller nå ut till oss från [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Du kan också ge oss feedback genom att lämna en kommentar på denna sida.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en MXChip IoT DevKit till din IoT-hubb och du har skickat de infångade sensordata till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
