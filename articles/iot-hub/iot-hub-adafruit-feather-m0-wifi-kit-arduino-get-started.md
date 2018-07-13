---
title: M0 till molnet – Anslut Feather M0 WiFi till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Adafruit Feather M0 WiFi i Azure IoT Hub att skicka data till Azure-molnplattformen i den här självstudien.
author: rangv
manager: nasing
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6899bbd294a16dee3a767e976a92deaa00f0e2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676700"
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Anslut Adafruit Feather M0 WiFi till Azure IoT Hub i molnet
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Anslutning mellan en BME280 och Feather M0 WiFi IoT-hubb](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Arduino-kort. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Vad du gör

Anslut Adafruit Feather M0 WiFi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på M0 WiFi att samla in temperatur och fuktighet data från en BME280. Slutligen kan skicka du sensordata till din IoT hub.


## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för Feather M0 WiFi
* Så här ansluter du Feather M0 WiFi med sensorn och din dator
* Hur du samlar in sensordata genom att köra ett exempelprogram på Feather M0 WiFi
* Hur du skickar sensordata till din IoT-hubb

## <a name="what-you-need"></a>Vad du behöver

![Delar som behövs för den här självstudien](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden, behöver du följande delar från din Feather M0 WiFi Starter-paket:

* Feather M0 WiFi-tavla
* En Micro USB till typ A USB-kabel

Du måste också följande för din utvecklingsmiljö:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.
* En Mac eller en dator som kör Windows eller Ubuntu.
* Ett trådlöst nätverk för Feather M0 WiFi att ansluta till.
* En Internetanslutning att hämta konfigurationsverktyget.
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 eller senare. Tidigare versioner fungerar inte med Azure IoT Hub-biblioteket.

Om du inte har en sensor är följande objekt valfria. Du kan också ha alternativet att använda simulerade sensordata:

* En BME280 temperatur och fuktighet sensor
* En breadboard
* M/M-omkopplare kablar

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Träffa sensorn och datorn Feather M0 WiFi
I det här avsnittet ska ansluta du sensorerna till din tavla. Sedan ansluter du din enhet till din dator för ytterligare användning.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Ansluta en DHT22 temperatur och fuktighet sensor till Feather M0 WiFi

Använd breadboard och omkopplare kablar för att upprätta anslutningen. Om du inte har en sensor kan du hoppa över det här avsnittet eftersom du kan använda simulerade sensordata i stället.

![Referens för anslutningar](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


För sensor PIN-koder, använder du följande koaxialkabel:


| Start (sensor)           | Slutpunkt (board)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 27A)            | 3v (PIN-kod 3A)            | Röd-kabel     |
| JORD (PIN-kod 29 a)            | JORD (PIN-kod 6A)           | Svarta kabeln   |
| SCK (PIN-kod 30A)            | SCK (PIN-kod 12A)          | Gul-kabel  |
| SDO (PIN-kod 31A)            | MI (PIN-kod 14A)           | Vit-kabel   |
| SDI (PIN-kod 32 a)            | M0 (PIN-kod 13 a)           | Blå kabeln    |
| CS (PIN-kod 33A)             | GPIO 5 (PIN-kod 15J)       | Orange kabel  |

Mer information finns i [Adafruit BME280 fuktighet + barometertrycket + temperatur Sensor seminarium om](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) och [Adafruit Feather M0 WiFi pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Feather M0 WiFi bör nu vara ansluten med en fungerande sensor.

![Träffa Feather Huzzah DHT22](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Ansluta Feather M0 WiFi till din dator

Använda Micro USB till typ A USB-kabel för att ansluta Feather M0 WiFi till datorn, som visas:

![Ansluta Feather Huzzah till din dator](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Lägg till behörigheter för seriell port (endast Ubuntu)

Om du använder Ubuntu kan du kontrollera att du har behörighet att fungera på den USB-port av Feather M0 WiFi. Följ dessa steg för att lägga till behörigheter för seriell port:


1. Kör följande kommandon i en terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får något av följande utdata:

   * crw-rw---1 rot uucp xxxxxxxx
   * crw-rw---1 rot antingen xxxxxxxx

   Observera att utdata och `uucp` eller `dialout` är gruppnamn för ägare av USB-port.

2. Om du vill lägga till användaren i gruppen, kör du följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   I föregående steg, som du fick ägare gruppnamn `<group-owner-name>`. Användarnamnet Ubuntu är `<username>`.

3. Logga ut från Ubuntu och logga sedan in igen för att ändringen ska visas.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensordata och skicka den till din IoT-hubb

I det här avsnittet ska du distribuera och köra ett exempelprogram på Feather M0 WiFi. Exempelprogrammet blir LED blinkningshastighet på Feather M0 WiFi. Den skickas data sedan temperaturen och fuktigheten som samlats in från BME280 sensorn till din IoT hub.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Hämta exempelprogrammet från GitHub och Förbered Arduino IDE

Exempelprogrammet finns på GitHub. Klona exempellagringsplatsen med exempelprogrammet från GitHub. Följ dessa steg för att klona exempellagringsplatsen:

1. Öppna en kommandotolk eller ett terminalfönster.

2. Gå till en mapp där du vill att exempelprogrammet som ska lagras.
3. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Installera paketet för Feather M0 WiFi i Arduino IDE

1. Öppna mappen där exempelprogrammet lagras.

2. Öppna filen app.ino i mappen i Arduino IDE.

   ![Öppna exempelprogrammet i Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Klicka på **filen** > **inställningar** (Windows/Linux) eller **Arduino** > **inställningar** (Mac) och kopiera och Klistra in länken nedan till det **ytterligare tavlor Manager URL: er** alternativ i Arduino IDE-inställningar.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Klicka på **verktyg** > **tavla** > **tavlor Manager**, och sedan installera den `Arduino SAMD Boards` version `1.6.2` eller senare. 

1. Installera sedan i fönstret samma `Adafruit SAMD Boards` paketet för att lägga till tavlan filen definitioner.

   ![Esp8266-paket installeras](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Klicka på **verktyg** > **tavla** > **Adafruit M0 WiFi**.

5. Installera drivrutiner (för Windows). När du ansluter Feather M0 WiFi, kan du behöva installera en drivrutin. Klicka på [länken på webbsidan](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) att hämta installationsprogrammet för drivrutinen. Följ stegen för att installera drivrutiner som du vill.

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Arduino IDE, klickar du på **skiss** > **inkluderar biblioteket** > **Hantera bibliotek**.

2. Sök efter följande biblioteket namn i taget. För alla bibliotek som du hittar, klickar du på **installera**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Installera manuellt `Adafruit_WINC1500`. Gå till [den här webbplatsen](https://github.com/adafruit/Adafruit_WINC1500) och klicka på **klona eller ladda ned** > **ladda ned ZIP**. Gå till i din Arduino IDE **skiss** > **inkluderar biblioteket** > **lägger du till .zip biblioteket** och Lägg till zip-filen.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Använda exempelprogrammet om du inte har en verklig BME280 sensor

Om du inte har en verklig sensor BME280 simulera exempelprogrammet temperatur- och data. Så här konfigurerar du exempelprogrammet till att använda simulerade data genom:

1. Öppna den `config.h` fil i den `app` mapp.

2. Leta upp följande rad med kod och ändra värdet från `false` till `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurera exempelprogrammet för att använda simulerade data](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Spara filen med `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Distribuera exempelprogrammet för att Feather M0 WiFi

1. I Arduino IDE, klickar du på **verktyget** > **Port**, och klicka sedan på den seriella porten för Feather M0 WiFi.

2. Klicka på **skiss** > **överför** att bygga och distribuera exempelprogrammet för att Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Ange autentiseringsuppgifter

När överföringen är klar följer du stegen nedan för att ange dina autentiseringsuppgifter:

1. I Arduino IDE, klickar du på **verktyg** > **seriell övervakaren**.

2. I det nedre högra hörnet i fönstret seriell väljer **inga raden slutar** i den nedrullningsbara listan till vänster.
3. Välj **115200 baud** i den nedrullningsbara listan till höger.
4. Ange följande information i textrutan längst upp om du uppmanas att ange den och klicka på **skicka**:

   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Enhetens anslutningssträng

> [!Note]
> Autentiseringsuppgifterna lagras i EEPROM av Feather M0 WiFi. Om du klickar på återställningsknappen på tavlan Feather M0 WiFi exempelprogrammet som frågar om du vill radera informationen. Ange `Y` att radera informationen. Du uppmanas att ange information om en andra gång.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Kontrollera att exempelprogrammet körts

Om du ser i följande utdata från seriell övervakningsfönstret och blinkande Indikator på Feather M0 WiFi exempelprogrammet körts:

![Slutgiltiga utdata i Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Nästa steg

Du har anslutit Feather M0 WiFi till din IoT-hubb och skickas de hämtade sensordata till din IoT-hubb. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

