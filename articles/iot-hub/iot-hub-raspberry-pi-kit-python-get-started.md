---
title: Raspberry Pi till molnet (Python) – Anslut Raspberry Pi till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Raspberry Pi till Azure IoT Hub för Raspberry Pi att skicka data till Azure-molnplattformen i den här självstudien.
author: rangv
manager: ''
keywords: Azure iot raspberry pi, raspberry pi iot-hubb, raspberry pi skicka data till molnet, raspberry pi till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 15b917d811dff2d70b638ab82c808bdc7fa1012b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38235737"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-python"></a>Anslut Raspberry Pi till Azure IoT Hub (Python)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Raspberry Pi med Raspbian. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](iot-hub-what-is-iot-hub.md). För Windows 10 IoT Core-exempel, går du till den [Windows Dev Center](http://www.windowsondevices.com/).

Har inte ett kit ännu? Försök [Raspberry Pi onlinesimulator](iot-hub-raspberry-pi-web-simulator-get-started.md). Eller köp en ny kit [här](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Vad du gör

* Skapa en IoT-hubb.
* Registrera en enhet för Pi i din IoT-hubb.
* Konfigurera Raspberry Pi.
* Kör ett exempelprogram på Pi att skicka sensordata till din IoT-hubb.

Anslut Raspberry Pi till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på Pi för att samla in temperatur och fuktighet data från en BME280 sensor. Slutligen kan skicka du sensordata till din IoT hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure-IoT-hubb och hämta nya enhetens anslutningssträng.
* Hur du ansluter Pi med en BME280 sensor.
* Så här att samla in sensordata genom att köra ett exempelprogram på Pi.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](media/iot-hub-raspberry-pi-kit-c-get-started/0_starter_kit.jpg)

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
Dessa objekt är valfritt eftersom kod exempel support simulerade sensordata.


[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurera Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Installera operativsystemet Raspbian för Pi

Förbered microSD-kort för installation av Raspbian avbildningen.

1. Hämta Raspbian.
   1. [Ladda ned Raspbian Jessie med Desktop](https://www.raspberrypi.org/downloads/raspbian/) (.zip-fil).
   1. Extrahera Raspbian avbildningen till en mapp på datorn.
1. Installera Raspbian microSD-kort.
   1. [Ladda ned och installera verktyget Etcher SD-kort brännare](https://etcher.io/).
   1. Kör Etcher och välj den Raspbian-avbildning som du extraherade i steg 1.
   1. Välj enhet för microSD-kort. Observera att Etcher kanske har redan valt rätt enhet.
   1. Klicka på Flash för att installera Raspbian till microSD-kort.
   1. Ta bort microSD-kort från datorn när installationen är klar. Det är säkert att ta bort microSD-kort direkt eftersom Etcher automatiskt matar ut eller demonteras microSD-kort när åtgärden har slutförts.
   1. Infoga microSD-kort i Pi.

### <a name="enable-ssh-and-i2c"></a>Aktivera SSH och I2C

1. Ansluta Pi till bildskärm, tangentbord och mus, starta Pi och logga sedan in Raspbian med hjälp av `pi` som användarnamn och `raspberry` som lösenord.
1. Klicka på ikonen Raspberry > **inställningar** > **Raspberry Pi Configuration**.

   ![Menyn Raspbian inställningar](media/iot-hub-raspberry-pi-kit-c-get-started/1_raspbian-preferences-menu.png)

1. På den **gränssnitt** fliken genom att ange **I2C** och **SSH** till **aktivera**, och klicka sedan på **OK**. Om du inte har fysisk sensorer och vill använda simulerade sensordata, är det här steget valfritt.

   ![Aktivera I2C och SSH på Raspberry Pi](media/iot-hub-raspberry-pi-kit-c-get-started/2_enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE] 
Om du vill aktivera SSH och I2C, du kan hitta mer referensdokument på [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) och [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).

### <a name="connect-the-sensor-to-pi"></a>Ansluta sensorn till Pi

Använd breadboard och omkopplare kablar för att ansluta en bildskärm och en BME280 till Pi på följande sätt. Om du inte har sensorn [hoppa över det här avsnittet](#connect-pi-to-the-network).

![Raspberry Pi och sensor-anslutning](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)

Sensorn BME280 kan samla in data för temperatur och fuktighet. Och i LED blinkar om det finns en kommunikationen mellan enheten och molnet. 

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

### <a name="install-the-prerequisite-packages"></a>Installera de nödvändiga paketen

Använd någon av följande SSH-klienter från värddatorn för att ansluta till Raspberry Pi.
   
   **Windows-användare**
   1. Ladda ned och installera [PuTTY](http://www.putty.org/) för Windows. 
   1. Kopiera IP-adressen för din Pi till värd (eller IP-adress)-avsnittet och välj SSH som anslutningstyp.
   
   
   **Mac- och Ubuntu-användare**
   
   Använd den inbyggda SSH-klienten på Ubuntu- eller macOS. Du kan behöva köra `ssh pi@<ip address of pi>` att ansluta Pi via SSH.
   > [!NOTE] 
   Standardanvändarnamnet är `pi` , och lösenordet är `raspberry`.


### <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Klona exempelprogrammet genom att köra följande kommando:

   ```bash
   cd ~
   git clone https://github.com/Azure-Samples/iot-hub-python-raspberrypi-client-app.git
   ```
1. Öppna konfigurationsfilen genom att köra följande kommandon:

   ```bash
   cd iot-hub-python-raspberrypi-client-app
   nano config.py
   ```

   Det finns 5 makron i den här filen kan du configurate. Den första är `MESSAGE_TIMESPAN`, som definierar tidsintervallet (i millisekunder) mellan två meddelanden som skickas till molnet. Den andra mallen `SIMULATED_DATA`, vilket är ett booleskt värde för om du vill använda simulerade sensordata eller inte. `I2C_ADDRESS` är den I2C-adress som din BME280 sensorn är ansluten. `GPIO_PIN_ADDRESS` är GPIO-adressen för din LED. Den senaste är `BLINK_TIMESPAN`, som definierats i timespan när din LED aktiveras i millisekunder.

   Om du **inte har sensorn**, ange den `SIMULATED_DATA` värde att `True` att exempelprogrammet skapar och använder simulerade sensordata.

1. Spara och avsluta genom att trycka på kontrollen-O > RETUR > kontroll-X.

### <a name="build-and-run-the-sample-application"></a>Skapa och köra exempelprogrammet

1. Skapa exempelprogrammet genom att köra följande kommando. Eftersom Azure IoT SDK för Python är omslutningar ovanpå Azure IoT Device C SDK, behöver du kompilera C-bibliotek om du vill ha eller behöver generera Python-biblioteken från källkoden.

   ```bash
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```
   > [!NOTE] 
   Du kan också ange den version som du vill använda genom att köra `sudo ./setup.sh [--python-version|-p] [2.7|3.4|3.5]`. Om du kör skriptet utan parametern skriptet identifierar versionen av python som har installerats automatiskt (Sökordning 2.7 -> 3.4 3.5 ->). Kontrollera Python-versionen håller konsekvent under utveckling och körning. 
   
   > [!NOTE] 
   Med att utveckla Python-klientbiblioteket (iothub_client.so) på Linux-enheter som har mindre än 1GB RAM-minne, kan du se Skapa fastnar vid 98% när du skapar iothub_client_python.cpp enligt nedan `[ 98%] Building CXX object python/src/CMakeFiles/iothub_client_python.dir/iothub_client_python.cpp.o`. Om du stöter på problemet, kontrollera minnesanvändningen för enheter med hjälp av `free -m command` i en annan terminalfönstret under den tiden. Om du har slut på minne vid kompilering iothub_client_python.cpp fil, kan du behöva tillfälligt öka växlingsutrymme för att få mer tillgängligt minne där Python klientsidan enheten SDK-biblioteket.
   
1. Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   python app.py '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Kontrollera att du kopiera och klistra in anslutningssträngen i de enkla citattecken. Och om du använder python 3 så kan du använda kommandot `python3 app.py '<your Azure IoT hub device connection string>'`.


   Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

   ![Resultat – sensordata som skickas från Raspberry Pi till din IoT-hubb](media/iot-hub-raspberry-pi-kit-c-get-started/success.png
)

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb. De meddelanden som Raspberry Pi skickade till din IoT hub eller skicka meddelanden till Raspberry Pi i ett kommandoradsgränssnitt finns i den [hantera cloud enhet-meddelanden med iothub-explorer självstudiekursen](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-explorer-cloud-device-messaging).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
