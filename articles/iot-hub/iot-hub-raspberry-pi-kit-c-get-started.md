---
title: Raspberry Pi till molnet (C) - ansluta hallon Pi till Azure IoT Hub | Microsoft Docs
description: Lär dig mer om att konfigurera och ansluta hallon Pi till Azure IoT-hubb för hallon Pi att skicka data till Azure-molnplattform i den här självstudiekursen.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skickar data till molnet, raspberry pi till molnet
ms.assetid: 68c0e730-1dc8-4e26-ac6b-573b217b302d
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea4a7b4084faf702cf303036b414a9bc9e153840
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Ansluta Raspberry Pi till Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här kursen kan du börja lära dig grunderna i att arbeta med hallon Pi med Raspbian. Du lär dig sedan sömlöst ansluter enheterna till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md). För Windows 10 IoT Core sampel, gå till den [Windows Dev Center](http://www.windowsondevices.com/).

Har du en kit ännu? Försök [hallon Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp en ny kit [här](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.
* Registrera en enhet för Pi i din IoT-hubb.
* Konfigurera Raspberry Pi.
* Kör ett exempelprogram på Pi sensordata ska skickas till din IoT-hubb.

Ansluta hallon Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på Pi för att samla in data för temperatur- och fuktighetskonsekvens från en BME280 sensor. Slutligen kan du skicka dessa sensordata för din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure IoT-hubb och hämta din nya anslutningssträngen för enheten.
* Så här ansluter du Pi med en BME280 sensor.
* Hur du samlar in sensordata genom att köra ett exempelprogram på Pi.
* Hur du skickar sensordata till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

* Hallon Pi 2 eller 3 för hallon Pi-kort.
* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
* En Övervakare, en USB-tangentbord och mus som ansluter till Pi.
* En Mac- eller en dator som kör Windows eller Linux.
* En Internetanslutning.
* 16 GB eller högre microSD-kort.
* En USB-SD-kort eller microSD-kort att bränna operativsystemavbildning på microSD-kort.
* En v 5-2-amp elkälla med 6 fotavtryck micro USB-kabel.

Följande objekt är valfria:

* En monterad Adafruit BME280 temperatur, tryck och fuktighet sensorn.
* En breadboard.
* 6-F/M omkopplare kablar.
* En indirekt Indikator för 10 mm.


> [!NOTE] 
Objekten är valfritt eftersom kod exempel support simulerade sensordata.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Konfigurera Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kort för installation av Raspbian bilden.

1. Hämta Raspbian.
   1. [Hämta Raspbian Jessie med skrivbordet](https://www.raspberrypi.org/downloads/raspbian/) (.zip-fil).
   1. Extrahera Raspbian-avbildning till en mapp på datorn.
1. Installera Raspbian microSD-kort.
   1. [Hämta och installera verktyget Etcher SD-kort brännare](https://etcher.io/).
   1. Kör Etcher och välj Raspbian bilden som du extraherade i steg 1.
   1. Välj enhet för microSD-kort. Observera att Etcher kanske redan har valt rätt enhet.
   1. Klicka på Flash för att installera Raspbian till microSD-kort.
   1. Ta bort microSD-kort från datorn när installationen är klar. Det är säkert att ta bort microSD-kort direkt eftersom Etcher automatiskt matar ut eller demonterar microSD-kort när åtgärden har slutförts.
   1. Infoga microSD-kort i Pi.

### <a name="enable-ssh-and-spi"></a>Aktivera SSH och SPI

1. Ansluta Pi till bildskärm, tangentbord och mus, starta Pi och logga sedan in Raspbian med hjälp av `pi` som användarnamn och `raspberry` som lösenord.
1. Klicka på ikonen Raspberry > **inställningar** > **hallon Pi Configuration**.

   ![Menyn Raspbian inställningar](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. På den **gränssnitt** ställer du in **SPI** och **SSH** till **aktivera**, och klicka sedan på **OK**. Om du inte har fysisk sensorer och vill använda simulerade sensordata, är det här steget valfritt.

   ![Aktivera SPI och SSH på Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Om du vill aktivera SSH och SPI hittar du mer referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Ansluta sensorn till Pi

Använd breadboard och omkopplare kablar för att ansluta en bildskärm och en BME280 till Pi på följande sätt. Om du inte har sensorn [hoppa över det här avsnittet](#connect-pi-to-the-network).

![Hallon Pi och sensor-anslutning](media/iot-hub-raspberry-pi-kit-c-get-started/3_raspberry-pi-sensor-connection.png)

BME280 sensor kan samla in data för temperatur- och fuktighetskonsekvens. Och Indikatorn blinkar om det finns en kommunikation mellan enheten och i molnet. 

Använd följande-kablar för sensor PIN:

| Starta (Sensor & Indikator)     | END (moderkort)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| Indikator VDD (PIN-kod 5G)         | GPIO 4 (PIN-kod 7)         | Vita kabeln   |
| Indikator jord (PIN-kod 6G)         | JORD (PIN-kod 6)            | Svart kabel   |
| VDD (PIN-kod 18F)            | 3, 3v PWR (PIN-kod 17)      | Vita kabeln   |
| JORD (PIN-kod 20F)            | JORD (PIN-kod 20)           | Svart kabel   |
| SCK (PIN-kod 21F)            | SPI0 SCLK (PIN-kod 23)     | Orange kabel  |
| SDO (PIN-kod 22F)            | SPI0 MISO (PIN-kod 21)     | Gult kabel  |
| SDI (PIN-kod 23F)            | SPI0 MOSI (PIN-kod 19)     | Grön kabel   |
| CS (PIN-kod 24F)             | SPI0 CS (PIN-kod 24)       | Blå kabeln    |

Klicka för att visa [hallon Pi 2 och 3 PIN-kod mappningar](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referens.

När du har anslutit BME280 till din hallon Pi, ska den som under bilden.

![Anslutna Pi och BME280](media/iot-hub-raspberry-pi-kit-c-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera Pi med hjälp av micro USB-kabel och strömförsörjningen. Använd Ethernet-kabel för att ansluta Pi till ett kabelanslutet nätverk eller följa den [instruktioner från grunden hallon Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) ansluta Pi till det trådlösa nätverket. När din Pi har lyckats ansluta till nätverket, måste du anteckna den [IP-adressen för din Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ansluten till kabelanslutna nätverk](media/iot-hub-raspberry-pi-kit-c-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="login-to-your-raspberry-pi"></a>Logga in på ditt Raspberry Pi

1. Använd en av följande SSH-klienter från värddatorn för att ansluta till din hallon Pi.
   
   **Windows-användare**
   1. Hämta och installera [PuTTY](http://www.putty.org/) för Windows. 
   1. Kopiera IP-adressen för ditt avsnitt Pi till värden (eller IP-adress) och välj SSH som anslutningstypen.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- och Ubuntu användare**
   
   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` ansluta Pi via SSH.
   > [!NOTE] 
   Standardanvändarnamnet är `pi` , och lösenordet är `raspberry`.


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
   > Om du **inte har en fysisk BME280**, kan du använda '--simulerade data' som kommandoradsparametern att simulera temperatur & fuktighet data. `sudo ./setup.sh --simulated-data`

### <a name="build-and-run-the-sample-application"></a>Skapa och köra exempelprogrammet

1. Skapa exempelprogrammet genom att köra följande kommando:

   ```bash
   cmake . && make
   ```
   ![Skapa utdata](media/iot-hub-raspberry-pi-kit-c-get-started/7_build-output.png)

1. Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Kontrollera att du kopiera / klistra in anslutningssträngen enheten till de enkla citattecken.


Du bör se följande utdata som visar dessa sensordata och meddelanden som skickas till din IoT-hubb.

![Utdata - sensordata som skickats från hallon Pi till din IoT-hubb](media/iot-hub-raspberry-pi-kit-c-get-started/8_run-output.png)

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka den till din IoT-hubb. Meddelanden som din hallon Pi har skickats till din IoT-hubb eller skicka meddelanden till din hallon Pi i ett kommandoradsgränssnitt finns i [hantera moln enhet meddelanden med iothub explorer kursen](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
