---
title: Raspberry Pi till molnet (Node.js) – ansluta hallon Pi till Azure IoT Hub | Microsoft Docs
description: Lär dig mer om att konfigurera och ansluta hallon Pi till Azure IoT-hubb för hallon Pi att skicka data till Azure-molnplattform i den här självstudiekursen.
services: iot-hub
documentationcenter: ''
author: rangv
manager: timlt
tags: ''
keywords: Azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skickar data till molnet, raspberry pi till molnet
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/11/2018
ms.author: rangv
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e7db2f78a5c1a942f64a2c0a40068fffe90749d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Ansluta Raspberry Pi till Azure IoT-hubb (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här kursen kan du börja lära dig grunderna i att arbeta med hallon Pi med Raspbian. Du lär dig sedan sömlöst ansluter enheterna till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md). För Windows 10 IoT Core sampel, gå till den [Windows Dev Center](http://www.windowsondevices.com/).

Har du en kit ännu? Försök [hallon Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp en ny kit [här](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.
* Registrera en enhet för Pi i din IoT-hubb.
* Ställ in hallon Pi.
* Kör ett exempelprogram på Pi sensordata ska skickas till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure IoT-hubb och hämta din nya anslutningssträngen för enheten.
* Så här ansluter du Pi med en BME280 sensor.
* Hur du samlar in sensordata genom att köra ett exempelprogram på Pi.
* Hur du skickar sensordata till din IoT-hubb.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Hallon Pi 2 eller 3 för hallon Pi-kort.
* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) i bara några minuter.
* En Övervakare, en USB-tangentbord och mus som ansluter till Pi.
* En Mac eller en dator som kör Windows eller Linux.
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
Du kan använda simulerade sensordata om du inte har de valfria objekt.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Ställ in hallon Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kort för installation av Raspbian bilden.

1. Hämta Raspbian.
   1. [Hämta Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (.zip-fil).

   > [!WARNING]
   > Använd ovanstående länken för att hämta `raspbian-2017-07-5` zip-bild. Den senaste versionen av Raspbian bilder har några kända problem med kablar Pi-nod, vilket kan orsaka fel i nästa steg.
   1. Extrahera Raspbian-avbildning till en mapp på datorn.

1. Installera Raspbian microSD-kort.
   1. [Hämta och installera verktyget Etcher SD-kort brännare](https://etcher.io/).
   1. Kör Etcher och välj Raspbian bilden som du extraherade i steg 1.
   1. Välj enhet för microSD-kort. Etcher kanske redan har valt rätt enhet.
   1. Klicka på Flash för att installera Raspbian till microSD-kort.
   1. Ta bort microSD-kort från datorn när installationen är klar. Det är säkert att ta bort microSD-kort direkt eftersom Etcher automatiskt matar ut eller demonterar microSD-kort när åtgärden har slutförts.
   1. Infoga microSD-kort i Pi.

### <a name="enable-ssh-and-i2c"></a>Aktivera SSH och I2C

1. Ansluta Pi till bildskärm, tangentbord och mus. 
1. Starta Pi och logga sedan in Raspbian med hjälp av `pi` som användarnamn och `raspberry` som lösenord.
1. Klicka på ikonen Raspberry > **inställningar** > **hallon Pi Configuration**.

   ![Menyn Raspbian inställningar](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. På den **gränssnitt** ställer du in **I2C** och **SSH** till **aktivera**, och klicka sedan på **OK**. Om du inte har fysisk sensorer och vill använda simulerade sensordata, är det här steget valfritt.

   ![Aktivera I2C och SSH på Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
Om du vill aktivera SSH och I2C hittar du mer referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Ansluta sensorn till Pi

Använd breadboard och omkopplare kablar för att ansluta en bildskärm och en BME280 till Pi på följande sätt. Om du inte har sensorn [hoppa över det här avsnittet](#connect-pi-to-the-network).

![Hallon Pi och sensor-anslutning](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

BME280 sensor kan samla in data för temperatur- och fuktighetskonsekvens. Indikatorn blinkar när enheten skickar ett meddelande till molnet. 

Använd följande-kablar för sensor PIN:

| Starta (Sensor & Indikator)     | END (moderkort)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 5G)             | 3, 3v PWR (PIN-kod 1)       | Vita kabeln   |
| JORD (PIN-kod 7G)             | JORD (PIN-kod 6)            | Bruna kabel   |
| SDI (PIN-kod 10G)            | I2C1 SDA (PIN-kod 3)       | Röd kabel     |
| SCK (PIN-kod 8G)             | I2C1 SCL (PIN-kod 5)       | Orange kabel  |
| Indikator VDD (PIN-kod 18F)        | GPIO 24 (PIN-kod 18)       | Vita kabeln   |
| Indikator jord (PIN-kod 17F)        | JORD (PIN-kod 20)           | Svart kabel   |

Klicka för att visa [hallon Pi 2 och 3 PIN-kod mappningar](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) referens.

När du har anslutit BME280 till din hallon Pi, ska den som under bilden.

![Anslutna Pi och BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera Pi med hjälp av micro USB-kabel och strömförsörjningen. Använd Ethernet-kabel för att ansluta Pi till ett kabelanslutet nätverk eller följa den [instruktioner från grunden hallon Pi](https://www.raspberrypi.org/learning/software-guide/wifi/) ansluta Pi till det trådlösa nätverket. När din Pi har lyckats ansluta till nätverket, måste du anteckna den [IP-adressen för din Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ansluten till kabelanslutna nätverk](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Kontrollera att Pi är ansluten till samma nätverk som datorn. Om datorn är ansluten till ett trådlöst nätverk när Pi är ansluten till ett kabelanslutet nätverk, kan du inte se IP-adressen i devdisco utdata.

## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klona exempelprogrammet och installera de nödvändiga paketen

1. Anslut till din hallon Pi med något av följande SSH-klienter från värddatorn:
   
   **Windows-användare**
   1. Hämta och installera [PuTTY](http://www.putty.org/) för Windows. 
   1. Kopiera IP-adressen för ditt avsnitt Pi till värden (eller IP-adress) och välj SSH som anslutningstypen.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- och Ubuntu användare**
   
   Använd den inbyggda SSH-klienten på Ubuntu eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` ansluta Pi via SSH.
   > [!NOTE] 
   Standardanvändarnamnet är `pi` och lösenordet är `raspberry`.

1. Installera Node.js och NPM till din Pi.
   
   Kontrollera först din Node.js-version. 
   
   ```bash
   node -v
   ```

   Om versionen som är lägre än 4.x, eller om det finns inga Node.js på din Pi, installera den senaste versionen.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. Klona exempelprogrammet.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. Installera alla paket för. Installationen innehåller Azure IoT-enhet SDK, BME280 Sensor bibliotek och kabeldragning Pi-biblioteket.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   Det kan ta flera minuter att slutföra den här processen beroende på nätverksanslutningen.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Öppna konfigurationsfilen genom att köra följande kommandon:

   ```bash
   nano config.json
   ```

   ![Konfigurationsfil](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Det finns två objekt i den här filen som du kan konfigurera. Den första är `interval`, som definierar tidsintervall (i millisekunder) mellan meddelanden som skickas till molnet. Den andra är `simulatedData`, vilket är ett booleskt värde för om du vill använda simulerade sensordata eller inte.

   Om du **saknar sensorn**, ange den `simulatedData` värde till `true` för att skapa och använda simulerade sensordata exempelprogrammet.

1. Spara och avsluta genom att skriva kontroll-O > Ange > kontrollen X.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   Kontrollera att du kopiera / klistra in anslutningssträngen enheten till de enkla citattecken.


Du bör se följande utdata som visar dessa sensordata och meddelanden som skickas till din IoT-hubb.

![Utdata - sensordata som skickats från hallon Pi till din IoT-hubb](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in sensordata och skicka den till din IoT-hubb. Visa meddelanden som din hallon Pi har skickats till din IoT-hubb eller skicka meddelanden till din hallon Pi i ett kommandoradsgränssnitt finns i [hantera moln enhet meddelanden med iothub explorer kursen](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
