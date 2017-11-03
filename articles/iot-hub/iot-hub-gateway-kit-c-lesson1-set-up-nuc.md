---
title: 'SensorTag enhet & Azure IoT Gateway - lektionen 1: Konfigurera Intel NUC | Microsoft Docs'
description: "Ställ in Intel NUC ska fungera som en IoT-gateway mellan en sensor och Azure IoT-hubb för att samla in sensorinformation och skicka den till IoT-hubb."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: IOT-gateway, intel nuc nuc dator, DE3815TYKE
ms.assetid: 917090d6-35c2-495b-a620-ca6f9c02b317
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.openlocfilehash: 1a3a92ab8d08c6ed6f047208217c46022027157e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-intel-nuc-as-an-iot-gateway"></a>Ställ in Intel NUC som en IoT-gateway
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

## <a name="what-you-will-do"></a>Vad du ska göra

- Ställ in Intel NUC som en IoT-gateway.
- Installera Azure IoT kanten på Intel NUC.
- Kör ett exempelprogram för ”hello_world” på Intel NUC att verifiera gateway-funktionerna.

  > Om du har några problem kan hitta lösningar på den [felsökning sidan](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="what-you-will-learn"></a>Vad får du lära dig

I den här lektionen får du lära dig:

- Så här ansluter du Intel NUC med kringutrustning.
- Hur du installerar och uppdatera de nödvändiga paketen på Intel NUC med Smart Package Manager.
- Så här kör exempelprogrammet ”hello_world” för att verifiera gateway-funktionerna.

## <a name="what-you-need"></a>Vad du behöver

- En Intel NUC Kit DE3815TYKE med Intel IoT Gateway-programsviten (vind floden Linux * 7.0.0.13) förinstallerat. [Klicka här om du vill köpa Groove IoT kommersiella Gateway Kit](https://www.seeedstudio.com/Grove-IoT-Commercial-Gateway-Kit-p-2724.html).
- En Ethernet-kabel.
- Ett tangentbord.
- HDMI eller VGA-kabel.
- En bildskärm med en HDMI eller VGA-port.
- Valfritt: [Texas Instruments Sensor Tag (CC2650STK)](http://www.ti.com/tool/cc2650stk)

![Gateway-Kit](media/iot-hub-gateway-kit-lessons/lesson1/kit.png)

## <a name="connect-intel-nuc-with-the-peripherals"></a>Anslut Intel NUC med kringutrustning

På bilden nedan är ett exempel på Intel NUC som är kopplad till olika kringutrustning:

1. Ansluten till ett tangentbord.
2. Ansluten till en bildskärm med en VGA- eller HDMI kabel.
3. Ansluten till ett kabelanslutet nätverk med en Ethernet-kabel.
4. Ansluten till en strömförsörjning med en power-kabel.

![Intel NUC ansluten till annan kringutrustning](media/iot-hub-gateway-kit-lessons/lesson1/nuc.png)

## <a name="connect-to-the-intel-nuc-system-from-host-computer-via-secure-shell-ssh"></a>Ansluta till Intel NUC system från värddatorn via SSH (Secure Shell)

Du behöver ett tangentbord och en Övervakare för att få IP-adressen till enheten Intel NUC. Om du redan vet IP-adress kan gå du vidare till steg 3 i det här avsnittet.

1. Aktivera Intel NUC genom att trycka på strömknappen och logga sedan in.

   Standard-användarnamn och lösenord är båda `root`.

       > Hit the enter key on your keyboard if you see either of the following errors when you boot: 'A TPM error (7) occurred attempting to read a pcr value.' or 'Timeout, No TPM chip found, activating TPM-bypass!'

2. Hämta IP-adressen för Intel-NUC genom att köra den `ifconfig` på Intel NUC enheten.

   Här är ett exempel på utdata från.

   ![ifconfig utdata visar Intel NUC IP](media/iot-hub-gateway-kit-lessons/lesson1/ifconfig.png)

   I detta exempel värdet som följer `inet addr:` är IP-adressen som du behöver när ansluter till Intel NUC från en värddator.

3. Använd en av följande SSH-klienter från värddatorn för att ansluta till Intel NUC.

    - [PuTTY](http://www.putty.org/) för Windows.
    - Den inbyggda SSH-klienten på Ubuntu eller macOS.

   Det är mer effektivt och effektiv att driva en Intel NUC från en värddator. Du behöver Intel-NUC IP-adress, användarnamn och lösenord för att ansluta till den via en SSH-klient. Här är ett exempel som använder en SSH-klient på macOS.
   ![SSH-klienten körs på macOS](media/iot-hub-gateway-kit-lessons/lesson1/ssh.png)

## <a name="install-the-azure-iot-edge-package"></a>Installera Azure IoT kant-paket

Azure IoT kant-paketet innehåller före kompilerade binärfilerna för IoT kant och dess beroenden. Dessa binärfiler är Azure IoT kant, Azure IoT-SDK och motsvarande verktyg. Paketet innehåller också en ”hello_world” exempelprogrammet används för att verifiera gateway-funktionerna. IoT-Edge ingår kärnor i gatewayen. 

Följ dessa steg för att installera paketet.

1. Lägg till molnet för IoT-databasen genom att köra följande kommandon i ett terminalfönster:

   ```bash
   rpm --import https://iotdk.intel.com/misc/iot_pub2.key
   smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
   smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
   ```

   > Ange ”y” när du uppmanas du att inkludera på den här kanalen?
   
   Om du får ett `import read failed(-1)` fel, Använd följande kommandon för att lösa problemet:
   ```bash
   wget http://iotdk.intel.com/misc/iot_pub2.key 
   rpm --import iot_pub2.key  
   ```

   Den `rpm` kommando importerar rpm-nyckel. Den `smart channel` kommando lägger till rpm-kanal till Smart Package Manager. Innan du kör den `smart update` kommandot visas utdata som nedan.

   ![rpm och smarta kanal kommandon utdata](media/iot-hub-gateway-kit-lessons/lesson1/rpm_smart_channel.png)

2. Kör kommandot smart update:

   ```bash
   smart update
   ```

3. Installera Azure IoT Gateway-paketet genom att köra följande kommando:

   ```bash
   smart install packagegroup-cloud-azure -y
   ```

   `packagegroup-cloud-azure`är namnet på paketet. Den `smart install` kommandot används för att installera paketet.

    > Kör följande kommando om du ser felet: 'offentlig nyckel som är inte tillgänglig ”

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```
    > Starta om Intel NUC om du ser felet: 'inga erbjuder util-linux-dev'

   När paketet har installerats, är Intel NUC redo att fungera som en gateway.

## <a name="run-the-azure-iot-edge-helloworld-sample-application"></a>Kör exempelprogrammet Azure IoT kant ”hello_world”

Följande exempelprogrammet skapar en gateway från en `hello_world.json` filen och använder de grundläggande komponenter av arkitekturen i Azure IoT kant för att logga en hello world-meddelande till en fil (log.txt) var femte sekund.

Du kan köra Hello World-exempel genom att köra följande kommandon:

```bash
cd /usr/share/azureiotgatewaysdk/samples/hello_world/
./hello_world hello_world.json
```

Kan programmet Hello World kör några minuter och tryck sedan på RETUR om du vill stoppa den.
![programutdata](media/iot-hub-gateway-kit-lessons/lesson1/hello_world.png)

> Du kan ignorera eventuella: Ogiltigt argument handle(NULL)'-fel som visas när du klickar på RETUR.

Du kan kontrollera att gatewayen har körts genom att öppna filen log.txt som nu finns i mappen hello_world ![log.txt directory vy](media/iot-hub-gateway-kit-lessons/lesson1/logtxtdir.png)

Öppna log.txt med följande kommando:

```bash
vim log.txt
```

Sedan visas innehållet i log.txt som ska vara en JSON-formaterade utdata loggning meddelanden som har skrivits var femte sekund av gateway Hello World-modulen.
![log.txt directory vy](media/iot-hub-gateway-kit-lessons/lesson1/logtxtview.png)

Om du har några problem kan hitta lösningar på den [felsökning sidan](iot-hub-gateway-kit-c-troubleshooting.md).

## <a name="summary"></a>Sammanfattning

Grattis! Du har slutfört konfigurationen Intel NUC som en gateway. Nu är du redo att gå vidare till nästa lektionen att konfigurera värddatorn, skapa en Azure IoT-hubb och registrera din logiska Azure IoT Hub-enhet.

## <a name="next-steps"></a>Nästa steg
[Använda en IoT-gateway för att ansluta en enhet till Azure IoT-hubb](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)

