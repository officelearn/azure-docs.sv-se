---
title: Tal enheter SDK Roobo Smart ljud Dev sats v1 - Speech Services
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med tal Devices SDK Roobo Smart ljud Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 0e5dc73c1f24ccbc2032cecbb857587eb20c6806
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026214"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Enhet: Roobo Smart ljud Dev-paket

Den här artikeln innehåller specifika enhetsinformation för Roobo Smart ljud Dev Kit.

## <a name="set-up-the-development-kit"></a>Ställ in i development kit

1. I development kit har två micro USB-anslutningar. Den vänstra anslutningen är att köra i development kit och markeras som kraften i bilden nedan. Höger en är ta kontroll över den och markeras felsöka i avbildningen.

    ![ansluta dev-paket](media/speech-devices-sdk/qsg-1.png)

1. Power i development kit med micro USB-kabel för att ansluta power-port till en dator eller driva nätverkskort. En grön power indikator tänds under den översta tavlan.

1. Om du vill styra i development kit, ansluter du debug-port till en dator med hjälp av en andra micro USB-kabel. Det är viktigt att använda en högkvalitativ kabel för att säkerställa tillförlitlig kommunikation.

1. Förstå din Utvecklingskit för antingen cirkulär eller linjär konfigurationen.

    |Development kit konfiguration|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Handen, riktas mot taket med mikrofoner|
    |Linjär|På sidan, inför med mikrofoner du (visas i följande bild)|

    ![linjär dev kit orientering](media/speech-devices-sdk/qsg-2.png)

1. Installera certifikat och ange behörigheter för enheten. Skriv följande kommandon i Kommandotolken:

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
    > Om du ser ett fel `no devices/emulators found` sedan kontrollera USB-kabel är ansluten och är en högkvalitativ kabel. Du kan använda `adb devices` att kontrollera att datorn kan kommunicera med i development kit som returneras en lista över enheter.
    >
    > [!TIP]
    > Stäng av datorns mikrofon och högtalare för att säkerställa att du arbetar med i development kit mikrofoner. På så sätt kan du inte av misstag utlösa enheten med ljud från datorn.

1. Om du vill koppla en talare till dev-paket kan ansluta du den till ljud rad ut. Du bör välja en god kvalitet högtalare med en 3,5 mm analog plugin.

    ![Vysor ljud](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Information om utveckling

Läs mer i utveckling, den [Roobo Utvecklingsguide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Ljud

Roobo ger ett verktyg som samlar in alla ljud flash-minnet. Det kan hjälpa dig felsökning av problem med ljud. En version av verktyget har angetts för varje development kit-konfiguration. På den [Roobo plats](http://ddk.roobo.com/), Välj din enhet och välj sedan den **Roobo verktyg** länken längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* [Köra Android-exempelappen](speech-devices-sdk-android-quickstart.md)
