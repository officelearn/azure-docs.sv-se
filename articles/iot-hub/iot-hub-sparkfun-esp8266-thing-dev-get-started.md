---
title: ESP8266 till molnet – ansluta Sparkfun ESP8266 Thing Dev på Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Sparkfun ESP8266 Thing Dev på Azure IoT Hub för att skicka data till Azure-molnplattformen i den här självstudien.
author: rangv
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 75ff53d5be29af08bb8e9c1b41f61040e5710cf7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452523"
---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Anslut Sparkfun ESP8266 Thing Dev till Azure IoT Hub i molnet

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![anslutning mellan DHT22 Thing Dev och IoT Hub](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>Vad du ska göra

Anslut Sparkfun ESP8266 Thing Dev till en IoT-hubb som du skapar. Därefter kör ett exempelprogram ESP8266 att samla in temperatur och fuktighet data från en DHT22 sensor. Slutligen skicka sensordata till din IoT-hubb.

> [!NOTE]
> Du kan följa stegen nedan för att ansluta till din IoT hub även om du använder andra ESP8266-kort. Beroende på den ESP8266 tavla som du använder kan du behöva konfigurera om den `LED_PIN`. Till exempel om du använder ESP8266 från AI-Thinker, du kan ändra den från `0` till `2`. Har ett paket än?: Klicka på [här](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>Vad du får lära dig

* Hur du skapar en IoT-hubb och registrera en enhet för sak utveckling.
* Så här att ansluta Thing Dev med sensorn och datorn.
* Hur du samlar in sensordata genom att köra ett exempelprogram på sak utveckling.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-will-need"></a>Vad du behöver

![Delar som behövs för den här självstudien](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

För att slutföra den här åtgärden, behöver du följande delar från Thing Dev Starter-paket:

* Sparkfun ESP8266 Thing Dev-kortet.
* En Micro USB till typ A USB-kabel.

Du måste också följande för din utvecklingsmiljö:

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.
* Eller Mac-dator som kör Windows eller Ubuntu.
* Sparkfun ESP8266 Thing Dev att ansluta till trådlöst nätverk.
* Internet-anslutning att hämta konfigurationsverktyget.
* [Arduino IDE](https://www.arduino.cc/en/main/software) version 1.6.8 (eller senare), tidigare versioner kommer inte att fungera med AzureIoT-biblioteket.

Följande objekt är valfritt om du inte har en sensor. Du har också möjlighet att använda simulerade sensordata.

* En Adafruit DHT22 temperatur och fuktighet sensorn.
* En breadboard.
* M/M-omkopplare kablar.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Anslut ESP8266 Thing Dev med sensorn och din dator

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Ansluta en DHT22 temperatur och fuktighet sensor till ESP8266 Thing Dev

Använd breadboard och omkopplare kablar för att upprätta anslutningen på följande sätt. Om du inte har en sensor kan du hoppa över det här avsnittet eftersom du kan använda simulerade sensordata i stället.

![Referens för anslutningar](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

För sensor PIN-koder, kommer vi att använda följande koaxialkabel:

| Starta (Sensor)           | Slutpunkt (Board)           | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 27F)            | 3v (PIN-kod 8A)           | Röd-kabel     |
| DATA (PIN-kod 28 f)           | GPIO 2 (PIN-kod 9A)       | Vit-kabel    |
| JORD (PIN-kod 30 f)            | JORD (PIN-kod 7J)          | Svarta kabeln   |


- Mer information finns i: [DHT22 sensorkonfiguration](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) och [Sparkfun ESP8266 Thing Dev-specifikation](https://www.sparkfun.com/products/13711)

Nu bör din Sparkfun ESP8266 Thing Dev anslutas till en fungerande sensor.

![träffa ESP8266 Thing Dev dht22](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Sparkfun ESP8266 Thing Dev ansluta till din dator

Använda Micro USB till typ A USB-kabel för att ansluta Sparkfun ESP8266 Thing Dev till din dator på följande sätt.

![ansluta feather huzzah till din dator](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Lägg till behörigheter för seriell port – endast Ubuntu

Om du använder Ubuntu, se till att en normal användare har behörighet att fungera på den USB-port av Sparkfun ESP8266 Thing utveckling. Lägg till seriell port behörigheter för en normal användare genom att följa dessa steg:

1. Kör följande kommandon i en terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Du får något av följande utdata:

   * crw-rw---1 rot uucp xxxxxxxx
   * crw-rw---1 rot antingen xxxxxxxx

   Utdata och notera `uucp` eller `dialout` som är ägare gruppnamnet av USB-port.

1. Lägga till användaren i gruppen genom att köra följande kommando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` är grupp ägarens namn som du hämtade i föregående steg. `<username>` är ditt Ubuntu-användarnamn.

1. Logga ut Ubuntu och logga in den igen för att ändringen ska börja gälla.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Samla in sensordata och skicka den till din IoT-hubb

I det här avsnittet ska du distribuera och köra ett exempelprogram på Sparkfun ESP8266 Thing utveckling. Exempelprogrammet blinkar Indikator på Sparkfun ESP8266 Thing Dev och skickar temperatur- och data som samlas in från DHT22 sensorn till din IoT hub.

### <a name="get-the-sample-application-from-github"></a>Hämta exempelprogrammet från GitHub

Exempelprogrammet finns på GitHub. Klona exempellagringsplatsen med exempelprogrammet från GitHub. Följ dessa steg för att klona exempellagringsplatsen:

1. Öppna en kommandotolk eller ett terminalfönster.
1. Gå till en mapp där du vill att exempelprogrammet som ska lagras.
1. Kör följande kommando:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Installera paketet för Sparkfun ESP8266 Thing Dev i Arduino IDE:

1. Öppna mappen där exempelprogrammet lagras.
1. Öppna filen app.ino i mappen i Arduino IDE.

   ![Öppna exempelprogrammet i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. I Arduino IDE, klickar du på **filen** > **inställningar**.
1. I den **inställningar** dialogrutan klickar du på ikonen bredvid den **ytterligare tavlor Manager URL: er** textrutan.
1. Ange följande URL i popup-fönstret och klicka sedan på **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![Peka på en paket-url i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. I den **inställningar** dialogrutan klickar du på **OK**.
1. Klicka på **verktyg** > **tavla** > **tavlor Manager**, och söker sedan efter esp8266.
   ESP8266 med en version av 2.2.0 eller senare ska installeras.

   ![Esp8266-paket installeras](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Klicka på **verktyg** > **tavla** > **Sparkfun ESP8266 Thing Dev**.

### <a name="install-necessary-libraries"></a>Installera nödvändiga bibliotek

1. I Arduino IDE, klickar du på **skiss** > **inkluderar biblioteket** > **Hantera bibliotek**.
1. Sök efter följande biblioteket namn i taget. På varje i biblioteket som du hittar **installera**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Har du en verklig sensor DHT22?

Exempelprogrammet simulera temperatur och fuktighet data om du inte har en verklig DHT22 sensor. Så här aktiverar du exempelprogrammet till att använda simulerade data, följer du dessa steg:

1. Öppna den `config.h` fil i den `app` mapp.
1. Leta upp följande rad med kod och ändra värdet från `false` till `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Konfigurera exempelprogrammet för att använda simulerade data](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Spara med `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Distribuera exempelprogrammet för att Sparkfun ESP8266 Thing Dev

1. I Arduino IDE, klickar du på **verktyget** > **Port**, och klicka sedan på den seriella porten för Sparkfun ESP8266 Thing utveckling.
1. Klicka på **skiss** > **överför** att bygga och distribuera exempelprogrammet för att Sparkfun ESP8266 Thing utveckling.

> [!Note]
> Om du använder macOS kan du antagligen se följande meddelanden under uppladdningen. `warning: espcomm_sync failed`,`error: espcomm_open failed`. Öppna fönstret ternimal och slutför nedan åtgärder för att lösa problemet.
> ```bash
> cd /System/Library/Extensions/IOUSBFamily.kext/Contents/PlugIns
> sudo mv AppleUSBFTDI.kext AppleUSBFTDI.disabled
> sudo touch /System/Library/Extensions
> ```

### <a name="enter-your-credentials"></a>Ange autentiseringsuppgifter

När överföringen är klar följer du stegen för att ange dina autentiseringsuppgifter:

1. I Arduino IDE, klickar du på **verktyg** > **seriell övervakaren**.
1. Observera de två listorna på nederkant högra hörnet i fönstret seriell monitor.
1. Välj **inga raden slutar** för den vänstra listan.
1. Välj **115200 baud** för just nedrullningsbara listan.
1. Ange följande information i textrutan längst upp i fönstret seriell, om du uppmanas att ange dem och klicka sedan på **skicka**.
   * Wi-Fi SSID
   * Wi-Fi-lösenord
   * Enhetens anslutningssträng

> [!Note]
> Autentiseringsuppgifterna lagras i den EEPROM av Sparkfun ESP8266 Thing utveckling. Om du klickar på återställningsknappen på Sparkfun ESP8266 Thing Dev-kortet frågar exempelprogrammet om du vill radera informationen. Ange `Y` ha informationen tas bort och du uppmanas att ange informationen igen.

### <a name="verify-the-sample-application-is-running-successfully"></a>Kontrollera exempelprogrammet körts

Exempelprogrammet körs om du ser i följande utdata från seriell övervakningsfönstret och blinkande Indikator på Sparkfun ESP8266 Thing Dev.

![slutgiltiga utdata i arduino ide](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Nästa steg

Du har anslutit en Sparkfun ESP8266 Thing Dev till din IoT-hubb och skickas de hämtade sensordata till din IoT-hubb. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
