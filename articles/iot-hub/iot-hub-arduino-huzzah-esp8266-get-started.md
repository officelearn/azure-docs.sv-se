---
title: ESP8266 till molnet – ansluta Feather HUZZAH ESP8266 till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Adafruit Feather HUZZAH ESP8266 på Azure IoT Hub för att skicka data till Azure-molnplattformen i den här självstudien.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 928860cd9b6493200ed8a14b7654f6a6f32c45d5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259396"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Anslut Adafruit Feather HUZZAH ESP8266 till Azure IoT Hub i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Anslutning mellan DHT22 Feather HUZZAH ESP8266 och IoT-hubb](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Vad du gör

Anslut Adafruit Feather HUZZAH ESP8266 till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på ESP8266 att samla in temperatur och fuktighet data från en DHT22 sensor. Slutligen kan skicka du sensordata till din IoT hub.

> [!NOTE]
> Du kan följa stegen nedan för att ansluta till din IoT hub även om du använder andra ESP8266-kort. Beroende på den ESP8266 tavla som du använder kan du behöva konfigurera om den `LED_PIN`. Till exempel om du använder ESP8266 från AI-Thinker, du kan ändra den från `0` till `2`. Har inte ett kit ännu? Hämta den från den [Azure-webbplatsen](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för Feather HUZZAH ESP8266
* Så här ansluter du Feather HUZZAH ESP8266 med sensorn och din dator
* Hur du samlar in sensordata genom att köra ett exempelprogram på Feather HUZZAH ESP8266
* Hur du skickar sensordata till din IoT-hubb

## <a name="what-you-need"></a>Vad du behöver

![Delar som behövs för den här självstudien](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden, behöver du följande delar från din Feather HUZZAH ESP8266 Starter-paket:

* Feather HUZZAH ESP8266-tavla
* En Micro USB till typ A USB-kabel

Du måste också följande för din utvecklingsmiljö:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.
* En Mac eller en dator som kör Windows eller Ubuntu.
* Ett trådlöst nätverk för Feather HUZZAH ESP8266 att ansluta till.
* En Internetanslutning att hämta konfigurationsverktyget.
* [Visual Studio Code-tillägg för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Arduino IDE-version som används av Visual Studio Code-tillägg för Arduino måste vara version 1.6.8 eller senare. Tidigare versioner fungerar inte med AzureIoT-biblioteket.

Följande objekt är valfritt om du inte har en sensor. Du har också möjlighet att använda simulerade sensordata.

* En Adafruit DHT22 temperatur och fuktighet sensor
* En breadboard
* M/M-omkopplare kablar

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Anslut Feather HUZZAH ESP8266 med sensorn och din dator

I det här avsnittet ska ansluta du sensorerna till din tavla. Sedan ansluter du din enhet till din dator för ytterligare användning.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Ansluta en DHT22 temperatur och fuktighet sensor till Feather HUZZAH ESP8266

Använd breadboard och omkopplare kablar för att upprätta anslutningen på följande sätt. Om du inte har en sensor kan du hoppa över det här avsnittet eftersom du kan använda simulerade sensordata i stället.

![Referens för anslutningar](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

För sensor PIN-koder, använder du följande koaxialkabel:

| Starta (Sensor)           | Slutpunkt (Board)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------  |
| VDD (PIN-kod 31F)            | 3v (fästa 58H)           | Röd-kabel     |
| DATA (PIN-kod 32F)           | GPIO 2 (PIN-kod 46A)       | Blå kabeln    |
| JORD (PIN-kod 34F)            | JORD (PIN-kod 56I)          | Svarta kabeln   |

Mer information finns i [Adafruit DHT22 sensorkonfiguration](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) och [Adafruit Feather HUZZAH Esp8266 Pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Feather Huzzah ESP8266 bör nu vara ansluten med en fungerande sensor.

![Träffa Feather Huzzah DHT22](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Ansluta Feather HUZZAH ESP8266 till din dator

Enligt nästa, kan du använda mikro USB till typ A USB-kabel för att ansluta Feather HUZZAH ESP8266 till datorn.

![Ansluta Feather Huzzah till din dator](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Lägg till behörigheter för seriell port (endast Ubuntu)

Om du använder Ubuntu kan du kontrollera att du har behörighet att fungera på den USB-port av Feather HUZZAH ESP8266. Följ dessa steg för att lägga till behörigheter för seriell port:

1. Kör följande kommandon i en terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får något av följande utdata:

   * crw-rw---1 rot uucp xxxxxxxx
   * crw-rw---1 rot antingen xxxxxxxx

   Observera att utdata och `uucp` eller `dialout` är gruppnamn för ägare av USB-port.

2. Lägga till användaren i gruppen genom att köra följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` är grupp ägarens namn som du hämtade i föregående steg. `<username>` är ditt Ubuntu-användarnamn.

3. Logga ut från Ubuntu och logga sedan in igen för att ändringen ska visas.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensordata och skicka den till din IoT-hubb

I det här avsnittet ska du distribuera och köra ett exempelprogram på Feather HUZZAH ESP8266. Exempelprogrammet blinkar Indikator på Feather HUZZAH ESP8266 och skickar temperatur- och data som samlas in från DHT22 sensorn till din IoT hub.

### <a name="get-the-sample-application-from-github"></a>Hämta exempelprogrammet från GitHub

Exempelprogrammet finns på GitHub. Klona exempellagringsplatsen med exempelprogrammet från GitHub. Följ dessa steg för att klona exempellagringsplatsen:

1. Öppna en kommandotolk eller ett terminalfönster.

2. Gå till en mapp där du vill att exempelprogrammet som ska lagras.

3. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Installera paketet för Feather HUZZAH ESP8266 i Visual Studio Code.

4. Öppna mappen där exempelprogrammet lagras.

5. Öppna filen app.ino i mappen i Visual Studio Code.

   ![Öppna exempelappen i Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. I Visual Studio Code, ange `F1`.

7. Typ **Arduino** och välj **Arduino: Tavla Manager**.

8. I den **Arduino tavla Manager** fliken **ytterligare URL: er**.

   ![VS Code Arduino-kort Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. I den **användarinställningar** fönster, kopiera och klistra in följande i slutet av filen

   ```json
   "arduino.additionalUrls": "http://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Konfigurera URL: en för Arduino-paketet i VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Spara filen och Stäng den **användarinställningar** fliken.

11. Klicka på **uppdatera paketet index**. När uppdateringen är klar letar **esp8266**.

12. Klicka på **installera** knappen för esp8266.

    Tavlor Manager anger att ESP8266 med en version av 2.2.0 eller senare är installerat.

    ![Esp8266-paket installeras](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Ange `F1`, Skriv **Arduino** och välj **Arduino: Board Config**.

14. Klicka på kryssrutan för **valt tavla:** och skriv **esp8266**och välj sedan **Adafruit HUZZAH ESP8266 (esp8266)**.

    ![Välj esp8266 tavla](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Visual Studio Code, ange `F1`, Skriv **Arduino** och välj **Arduino: Biblioteket Manager**.

2. Sök efter följande biblioteket namn i taget. För alla bibliotek som du hittar, klickar du på **installera**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Har du en verklig sensor DHT22?

Exempelprogrammet simulera temperatur och fuktighet data om du inte har en verklig DHT22 sensor. Så här konfigurerar du exempelprogrammet till att använda simulerade data genom:

1. Öppna den `config.h` fil i den `app` mapp.

2. Leta upp följande rad med kod och ändra värdet från `false` till `true`:

   ```c
   define SIMULATED_DATA true
   ```

   ![Konfigurera exempelprogrammet för att använda simulerade data](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Spara filen.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Distribuera exempelprogrammet för att Feather HUZZAH ESP8266

1. I Visual Studio Code, klickar du på **<Select Serial Port>** statusen, och klicka sedan på den seriella porten för Feather HUZZAH ESP8266.

2. Ange `F1`, Skriv **Arduino** och välj **Arduino: Ladda upp** att bygga och distribuera exempelprogrammet för att Feather HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Ange autentiseringsuppgifter

När överföringen är klar följer du stegen nedan för att ange dina autentiseringsuppgifter:

1. Öppna Arduino IDE, klicka på **verktyg** > **seriell övervakaren**.

2. Observera att två listrutorna i det nedre högra hörnet i fönstret seriell monitor.

3. Välj **inga raden slutar** för den vänstra listan.

4. Välj **115200 baud** för just nedrullningsbara listan.

5. Ange följande information i textrutan längst upp i fönstret seriell, om du uppmanas att ange dem och klicka sedan på **skicka**.

   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Enhetens anslutningssträng

> [!Note]
> Autentiseringsuppgifterna lagras i EEPROM av Feather HUZZAH ESP8266. Om du klickar på återställningsknappen på tavlan Feather HUZZAH ESP8266 exempelprogrammet som frågar om du vill radera informationen. Ange `Y` att ha information raderas. Du uppmanas att ange information om en andra gång.

### <a name="verify-the-sample-application-is-running-successfully"></a>Kontrollera exempelprogrammet körts

Exempelprogrammet körs om du ser i följande utdata från seriell övervakningsfönstret och blinkande Indikator på Feather HUZZAH ESP8266.

![Slutgiltiga utdata i Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelandena som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av IoT-hubben från din enhet är att använda Azure IoT Tools för Visual Studio Code. Mer information finns i [Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt för fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en Feather HUZZAH ESP8266 till din IoT-hubb, och skickas de hämtade sensordata till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]