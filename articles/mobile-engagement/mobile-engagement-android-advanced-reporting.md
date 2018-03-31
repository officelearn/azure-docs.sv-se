---
title: Avancerade reporting alternativ för Azure Mobile Engagement Android SDK
description: Beskriver hur du gör avancerade rapportering för att avbilda analytics för Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 419b65250d2b65e184e6e36349b17b5ac9e7a6ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Avancerade rapportering med Engagement på Android
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!div class="op_single_selector"]
> * [Universell Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Det här avsnittet beskrivs ytterligare rapporteringsscenarier i din Android-App. Du kan använda dessa alternativ för den app som skapats i den [komma igång](mobile-engagement-android-get-started.md) kursen.

## <a name="prerequisites"></a>Förutsättningar
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Kursen du slutfört har avsiktligt direkt och enkel, men det är avancerade alternativ som du kan välja.

## <a name="modifying-your-activity-classes"></a>Ändra din `Activity` klasser
I den [komma igång-kursen](mobile-engagement-android-get-started.md), alla var du tvungen att göra har att göra din `*Activity` underklasser ärva från motsvarande `Engagement*Activity` klasser. Till exempel om en bakåtkompatibel aktivitet utökat `ListActivity`, gör du det utöka `EngagementListActivity`.

> [!IMPORTANT]
> När du använder `EngagementListActivity` eller `EngagementExpandableListActivity`, se till att alla anrop till `requestWindowFeature(...);` görs innan anropet till `super.onCreate(...);`, annars kraschar.
> 
> 

Du hittar de här klasserna i den `src` mappen och kopiera dem till ditt projekt. Klasserna finns också i den **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativ metod: anropa `startActivity()` och `endActivity()` manuellt
Om du inte kan eller inte vill överlagra din `Activity` klasser, kan du i stället börja och sluta dina aktiviteter genom att anropa den `EngagementAgent`'s metoder direkt.

> [!IMPORTANT]
> Android SDK anropar aldrig den `endActivity()` metod, även när programmet avslutas (på Android program aldrig stängs). Därför är det *hög* rekommenderas att anropa den `startActivity()` metod i den `onResume` återanrop av *alla* dina aktiviteter och `endActivity()` metod i den `onPause()` återanrop av *alla* dina aktiviteter. Detta är det enda sättet att se till att sessioner inte sprids. Om en session har läckts kopplar tjänsten Engagement aldrig från Engagement-serverdelen (eftersom tjänsten fortfarande ansluten så länge en session väntar).
> 
> 

Här är ett exempel:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Det här exemplet liknar den `EngagementActivity` klassen och dess varianter vars källkoden har angetts i den `src` mapp.

## <a name="using-applicationoncreate"></a>Med hjälp av Application.onCreate()
All kod som du `Application.onCreate()` och i andra program körs återanrop för ditt programs processer, inklusive tjänsten Engagement. Det kan ha oönskade sidoeffekter, som inte behövs minnesallokering och trådar i processen för hur engagerade eller dubbla broadcast mottagare eller tjänster.

Om du åsidosätter `Application.onCreate()`, rekommenderar vi att lägga till följande kodavsnitt i början av din `Application.onCreate()` funktionen:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Du kan göra samma sak för `Application.onTerminate()`, `Application.onLowMemory()`, och `Application.onConfigurationChanged(...)`.

Du kan också utöka `EngagementApplication` i stället för att utöka `Application`: återanropet `Application.onCreate()` har kontrollen av processen och anrop `Application.onApplicationProcessCreate()` endast om den aktuella processen inte är en värdtjänst Engagement samma regler gäller för de andra återanrop.

## <a name="tags-in-the-androidmanifestxml-file"></a>Taggar i filen AndroidManifest.xml
I service-tagg i filen AndroidManifest.xml den `android:label` attribut kan du välja namnet på tjänsten Engagement som det visas för slutanvändare på skärmen ”kör services” i sin telefon. Vi rekommenderar att det här attributet `"<Your application name>Service"` (till exempel `"AcmeFunGameService"`).

Ange den `android:process` attributet säkerställer att tjänsten Engagement körs i sin egen process (som kör Engagement i samma process som programmet gör main/UI-tråden potentiellt mindre känslig).

## <a name="building-with-proguard"></a>Skapa med ProGuard
Om du skapar ditt programpaket med ProGuard, måste du behålla vissa klasser. Du kan använda följande kodavsnitt i konfigurationen:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
