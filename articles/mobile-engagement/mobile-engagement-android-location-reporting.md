---
title: "Plats för Azure Mobile Engagement Android SDK"
description: "Beskriver hur du konfigurerar platsen för Azure Mobile Engagement Android SDK"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Plats för Azure Mobile Engagement Android SDK
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Det här avsnittet beskriver hur du gör plats rapportering för din Android-App.

## <a name="prerequisites"></a>Krav
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Platsrapportering
Om du vill att platser som ska rapporteras som du behöver lägga till några rader configuration (mellan den `<application>` och `</application>` taggar).

### <a name="lazy-area-location-reporting"></a>Områdesrapportering
Områdesrapportering kan reporting land, region och ort som är associerade med enheter. Den här typen av plats reporting används endast nätverksplatser (baserat på cellen ID eller Wi-Fi). Området enheten rapporteras som mest en gång per session. GPS används aldrig och därmed den här typen av plats rapporten har låg påverkan på batteridrift.

Rapporterat områden används för att beräkna geografiska statistik om användare, sessioner, händelser och fel. De kan också användas som kriterium i Reach-kampanjer.

Du aktiverar lazy Områdesplats rapportering genom att använda konfigurationen som nämnts tidigare i den här proceduren:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du måste också ange en plats-behörighet. Den här koden använder ``COARSE`` behörighet:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Om din app kräver det, kan du använda ``ACCESS_FINE_LOCATION`` i stället.

### <a name="real-time-location-reporting"></a>Rapportering i realtid plats
Rapportering i realtid plats kan reporting latituden och longituden som är associerade med enheter. Som standard används den här typen av plats reporting endast platser i nätverket, baserat på cellen ID eller Wi-Fi. Reporting är endast aktiv när programmet körs i förgrunden (till exempel under en session).

Realtid platser är *inte* används för att beräkna statistik. Deras enda syfte är att tillåta användning av geobegränsning i realtid \<Reach-målgruppen-geofencing\> kriterium i Reach-kampanjer.

Lägga till en rad med kod där du kan ange anslutningssträngen Engagement i aktiviteten starta om du vill aktivera realtidsskydd plats reporting. Resultatet ser ut ungefär så här:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS baseras reporting
Som standard använder rapportering i realtid plats endast nätverksbaserade platser. Använd konfigurationsobjektet för att aktivera användning av GPS-baserade platser, vilket är mycket mer precisa:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du måste också lägga till följande behörigheter om de saknas:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapportering i bakgrunden
Som standard är rapportering i realtid plats endast aktiv när programmet körs i förgrunden (till exempel under en session). Aktivera reporting även i bakgrunden med det här konfigurationsobjektet:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> När programmet körs i bakgrunden, nätverksbaserade platser rapporteras även om du har aktiverat GPS.
> 
> 

Om användaren startar om sin enhet, har rapportens bakgrund plats stoppats. Lägg till denna kod så att den startar om automatiskt när datorn startas.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Du måste också lägga till följande behörigheter om de saknas:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M-behörigheter
Från och med Android M kan vissa behörigheter hanteras vid körning och kräver användargodkännande.

Om du riktar Android API-nivå 23 är behörigheterna runtime inaktiverade som standard för nya installationer av appar. Annars är aktiverade som standard.

Du kan aktivera och inaktivera dessa behörigheter på inställningsmenyn för enheten. Om du inaktiverar behörigheter från system-menyn stängs bakgrundsprocesser av programmet, vilket är en systembeteende och har ingen inverkan på förmåga att ta emot push i bakgrunden.

I samband med Mobile Engagement plats reporting är de behörigheter som kräver godkännande vid körning:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Begär behörighet från den användare som använder en standard systemdialogruta. Om användaren godkänner berätta ``EngagementAgent`` göra den ändringen i beräkningen i realtid. Annars bearbetas ändringen nästa gång användaren startar programmet.

Här följer ett kodexempel på ska användas i en aktivitet på ditt program begär behörighet och vidarebefordra resultatet om positivt till ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
