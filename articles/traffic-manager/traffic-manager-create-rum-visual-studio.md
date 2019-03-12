---
title: Faktisk Slutanvändarmätning till Azure Traffic Manager med Visual Studio Mobile Center | Microsoft Docs
description: Konfigurera din mobila program som utvecklats med Visual Studio Mobile Center för att skicka faktisk Slutanvändarmätning till Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 35b4b06a09b8ef7b27e8d105d7f2336668c49f8f
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570869"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Hur du skickar faktisk Slutanvändarmätning till Traffic Manager med Visual Studio Mobile Center

Du kan ställa in din mobila program som utvecklats med Visual Studio Mobile Center för att skicka faktisk Slutanvändarmätning till Traffic Manager genom att följa stegen:

>[!NOTE]
> För närvarande kan stöds skicka faktisk Slutanvändarmätning till Traffic manager endast för Android.

Om du vill konfigurera Real User Measurements måste du hämta en nyckel och instrumenterar din app med köra paketet.

## <a name="step-1-obtain-a-key"></a>Steg 1: Hämta en nyckel
    
Mätningar du ta och skickas till Traffic Manager från klientprogrammet identifieras av tjänsten med hjälp av en unik sträng som kallas nyckeln Real User Measurements (ROM). Du kan få en köra nyckel med hjälp av Azure portal, REST-API eller med hjälp av PowerShell / CLI-gränssnitt.

Du kan hämta den ROM-nyckeln med hjälp av Azure portal på följande sätt:
   1. Logga in på Azure portal från en webbläsare. Om du inte redan har ett konto kan registrera du dig för en kostnadsfri utvärderingsversion för en månad.
   2. I portalens sökfältet söker du efter namnet på Traffic Manager-profilen som du vill ändra och klicka sedan på Traffic Manager-profilen i resultaten som visas.
   3. På profilsidan Traffic Manager-klickar du på **Real User Measurements** under **inställningar**.
   4. Klicka på **Generera nyckel** att skapa en ny ROM-nyckel.
        
   ![Generera Real User Measurements nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Real User Measurements nyckelgenerering**

   5.   Sidan visar ROM-nyckeln som genereras och ett JavaScript-kodfragment som krävs för att bädda in HTML-sidan.
 
   ![JavaScript-kod för Real User Measurements nyckel](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Bild 2: Real User Measurements nyckel och JavaScript-mått**
 
   6. Klicka på den **kopiera** för att kopiera nyckeln ROM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Steg 2: Instrumentera din app i köra Mobile Center SDK-paketet

Om du inte har använt Visual Studio Mobile Center, gå till dess [webbplats](https://mobile.azure.com). Detaljerad information om SDK-integrering finns [komma igång med Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Utför följande procedur för att använda Real User Measurements:

1.  Lägg till SDK: N i projektet

    Du måste uttryckligen referera paketdatabasen i förhandsversionen av ATM ROM SDK.

    I din **App/build.gradle** filen lägger du till följande rader:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    I din **App/build.gradle** filen lägger du till följande rader:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Starta SDK

    Öppna appens huvudaktivitetsklassen och Lägg till följande importuttryck:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Leta efter den `onCreate` återanrop i samma filen och Lägg till följande kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Real User Measurements](traffic-manager-rum-overview.md)
- Lär dig [så här fungerar Traffic Manager](traffic-manager-overview.md)
- Läs mer om [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Registrera dig](https://mobile.azure.com) för Mobile Center
- Läs mer om den [trafikroutningsmetoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

