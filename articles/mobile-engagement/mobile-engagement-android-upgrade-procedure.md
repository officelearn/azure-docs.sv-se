---
title: Azure Mobile Engagement Android SDK Integration
description: Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 11618586-c709-49ca-bcd8-745323ff1af6
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 77047cb1dc39fa3c05f58550ceea74e78396157f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="upgrade-procedures"></a>Uppgraderingsprocesser
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

Om du redan har integrerat en äldre version av våra SDK i programmet, måste du Tänk på följande när du uppgraderar SDK.

Du kan behöva utföra flera procedurer om du har missat flera versioner av SDK. Till exempel om du migrerar från 1.4.0 till 1.6.0 måste du först Följ proceduren ”från 1.4.0 till 1.5.0” sedan proceduren ”från 1.5.0 till 1.6.0”.

Oavsett vilken version som du uppgraderar från, du måste ersätta den `mobile-engagement-VERSION.jar` med den nya.

## <a name="from-420-to-421"></a>Från 4.2.0 till 4.2.1
Det här steget kan faktiskt utförs på någon version av SDK, är en säkerhetsförbättring av när du integrerar räckvidden aktiviteter.

Du bör nu lägga till `exported="false"` för alla Reach-aktiviteter.

Reach-aktiviteter bör nu se ut så här din `AndroidManifest.xml`:

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

## <a name="from-400-to-410"></a>Från 4.0.0 till 4.1.0
Den SDK nu hantera nya behörighetsmodellen från Android M.

