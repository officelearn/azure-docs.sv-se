---
title: Anslut Raspberry Pi till Azure IoT Hub i molnet (Node.js)
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi för att skicka data till Azure-molnplattformen i den här självstudien.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi skicka data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 7c32ae73f065aa5cd1d0dabec421d354684fbb3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371517"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Anslut Raspberry Pi till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi som kör Raspbian. Du lär dig sedan hur du sömlöst ansluter dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel går du till [Windows Dev Center](https://www.windowsondevices.com/).

Har du inget kit än? Prova [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp ett nytt kit [här.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.

* Registrera en enhet för Pi i din IoT-hubb.

* Sätt upp Raspberry Pi.

* Kör ett exempelprogram på Pi för att skicka sensordata till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhetsanslutningssträng.

* Hur man ansluter Pi med en BME280 sensor.

* Så här samlar du in sensordata genom att köra ett exempelprogram på Pi.

* Så här skickar du sensordata till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* En Raspberry Pi 2 eller Raspberry Pi 3 ombord.

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

* En bildskärm, ett USB-tangentbord och en mus som ansluter till Pi.

* En Mac eller PC som kör Windows eller Linux.

* En internetuppkoppling.

* Ett 16 GB eller högre microSD-kort.

* En USB-SD-adapter eller microSD-kort för att bränna operativsystembilden på microSD-kortet.

* En 5-volts 2-amp strömförsörjning med 6-fots mikro USB-kabel.

Följande objekt är valfria:

* En monterad Adafruit BME280 temperatur, tryck och fuktighetssensor.

* En brödbräda.

* 6 F/M bygeltrådar.

* En diffus 10 mm LED.

> [!NOTE]
> Om du inte har de valfria objekten kan du använda simulerade sensordata.

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Ställ in Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kortet för installation av Raspbian-avbildningen.

1. Ladda ner Raspbian.

   a. [Raspbian Buster med skrivbordet](https://www.raspberrypi.org/downloads/raspbian/) (ZIP-filen).

   b. Extrahera Raspbian-avbildningen till en mapp på datorn.

2. Installera Raspbian på microSD-kortet.

   a. [Ladda ner och installera Etcher SD-kort brännare verktyget](https://etcher.io/).

   b. Kör Etcher och välj Den Raspbian-avbildning som du extraherade i steg 1.

   c. Välj microSD-kortenheten. Etcher kan redan ha valt rätt enhet.

   d. Klicka på Flash för att installera Raspbian på microSD-kortet.

   e. Ta bort microSD-kortet från datorn när installationen är klar. Det är säkert att ta bort microSD-kortet direkt eftersom Etcher automatiskt matar ut eller avmonterar microSD-kortet när det är klart.

   f. Sätt i microSD-kortet i Pi.

### <a name="enable-ssh-and-i2c"></a>Aktivera SSH och I2C

1. Anslut Pi till bildskärmen, tangentbordet och musen.

2. Starta Pi och logga sedan in `pi` på Raspbian med användarnamn och `raspberry` lösenord.

3. Klicka på raspberry-ikonen > **Inställningar** > **Raspberry Pi-konfiguration**.

   ![Menyn Raspbian-inställningar](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. På fliken **Gränssnitt** ställer du in **I2C** och **SSH** för **att aktivera**och klickar sedan på **OK**. Om du inte har fysiska sensorer och vill använda simulerade sensordata är det här steget valfritt.

   ![Aktivera I2C och SSH på Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om du vill aktivera SSH och I2C hittar du fler referensdokument [på raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Anslut sensorn till Pi

Använd brödbrädan och bygeltrådarna för att ansluta en LED och en BME280 till Pi enligt följande. Om du inte har sensorn [hoppar du över det här avsnittet](#connect-pi-to-the-network).

![Raspberry Pi och sensoranslutning](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280-sensorn kan samla in temperatur- och fuktighetsdata. Lysdioden blinkar när enheten skickar ett meddelande till molnet.

För sensorstift använder du följande ledningar:

| Start (Sensor & LED)     | (styrelse)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (stift 5G)             | 3.3V PWR (Stift 1)       | Vit kabel   |
| GND (stift 7G)             | GND (stift 6)            | Brun kabel   |
| SDI (Stift 10G)            | I2C1 SDA (stift 3)       | Röd kabel     |
| SCK (Stift 8G)             | I2C1 SCL (stift 5)       | Orange kabel  |
| LED-VDD (stift 18F)        | GPIO 24 (Stift 18)       | Vit kabel   |
| LED GND (stift 17F)        | GND (stift 20)           | Svart kabel   |

Klicka här om du vill visa [Raspberry Pi 2 & 3-stiftsmappningar](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) som referens.

När du har anslutit BME280 till din Raspberry Pi, bör det vara som nedan bild.

![Ansluten Pi och BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Anslut Pi till nätverket

Slå på Pi med hjälp av mikro-USB-kabeln och strömförsörjningen. Använd Ethernet-kabeln för att ansluta Pi till ditt kabelanslutna nätverk eller följ [instruktionerna från Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) för att ansluta Pi till ditt trådlösa nätverk. När din Pi har anslutits till nätverket måste du ta del av [IP-adressen](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)för din Pi .

![Ansluten till kabelanslutet nätverk](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Kontrollera att Pi är ansluten till samma nätverk som datorn. Om datorn till exempel är ansluten till ett trådlöst nätverk medan Pi är ansluten till ett kabelanslutet nätverk kanske du inte ser IP-adressen i devdisco-utdata.

## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klona exempelprogram och installera nödvändiga paket

1. Anslut till din Raspberry Pi med någon av följande SSH-klienter från värddatorn:

   **Windows-användare**

   a. Ladda ned och installera [PuTTY](https://www.putty.org/) för Windows.

   b. Kopiera din Pi-IP-adress till avsnittet Värdnamn (eller IP-adress) och välj SSH som anslutningstyp.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- och Ubuntu-användare**

   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva `ssh pi@<ip address of pi>` köra för att ansluta Pi via SSH.

   > [!NOTE]
   > Standardanvändarnamnet `pi` är och `raspberry`lösenordet är .

2. Installera Node.js och NPM till din Pi.

   Kontrollera först din Node.js-version.

   ```bash
   node -v
   ```

   Om versionen är lägre än 10.x, eller om det inte finns någon Node.js på din Pi, installerar du den senaste versionen.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klona exempelprogrammet.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Installera alla paket för exemplet. Installationen inkluderar Azure IoT-enhet SDK, BME280 Sensor bibliotek och Wiring Pi bibliotek.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Det kan ta flera minuter att slutföra den här installationsprocessen beroende på nätverksanslutningen.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Öppna konfigurationsfilen genom att köra följande kommandon:

   ```bash
   nano config.json
   ```

   ![Config-fil](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   Det finns två objekt i den här filen som du kan konfigurera. Den första `interval`är , som definierar tidsintervallet (i millisekunder) mellan meddelanden som skickas till molnet. Den andra `simulatedData`är , vilket är ett booleskt värde för om man ska använda simulerade sensordata eller inte.

   Om du **inte har sensorn** `simulatedData` ställer `true` du in värdet på att göra exempelprogrammet att skapa och använda simulerade sensordata.

   *Obs: I2c-adressen som används i den här självstudien är 0x77 som standard. Beroende på din konfiguration kan det också vara 0x76: om du stöter på ett i2c-fel, försök att ändra värdet till 118 och se om det fungerar bättre. För att se vilken adress som `sudo i2cdetect -y 1` används av sensorn, kör i ett skal på raspberry pi*

2. Spara och avsluta genom att skriva Control-O > Enter > Control-X.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Kontrollera att du kopierar in enhetsanslutningssträngen i de enskilda citaten.

Du bör se följande utdata som visar de sensordata och de meddelanden som skickas till din IoT-hubb.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs de meddelanden som tas emot av din hubb

Ett sätt att övervaka meddelanden som tas emot av din IoT-hubb från enheten är att använda Azure IoT Tools for Visual Studio Code. Mer information finns i [Använda Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Om du vill ha fler sätt att bearbeta data som skickas av enheten fortsätter du till nästa avsnitt.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
