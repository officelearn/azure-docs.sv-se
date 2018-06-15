---
title: Verklig användare mått i Azure Traffic Manager med Visual Studio Mobile Center | Microsoft Docs
description: Konfigurera din mobila program som utvecklats med hjälp av Visual Studio Mobile Center skicka verkliga användaren mått i Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 893e84b07b365fb0b534e0ddc021b2249c4174cf
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181018"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Hur du skickar verkliga användaren mått i Traffic Manager med Visual Studio Mobile Center

Du kan ställa in din mobila program som utvecklats med hjälp av Visual Studio Mobile Center skicka verkliga användaren mått i Traffic Manager genom att följa stegen:

>[!NOTE]
> För närvarande stöds skickar verkliga användaren mått till Traffic manager endast för Android.

Om du vill konfigurera verkliga användaren mätningar, måste du skaffa en nyckel och instrumentera din app med ROM paketet.

## <a name="step-1-obtain-a-key"></a>Steg 1: Få en nyckel
    
De mått som du tar och skickas till Traffic Manager från ditt klientprogram identifieras av tjänsten med hjälp av en unik sträng som kallas nyckeln verkliga användaren mått (ROM). Du kan få en ROM nyckel med hjälp av Azure portal, en REST-API eller genom att använda PowerShell / CLI-gränssnitt.

Du kan hämta ROM nyckeln med hjälp av Azure portal på följande sätt:
   1. Logga in på Azure-portalen från en webbläsare. Om du inte redan har ett konto kan registrera du dig för en kostnadsfri utvärderingsversion för en månad.
   2. I portalens sökfältet, söker du efter Traffic Manager-profilnamn som du vill ändra och klicka sedan på Traffic Manager-profilen i resultat som visas.
   3. På profilsidan Traffic Manager-klickar du på **verkliga användaren mätningar** under **inställningar**.
   4. Klicka på **Generera nyckel** att skapa en ny ROM-nyckel.
        
   ![Skapa verkliga användaren mätningar nyckel](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Verkliga användaren mätningar nyckelgenerering**

   5.   Sidan visar ROM-nyckeln som genereras och ett JavaScript-kodfragment som krävs för att bädda in i HTML-sidan.
 
   ![JavaScript-kod för verklig användare mätningar nyckeln](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Bild 2: Verkliga mätningar nyckeln och mätning JavaScript**
 
   6. Klicka på den **kopiera** för att kopiera nyckeln ROM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Steg 2: Instrumentera din app med ROM Mobile Center SDK-paketet

Om du inte har arbetat med Visual Studio Mobile Center gå dess [webbplats](https://mobile.azure.com). Detaljerad information om SDK-integration finns [komma igång med Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Slutför följande procedur om du vill använda den verkliga användaren mått:

1.  Lägg till SDK i projektet

    Under förhandsgranskningen av ATM ROM SDK måste du explicit referera till paketdatabasen.

    I din **app/build.gradle** filen lägger du till följande rader:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    I din **app/build.gradle** filen lägger du till följande rader:

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

    Leta efter den `onCreate` återanrop i samma fil- och Lägg till följande kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [verkliga användaren mått](traffic-manager-rum-overview.md)
- Läs [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Lär dig mer om [Center Mobile](https://docs.microsoft.com/mobile-center/)
- [Registrera dig](https://mobile.azure.com) för Mobile Center
- Lär dig mer om den [routning av nätverkstrafik metoder](traffic-manager-routing-methods.md) stöds av Traffic Manager
- Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

