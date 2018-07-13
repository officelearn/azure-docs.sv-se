---
title: Ansluta en Raspberry Pi till Azure IoT Suite med hjälp av C för uppdateringar av inbyggd programvara | Microsoft Docs
description: Använd Microsoft Azure IoT-startpaket för Raspberry Pi 3 och Azure IoT Suite. Använd C för att ansluta Raspberry Pi till fjärrövervakningslösningen, skicka telemetri från sensorer till molnet och utför en fjärransluten firmware-uppdatering.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 8160752b0116c3ef3e6b6ab7920bb35e471f180b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38687703"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Anslut Raspberry Pi 3 till lösningen för fjärrövervakning och aktivera fjärråtkomst firmwareuppdateringar med hjälp av C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Den här självstudien visar hur du använder Microsoft Azure IoT-startpaket för Raspberry Pi 3 för att:

* Utveckla en läsare för temperaturen och fuktigheten som kan kommunicera med molnet.
* Aktivera och utföra en fjärransluten firmware uppdatering att uppdatera klientprogrammet på Raspberry Pi.

I självstudiekursen används:

* Raspbian OS programmeringsspråket C och Microsoft Azure IoT SDK för C för att implementera en exempel-enhet.
* I IoT Suite för den förkonfigurerade lösningen som molnbaserad serverdel.

## <a name="overview"></a>Översikt

I den här självstudien gör du följande:

* Distribuera en instans av den förkonfigurerade lösningen för fjärrövervakning till din Azure-prenumeration. Det här steget automatiskt distribuerar och konfigurerar flera Azure-tjänster.
* Konfigurera din enhet och sensorer att kommunicera med datorn och lösningen för fjärrövervakning.
* Uppdatera exempelkod för enheten att ansluta till lösningen för fjärrövervakning och skickar telemetri som du kan titta på instrumentpanelen för lösningen.
* Använd exempelkoden för enheten för att uppdatera klientprogrammet.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Lösningen för fjärrövervakning etablerar en uppsättning Azure-tjänster i Azure-prenumerationen. Distributionen återspeglar en verklig företagsarkitektur. Om du vill undvika onödiga Azure-förbrukning kostnader genom att ta bort din instans av den förkonfigurerade lösningen på azureiotsuite.com när du är klar med den. Om du behöver den förkonfigurerade lösningen kan återskapa du enkelt den. Läs mer om att minskas när fjärrövervakningslösningen körs [konfigurera Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Hämta och konfigurera exemplet

Du kan nu hämta och konfigurera fjärråtkomst övervakning klientprogrammet på Raspberry Pi.

### <a name="clone-the-repositories"></a>Klona lagringsplatser

Om du inte redan gjort det, klona de nödvändiga databaserna genom att köra följande kommandon på din Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Uppdatera enhetens anslutningssträng

Öppna konfigurationsfilen exemplet i den **nano** redigeraren med följande kommando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Ersätt platshållarvärdena med ID: T och IoT Hub enhetsinformationen du skapade och sparade i början av den här självstudien.

När du är klar bör innehållet i deviceinfo-fil se ut som i följande exempel:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Spara dina ändringar (**Ctrl-O**, **RETUR**) och avsluta redigeraren (**Ctrl + X**).

## <a name="build-the-sample"></a>Skapa exemplet

Om du inte redan har gjort det, ska du installera de nödvändiga paketen för Microsoft Azure IoT Device SDK för C genom att köra följande kommandon i en terminal på Raspberry Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Du kan nu skapa exempellösningen på Raspberry Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Du kan nu köra exempelprogrammet på Raspberry Pi. Ange kommandot:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

Följande exempel på utdata är ett exempel på utdata som du ser i Kommandotolken på Raspberry Pi:

![Utdata från Raspberry Pi-app][img-raspberry-output]

Tryck på **Ctrl-C** att avsluta programmet när som helst.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. I instrumentpanelen för lösningen klickar du på **enheter** att besöka den **enheter** sidan. Välj Raspberry Pi i den **enhetslistan**. Välj sedan **metoder**:

    ![Lista över enheter i instrumentpanelen][img-list-devices]

1. På den **anropa metod** väljer **InitiateFirmwareUpdate** i den **metoden** listrutan.

1. I den **FWPackageURI** anger **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Det här arkivet innehåller implementeringen av version 2.0 av den inbyggda programvaran.

1. Välj **InvokeMethod**. Appen på Raspberry Pi skickar en bekräftelse tillbaka till instrumentpanelen för lösningen. Därefter startar uppdateringsprocessen genom att ladda ned den nya versionen av den inbyggda programvaran:

    ![Visa metodhistorik][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Notera den inbyggda programvaran uppdatera process

Du kan se den inbyggda programvaran uppdateringsprocessen under tiden den pågår på enheten och genom att visa de rapporterade egenskaperna i lösningens instrumentpanel:

1. Du kan visa förloppet i för uppdateringsprocessen på Raspberry Pi:

    ![Visa förlopp för uppdatering][img-update-progress]

    > [!NOTE]
    > Fjärråtkomst övervakning appen startar om tyst när uppdateringen är klar. Använd kommandot `ps -ef` att verifiera att den körs. Om du vill avsluta processen kan använda den `kill` med process-id.

1. Du kan visa status för uppdatering av inbyggd programvara som rapporteras av enheten i lösningsportalen. Följande skärmbild visar status och varaktigheten för varje steg av uppdateringsprocessen och den nya versionen för inbyggd programvara:

    ![Visa jobbstatus][img-job-status]

    Om du går tillbaka till instrumentpanelen kan du kontrollera att enheten fortfarande skickar telemetri efter uppdatering av inbyggd programvara.

> [!WARNING]
> Om du lämnar den lösningen för fjärrövervakning som körs i ditt Azure-konto, debiteras du för den tid som den körs. Läs mer om att minskas när fjärrövervakningslösningen körs [konfigurera Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config]. Ta bort den förkonfigurerade lösningen från ditt Azure-konto när du är klar med den.

## <a name="next-steps"></a>Nästa steg

Gå till den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) för fler exempel och dokumentation om Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md