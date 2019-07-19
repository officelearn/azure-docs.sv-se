---
title: ESP8266 to Cloud – Connect fjädra HUZZAH ESP8266 till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Adafruit ludd HUZZAH ESP8266 till Azure IoT Hub för att skicka data till Azure Cloud Platform i den här självstudien.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 8e97a979c37af8ade51b4ff6ca4b2c5b4eec126e
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232728"
---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Anslut Adafruit ludd HUZZAH ESP8266 till Azure IoT Hub i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Anslutning mellan DHT22, ludd HUZZAH ESP8266 och IoT Hub](./media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-do"></a>Vad du gör

Anslut Adafruit ludd HUZZAH ESP8266 till en IoT-hubb som du skapar. Sedan kör du ett exempel program på ESP8266 för att samla in temperatur-och fuktighets data från en DHT22-sensor. Slutligen skickar du sensor data till din IoT-hubb.

> [!NOTE]
> Om du använder andra ESP8266-kort kan du fortfarande följa de här stegen för att ansluta den till din IoT-hubb. Beroende på vilken ESP8266-tavla du använder kan du behöva konfigurera `LED_PIN`om. Om du till exempel använder ESP8266 från AI-trodde kan du ändra den från `0` till. `2` Har du inget paket än? Hämta den från [Azure-webbplatsen](https://azure.com/iotstarterkits).

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en IoT-hubb och registrerar en enhet för ludd HUZZAH ESP8266
* Så här ansluter du ludd HUZZAH ESP8266 med sensorn och datorn
* Samla in sensor data genom att köra ett exempel program på ludd HUZZAH ESP8266
* Skicka sensor data till din IoT-hubb

## <a name="what-you-need"></a>Vad du behöver

![Delar som behövs för självstudien](./media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden behöver du följande delar från ditt fjädra HUZZAH-ESP8266 start paket:

* ESP8266-tavlan för ludd HUZZAH
* En mikrousb-enhet för att ange en USB-kabel

Du behöver också följande saker för utvecklings miljön:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto kan du [skapa ett kostnads fritt Azures utvärderings konto](https://azure.microsoft.com/free/) på bara några minuter.
* En Mac-eller PC-dator som kör Windows eller Ubuntu.
* [GIT](https://git-scm.com/download)
* [Arduino](https://www.arduino.cc/en/main/software#download)
* [Adafruit Huzzah ESP8266-drivrutin för USB](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide)
* Ett trådlöst nätverk för ludd HUZZAH ESP8266 att ansluta till.
* En Internet anslutning för att ladda ned konfigurations verktyget.
* [Visual Studio Code-tillägg för Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino).

> [!Note]
> Den Arduino IDE-version som används av Visual Studio Code Extension för Arduino måste vara version 1.6.8 eller senare. Tidigare versioner fungerar inte med AzureIoT-biblioteket.

Följande objekt är valfria om du inte har en sensor. Du kan också välja att använda simulerade sensor data.

* En Adafruit DHT22-temperatur och fuktighets sensor
* En breadboard
* M/M hopp ledningar

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Anslut ludd HUZZAH ESP8266 med sensorn och datorn

I det här avsnittet ansluter du sensorer till din tavla. Sedan ansluter du enheten till datorn för att kunna använda den.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Anslut en DHT22 temperatur-och fuktighets sensor till ludd HUZZAH ESP8266

Använd breadboard och bygel-kablar för att upprätta anslutningen på följande sätt. Om du inte har en sensor hoppar du över det här avsnittet eftersom du kan använda simulerade sensor data i stället.

![Referens för anslutningar](./media/iot-hub-arduino-huzzah-esp8266-get-started/17_connections_on_breadboard.png)

För sensor stift använder du följande kablar:

| Starta (sensor)           | End (tavla)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------  |
| VDD (PIN-31F)            | 3V (PIN-58H)           | Röd kabel     |
| DATA (PIN-32F)           | GPIO 2 (PIN-46A)       | Blå kabel    |
| GND (PIN-34F)            | GND (PIn-56I)          | Svart kabel   |

Mer information finns i [ADAFRUIT DHT22 sensor setup](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) and [Adafruit ludd HUZZAH Esp8266 pinouts](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts).

Nu bör din fjädra Huzzah-ESP8266 vara ansluten till en fungerande sensor.

![Ansluta DHT22 med ludd Huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Ansluta fjädra HUZZAH-ESP8266 till datorn

Som du ser härnäst använder du mikrousb för att skriva en USB-kabel för att ansluta fjädra HUZZAH-ESP8266 till datorn.

![Anslut ludd Huzzah till datorn](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Lägg till behörigheter för seriell port (endast Ubuntu)

Om du använder Ubuntu måste du kontrol lera att du har behörighet att använda USB-porten för ludd HUZZAH ESP8266. Följ dessa steg om du vill lägga till behörigheter för seriella portar:

1. Kör följande kommandon i en Terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får en av följande utdata:

   * CRW-RW----1 rot-uucp xxxxxxxx
   * CRW-RW----1 rot anslutning xxxxxxxx

   I utdata, Lägg märke till `uucp` att `dialout` eller är grupp ägarens namn på USB-porten.

2. Lägg till användaren i gruppen genom att köra följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>`är gruppens ägar namn som du fick i föregående steg. `<username>`är ditt Ubuntu användar namn.

3. Logga ut från Ubuntu och logga sedan in igen för att ändringen ska visas.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensor data och skicka dem till din IoT-hubb

I det här avsnittet ska du distribuera och köra ett exempel program på ludd HUZZAH-ESP8266. Exempel programmet blinkar HUZZAH-ESP8266 och skickar temperatur-och fuktighets data som samlas in från DHT22-sensorn till IoT-hubben.

### <a name="get-the-sample-application-from-github"></a>Hämta exempel programmet från GitHub

Exempel programmet finns på GitHub. Klona exempel lagrings platsen som innehåller exempel programmet från GitHub. Följ dessa steg om du vill klona exempel lagrings platsen:

1. Öppna en kommando tolk eller ett terminalfönster.

2. Gå till en mapp där du vill att exempel programmet ska lagras.

3. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

   Sedan installerar du paketet för ludd HUZZAH-ESP8266 i Visual Studio Code.

4. Öppna mappen där exempel programmet lagras.

5. Öppna filen app. INO i mappen app i Visual Studio Code.

   ![Öppna exempel programmet i Visual Studio Code](media/iot-hub-arduino-huzzah-esp8266-get-started/10_vscode-open-sample-app.png)

6. I Visual Studio Code anger `F1`du.

7. Skriv **Arduino** och välj **Arduino: Board Manager**.

8. Klicka på **ytterligare URL: er**på fliken **Arduino Board Manager** .

   ![VS Code Arduino Board Manager](media/iot-hub-arduino-huzzah-esp8266-get-started/11_vscode-arduino-board-manager.png)

9. I fönstret **användar inställningar** kopierar du och klistrar in följande i slutet av filen

   ```json
   "arduino.additionalUrls": "https://arduino.esp8266.com/stable/package_esp8266com_index.json"
   ```

   ![Konfigurera Arduino-paketets URL i VS Code](media/iot-hub-arduino-huzzah-esp8266-get-started/12_vscode-package-url.png)

10. Spara filen och Stäng fliken **användar inställningar** .

11. Klicka på **Uppdatera paket index**. När uppdateringen är klar söker du efter **ESP8266**.

12. Klicka på knappen **Installera** för ESP8266.

    Board Manager anger att ESP8266 med en version av 2.2.0 eller senare är installerad.

    ![ESP8266-paketet har installerats](media/iot-hub-arduino-huzzah-esp8266-get-started/13_vscode-esp8266-installed.png)

13. Ange `F1`, Skriv **Arduino** och välj **Arduino: Tavlans**konfiguration.

14. Klicka på box för **vald tavla:** och skriv **ESP8266**och välj sedan **Adafruit HUZZAH ESP8266 (ESP8266)** .

    ![Välj ESP8266-tavlan](media/iot-hub-arduino-huzzah-esp8266-get-started/14_vscode-select-esp8266.png)

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Visual Studio Code anger `F1`du och skriver **Arduino** och väljer **Arduino: Biblioteks**hanteraren.

2. Sök efter följande biblioteks namn ett i taget. För varje bibliotek som du hittar klickar du på **Installera**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Har du ingen verklig DHT22-sensor?

Exempel programmet kan simulera temperatur-och fuktighets data om du inte har en riktig DHT22-sensor. Följ dessa steg om du vill ställa in exempel programmet för att använda simulerade data:

1. `config.h` Öppna filen`app` i mappen.

2. Leta upp följande rad med kod och ändra värdet från `false` till: `true`

   ```c
   define SIMULATED_DATA true
   ```

   ![Konfigurera exempel programmet att använda simulerade data](media/iot-hub-arduino-huzzah-esp8266-get-started/15_vscode-configure-app-use-simulated-data.png)

3. Spara filen.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Distribuera exempel programmet till ludd HUZZAH ESP8266

1. I Visual Studio Code klickar du på  **\<Välj seriell port >** i statusfältet och klickar sedan på den seriella porten för ludd HUZZAH ESP8266.

2. Ange `F1`, Skriv **Arduino** och välj **Arduino: Ladda** upp för att bygga och distribuera exempel programmet till ludd HUZZAH ESP8266.

### <a name="enter-your-credentials"></a>Ange dina autentiseringsuppgifter

När överföringen är klar följer du de här stegen för att ange dina autentiseringsuppgifter:

1. Öppna Arduino IDE, klicka på **verktyg** > **seriell övervakning**.

2. Lägg märke till de två List rutorna i det nedre högra hörnet i fönstret för seriell övervakning.

3. Välj **Ingen rad i slutet** av den vänstra List rutan.

4. Välj **115200 baud** för den högra List rutan.

5. Ange följande information i rutan indata överst i fönstret för seriell övervakning om du uppmanas att ange dem och klicka sedan på **Skicka**.

   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Enhets anslutnings sträng

> [!Note]
> Informationen om autentiseringsuppgifter lagras i EEPROM HUZZAH-ESP8266. Om du klickar på knappen Återställ på ludd HUZZAH ESP8266-tavlan, frågar exempel programmet om du vill radera informationen. Ange `Y` att informationen ska raderas. Du uppmanas att ange informationen en andra gång.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verifiera att exempel programmet körs

Om du ser följande utdata från fönstret seriella övervakare och blinkande indikator för ludd HUZZAH ESP8266, körs exempel programmet.

![Slutliga utdata i Arduino IDE](media/iot-hub-arduino-huzzah-esp8266-get-started/16_arduino-ide-final-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelanden som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av din IoT Hub från din enhet är att använda Azure IoT-verktyg för Visual Studio Code. Läs mer i [använda Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt om du vill ha fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har anslutit en ludd HUZZAH-ESP8266 till din IoT-hubb och skickat de fångade sensor data till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]