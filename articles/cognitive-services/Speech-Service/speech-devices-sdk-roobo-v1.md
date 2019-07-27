---
title: Tal enheter SDK Roobo Smart Audio dev kit v1-tal service
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med tal enheter SDK, Roobo Smart Audio dev kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 08bac7cd833f52d2dfec4561c2f87330a4119748
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552875"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Enhet: Roobo Smart Audio dev-paket

Den här artikeln innehåller enhets information för Roobo Smart Audio dev kit.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit

1. Utvecklings paketet har två mikrousb-anslutningar. Den vänstra kopplingen är att sätta utvecklings paketet och är markerat som exponent i bilden nedan. Det högra är att kontrol lera den och har marker ATS som en fel sökning i avbildningen.

    ![ansluta dev-paket](media/speech-devices-sdk/qsg-1.png)

1. Utveckla utvecklings paketet genom att använda en mikrousb-kabel för att ansluta ström porten till en dator eller ett nätadapter. En grön energi indikator kommer att tändas under det översta brädet.

1. Om du vill styra utvecklings paketet ansluter du fel söknings porten till en dator genom att använda en andra Micro USB-kabel. Det är viktigt att använda en hög kvalitets kabel för att säkerställa tillförlitlig kommunikation.

1. Förstå din Utvecklingskit för antingen cirkulär eller linjär konfigurationen.

    |Development kit konfiguration|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Handen, riktas mot taket med mikrofoner|
    |Linjär|På sidan, inför med mikrofoner du (visas i följande bild)|

    ![linjär dev kit orientering](media/speech-devices-sdk/qsg-2.png)

1. Installera certifikaten och ange behörigheterna för ljud enheten. Skriv följande kommandon i Kommandotolken:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > De här kommandona använder Android Debug-bryggan, `adb.exe`, vilket är en del av Android Studio-installationen. Det här verktyget finns i C:\Users\[användarnamn] \AppData\Local\Android\Sdk\platform-verktyg. Du kan lägga till den här katalogen till sökvägen att göra det mer praktiskt att anropa `adb`. Annars måste du ange den fullständiga sökvägen för din installation av adb.exe i alla kommandon som anropar `adb`.
    >
    > Om du ser ett fel `no devices/emulators found` kan du kontrol lera att USB-kabeln är ansluten och är en högkvalitativ kabel. Du kan använda `adb devices` för att kontrol lera att datorn kan kommunicera med utvecklings paketet eftersom den kommer att returnera en lista över enheter.
    >
    > [!TIP]
    > Stäng av datorns mikrofon och högtalare för att säkerställa att du arbetar med i development kit mikrofoner. På så sätt kan du inte av misstag utlösa enheten med ljud från datorn.

1. Om du vill koppla en talare till dev-paket kan ansluta du den till ljud rad ut. Du bör välja en bra kvalitets högtalare med en analog kontakt med 3,5 mm.

    ![Vysor ljud](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Utvecklings information

Mer utvecklings information finns i [utvecklings guiden för Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Ljud

Roobo tillhandahåller ett verktyg som fångar upp allt ljud till Flash-minnet. Det kan hjälpa dig felsökning av problem med ljud. En version av verktyget har angetts för varje development kit-konfiguration. Välj din enhet på [Roobo-webbplatsen](https://ddk.roobo.com/)och välj sedan länken **Roobo tools** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* [Kör appen Android-exempel](speech-devices-sdk-android-quickstart.md)
