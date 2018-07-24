---
title: Intel Edison till molnet (Node.js) – Anslut Intel Edison till Azure IoT Hub | Microsoft Docs
description: Lär dig hur du konfigurerar och ansluter Intel Edison till Azure IoT Hub för Intel Edison att skicka data till Azure-molnplattformen i den här självstudien.
author: rangv
manager: ''
keywords: Azure iot intel edison, intel edison iot hub, intel edison skicka data till molnet, intel edison till molnet
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 5adb6e6119856879e52836a8d3a797a7b40c6fe8
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214438"
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>Anslut Intel Edison till Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

I den här självstudien börjar du med att lära dig grunderna i att arbeta med Intel Edison. Du lär dig sedan att sömlöst ansluta dina enheter till molnet med hjälp av [Azure IoT Hub](about-iot-hub.md).

Har inte ett kit ännu? Starta [här](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Vad du gör

* Konfigurera Intel Edison och Groove-moduler.
* Skapa en IoT-hubb.
* Registrera en enhet för Edison i din IoT-hubb.
* Kör ett exempelprogram på Edison att skicka sensordata till din IoT-hubb.

Anslut Intel Edison till en IoT-hubb som du skapar. Sedan kör du ett exempelprogram på Edison att samla in temperatur och fuktighet data från en Groove-temperatursensor. Slutligen kan skicka du sensordata till din IoT hub.

## <a name="what-you-learn"></a>Detta får du får lära dig

* Hur du skapar en Azure-IoT-hubb och hämta nya enhetens anslutningssträng.
* Hur du ansluter Edison med en Groove-temperatursensor.
* Så här att samla in sensordata genom att köra ett exempelprogram på Edison.
* Så här skickar sensordata till din IoT hub.

## <a name="what-you-need"></a>Vad du behöver

![Vad du behöver](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Intel Edison-tavla
* Expansion Arduino-kort
* En aktiv Azure-prenumeration. Om du inte har ett Azure-konto, [skapa ett kostnadsfritt Azure konto](https://azure.microsoft.com/free/) på bara några minuter.
* En Mac-dator eller en dator som kör Windows eller Linux.
* En Internetanslutning.
* En Micro B till typ A USB-kabel
* Strömförsörjning en direct aktuella (DC). Din strömförsörjning bör klassificeras på följande sätt:
  - 7 – 15V DC
  - Minst 1500mA
  - Center/inre PIN-kod ska vara positiva Polen för strömförsörjningen

Följande objekt är valfria:

* Groove grundläggande Shield V2
* Groove - temperatursensor
* Groove-kabel
* Alla GIF staplarna och skruvar som ingår i paketering, inklusive två skruvar för att fästa modulen till tavlan expansion och utföra fyra uppsättningar skruvar och form mellanrum.

> [!NOTE] 
Dessa objekt är valfritt eftersom kod exempel support simulerade sensordata.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>Konfigurera Intel Edison

### <a name="assemble-your-board"></a>Samla ihop din tavla

Det här avsnittet innehåller steg för att koppla din Intel® Edison-modul till din expansion tavla.

1. Placera Intel® Edison-modul i vit kontur på din expansion tavla som du vill anpassa tomrum i modulen med skruvar på tavlan expansion.

2. Tryck ned i modulen nedanför orden `What will you make?` tills du känner dig ett ögonblick.

   ![Assemblera tavla 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. Använda de två hexadecimala nuts (ingår i paketet) för att skydda modulen till expansion tavlan.

   ![Assemblera tavla 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. Infoga en skruv i någon av fyra hörnet hål på tavlan expansion. Genom att vrida och öka en vit form mellanrum till skruven.

   ![Assemblera tavla 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. Upprepa för de andra tre hörnet mellanrum.

   ![Assemblera tavla 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

Din tavla är nu klar.

   ![monterade tavla](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>Anslut Groove Base Shield och temperatursensorn

1. Placera Groove Base Shield in på din tavla. Kontrollera att alla PIN-koder nära ansluts till din tavla.
   
   ![Groove grundläggande Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. Använd Groove kabel för att ansluta Groove-temperatursensor till Groove Base Shield **A0** port.

   ![Anslut till-temperatursensor](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![EDISON och sensor-anslutning](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

Din sensorn är nu klar.

### <a name="power-up-edison"></a>MAXA Edison

1. Anslut strömförsörjningen.

   ![Plugin-strömförsörjning](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. En grön Indikator (märkta DS1 på Arduino * expansion tavlan) bör tändas och brinner.

3. Vänta en minut för den tavla som ska avsluta startar.

   > [!NOTE]
   > Om du inte har en DC-strömförsörjning kan sätta du fortfarande tavlan via en USB-port. Se `Connect Edison to your computer` information. Är din tavla på detta sätt kan resultera i oväntade funktionssätt från din tavla särskilt när med hjälp av Wi-Fi eller drivande motorer.

### <a name="connect-edison-to-your-computer"></a>Ansluta Edison till din dator

1. Växla ned microswitch mot de två micro USB-portarna så att Edison är i läget för enheten. Skillnader mellan enheten och värd-läge refererar [här](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode).

   ![Växla ned microswitch](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. Anslut micro USB-kabel till översta micro USB-port.

   ![Främsta micro USB-port](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. Anslut den andra änden av USB-kabel till datorn.

   ![Datorn USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. Du vet att din tavla initieras fullständigt när datorn monterar en ny enhet (ungefär som när du skapar ett SD-kort till datorn).

## <a name="download-and-run-the-configuration-tool"></a>Ladda ned och kör konfigurationsverktyget
Hämta senaste konfigurationsverktyget från [den här länken](https://software.intel.com/en-us/iot/hardware/edison/downloads) visas under den `Installers` rubrik. Kör verktyget och följa dess anvisningarna på skärmen anvisningarna, klicka på nästa där det behövs

### <a name="flash-firmware"></a>Flash-inbyggd programvara
1. På den `Set up options` klickar du på `Flash Firmware`.
2. Välj avbildningen till flash på din tavla genom att göra något av följande:
   - Om du vill ladda ned och flash din tavla med den senaste inbyggda programvara-avbildningen från Intel, Välj `Download the latest image version xxxx`.
   - Om du vill flash din tavla med en avbildning som du redan har sparats på din dator, Välj `Select the local image`. Bläddra till och välj den avbildning som du vill flash till på din tavla.
3. Verktyget installationsprogrammet försöker flash din tavla. Blinkande processen kan ta upp till 10 minuter.

### <a name="set-password"></a>Ange lösenord
1. På den `Set up options` klickar du på `Enable Security`.
2. Du kan ange ett eget namn för din Intel® Edison-tavla. Det här är valfritt.
3. Ange ett lösenord för din tavla och klicka sedan på `Set password`.
4. Anteckna lösenordet som används senare.

### <a name="connect-wi-fi"></a>Ansluta Wi-Fi
1. På den `Set up options` klickar du på `Connect Wi-Fi`. Vänta upp till en minut som datorn söker igenom tillgängliga Wi-Fi-nätverk.
2. Från den `Detected Networks` listrutan väljer du ditt nätverk.
3. Från den `Security` listrutan väljer du nätverkstyp säkerhet.
4. Ange din information användarnamn och lösenord och klicka sedan på `Configure Wi-Fi`.
5. Anteckna IP-adressen som används senare.

> [!NOTE]
> Se till att Edison är ansluten till samma nätverk som datorn. Datorn ansluter till din Edison med hjälp av IP-adress.

   ![Anslut till-temperatursensor](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

Grattis! Du konfigurerat har Edison.

## <a name="run-a-sample-application-on-intel-edison"></a>Kör ett exempelprogram på Intel Edison

### <a name="prepare-the-azure-iot-device-sdk"></a>Förbereda Azure IoT-enhetens SDK

1. Använd något av följande SSH-klienter från värddatorn för att ansluta till din Intel Edison. IP-adressen från konfigurationsverktyget och lösenordet är det som du har ställt in i verktyget.
    - [PuTTY](http://www.putty.org/) för Windows.
    - Inbyggda SSH-klienten på Ubuntu- eller macOS.

2. Klona exempelappen för klient till din enhet. 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. Gå sedan till mappen lagringsplatsen för att köra följande kommando för att installera alla paket, det kan ta serval minuter att slutföra.
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>Konfigurera och kör exempelprogrammet

1. Öppna konfigurationsfilen genom att köra följande kommandon:

   ```bash
   nano config.json
   ```

   ![Config-fil](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   Det finns två makron i den här filen kan du configurate. Den första är `INTERVAL`, som definierar tidsintervallet mellan två meddelanden som skickas till molnet. Den andra mallen `simulatedData`, vilket är ett booleskt värde för om du vill använda simulerade sensordata eller inte.

   Om du **inte har sensorn**, ange den `simulatedData` värde att `true` att exempelprogrammet skapar och använder simulerade sensordata.

1. Spara och avsluta genom att trycka på kontrollen-O > RETUR > kontroll-X.


1. Kör exempelprogrammet genom att köra följande kommando:

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   Kontrollera att du kopiera och klistra in anslutningssträngen i de enkla citattecken.

Du bör se följande utdata som visar sensordata och meddelanden som skickas till din IoT hub.

![Resultat – sensordata som skickas från Intel Edison till din IoT-hubb](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>Nästa steg

Du har kört ett exempelprogram för att samla in data för kroppssensor och skicka den till din IoT-hubb.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
