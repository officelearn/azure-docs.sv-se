---
title: Raspberry Pi till molnet (Node.js) – Anslut Raspberry Pi till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi att skicka data till Azure-molnplattformen i den här självstudien.
author: rangv
manager: ''
keywords: Azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skicka data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 348186bcb29b272b7e6512ce42221d54d6b388d9
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161823"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Anslut Raspberry Pi till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi med Raspbian. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md). För Windows 10 IoT Core-exempel, går du till den [Windows Dev Center](http://www.windowsondevices.com/).

Har inte ett kit ännu? Försök [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp en ny kit [här](https://azure.microsoft.com/develop/iot/starter-kits).


## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.
* Registrera en enhet för Pi i din IoT-hubb.
* Ställ in Raspberry Pi.
* Kör ett exempelprogram på Pi att skicka sensordata till din IoT-hubb.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure-IoT-hubb och hämta nya enhetens anslutningssträng.
* Hur du ansluter Pi med en BME280 sensor.
* Så här att samla in sensordata genom att köra ett exempelprogram på Pi.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* En Raspberry Pi 2 eller Raspberry Pi 3 tavla.
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* En Övervakare, en USB-tangentbord och mus som ansluter till Pi.
* En Mac eller en dator som kör Windows eller Linux.
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
> Om du inte har de valfria objekt, kan du använda simulerade sensordata.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurera Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kort för installation av Raspbian avbildningen.

1. Hämta Raspbian.

   a. [Ladda ned Raspbian Stretch](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/) (.zip-fil).

   > [!WARNING]
   > Använd ovan länk för att ladda ned `raspbian-2017-07-5` zip-avbildning. Den senaste versionen av Raspbian bilder har några kända problem med koaxialkabel Pi-nod, vilket kan orsaka fel i nästa steg.
 
   b. Extrahera Raspbian avbildningen till en mapp på datorn.

2. Installera Raspbian microSD-kort.

   a. [Ladda ned och installera verktyget Etcher SD-kort brännare](https://etcher.io/).

   b. Kör Etcher och välj den Raspbian-avbildning som du extraherade i steg 1.

   c. Välj enhet för microSD-kort. Etcher kanske redan har valt rätt enhet.

   d. Klicka på Flash för att installera Raspbian till microSD-kort.

   e. Ta bort microSD-kort från datorn när installationen är klar. Det är säkert att ta bort microSD-kort direkt eftersom Etcher automatiskt matar ut eller demonteras microSD-kort när åtgärden har slutförts.

   f. Infoga microSD-kort i Pi.

### <a name="enable-ssh-and-i2c"></a>Aktivera SSH och I2C

1. Anslut Pi till den bildskärm, tangentbord och mus. 

2. Starta Pi och logga sedan in Raspbian med hjälp av `pi` som användarnamn och `raspberry` som lösenord.

3. Klicka på ikonen Raspberry > **inställningar** > **Raspberry Pi Configuration**.

   ![Menyn Raspbian inställningar](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

4. På den **gränssnitt** fliken genom att ange **I2C** och **SSH** till **aktivera**, och klicka sedan på **OK**. Om du inte har fysisk sensorer och vill använda simulerade sensordata, är det här steget valfritt.

   ![Aktivera I2C och SSH på Raspberry Pi](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
> Om du vill aktivera SSH och I2C, du kan hitta mer referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Ansluta sensorn till Pi

Använd breadboard och omkopplare kablar för att ansluta en bildskärm och en BME280 till Pi på följande sätt. Om du inte har sensorn [hoppa över det här avsnittet](#connect-pi-to-the-network).

![Raspberry Pi och sensor-anslutning](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Sensorn BME280 kan samla in data för temperatur och fuktighet. Indikatorn blinkar när enheten skickar ett meddelande till molnet. 

För sensor PIN-koder, använder du följande koaxialkabel:

| Starta (Sensor och LED)     | Slutpunkt (Board)            | Kabel färg   |
| -----------------------  | ---------------------- | ------------: |
| VDD (PIN-kod 5G)             | 3, 3v PWR (PIN-kod 1)       | Vit-kabel   |
| JORD (PIN-kod 7G)             | JORD (PIN-kod 6)            | Bruna kabel   |
| SDI (PIN-kod 10G)            | I2C1 SDA (PIN-kod 3)       | Röd-kabel     |
| SCK (PIN-kod 8G)             | I2C1 SCL (PIN-kod 5)       | Orange kabel  |
| LED VDD (PIN-kod 18F)        | GPIO 24 (PIN-kod 18)       | Vit-kabel   |
| LED jord (PIN-kod 17F)        | JORD (PIN-kod 20)           | Svarta kabeln   |

Klicka för att visa [Raspberry Pi 2 och 3 PIN-kod mappningar](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) som referens.

När du har anslutit BME280 Raspberry Pi, bör den vara som bilden nedan.

![Anslutna Pi och BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>Ansluta Pi till nätverket

Aktivera Pi med hjälp av micro USB-kabel och strömförsörjningen. Använd Ethernet-kabel för att ansluta Pi till ett kabelanslutet nätverk eller följa den [instruktioner från Raspberry Pi Foundation](https://www.raspberrypi.org/learning/software-guide/wifi/) ansluta Pi till det trådlösa nätverket. När din Pi har lyckats ansluta till nätverket, måste du anteckna den [IP-adressen för din Pi](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address).

![Ansluten till kabelanslutna nätverk](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> Se till att Pi är ansluten till samma nätverk som datorn. Om datorn är ansluten till ett trådlöst nätverk när Pi är ansluten till ett kabelanslutet nätverk, kan du inte se IP-adressen i devdisco utdata.

## <a name="run-a-sample-application-on-pi"></a>Kör ett exempelprogram på Pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klona exempelprogrammet och installera de nödvändiga paketen

1. Anslut till Raspberry Pi med någon av följande SSH-klienter från din värddator:
   
   **Windows-användare**
  
   a. Ladda ned och installera [PuTTY](http://www.putty.org/) för Windows. 

   b. Kopiera IP-adressen för din Pi till värd (eller IP-adress)-avsnittet och välj SSH som anslutningstyp.
   
   ![PuTTy](media/iot-hub-raspberry-pi-kit-node-get-started/7_putty-windows.png)
   
   **Mac- och Ubuntu-användare**
   
   Använd den inbyggda SSH-klienten på Ubuntu- eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` att ansluta Pi via SSH.

   > [!NOTE] 
   > Standardanvändarnamnet är `pi` och lösenordet är `raspberry`.

2. Installera Node.js och NPM i din Pi.
   
   Kontrollera först din Node.js-version. 
   
   ```bash
   node -v
   ```

   Om versionen är lägre än 4.x, eller om det finns inga Node.js på din Pi, installera den senaste versionen.

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Klona exempelprogrammet.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. Installera alla paket för exemplet. Installationen inkluderar Azure IoT-enhetens SDK, BME280 Sensor bibliotek och koppla Pi-biblioteket.

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   >Det kan ta flera minuter att slutföra den här processen beroende på nätverksanslutningen.

### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Öppna konfigurationsfilen genom att köra följande kommandon:

   ```bash
   nano config.json
   ```

   ![Konfigurationsfil](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   Det finns två objekt i den här filen som du kan konfigurera. Den första är `interval`, som definierar tidsintervallet (i millisekunder) mellan meddelanden som skickas till molnet. Den andra är `simulatedData`, vilket är ett booleskt värde för om du vill använda simulerade sensordata eller inte.

   Om du **inte har sensorn**, ange den `simulatedData` värde att `true` att exempelprogrammet skapar och använder simulerade sensordata.

2. Spara och avsluta genom att skriva kontroll-O > RETUR > kontroll-X.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Kontrollera att du kopiera och klistra in anslutningssträngen i de enkla citattecken.


Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb. Om du vill se de meddelanden som Raspberry Pi skickade till din IoT hub eller skicka meddelanden till Raspberry Pi, se den [Använd Azure IoT Toolkit-tillägget för Visual Studio Code för att skicka och ta emot meddelanden mellan enheten och IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
