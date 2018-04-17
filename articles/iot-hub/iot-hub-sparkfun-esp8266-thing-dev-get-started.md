---
title: ESP8266 till molnet - ansluta Sparkfun ESP8266 sak Dev till Azure IoT Hub | Microsoft Docs
description: Lär dig mer om att konfigurera och ansluta Sparkfun ESP8266 sak Dev till Azure IoT-hubb för att skicka data till Azure-molnplattform i den här självstudiekursen.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: ''
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: e6837d0312217d8e27e3639b8220f5016a2505a6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Ansluta Sparkfun ESP8266 sak Dev till Azure IoT-hubb i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![anslutningen mellan DHT22 och sak Dev IoT-hubb](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Vad du ska göra

Ansluta Sparkfun ESP8266 sak Dev till en IoT-hubb skapar du. Kör sedan ett exempelprogram på ESP8266 att samla in data för temperatur- och fuktighetskonsekvens från en DHT22 sensor. Slutligen skicka sensordata till din IoT-hubb.

> [!NOTE]
> Du kan följa stegen nedan för att ansluta till din IoT-hubb även om du använder andra ESP8266: er. Beroende på ESP8266-kort som du använder, kan du behöva konfigurera om den `LED_PIN`. Till exempel om du använder ESP8266 från AI-Thinker, du kan ändra den från `0` till `2`. Har ett kit än?: Klicka på [här](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Vad får du lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för sak Utvecklartestning
* Så här ansluter du sak Dev med sensorn och din dator.
* Hur du samlar in sensordata genom att köra ett exempelprogram på sak Utvecklartestning
* Hur du skickar dessa sensordata till din IoT-hubb.

## <a name="what-you-will-need"></a>Vad du behöver

![Delar som behövs för kursen](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden behöver du följande delar från din sak Dev startpaket:

* Sparkfun ESP8266 sak Dev-kort.
* En Micro USB till typ A USB-kabel.

Du måste också följande för din utvecklingsmiljö:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
* Eller Mac-dator som kör Windows eller Ubuntu.
* Trådlöst nätverk för Sparkfun ESP8266 sak Dev att ansluta till.
* Internet-anslutning att hämta verktyget configuration.
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 (eller senare), tidigare versioner fungerar inte med AzureIoT-biblioteket.

Följande objekt är valfritt om du inte har en sensor. Du har också möjlighet att använda simulerade sensordata.

* En Adafruit DHT22 temperatur- och fuktighetskonsekvens sensor.
* En breadboard.
* M/M omkopplare kablar.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Anslut ESP8266 sak Dev med sensorn och din dator

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Ansluta en DHT22 temperatur- och fuktighetskonsekvens sensor till ESP8266 sak Dev

Använd breadboard och omkopplare kablar för att ansluta på följande sätt. Om du inte har en sensor hoppa över det här avsnittet, eftersom du kan använda simulerade sensordata i stället.

![Referens för anslutningar](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Vi använder följande kablar för sensor PIN:

| Starta (Sensor)           | END (moderkort)           | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 27F)            | 3v (8A PIN-kod)           | Röd kabel     |
| DATA (PIN-kod 28 f)           | GPIO 2 (9A PIN-kod)       | Vita kabeln    |
| JORD (PIN-kod 30 f)            | JORD (7J PIN-kod)          | Svart kabel   |


- Mer information finns: [DHT22 sensor installationsprogrammet](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) och [Sparkfun ESP8266 sak Dev-specifikationen](https://www.sparkfun.com/products/13711)

Nu bör din Sparkfun ESP8266 sak Dev anslutas med en fungerar sensor.

![Anslut dht22 med ESP8266 sak Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Anslut Sparkfun ESP8266 sak Dev till datorn

Använd Micro USB till typ A USB-kabel för att ansluta Sparkfun ESP8266 sak Dev till datorn enligt följande.

![ansluta ludd huzzah till din dator](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Lägg till behörigheter för seriell port – Ubuntu endast

Om du använder Ubuntu, kontrollera att normal användare har behörighet att fungera på den USB-port för Sparkfun ESP8266 sak Utvecklartestning Så här lägger du till seriell port behörigheter för en normal användare:

1. Kör följande kommandon i en terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får ett av följande utdata:

   * crw-rw---1 rot uucp xxxxxxxx
   * crw-rw---1 rot antingen xxxxxxxx

   Utdata och notera `uucp` eller `dialout` som ägare gruppnamnet för USB-port.

1. Lägg till användaren i gruppen genom att köra följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` är ägare gruppnamnet du hämtade i föregående steg. `<username>` är din Ubuntu användarnamn.

1. Logga ut Ubuntu och logga in igen för att ändringarna ska börja gälla.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensordata och skicka den till din IoT-hubb

I det här avsnittet kan du distribuera och köra ett exempelprogram på Sparkfun ESP8266 sak Utvecklartestning Exempelprogrammet blinkar Indikator på Sparkfun ESP8266 sak Dev och skickar temperatur- och fuktighetskonsekvens data som samlas in från DHT22 sensor till din IoT-hubb.

### <a name="get-the-sample-application-from-github"></a>Hämta exempelprogrammet från GitHub

Exempelprogrammet finns på GitHub. Klona lagringsplatsen prov som innehåller exempelprogrammet från GitHub. Följ dessa steg om du vill klona databasen exempel:

1. Öppna en kommandotolk eller ett terminalfönster.
1. Gå till en mapp där du vill att exempelprogrammet ska lagras.
1. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Installera paketet för Sparkfun ESP8266 sak Dev i Arduino IDE:

1. Öppna mappen där exempelprogrammet lagras.
1. Öppna filen app.ino i mappen app i Arduino IDE.

   ![Öppna exempelprogrammet i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. Klicka i IDE Arduino **filen** > **inställningar**.
1. I den **inställningar** dialogrutan klickar du på ikonen bredvid den **ytterligare anslagstavlor Manager URL: er** textruta.
1. Ange följande URL i popup-fönstret och klicka sedan på **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Peka på en paket-url i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. I den **inställningar** dialogrutan klickar du på **OK**.
1. Klicka på **verktyg** > **Hanteringsstyrenhetens** > **anslagstavlor Manager**, och sök sedan efter esp8266.
   ESP8266 med en version av 2.2.0 eller senare ska installeras.

   ![Esp8266 paketet har installerats](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Klicka på **verktyg** > **Hanteringsstyrenhetens** > **Sparkfun ESP8266 sak Dev**.

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Arduino IDE, klickar du på **skiss** > **innehåller biblioteket** > **Hantera bibliotek**.
1. Sök efter följande biblioteket namn i taget. För var och en av biblioteket som du hittar klickar **installera**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Har en verklig DHT22 sensor?

Exempelprogrammet simulera temperatur- och fuktighetskonsekvens data om du inte har en verklig DHT22 sensor. Följ dessa steg om du vill aktivera exempelprogrammet använda simulerade data:

1. Öppna den `config.h` filen i den `app` mapp.
1. Leta upp följande kodrad och ändrar du värdet från `false` till `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurera exempelprogrammet använda simulerade data](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Spara med `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Distribuera exempelprogrammet till Sparkfun ESP8266 sak Dev

1. Klicka i IDE Arduino **verktyget** > **Port**, och klicka sedan på den seriella porten för Sparkfun ESP8266 sak Utvecklartestning
1. Klicka på **skiss** > **överför** att skapa och distribuera exempelprogrammet till Sparkfun ESP8266 sak Utvecklartestning

> [!Note]
> Om du använder macOS kunde du antagligen se följande meddelanden under överföring. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Öppna fönstret ternimal och slutför under åtgärder för att lösa problemet.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Ange autentiseringsuppgifter

När överföringen är klar följer du stegen för att ange dina autentiseringsuppgifter:

1. Klicka i IDE Arduino **verktyg** > **seriella övervakaren**.
1. Observera två listrutorna i nederkant högra hörnet i fönstret seriella monitor.
1. Välj **ingen rad avslutas** för den vänstra listrutan.
1. Välj **115200 baud** för höger nedrullningsbara listan.
1. Ange följande information i inmatningsrutan som visas överst i fönstret seriell, om du uppmanas att ange dem och klickar sedan på **skicka**.
   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Anslutningssträngen för enhet

> [!Note]
> Information om autentiseringsuppgifter lagras i den EEPROM av Sparkfun ESP8266 sak Utvecklartestning Om du klickar på återställningsknappen på Sparkfun ESP8266 sak Dev-kort ber exempelprogrammet dig om du vill radera informationen. Ange `Y` för att ha informationen raderas och du uppmanas att ange informationen igen.

### <a name="verify-the-sample-application-is-running-successfully"></a>Kontrollera exempelprogrammet körts

Om du ser i följande utdata från den seriella övervakningsfönstret och blinkande Indikator på Sparkfun ESP8266 sak Dev exempelprogrammet körts.

![slutversionen i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Nästa steg

Du har anslutit en Sparkfun ESP8266 sak Dev till din IoT-hubb och skickas avbildade sensordata till din IoT-hubb. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
