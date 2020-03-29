---
title: Verkliga användarmätningar med Visual Studio Mobile Center - Azure Traffic Manager
description: Konfigurera din mobilapplikation som utvecklats med Visual Studio Mobile Center för att skicka riktiga användarmätningar till Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939188"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Så här skickar du riktiga användarmätningar till Traffic Manager med Visual Studio Mobile Center

Du kan konfigurera din mobilapplikation som utvecklats med Visual Studio Mobile Center för att skicka riktiga användarmätningar till Traffic Manager genom att följa stegen:

>[!NOTE]
> För närvarande stöds endast att skicka riktiga användarmätningar till Traffic Manager för Android.

Om du vill konfigurera reala användarmätningar måste du skaffa en nyckel och instrumentera appen med RUM-paketet.

## <a name="step-1-obtain-a-key"></a>Steg 1: Skaffa en nyckel
    
De mätningar du tar och skickade till Traffic Manager från klientprogrammet identifieras av tjänsten med hjälp av en unik sträng, kallad RUM-nyckeln (Real User Measurements). Du kan hämta en RUM-nyckel med Azure-portalen, ett REST API eller genom att använda PowerShell/ CLI-gränssnitten.

Så här hämtar du RUM-nyckeln med hjälp av Azure-portalen med följande procedur:
1. Logga in på Azure Portal från en webbläsare. Om du inte redan har ett konto kan du registrera dig för en kostnadsfri utvärderingsmånad.
2. Leta efter namnet på Traffic Manager-profilen som du vill modifiera och klicka sedan på Traffic Manager-profilen i resultaten som visas.
3. Klicka på **Riktiga användarmått** under **Inställningar**på profilsidan Traffic Manager .
4. Klicka på **Generera nyckel** för att skapa en ny RUM-nyckel.
        
   ![Generera nyckeln Verkliga användarmätningar](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Bild 1: Nyckelgenerering för verkliga användarmätningar**

5. På sidan visas DEN RUM-nyckel som genereras och ett JavaScript-kodavsnitt som måste bäddas in på HTML-sidan.
 
   ![Javascript-kod för nyckeln Riktiga användarmätningar](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Bild 2: Real User Measurements Key och Mätning JavaScript**
 
6. Klicka på knappen **Kopiera** om du vill kopiera RUM-tangenten. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Steg 2: Instrumentera din app med RUM-paketet med Mobile Center SDK

Om du inte har något nytt för Visual Studio Mobile Center besöker du dess [webbplats](https://mobile.azure.com). Detaljerade instruktioner om SDK-integrering finns i [Komma igång med Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Så här använder du riktiga användarmätningar:

1.  Lägga till SDK i projektet

    Under förhandsgranskningen av ATM RUM SDK måste du uttryckligen referera till paketdatabasen.

    Lägg till följande rader i **filen app/build.gradle:**

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Lägg till följande rader i **filen app/build.gradle:**

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Starta SDK

    Öppna appens huvudaktivitetsklass och lägg till följande importsatser:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Leta efter `onCreate` motringningen i samma fil och lägg till följande kod:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [riktiga användarmätningar](traffic-manager-rum-overview.md)
- Läs om [hur Traffic Manager fungerar](traffic-manager-overview.md)
- Läs mer om [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Registrera dig](https://mobile.azure.com) för Mobile Center
- Läs mer om [de trafikroutningsmetoder](traffic-manager-routing-methods.md) som stöds av Traffic Manager
- Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

