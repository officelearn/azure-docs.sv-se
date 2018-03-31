---
title: Azure Mobile Engagement Android SDK Integration
description: Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Hur du integrerar Engagement på Android
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Den här proceduren beskriver det enklaste sättet att aktivera Engagement analyser och övervakningsfunktionerna i din Android-App.

> [!IMPORTANT]
> Den lägsta Android SDK-API-nivån måste vara 10 eller senare (Android 2.3.3 eller högre).
> 
> 

Följande steg är tillräckligt för att aktiverar rapporten loggar som behövs för att beräkna all statistik om användare, sessioner, aktiviteter, krascher och Technicals. Rapporten loggar som behövs för att beräkna andra statistik som händelser, fel och jobb måste göras manuellt med hjälp av Engagement-API (se [hur du använder avancerade Mobile Engagement i din Android-märkning API](mobile-engagement-android-use-engagement-api.md) eftersom statistiken är programmet beroende.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Bädda in Engagement SDK och tjänsten i din Android-projekt
Hämta Android SDK från [här](https://aka.ms/vq9mfn) hämta `mobile-engagement-VERSION.jar` och placera dem i den `libs` mappen för din Android-projekt (skapa biblioteksmappen om det inte finns ännu).

> [!IMPORTANT]
> Om du skapar ditt programpaket med ProGuard, måste du behålla vissa klasser. Du kan använda följande kodavsnitt i konfigurationen:
> 
> -Behåll publik klass * utökar android.os.IInterface-hålla klassen com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Ange anslutningssträngen Engagement genom att anropa följande metod i aktiviteten starta:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Anslutningssträngen för ditt program visas på Azure Portal.

* Om saknas, lägger du till följande behörigheter för Android (innan den `<application>` tagg):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Lägg till följande avsnitt (mellan den `<application>` och `</application>` taggar):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Ändra `<Your application name>` av namnet på ditt program.

> [!TIP]
> Den `android:label` attribut kan du välja namnet på tjänsten Engagement som kommer att visas för användarna på skärmen ”kör services” i sin telefon. Det rekommenderas att du anger du attributet till `"<Your application name>Service"` (t.ex. `"AcmeFunGameService"`).
> 
> 

Ange den `android:process` attributet säkerställer att Engagement tjänsten kommer att köras i en egen process (som kör Engagement i samma process som programmet gör main/UI-tråden potentiellt mindre känslig).

> [!NOTE]
> All kod som du `Application.onCreate()` och andra program återanrop ska köras för ditt programs processer, inklusive tjänsten Engagement. Det kan orsaka oönskade sidoeffekter (till exempel onödiga minnesallokering och trådar i processen för hur engagerade dubbla broadcast mottagare eller tjänster).
> 
> 

Om du åsidosätter `Application.onCreate()`, rekommenderar vi att lägga till följande kodavsnitt i början av din `Application.onCreate()` funktionen:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Du kan göra samma sak för `Application.onTerminate()`, `Application.onLowMemory()` och `Application.onConfigurationChanged(...)`.

Du kan också utöka `EngagementApplication` i stället för att utöka `Application`: återanropet `Application.onCreate()` har kontrollen av processen och anrop `Application.onApplicationProcessCreate()` endast om den aktuella processen inte är en värdtjänst Engagement samma regler gäller för de andra återanrop.

## <a name="basic-reporting"></a>Grundläggande reporting
### <a name="recommended-method-overload-your-activity-classes"></a>Rekommenderad metod: överlagra din `Activity` klasser
För att aktivera rapporten i alla loggar som krävs av Engagement att beräkna användare, sessioner, aktiviteter, krascher och tekniska uppgifter du behöver bara göra alla dina `*Activity` underordnade klasser ärver från motsvarande `Engagement*Activity` klasser (t.ex. Om din bakåtkompatibelt utökar `ListActivity`, se den utökar `EngagementListActivity`).

**Utan Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Med Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> När du använder `EngagementListActivity` eller `EngagementExpandableListActivity`, se till att alla anrop till `requestWindowFeature(...);` görs innan anropet till `super.onCreate(...);`, annars en krasch inträffar.
> 
> 

Du hittar de här klasserna i den `src` mappen och kopiera dem till ditt projekt. Klasserna finns också i den **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativ metod: anropa `startActivity()` och `endActivity()` manuellt
Om du inte kan eller inte vill överlagra din `Activity` klasser, kan du i stället börja och sluta dina aktiviteter genom att anropa `EngagementAgent`'s metoder direkt.

> [!IMPORTANT]
> Android SDK anropar aldrig den `endActivity()` metod, även när programmet avslutas (på Android program faktiskt aldrig stängs). Därför är det *hög* rekommenderas att anropa den `startActivity()` metod i den `onResume` återanrop av *alla* dina aktiviteter och `endActivity()` metod i den `onPause()` återanrop av *alla* dina aktiviteter. Detta är det enda sättet att se till att sessioner inte spridas. Om en session är känd koppla tjänsten Engagement aldrig från Engagement-serverdelen (eftersom tjänsten fortfarande ansluten så länge en session väntar).
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

Det här exemplet som liknar den `EngagementActivity` klassen och dess varianter vars källkoden har angetts i den `src` mapp.

## <a name="test"></a>Testa
Nu verifiera din integrering genom att köra din mobila app i en emulator eller en enhet och verifiera att den registreras en session på fliken övervakning.

I nästa avsnitt är valfria.

## <a name="location-reporting"></a>Platsrapportering
Om du vill att platser som ska rapporteras som du behöver lägga till några rader configuration (mellan den `<application>` och `</application>` taggar).

### <a name="lazy-area-location-reporting"></a>Områdesrapportering
Områdesrapportering kan rapportera land, region och ort som är kopplade till enheter. Den här typen av plats reporting används endast nätverksplatser (baserat på cellen ID eller Wi-Fi). Området enheten rapporteras som mest en gång per session. GPS används aldrig och därmed den här typen av plats rapport har bara ett fåtal (ska säga Nej) inverkan på batteridrift.

Rapporterat områden används för att beräkna geografiska statistik om användare, sessioner, händelser och fel. De kan också användas som kriterium i Reach-kampanjer.

Du kan göra det genom att använda konfigurationen som nämnts tidigare i den här proceduren om du vill aktivera områdesrapportering:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du måste också lägga till följande behörigheter om de saknas:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Eller du kan fortsätta att använda ``ACCESS_FINE_LOCATION`` om du redan använder den i ditt program.

### <a name="real-time-location-reporting"></a>Rapportering av platsen i realtid
Realtid plats reporting kan rapportera latituden och longituden som är kopplade till enheter. Den här typen av plats reporting använder endast nätverksplatser (baserat på cellen ID eller Wi-Fi) som standard och reporting är bara aktivt när programmet körs i förgrunden (dvs. under en session).

Realtid platser är *inte* används för att beräkna statistik. Deras endast syfte är att tillåta användning av realtid geobegränsning \<Reach-målgruppen-geofencing\> kriterium i Reach-kampanjer.

Du kan göra det genom att använda konfigurationen som nämnts tidigare i den här proceduren om du vill aktivera realtid plats reporting:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du måste också lägga till följande behörigheter om de saknas:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Eller du kan fortsätta att använda ``ACCESS_FINE_LOCATION`` om du redan använder den i ditt program.

#### <a name="gps-based-reporting"></a>GPS baseras reporting
Som standard används rapportering av platsen i realtid endast baserat nätverksplatser. Använd konfigurationsobjektet för att aktivera användning av GPS-baserade platser (vilket är mycket mer exakt):

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du måste också lägga till följande behörigheter om de saknas:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapportering i bakgrunden
Som standard är realtid plats reporting endast aktiv när programmet körs i förgrunden (dvs. under en session). Använd konfigurationsobjektet för att aktivera reporting även i bakgrunden:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> När programmet körs i bakgrunden, endast nätverksbaserat platser rapporteras, även om du har aktiverat GPS.
> 
> 

Rapportens bakgrund plats kommer att stoppas om användaren startar om sin enhet, kan du lägga till det så att den startar om automatiskt när datorn startas:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Du måste också lägga till följande behörigheter om de saknas:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M-behörigheter
Från och med Android M kan vissa behörigheter hanteras under körning och måste användare att godkänna.

Behörigheterna runtime inaktiveras som standard för nya installationer av appar om du riktar Android API-nivå 23. Annars aktiveras som standard.

Användaren kan aktivera och inaktivera dessa behörigheter på inställningsmenyn för enheten. Om du inaktiverar behörigheter från system-menyn stängs bakgrundsprocesser av programmet, detta är en systembeteende och har ingen inverkan på förmåga att ta emot push i bakgrunden.

I samband med Mobile Engagement är de behörigheter som kräver godkännande vid körning

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (endast när måldatorn Android API-nivå 23 för den här)

Extern lagring används endast för Reach helheten funktionen. Om du hittar genom att be användarna behörigheten ska vara störande, du kan ta bort den om används endast för Mobile Engagement men på bekostnad av inaktivera funktionen för stor bild.

För plats-funktioner, bör du begära behörighet till användare som använder en standard systemdialogruta. Om användaren godkänner, måste du tala ``EngagementAgent`` göra den ändringen i beräkningen i realtid (annars ändringen kommer att bearbetas nästa gång användaren startar programmet).

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
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Avancerad rapportering
Du kan också om du vill rapportera programmet specifika händelser, fel och jobb du behöver använda Engagement API via metoderna i det `EngagementAgent` klass. Ett objekt av den här klassen kan vara retreived genom att anropa den `EngagementAgent.getInstance()` statisk metod.

Engagement-API kan använda alla Engagement avancerade funktioner och beskrivs i hur du använder Engagement API på Android (samt som i den tekniska dokumentationen för den `EngagementAgent` klassen).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Avancerad konfiguration (i AndroidManifest.xml)
### <a name="wake-locks"></a>Aktivera Lås
Om du vill se till att statistik skickas i realtid vid användning av Wi-Fi eller när skärmen är inaktiverat, lägger du till följande valfria behörighet:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Kraschrapport
Lägg till den här om du vill inaktivera kraschrapporter (mellan den `<application>` och `</application>` taggar):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Burst tröskelvärde
Som standard loggas Engagement service-rapporter i realtid. Om ditt program rapporterar loggar väldigt ofta, är det bättre att buffra loggarna och rapportera dem samtidigt på en vanlig tiden som bas (Detta kallas ”burst-läge”). Om du vill göra det, Lägg till denna (mellan den `<application>` och `</application>` taggar):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Burst-läge något öka för batterilivslängd men påverkar Engagement-Övervakare: varaktighet för alla sessioner och jobb ska avrundas till burst tröskelvärdet (alltså sessioner och jobb som är kortare än tröskelvärdet burst inte kanske visas). Det rekommenderas att använda ett tröskelvärde i burst 30000 (30s).

### <a name="session-timeout"></a>Tidsgräns för session
Som standard är en session avslutades 10-tal efter slutet av den senaste aktiviteten (som vanligtvis sker genom att trycka på Home eller tillbaka, genom att ange telefonnummer som inaktiv eller genom att hoppa över i ett annat program). Detta är att undvika en session-delning varje gång användaren Avsluta och återgå till programmet snabbt (som kan hända när han hämta en avbildning, kontrollera en avisering, osv.). Du kanske vill ändra den här parametern. Om du vill göra det, Lägg till denna (mellan den `<application>` och `</application>` taggar):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Inaktivera rapportering av logg
### <a name="using-a-method-call"></a>Med hjälp av ett metodanrop
Om du vill Engagement slutar skicka loggar, kan du anropa:

            EngagementAgent.getInstance(context).setEnabled(false);

Det här anropet är beständiga: filen delade inställningar används.

Om Engagement är aktiv när du anropar den här funktionen kan det ta 1 minut att stoppa tjänsten. Men det inte kommer starta tjänsten alls nästa gång startar du programmet.

Du kan aktivera loggen reporting igen genom att anropa funktionen samma med `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrering i din egen `PreferenceActivity`
I stället för att anropa den här funktionen kan du också integrera inställningen direkt i din befintliga `PreferenceActivity`.

Du kan konfigurera Engagement att använda inställningar-fil (med läge) i den `AndroidManifest.xml` filen med `application meta-data`:

* Den `engagement:agent:settings:name` nyckel används för att ange namnet på filen med delade inställningar.
* Den `engagement:agent:settings:mode` nyckel används för att definiera läget för filen delade inställningar bör du använda samma läge som i din `PreferenceActivity`. Läget måste skickas som ett tal: Om du använder en kombination av konstant flaggor i koden, kontrollera det totala värdet.

Engagement alltid använda den `engagement:key` booleskt nyckel i filen med inställningar för att hantera den här inställningen.

Följande exempel visar `AndroidManifest.xml` visar standardvärden:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Du kan lägga till en `CheckBoxPreference` i layouten inställningar som den följande:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