Om du använder funktioner för plats eller stor bild meddelanden Läs [i det här avsnittet](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

Förutom nya behörighetsmodellen stöder vi nu konfigurera funktioner för plats vid körning.
Vi är fortfarande är kompatibla med manifestet parametrarna för plats men den är nu föråldrad. Använd runtime-konfiguration genom att ta bort följande avsnitt från din ``AndroidManifest.xml``:

    <meta-data
      android:name="engagement:locationReport:lazyArea"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:background"
      android:value="true"/>
    <meta-data
      android:name="engagement:locationReport:realTime:fine"
      android:value="true"/>

och läsa [uppdateras det här förfarandet](mobile-engagement-android-integrate-engagement.md#location-reporting) använda runtime konfiguration i stället.

## <a name="from-300-to-400"></a>Från 3.0.0 till 4.0.0
### <a name="native-push"></a>Intern push
Intern push (GCM/ADM) nu används också för i appen meddelanden så att du måste konfigurera native push-autentiseringsuppgifter för alla typer av push-kampanj.

Om den inte redan Följ [proceduren](mobile-engagement-android-integrate-engagement-reach.md#native-push).

### <a name="androidmanifestxml"></a>AndroidManifest.xml
Reach-integrering har ändrats i ``AndroidManifest.xml``.

Ersätt detta:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Av

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Det finns eventuellt en skärm för inläsning nu när du klickar på ett meddelande (med text/webbinnehåll) eller en avsökning.
Du måste lägga till detta för de kampanjer att arbeta i 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

### <a name="resources"></a>Resurser
Bädda in den nya `res/layout/engagement_loading.xml` filen i projektet.

## <a name="from-240-to-300"></a>Från 2.4.0 till 3.0.0
Nedan beskrivs hur du migrerar en SDK-integration från tjänsten Capptain som erbjuds av Capptain SAS i en app med Azure Mobile Engagement. Om du migrerar från en tidigare version finns Capptain webbplats för att migrera till 2.4.0 först och sedan använda följande procedur.

> [!IMPORTANT]
> Capptain och Mobile Engagement är inte samma tjänster och proceduren nedan visar hur du migrerar klientappen endast. Migrera SDK i appen migreras inte data från servrar som Capptain till Mobile Engagement-servrar.
> 
> 

### <a name="jar-file"></a>JAR-filen
Ersätt `capptain.jar` av `mobile-engagement-VERSION.jar` i din `libs` mapp.

### <a name="resource-files"></a>Resursfiler
Varje resursfil som vi angav (föregås av `capptain_`) har ersätts med nya (med prefixet `engagement_`).

Om du har anpassat dessa filer, måste du åter tillämpa anpassningen på de nya filerna **alla identifierare i Resursfiler har bytt**.

### <a name="application-id"></a>Program-ID:t
Engagement använder nu en anslutningssträng för att konfigurera SDK-identifierare som program-ID.

Du måste använda `EngagementAgent.init` metod i din Starta aktivitet så här:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Anslutningssträngen för ditt program visas på Azure Portal.

Ta bort alla anrop till `CapptainAgent.configure` som `EngagementAgent.init` ersätter den här metoden.

Den `appId` inte längre kan konfigureras med `AndroidManifest.xml`.

Ta bort det här avsnittet från din `AndroidManifest.xml` om du har:

            <meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

### <a name="java-api"></a>Java-API
Varje anrop till Java-klass av våra SDK måste namnges; till exempel `CapptainAgent.getInstance(this)` måste ändras `EngagementAgent.getInstance(this)`, `extends CapptainActivity` måste ändras `extends EngagementActivity` etc...

Om du har integrerat med standard agent inställningsfiler Standardfilnamnet är nu `engagement.agent` och nyckeln är `engagement:agent`.

När du skapar web meddelanden Javascript binder är nu `engagementReachContent`.

### <a name="androidmanifestxml"></a>AndroidManifest.xml
En mängd ändringar har hänt det tjänsten delas inte längre och mycket mottagare kan inte exporteras längre.

Service-deklarationen är nu enklare; ta bort filtret avsiktshantering och alla metadata i den och lägga till `exportable=false`.

Plus allt har bytt namn om du vill använda engagement.

Nu ser ut som:

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

När du vill aktivera test loggar meta-data har nu flyttats till taggen program och har bytt namn:

            <application>

              <meta-data android:name="engagement:log:test" android:value="true" />

              <service/>

            </application>

Alla andra metadata har precis bytt namn, här är en fullständig lista (av kursen rename endast de som du använder):

            <meta-data
              android:name="engagement:reportCrash"
              android:value="true"/>
            <meta-data
              android:name="engagement:sessionTimeout"
              android:value="10000"/>
            <meta-data
              android:name="engagement:burstThreshold"
              android:value="0"/>
            <meta-data
              android:name="engagement:connection:delay"
              android:value="0"/>
            <meta-data
              android:name="engagement:locationReport:lazyArea"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:background"
              android:value="false"/>
            <meta-data
              android:name="engagement:locationReport:realTime:fine"
              android:value="false"/>
            <meta-data
              android:name="engagement:agent:settings:name"
              android:value="engagement.agent"/>
            <meta-data
              android:name="engagement:agent:settings:mode"
              android:value="0"/>
            <meta-data
              android:name="engagement:gcm:sender"
              android:value="<YOUR_PROJECT_NUMBER>\n"/>
            <meta-data
              android:name="engagement:adm:register"
              android:value="true"/>
            <meta-data
              android:name="engagement:reach:notification:icon"
              android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>

            <activity android:name="SomeActivityWithoutReachOverlay">
              <meta-data
                android:name="engagement:notification:overlay"
                android:value="false"/>
            </activity>

Google Play och SmartAd spårning har tagits bort från SDK som du behöver bara ta bort den här utan byte:

            <meta-data 
                android:name="capptain:track:installReferrerForwardList"
                android:value="com.class1,com.class2"/>
            <meta-data
                android:name="capptain:track:adservers"
                android:value="smartad" />

Reach-aktiviteter deklareras nu så här:

            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/plain"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <data android:mimeType="text/html"/>
              </intent-filter>
            </activity>
            <activity
              android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
              android:theme="@android:style/Theme.Light">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>

Om du har anpassade Reach-aktiviteter, behöver du bara ändra avsiktshantering åtgärder för att matcha antingen `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` eller `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Broadcast mottagarna har bytt namn plus vi nu lägga till `exported=false`. Här är en fullständig lista över mottagare med nya specifikationen (av kursen rename endast de som du använder):

            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
              android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
              android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
                <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
              android:permission="com.amazon.device.messaging.permission.SEND">
              <intent-filter>
                <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
                <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
                <category android:name="<your_package_name>"/>
              </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
              </intent-filter>
            </receiver>

            <receiver
              android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
              </intent-filter>
            </receiver>

Spåra mottagaren har tagits bort, så du måste ta bort det här avsnittet:

          <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
            <intent-filter>
              <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
              <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
            </intent-filter>
          </receiver>

Observera att deklarationen av din implementering av broadcast mottagaren **EngagementMessageReceiver** har ändrats i den `AndroidManifest.xml`. Det beror på att API för att skicka och ta bort valfri XMPP meddelanden från valfri XMPP entiteter och API för att skicka och ta emot meddelanden mellan enheter har tagits bort. Du har alltså även att ta bort följande återanrop från din **EngagementMessageReceiver** implementering:

            protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

och

            protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

ta bort alla anrop på **EngagementAgent** för:

            sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

och

            sendXMPPMessage(android.os.Bundle msg)

### <a name="proguard"></a>Proguard
Proguard konfiguration kan påverkas av rebranding, reglerna nu ser ut som:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
              <methods>;
            }

