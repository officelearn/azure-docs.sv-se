---
title: Ansluta en hallon Pi till Azure IoT Suite med C för att stödja uppdateringar av inbyggd | Microsoft Docs
description: Använd Microsoft Azure IoT-startpaket för Raspberry Pi 3 och Azure IoT Suite. Använd C om du vill ansluta din hallon Pi till fjärranslutna övervakningslösning skicka telemetri från sensorer till molnet, och utför en fjärransluten firmware-uppdatering.
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
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012066"
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-c"></a>Anslut din hallon Pi 3 till fjärranslutna övervakningslösning och aktivera fjärråtkomst firmware-uppdateringar med hjälp av C

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

Den här kursen visar hur du använder Microsoft Azure IoT-startpaket för hallon Pi 3:

* Utveckla en läsare för temperatur- och fuktighetskonsekvens som kan kommunicera med molnet.
* Aktivera och utföra en fjärransluten inbyggd programvara update uppdatering klientprogrammet på hallon Pi.

I självstudiekursen används:

* Raspbian OS programmeringsspråket C och Microsoft Azure IoT-SDK för C att implementera en exempel-enhet.
* IoT Suite fjärråtkomst övervakning förkonfigurerade lösning som molnbaserade serverdelen.

## <a name="overview"></a>Översikt

I den här kursen kan du utföra följande steg:

* Distribuera en instans av fjärråtkomst övervakning förkonfigurerade lösningen till din Azure-prenumeration. Det här steget kan du automatiskt distribuerar och konfigurerar Azure-tjänster.
* Konfigurera din enhet och sensorer för kommunikation med datorn och den fjärranslutna övervakningslösning.
* Uppdatera enheten exempelkod för att ansluta till den fjärranslutna övervakningslösning och skicka telemetri som kan visas på instrumentpanelen för lösningen.
* Använda exempelkod för enheten för att uppdatera klientprogrammet.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> Fjärråtkomst övervakningslösning etablerar en mängd olika Azure-tjänster i din Azure-prenumeration. Distributionen visar en verklig enterprise-arkitektur. Ta bort din instans av förkonfigurerade lösningen vid azureiotsuite.com för att undvika onödiga Azure-förbrukningen avgifter, när du är klar med den. Om du behöver den förkonfigurerade lösningen, kan du enkelt återskapa den. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config].

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>Hämta och konfigurera exemplet

Du kan nu hämta och konfigurera fjärråtkomst övervakning klientprogrammet på din hallon Pi.

### <a name="clone-the-repositories"></a>Klona databaser

Om du inte redan har gjort klona krävs databaser genom att köra följande kommandon på din Pi:

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>Uppdatera anslutningssträngen enhet

Öppna exempelkonfigurationsfilen i den **nano** redigeraren med följande kommando:

```sh
nano ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

Ersätt platshållarvärdena med ID- och IoT-hubb enhetsinformationen du skapade och sparade i början av den här kursen.

När du är klar innehållet i filen deviceinfo bör se ut som i följande exempel:

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

Spara dina ändringar (**Ctrl-O**, **RETUR**) och avsluta redigeraren (**Ctrl + X**).

## <a name="build-the-sample"></a>Bygga exemplet

Om du inte redan har gjort det, ska du installera de nödvändiga paketen för Microsoft Azure IoT-enhet SDK för C genom att köra följande kommandon i en terminal på hallon Pi:

```sh
sudo apt-get update
sudo apt-get install g++ make cmake git libcurl4-openssl-dev libssl-dev uuid-dev
```

Du kan nu skapa provlösningen på hallon Pi:

```sh
chmod +x ~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
~/iot-remote-monitoring-c-raspberrypi-getstartedkit/advanced/1.0/build.sh
```

Du kan nu köra det här exempelprogrammet på hallon Pi. Ange kommandot:

  ```sh
  sudo ~/cmake/remote_monitoring/remote_monitoring
  ```

Följande exempel på utdata är ett exempel på utdata som du ser i Kommandotolken på hallon Pi:

![Utdata från Raspberry Pi-app][img-raspberry-output]

Tryck på **Ctrl-C** avsluta programmet när som helst.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. I lösningen instrumentpanelen, klickar du på **enheter** besöka den **enheter** sidan. Välj din Raspberry Pi i den **enhetslistan**. Välj **metoder**:

    ![Lista över enheter i instrumentpanelen][img-list-devices]

1. På den **anropa metoden** väljer **InitiateFirmwareUpdate** i den **metoden** listrutan.

1. I den **FWPackageURI** anger **https://github.com/Azure-Samples/iot-remote-monitoring-c-raspberrypi-getstartedkit/raw/master/advanced/2.0/package/remote_monitoring.zip**. Det här arkivet innehåller implementeringen av version 2.0 av den inbyggda programvaran.

1. Välj **InvokeMethod**. Appen på hallon Pi skickar en bekräftelse tillbaka till instrumentpanelen lösning. Därefter startar den inbyggda programvara uppdateringen genom att hämta den nya versionen av den inbyggda programvaran:

    ![Visa historiken för metoden][img-method-history]

## <a name="observe-the-firmware-update-process"></a>Se den inbyggda programvaran uppdatera process

Du kan se den inbyggda programvaran uppdatera process som körs på enheten och genom att visa rapporterade egenskaperna i instrumentpanelen för lösningen:

1. Du kan visa förloppet på för uppdateringsprocessen på hallon Pi:

    ![Visa förlopp för uppdatering][img-update-progress]

    > [!NOTE]
    > Fjärråtkomst övervakning appen startar om tyst när uppdateringen är klar. Använd kommandot `ps -ef` att verifiera att den körs. Om du vill avsluta processen använder den `kill` med process-id.

1. Du kan visa statusen för firmware-uppdatering som rapporteras av enheten i lösningen portal. Följande skärmbild visar status och varaktighet för varje fas av uppdateringen och den nya versionen av inbyggd programvara:

    ![Visa jobbstatus][img-job-status]

    Om du gå tillbaka till instrumentpanelen kan du kontrollera att enheten är fortfarande skicka telemetri följande firmware-uppdatering.

> [!WARNING]
> Om du lämnar den fjärranslutna övervakningslösning som körs i ditt Azure-konto, debiteras du för den tid som den körs. Mer information om hur du minskar användning när den fjärranslutna övervakningslösning körs finns [konfigurerar Azure IoT Suite förkonfigurerade lösningar för demonstration][lnk-demo-config]. Ta bort den förkonfigurerade lösningen från ditt Azure-konto när du är klar.

## <a name="next-steps"></a>Nästa steg

Besök den [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) fler exempel och dokumentation om Azure IoT.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md