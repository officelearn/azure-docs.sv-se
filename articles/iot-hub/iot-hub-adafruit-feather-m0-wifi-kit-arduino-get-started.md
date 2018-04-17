---
title: 'M0 till molnet: ansluta ludd M0 WiFi till Azure IoT Hub | Microsoft Docs'
description: Lär dig hur du ställer in och ansluta Adafruit ludd M0 WiFi till Azure IoT-hubb för att skicka data till Azure-molnplattform i den här självstudiekursen.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 2a6a65a3c4a69a49788ce9799ceed53d53edcd77
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Ansluta Adafruit ludd M0 WiFi till Azure IoT-hubb i molnet
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Anslutning mellan en BME280 och ludd M0 WiFi IoT-hubb](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

I den här kursen kan du börja genom att lära dig grunderna i att arbeta med Arduino-kort. Du lär dig sedan sömlöst ansluter enheterna till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>Vad du gör

Ansluta Adafruit ludd M0 WiFi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på M0 WiFi samla in data för temperatur- och fuktighetskonsekvens från en BME280. Slutligen kan du skicka dessa sensordata för din IoT-hubb.


## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för Ludd M0 WiFi
* Så här ansluter du ludd M0 WiFi med sensorn och datorn
* Hur du samlar in sensordata genom att köra ett exempelprogram på ludd M0 WiFi
* Hur du skickar dessa sensordata till din IoT-hubb

## <a name="what-you-need"></a>Vad du behöver

![Delar som behövs för kursen](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden behöver du följande delar från din startpaket för Ludd M0 Wi-Fi:

* Ludd M0 WiFi-kort
* En Micro USB till typ A USB-kabel

Du måste också följande saker för din utvecklingsmiljö:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
* En Mac eller en dator som kör Windows eller Ubuntu.
* Ett trådlöst nätverk för Ludd M0 WiFi att ansluta till.
* En Internet-anslutning att hämta verktyget configuration.
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 eller senare. Tidigare versioner fungerar inte med Azure IoT Hub-biblioteket.

Om du inte har en sensor är följande objekt valfria. Du har också möjlighet att använda simulerade sensordata:

* En BME280 temperatur- och fuktighetskonsekvens sensor
* En breadboard
* M/M omkopplare kablar

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Anslut ludd M0 WiFi med sensorn och din dator
I det här avsnittet kan ansluta du sensorerna till kortets. Sedan ansluta du enheten till din dator för framtida användning.

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Ansluta en DHT22 temperatur- och fuktighetskonsekvens sensor till ludd M0 WiFi

Använd breadboard och omkopplare kablar för att ansluta. Om du inte har en sensor hoppa över det här avsnittet, eftersom du kan använda simulerade sensordata i stället.

![Referens för anslutningar](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Använd följande-kablar för sensor PIN:


| Start (sensor)           | END (moderkort)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 27A)            | 3v (3A PIN-kod)            | Röd kabel     |
| JORD (PIN-kod 29 a)            | JORD (6A PIN-kod)           | Svart kabel   |
| SCK (PIN-kod 30A)            | SCK (PIN-kod 12A)          | Gult kabel  |
| SDO (PIN-kod 31A)            | MI (PIN-kod 14A)           | Vita kabeln   |
| SDI (PIN-kod 32 a)            | M0 (PIN-kod 13 a)           | Blå kabeln    |
| CS (PIN-kod 33A)             | GPIO 5 (PIN-kod 15J)       | Orange kabel  |

Mer information finns i [Adafruit BME280 fuktighet barometertrycket + temperatur Sensor nedbrytning](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) och [Adafruit ludd M0 WiFi pinouts](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Ludd M0 Wi-Fi bör nu vara ansluten med en fungerar sensor.

![Anslut DHT22 med ludd Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Ansluta ludd M0 WiFi till din dator

Använda Micro USB till typ A USB-kabel för att ansluta ludd M0 WiFi till datorn, som visas:

![Ansluta ludd Huzzah till din dator](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Lägg till behörigheter för seriell port (Ubuntu)

Om du använder Ubuntu, kontrollera att du har behörighet att fungera på den USB-port för Ludd M0 WiFi. Följ dessa steg för att lägga till behörigheter för seriell port:


1. Kör följande kommandon i en terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får ett av följande utdata:

   * crw-rw---1 rot uucp xxxxxxxx
   * crw-rw---1 rot antingen xxxxxxxx

   Observera att i utdata, `uucp` eller `dialout` är gruppnamn för ägare av USB-port.

2. Lägg till användaren i gruppen genom att köra följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   I föregående steg, som du fick ägare gruppnamnet `<group-owner-name>`. Användarnamnet Ubuntu är `<username>`.

3. Logga ut från Ubuntu och loggar sedan in igen för att ändringen ska visas.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensordata och skicka den till din IoT-hubb

I det här avsnittet, distribuera och köra ett exempelprogram på ludd M0 WiFi. Exempelprogrammet gör Indikator blinka på ludd M0 WiFi. Därefter skickas temperatur- och fuktighetskonsekvens data som samlas in från BME280 sensor till din IoT-hubb.

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>Hämta exempelprogrammet från GitHub och förbereda Arduino IDE

Exempelprogrammet finns på GitHub. Klona lagringsplatsen prov som innehåller exempelprogrammet från GitHub. Följ dessa steg om du vill klona databasen exempel:

1. Öppna en kommandotolk eller ett terminalfönster.

2. Gå till en mapp där du vill att exempelprogrammet ska lagras.
3. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>Installera paketet för Ludd M0 WiFi i Arduino IDE

1. Öppna mappen där exempelprogrammet lagras.

2. Öppna filen app.ino i mappen app i Arduino IDE.

   ![Öppna exempelprogrammet i Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. Klicka på **filen** > **inställningar** (Windows-/ Linux) eller **Arduino** > **inställningar** (Mac) och kopiera och Klistra in en länk nedan i den **ytterligare anslagstavlor Manager URL: er** alternativet i Arduino IDE-inställningar.
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. Klicka på **verktyg** > **Hanteringsstyrenhetens** > **anslagstavlor Manager**, och sedan installera den `Arduino SAMD Boards` version `1.6.2` eller senare. 

1. Installera sedan i fönstret samma `Adafruit SAMD Boards` paket för att lägga till kort definitioner.

   ![Esp8266 paketet har installerats](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. Klicka på **verktyg** > **Hanteringsstyrenhetens** > **Adafruit M0 WiFi**.

5. Installera drivrutiner (för Windows). När du ansluter ludd M0 WiFi, kan du behöva installera en drivrutin. Klicka på [länken på webbsidan](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) att hämta installationsprogrammet för drivrutinen. Följ stegen för att installera drivrutiner som du vill använda.

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Arduino IDE, klickar du på **skiss** > **innehåller biblioteket** > **Hantera bibliotek**.

2. Sök efter följande biblioteket namn i taget. Klicka på för alla bibliotek som du hittar **installera**:

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. Installera manuellt `Adafruit_WINC1500`. Gå till [webbplatsen](https://github.com/adafruit/Adafruit_WINC1500) och på **kloning eller hämta** > **hämta ZIP**. Gå till i din Arduino IDE **skiss** > **innehåller biblioteket** > **lägger du till .zip biblioteket** och Lägg till zip-filen.

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>Använd exempelprogrammet om du inte har en verklig BME280 sensor

Om du inte har en verklig BME280 sensor simulera exempelprogrammet temperatur- och fuktighetskonsekvens data. Följ dessa steg om du vill konfigurera exempelprogrammet använda simulerade data:

1. Öppna den `config.h` filen i den `app` mapp.

2. Leta upp följande kodrad och ändrar du värdet från `false` till `true`:

   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurera exempelprogrammet använda simulerade data](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. Spara filen med `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Distribuera exempelprogrammet till ludd M0 WiFi

1. Klicka i IDE Arduino **verktyget** > **Port**, och klicka sedan på den seriella porten för Ludd M0 WiFi.

2. Klicka på **skiss** > **överför** att skapa och distribuera exempelprogrammet till ludd M0 WiFi.

### <a name="enter-your-credentials"></a>Ange autentiseringsuppgifter

När överföringen är klar följer du stegen nedan för att ange dina autentiseringsuppgifter:

1. Klicka i IDE Arduino **verktyg** > **seriella övervakaren**.

2. I det nedre högra hörnet i fönstret seriella väljer **ingen rad avslutas** i den nedrullningsbara listan till vänster.
3. Välj **115200 baud** i den nedrullningsbara listan till höger.
4. Ange följande information i textrutan längst upp om du uppmanas att ange den och klicka på **skicka**:

   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Anslutningssträngen för enhet

> [!Note]
> Information om autentiseringsuppgifter lagras i EEPROM av ludd M0 WiFi. Om du klickar på återställningsknappen på ludd M0 WiFi-kort frågar exempelprogrammet om du vill radera informationen. Ange `Y` att radera informationen. Du uppmanas att ange informationen som en andra gång.

### <a name="verify-that-the-sample-application-is-running-successfully"></a>Kontrollera att exempelprogrammet körts

Om du ser i följande utdata från den seriella övervakningsfönstret och blinkande Indikator på ludd M0 WiFi exempelprogrammet körts:

![Slutversionen i Arduino IDE](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Nästa steg

Du har anslutna ludd M0 WiFi till din IoT-hubb och skickas avbildade sensordata till din IoT-hubb. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

