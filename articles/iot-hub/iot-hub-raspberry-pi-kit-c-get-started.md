---
title: Anslut Raspberry Pi till Azure IoT Hub med C | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi att skicka data till Azure-molnplattformen
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 6a895d7978f1af3914bbb9dee3594dbfffd9f317
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607911"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Anslut Raspberry Pi till Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi med Raspbian. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel, går du till den [Windows Dev Center](https://www.windowsondevices.com/).

Har inte ett kit ännu? Försök [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp en ny kit [här](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.

* Registrera en enhet för Pi i din IoT-hubb.

* Setup Raspberry Pi.

* Kör ett exempelprogram på Pi att skicka sensordata till din IoT-hubb.

Anslut Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på Pi för att samla in temperatur och fuktighet data från en BME280 sensor. Slutligen kan skicka du sensordata till din IoT hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure-IoT-hubb och hämta nya enhetens anslutningssträng.

* Hur du ansluter Pi med en BME280 sensor.

* Så här att samla in sensordata genom att köra ett exempelprogram på Pi.

* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 eller Raspberry Pi 3-kort.

* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.

* En Övervakare, en USB-tangentbord och mus som ansluter till Pi.

* En Mac-dator eller en dator som kör Windows eller Linux.

* En Internetanslutning.

* 16 GB eller över microSD-kort.

* En USB-SD nätverkskort eller microSD-kort bränna angiven operativsystemavbildning på microSD-kort.

* En 5 v-2-och elkälla med 6 webbsäkerhet micro USB-kabel.

Följande objekt är valfria:

* En monterade Adafruit BME280 temperatur, tryck och fuktighet sensorn.

* En breadboard.

* 6-F/M omkopplare kablar.

* En indirekt Indikator för 10 mm.

> [!NOTE]
> De här objekten är valfritt eftersom kodexemplet stöder simulerade sensordata.
>

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Hämta anslutningssträngen för IoT-hubben

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrera en ny enhet i IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurera Raspberry Pi

Konfigurera nu Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kort för installation av Raspbian avbildningen.

1. Hämta Raspbian.

   1. [Ladda ned Raspbian Stretch med Desktop](https://www.raspberrypi.org/downloads/raspbian/) (.zip-fil).

   2. Extrahera Raspbian avbildningen till en mapp på datorn.

2. Installera Raspbian microSD-kort.

   1. [Ladda ned och installera verktyget Etcher SD-kort brännare](https://etcher.io/).

   2. Kör Etcher och välj den Raspbian-avbildning som du extraherade i steg 1.

   3. Välj enhet för microSD-kort. Observera att Etcher kanske har redan valt rätt enhet.

   4. Klicka på Flash för att installera Raspbian till microSD-kort.

   5. Ta bort microSD-kort från datorn när installationen är klar. Det är säkert att ta bort microSD-kort direkt eftersom Etcher automatiskt matar ut eller demonteras microSD-kort när åtgärden har slutförts.

   6. Infoga microSD-kort i Pi.

### <a name="enable-ssh-and-spi"></a>Aktivera SSH och SPI

1. Ansluta Pi till bildskärm, tangentbord och mus, starta Pi och logga sedan in Raspbian med hjälp av `pi` som användarnamn och `raspberry` som lösenord.
 
2. Klicka på ikonen Raspberry > **inställningar** > **Raspberry Pi Configuration**.

   ![Menyn Raspbian inställningar](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. På den **gränssnitt** fliken genom att ange **SPI** och **SSH** till **aktivera**, och klicka sedan på **OK**. Om du inte har fysisk sensorer och vill använda simulerade sensordata, är det här steget valfritt.

   ![Aktivera SPI och SSH på Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Om du vill aktivera SSH och SPI, du kan hitta mer referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Ansluta sensorn till Pi

Använd breadboard och omkopplare kablar för att ansluta en bildskärm och en BME280 till Pi på följande sätt. Om du inte har sensorn [hoppa över det här avsnittet](#connect-pi-to-the-network).

![Raspberry Pi och sensor-anslutning](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Sensorn BME280 kan samla in data för temperatur och fuktighet. Och i LED blinkar om det finns en kommunikationen mellan enheten och molnet.

För sensor PIN-koder, använder du följande koaxialkabel:

| Starta (Sensor och LED)     | Slutpunkt (Board)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (PIN-kod 5G)         | GPIO 4 (PIN-kod 7)         | Vit-kabel   |
| LED jord (PIN-kod 6G)         | JORD (PIN-kod 6)            | Svarta kabeln   |
| VDD (PIN-kod 18F)            | 3, 3v PWR (PIN-kod 17)      | Vit-kabel   |
| JORD (PIN-kod 20F)            | JORD (PIN-kod 20)           | Svarta kabeln   |
| SCK (PIN-kod 21F)            | SPI0 SCLK (Pin 23)     | Orange kabel  |
| SDO (PIN-kod 22F)            | SPI0 MISO (PIN-kod 21)     | Gul-kabel  |
| SDI (PIN-kod 23F)            | SPI0 MOSI (PIN-kod 19)     | Grön-kabel   |
| CS (PIN-kod 24F)             | SPI0 CS (PIN-kod 24)       | Blå kabeln    |

Klicka för att visa [Raspberry Pi 2 och 3 PIN-kod mappningar](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) som referens.

När du har anslutit BME280 Raspberry Pi, bör den vara som bilden nedan.

![Anslutna Pi och BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera Pi med hjälp av micro USB-kabel och strömförsörjningen. Använd Ethernet-kabel för att ansluta Pi till ett kabelanslutet nätverk eller följa den [instruktioner från Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) ansluta Pi till det trådlösa nätverket. När din Pi har lyckats ansluta till nätverket, måste du anteckna den [IP-adressen för din Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ansluten till kabelanslutna nätverk](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="sign-into-your-raspberry-pi"></a>Logga in på Raspberry Pi

1. Använd någon av följande SSH-klienter från värddatorn för att ansluta till Raspberry Pi.
   
   **Windows-användare**
   1. Ladda ned och installera [PuTTY](https://www.putty.org/) för Windows. 
   1. Kopiera IP-adressen för din Pi till värd (eller IP-adress)-avsnittet och välj SSH som anslutningstyp.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac- och Ubuntu-användare**

   Använd den inbyggda SSH-klienten på Ubuntu- eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` att ansluta Pi via SSH.
   > [!NOTE]
   > Standardanvändarnamnet är `pi` , och lösenordet är `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Klona exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Kör installationsskriptet:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Om du **inte har en fysisk BME280**, du kan använda ”--simulerade data, som kommandoradsparametern att simulera temperatur och fuktighet. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Skapa och köra exempelprogrammet

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
   > Kontrollera att du kopiera och klistra in anslutningssträngen i de enkla citattecken.
   >

Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Läs meddelandena som tagits emot av hubben

Ett sätt att övervaka meddelanden som tas emot av IoT-hubben från din enhet är att använda Azure IoT Tools för Visual Studio Code. Mer information finns i [Använd Azure IoT-verktyg för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Fortsätt till nästa avsnitt för fler sätt att bearbeta data som skickas av enheten.

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
