---
title: Anslut Raspberry Pi till Azure IoT Hub med C | Microsoft-dokument
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi för att skicka data till Azure-molnplattformen
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67838783"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Anslut Raspberry Pi till Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi som kör Raspbian. Du lär dig sedan hur du sömlöst ansluter dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel går du till [Windows Dev Center](https://www.windowsondevices.com/).

Har du inget kit än? Prova [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp ett nytt kit [här.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.

* Registrera en enhet för Pi i din IoT-hubb.

* Setup Raspberry Pi.

* Kör ett exempelprogram på Pi för att skicka sensordata till din IoT-hubb.

Anslut Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på Pi för att samla in temperatur- och fuktighetsdata från en BME280-sensor. Slutligen skickar du sensordata till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Så här skapar du en Azure IoT-hubb och hämtar din nya enhetsanslutningssträng.

* Hur man ansluter Pi med en BME280 sensor.

* Så här samlar du in sensordata genom att köra ett exempelprogram på Pi.

* Så här skickar du sensordata till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 eller Raspberry Pi 3 ombord.

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapar du ett kostnadsfritt Azure-utvärderingskonto](https://azure.microsoft.com/free/) på bara några minuter.

* En bildskärm, ett USB-tangentbord och en mus som ansluter till Pi.

* En Mac eller en PC som kör Windows eller Linux.

* En Internetanslutning.

* Ett 16 GB eller högre microSD-kort.

* En USB-SD-adapter eller microSD-kort för att bränna operativsystembilden på microSD-kortet.

* En 5-volts 2-amp strömförsörjning med 6-fots mikro USB-kabel.

Följande objekt är valfria:

* En monterad Adafruit BME280 temperatur, tryck och fuktighetssensor.

* En brödbräda.

* 6 F/M bygeltrådar.

* En diffus 10 mm LED.

> [!NOTE]
> Dessa objekt är valfria eftersom kodexemplet stöder simulerade sensordata.
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT-hubben

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Ställ in Raspberry Pi

Nu inrätta Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kortet för installation av Raspbian-avbildningen.

1. Ladda ner Raspbian.

   1. [Ladda ner Raspbian Stretch med desktop](https://www.raspberrypi.org/downloads/raspbian/) (ZIP-filen).

   2. Extrahera Raspbian-avbildningen till en mapp på datorn.

2. Installera Raspbian på microSD-kortet.

   1. [Ladda ner och installera Etcher SD-kort brännare verktyget](https://etcher.io/).

   2. Kör Etcher och välj Den Raspbian-avbildning som du extraherade i steg 1.

   3. Välj microSD-kortenheten. Observera att Etcher redan har valt rätt enhet.

   4. Klicka på Flash för att installera Raspbian på microSD-kortet.

   5. Ta bort microSD-kortet från datorn när installationen är klar. Det är säkert att ta bort microSD-kortet direkt eftersom Etcher automatiskt matar ut eller avmonterar microSD-kortet när det är klart.

   6. Sätt i microSD-kortet i Pi.

### <a name="enable-ssh-and-spi"></a>Aktivera SSH och SPI

1. Anslut Pi till bildskärmen, tangentbordet och musen, starta Pi och `pi` logga sedan `raspberry` in på Raspbian med hjälp av som användarnamn och som lösenord.
 
2. Klicka på raspberry-ikonen > **Inställningar** > **Raspberry Pi-konfiguration**.

   ![Menyn Raspbian-inställningar](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. På fliken **Gränssnitt** ställer du in **SPI** och **SSH** för **att aktivera**och klickar sedan på **OK**. Om du inte har fysiska sensorer och vill använda simulerade sensordata är det här steget valfritt.

   ![Aktivera SPI och SSH på Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om du vill aktivera SSH och SPI hittar du fler referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Anslut sensorn till Pi

Använd brödbrädan och bygeltrådarna för att ansluta en LED och en BME280 till Pi enligt följande. Om du inte har sensorn [hoppar du över det här avsnittet](#connect-pi-to-the-network).

![Raspberry Pi och sensoranslutning](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280-sensorn kan samla in temperatur- och fuktighetsdata. Och lysdioden blinkar om det finns en kommunikation mellan enheten och molnet.

För sensorstift använder du följande ledningar:

| Start (Sensor & LED)     | (styrelse)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| LED-VDD (stift 5G)         | GPIO 4 (Stift 7)         | Vit kabel   |
| LED GND (stift 6G)         | GND (stift 6)            | Svart kabel   |
| VDD (stift 18F)            | 3.3V PWR (Stift 17)      | Vit kabel   |
| GND (stift 20F)            | GND (stift 20)           | Svart kabel   |
| SCK (stift 21F)            | SPI0 SCLK (stift 23)     | Orange kabel  |
| SDO (stift 22F)            | SPI0 MISO (Stift 21)     | Gul kabel  |
| SDI (stift 23F)            | SPI0 MOSI (Stift 19)     | Grön kabel   |
| CS (Stift 24F)             | SPI0 CS (stift 24)       | Blå kabel    |

Klicka här om du vill visa [Raspberry Pi 2 & 3-pinmappningar](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) som du vill ha.

När du har anslutit BME280 till din Raspberry Pi, bör det vara som nedan bild.

![Ansluten Pi och BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Anslut Pi till nätverket

Slå på Pi med hjälp av mikro-USB-kabeln och strömförsörjningen. Använd Ethernet-kabeln för att ansluta Pi till ditt kabelanslutna nätverk eller följ [instruktionerna från Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) för att ansluta Pi till ditt trådlösa nätverk. När din Pi har anslutits till nätverket måste du ta del av [IP-adressen](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)för din Pi .

![Ansluten till kabelanslutet nätverk](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="sign-into-your-raspberry-pi"></a>Logga in på din Raspberry Pi

1. Använd en av följande SSH-klienter från värddatorn för att ansluta till din Raspberry Pi.
   
   **Windows-användare**
   1. Ladda ned och installera [PuTTY](https://www.putty.org/) för Windows. 
   1. Kopiera din Pi-IP-adress till avsnittet Värdnamn (eller IP-adress) och välj SSH som anslutningstyp.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- och Ubuntu-användare**

   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva `ssh pi@<ip address of pi>` köra för att ansluta Pi via SSH.
   > [!NOTE]
   > Standardanvändarnamnet `pi` är och lösenordet `raspberry`är .


### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Klona exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Kör installationsskript:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Om du **inte har en fysisk BME280**kan du använda '--simulerade data' som kommandoradsparameter för att simulera temperatur&fuktighetsdata. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Skapa och kör exempelprogrammet

1. Skapa exempelprogrammet genom att köra följande kommando:

   ```bash
   cmake . && make
   ```
   
   ![Skapa utdata](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Kontrollera att du kopierar in enhetsanslutningssträngen i de enskilda citaten.
   >

Du bör se följande utdata som visar de sensordata och de meddelanden som skickas till din IoT-hubb.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs de meddelanden som tas emot av din hubb

Ett sätt att övervaka meddelanden som tas emot av din IoT-hubb från enheten är att använda Azure IoT Tools for Visual Studio Code. Mer information finns i [Använda Azure IoT Tools for Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Om du vill ha fler sätt att bearbeta data som skickas av enheten fortsätter du till nästa avsnitt.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka dem till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
