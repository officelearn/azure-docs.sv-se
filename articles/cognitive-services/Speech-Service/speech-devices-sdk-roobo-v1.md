---
title: Talenheter SDK Roobo Smart Audio Dev Kit v1 - Taltjänst
titleSuffix: Azure Cognitive Services
description: Förutsättningar och instruktioner för att komma igång med Speech Devices SDK, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815588"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Enhet: Roobo Smart Audio Dev Kit

Den här artikeln innehåller enhetsspecifik information för Roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>Ställ in utvecklingspaketet

1. Utvecklingssatsen har två mikro-USB-kontakter. Den vänstra kontakten är att driva utvecklingssatsen och markeras som Power i bilden nedan. Den rätta är att kontrollera den, och är märkt Debug i bilden.

    ![Ansluta utvecklingspaketet](media/speech-devices-sdk/qsg-1.png)

1. Ström av utvecklingssatsen genom att använda en mikro-USB-kabel för att ansluta strömporten till en dator eller strömadapter. En grön strömindikator tänds under den övre brädan.

1. Om du vill styra utvecklingspaketet ansluter du felsökningsporten till en dator med hjälp av en andra mikro-USB-kabel. Det är viktigt att använda en högkvalitativ kabel för att säkerställa tillförlitlig kommunikation.

1. Rikta ditt utvecklingspaket för antingen den cirkulära eller linjära konfigurationen.

    |Konfiguration av utvecklingspaket|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Upprätt, med mikrofoner mot taket|
    |Linjär|På sidan, med mikrofoner vända mot dig (visas i följande bild)|

    ![Linjär dev kit orientering](media/speech-devices-sdk/qsg-2.png)

1. Installera certifikaten och ange behörigheterna för ljudenheten. Skriv följande kommandon i ett kommandotolksfönster:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Dessa kommandon använder Android Debug `adb.exe`Bridge, som är en del av Android Studio-installationen. Det här verktyget finns i\[C:\Användares användarnamn]\AppData\Local\Android\Sdk\platform-tools. Du kan lägga till den här katalogen i `adb`sökvägen så att det blir bekvämare att anropa . Annars måste du ange den fullständiga sökvägen till installationen av adb.exe i varje kommando som anropar `adb`.
    >
    > Om du ser `no devices/emulators found` ett fel kontrollerar du att USB-kabeln är ansluten och är en kabel av hög kvalitet. Du kan `adb devices` använda för att kontrollera att datorn kan prata med utvecklingspaketet eftersom det returnerar en lista över enheter.
    >
    > [!TIP]
    > Stäng av datorns mikrofon och högtalare för att vara säker på att du arbetar med utvecklingssatsens mikrofoner. På så sätt kommer du inte av misstag att utlösa enheten med ljud från datorn.

1. Om du vill ansluta en högtalare till utvecklingssatsen kan du ansluta den till ljudlinjen ut. Du bör välja en högtalare av god kvalitet med en 3,5 mm analog kontakt.

    ![Vysor ljud](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Utvecklingsinformation

Mer utvecklingsinformation finns i [Roobos utvecklingsguide](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Ljud

Roobo tillhandahåller ett verktyg som fångar in allt ljud för att flasha minnet. Det kan hjälpa dig att felsöka ljudproblem. En version av verktyget tillhandahålls för varje konfiguration av utvecklingspaket. På [Roobo-webbplatsen](https://ddk.roobo.com/)väljer du enheten och väljer sedan länken **Roobo Tools** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* [Köra exempelappen För Android](speech-devices-sdk-android-quickstart.md)
