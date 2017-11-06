---
title: Azure Mobile Engagement Android SDK-Integration
description: "Senaste uppdateringarna och procedurer för Android SDK för Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 26ba47b19f3a503693d60d344ad39b9eba74fe99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Hur du integrerar Engagement Reach på Android
> [!IMPORTANT]
> Du måste följa integration proceduren i hur du integrerar Engagement på Android dokumentet innan du följer den här guiden.
> 
> 

## <a name="standard-integration"></a>Standard-integrering

Kopiera Reach resursfiler från SDK i projektet:

* Kopiera filerna från den `res/layout` mappen levereras med SDK i den `res/layout` för ditt program.
* Kopiera filerna från den `res/drawable` mappen levereras med SDK i den `res/drawable` för ditt program.

Redigera din `AndroidManifest.xml` fil:

* Lägg till följande avsnitt (mellan den `<application>` och `</application>` taggar):
  
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
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
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
* Du behöver den här behörigheten att spela upp systemmeddelanden som inte har klickat på vid start (annars de sparas på disk men visas inte längre behöver du verkligen ta med den här).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Ange en ikon som används för meddelanden (både i appen och system som) genom att kopiera och redigera följande avsnitt (mellan den `<application>` och `</application>` taggar):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Det här avsnittet är **obligatoriska** om du tänker använda systemmeddelanden när du skapar Reach-kampanjer. Android förhindrar systemmeddelanden utan ikoner som visas. Så om du tar bort det här avsnittet kommer slutanvändarna inte att kunna ta emot dem.
> 
> 

* Om du skapar kampanjer med systemmeddelanden med stor bild, måste du lägga till följande behörigheter (när den `</application>` tagg) om de saknas:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * På Android M och om ditt program riktar sig till Android API-nivå 23 eller högre, ``WRITE_EXTERNAL_STORAGE`` behörighet kräver användargodkännande av. Läs [i det här avsnittet](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* För systemmeddelanden kan du också ange i Reach kampanjen om enheten ska ringa och/eller vibrerar. Att fungera, du måste se till att du deklarerat följande behörighet (när den `</application>` tagg):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Utan den här behörigheten Android förhindrar systemmeddelanden från att visas om du har markerat ringen eller alternativet vibrate i Reach-kampanjhanterare.

## <a name="native-push"></a>Intern Push
Nu när du har konfigurerat Reach-modulen måste du konfigurera intern push för att kunna ta emot kampanjer på enheten.

Vi stöder två tjänster på Android:

* Google Play-enheter: Använd [Google Cloud Messaging] genom att följa den [hur du integrerar GCM med Engagement guide](mobile-engagement-android-gcm-integrate.md) guide.
* Amazon-enheter: Använd [Amazon Device Messaging] genom att följa den [hur du integrerar ADM med Engagement guide](mobile-engagement-android-adm-integrate.md) guide.

Om du vill rikta Amazon- och Google Play-enheter, de möjliga har allt inuti 1 AndroidManifest.xml/APK för utveckling. Men när du skickar in till Amazon, de kan avvisa tillämpningsprogrammet om de hittar GCM-kod.

I så fall bör du använda flera APKs.

**Tillämpningsprogrammet är nu redo att ta emot och visa reach-kampanjer!**

## <a name="how-to-handle-data-push"></a>Hur du hanterar data-push
### <a name="integration"></a>Integrering
Om du vill att programmet ska kunna ta emot Reach data push-meddelanden måste du skapa en underklass till `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` och referera till den i den `AndroidManifest.xml` fil (mellan den `<application>` och/eller `</application>` taggar):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Sedan kan du åsidosätta den `onDataPushStringReceived` och `onDataPushBase64Received` återanrop. Här är ett exempel:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Kategori
Kategori-parametern är valfri när du skapar en Data-Push-kampanj och gör att du kan filtrera data push-meddelanden. Detta är användbart om du har flera broadcast mottagare hantera olika typer av data-push, eller om du vill skicka olika typer av `Base64` data och vill identifiera typ innan parsningen dem.

### <a name="callbacks-return-parameter"></a>Återanrop returparameter
Här följer några riktlinjer för att kunna hantera returparametern för `onDataPushStringReceived` och `onDataPushBase64Received`:

* En broadcast mottagare ska returnera `null` i återanropet om den inte vet hur du hanterar en data-push. Du bör använda kategorin för att fastställa om din broadcast mottagare ska hantera data-push eller inte.
* En broadcast mottagaren ska returnera `true` i återanropet om den tar emot data-push.
* En broadcast mottagaren ska returnera `false` i återanropet om den identifierar data-push, men ignorerar oavsett orsak. Till exempel returnera `false` när mottagna data är ogiltiga.
* Om en broadcast-mottagare returnerar `true` medan en annan en returnerar `false` för samma data-push är funktionen Odefinierad, gör aldrig som.

Returtypen används endast för Reach-statistik:

* `Replied`ökas om något av broadcast mottagarna returneras antingen `true` eller `false`.
* `Actioned`ökas endast om en broadcast mottagarna returnerade `true`.

## <a name="how-to-customize-campaigns"></a>Hur du anpassar kampanjer
Du kan ändra layouter i nå SDK för att anpassa kampanjer.

Du bör hålla de identifierare som används i layouterna och att vissa typer av vyer som använder en identifierare, särskilt för text vyer och avbildningen vyer. Vissa vyer används bara för att visa eller dölja områden så att deras typen kan ändras. Kontrollera källkoden om du vill ändra typen av en vy i de angivna layouterna.

### <a name="notifications"></a>Meddelanden
Det finns två typer av aviseringar: systemet och i appen meddelanden som använder olika layoutfiler.

#### <a name="system-notifications"></a>Systemmeddelanden
Anpassa systemmeddelanden måste du använda den **kategorier**. Du kan hoppa till [kategorier](#categories).

#### <a name="in-app-notifications"></a>Meddelanden i appen
Som standard ett meddelande i appen är en vy som läggs till dynamiskt till det aktuella aktiviteten användargränssnittet tack vare metoden Android `addContentView()`. Detta kallas en meddelandet överlägget. Meddelande överlägg är bra för en snabb integrering eftersom de inte kräver att du kan ändra alla layouter i ditt program.

Om du vill ändra utseendet på din anmälan överlägg, kan du bara ändra filen `engagement_notification_area.xml` efter dina behov.

> [!NOTE]
> Filen `engagement_notification_overlay.xml` är den som används för att skapa en meddelandet överlägget, innehåller filen `engagement_notification_area.xml`. Du kan också anpassa den så att den passar dina behov (till exempel för att placera meddelandefältet i överlägget).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Inkludera meddelande layout som en del av en aktivitet layout
Överlägg kan är bra för en snabb integrering men vara olämplig eller ha sidoeffekter i särskilda fall. Systemets överlägget kan anpassas på en aktivitetsnivå, vilket gör det enkelt att förhindra sidoeffekter för särskilda aktiviteter.

Du kan välja att inkludera layout för våra meddelande i din befintliga layout tack vare Android **inkluderar** instruktionen. Följande är ett exempel på en ändrade `ListActivity` layout som bara innehåller en `ListView`.

**Innan du integrering av Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Efter integrering av Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

I det här exemplet vi har lagt till en överordnad behållare eftersom den ursprungliga layouten används en listvy som elementet på översta nivån. Vi har även lagt till `android:layout_weight="1"` för att kunna lägga till en vy under en listvy som konfigurerats med `android:layout_height="fill_parent"`.

Engagement nå SDK identifierar automatiskt att meddelande layout ingår i den här aktiviteten och kommer inte att lägga till ett överlägg för den här aktiviteten.

> [!TIP]
> Om du använder en ListActivity i ditt program hindrar ett synliga Reach-överlägg dig från att reagera på klickade på objekt i listvyn längre. Detta är ett känt problem. Vi rekommenderar att bädda in notification layouten i listan aktivitet layouten som i föregående exempel om du vill undvika det här problemet.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Inaktivera programmet meddelanden per aktivitet
Om du inte vill överlägget som ska läggas till dina aktiviteter och om du inte anger meddelande layouten i layouten, kan du inaktivera överlägget för aktiviteten i den `AndroidManifest.xml` genom att lägga till en `meta-data` avsnittet precis som i följande exempel:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Kategorier
När du ändrar de angivna layouterna kan ändra du utseendet på alla meddelanden. Kategorier kan du definiera olika riktade verkar (möjligen beteenden) för meddelanden. En kategori kan anges när du skapar en Reach-kampanj. Tänk på att kategorier kan du anpassa meddelanden och avsökningar, som beskrivs senare i det här dokumentet.

Om du vill registrera en hanterare för kategori för meddelanden som du behöver lägga till ett anrop när programmet har initierats.

> [!IMPORTANT]
> Läs varningen om attributet android: processen \<android-sdk-engagement-process\> i hur du integrerar Engagement på Android avsnittet innan du fortsätter.
> 
> 

I följande exempel förutsätter att du godkänt tidigare varningen och använder en underklass till `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

Den `MyNotifier` objektet är implementeringen av kategori meddelandehanteraren. Det är antingen en implementering av den `EngagementNotifier` -gränssnittet eller en underordnad klass av standardimplementering: `EngagementDefaultNotifier`.

Observera att samma meddelaren kan hantera flera kategorier, kan du registrera dem så här:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Om du vill ersätta kategori standardimplementering, kan du registrera din implementering som i följande exempel:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Den aktuella kategorin som används i en hanterare skickas som en parameter i de flesta metoder som du kan åsidosätta i `EngagementDefaultNotifier`.

Den skickas antingen som en `String` parametern eller indirekt i en `EngagementReachContent` objekt som har en `getCategory()` metod.

Du kan ändra de flesta av meddelande-processen genom att omdefiniera metoder på `EngagementDefaultNotifier`, för mer avancerad anpassning passa på att ta en titt på den tekniska dokumentationen och källkoden.

##### <a name="in-app-notifications"></a>Meddelanden i appen
Om du vill använda alternativa layout för en viss kategori kan du implementera detta som i följande exempel:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Exempel på `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Överlägget visa identifierare är annat än det som standard. Det är viktigt att varje layout använder en unik identifierare för överlägg.

**Exempel på `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Meddelande området Visa identifierare är annat än det som standard. Det är viktigt att varje layout använder en unik identifierare för meddelandefält.

Det här enkla exemplet på kategorin gör ett program (eller i appen) meddelanden som visas längst upp på skärmen. Vi inte ändra standard identifierare som används i meddelandefältet sig själv.

Om du vill ändra som du behöver definiera om den `EngagementDefaultNotifier.prepareInAppArea` metoden. Vi rekommenderar att du titta på den tekniska dokumentationen och källkoden för `EngagementNotifier` och `EngagementDefaultNotifier` om du vill använda denna nivå av avancerad anpassning.

##### <a name="system-notifications"></a>Systemmeddelanden
Genom att utöka `EngagementDefaultNotifier`, kan du åsidosätta `onNotificationPrepared` att ändra meddelandet som har sammanställts av standardimplementering.

Exempel:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Det här exemplet gör en systemavisering för en innehåll visas som en pågående händelse när kategorin ”pågående” används.

Om du vill skapa den `Notification` objekt från början, du kan returnera `false` till metoden och anropet `notify` själv på den `NotificationManager`. I så fall är det viktigt att du behåller en `contentIntent`, `deleteIntent` och meddelande-ID som används av `EngagementReachReceiver`.

Här är en korrekt exempel på sådana implementering:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Meddelande endast meddelanden
Hanteringen av Klicka på ett meddelande som endast meddelande kan anpassas genom att åsidosätta `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` att ändra den förberedda `Intent`. Med den här metoden kan du finjustera flaggorna enkelt.

Till exempel att lägga till den `SINGLE_TOP` flaggan:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

För äldre Engagement användare, Observera att systemmeddelanden utan åtgärd URL nu startar programmet om den var i bakgrunden, så den här metoden kan anropas med ett meddelande utan åtgärds-URL. Du bör som när du anpassar avsikten.

Du kan också implementeras `EngagementNotifier.executeNotifAnnouncementAction` från början.

##### <a name="notification-life-cycle"></a>Livscykel för meddelande
När du använder standardkategori, vissa livscykel metoder anropas på den `EngagementReachInteractiveContent` objekt till rapporten statistik och uppdatera tillståndet kampanj:

* När meddelandet visas i programmet eller placera i statusfältet, den `displayNotification` metoden anropas (som rapporterar statistik) av `EngagementReachAgent` om `handleNotification` returnerar `true`.
* Om meddelandet avvisas den `exitNotification` metoden anropas statistik rapporteras och nästa kampanjer kan nu bearbetas.
* Om du klickar på meddelandet `actionNotification` är anropas statistik rapporteras och associerade avsikten har startats.

Om din implementering av `EngagementNotifier` kringgår standardbeteendet, måste du anropa metoderna livscykel själv. I följande exempel visas tillfällen där standardbeteendet kringgås:

* Du inte utökar `EngagementDefaultNotifier`, t.ex. du har implementerat kategori hantering från grunden.
* För systemmeddelanden, overrode den `onNotificationPrepared` och du ändrat `contentIntent` eller `deleteIntent` i den `Notification` objekt.
* För meddelanden i appen, overrode `prepareInAppArea`, se till att mappa minst `actionNotification` till en av dina U.I styr.

> [!NOTE]
> Om `handleNotification` returnerar ett undantag innehållet tas bort och `dropContent` anropas. Detta rapporteras i statistik och nästa kampanjer kan nu bearbetas.
> 
> 

### <a name="announcements-and-polls"></a>Meddelanden och avsökningar
#### <a name="layouts"></a>Layouter
Du kan ändra den `engagement_text_announcement.xml`, `engagement_web_announcement.xml` och `engagement_poll.xml` filer för att anpassa text meddelanden, web meddelanden och avsökningar.

De här filerna dela två vanliga layouter för rubrikområdet och det aktiva området. Layouten för rubriken är `engagement_content_title.xml` och använder eponymous drawable filen för bakgrund. Layouten för knapparna åtgärd och avsluta är `engagement_button_bar.xml` och använder eponymous drawable filen för bakgrund.

I en omröstning fråga layout och deras val dynamiskt är förstoras med flera gånger i `engagement_question.xml` layoutfilen för frågorna och `engagement_choice.xml` filen för alternativen.

#### <a name="categories"></a>Kategorier
##### <a name="alternate-layouts"></a>Alternativa layouter
Kampanjens kategori kan användas ha alternativa layouter för meddelanden och avsökningar som meddelanden.

Till exempel för att skapa en kategori för en SMS-meddelande, du kan utöka `EngagementTextAnnouncementActivity` och referera till den i `AndroidManifest.xml` fil:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Observera att filtrera kategori i avsikten används för att göra skillnaden med meddelande standardaktivitet.

Nå SDK använder avsiktshantering systemet för att matcha rätt aktivitet för en viss kategori och använder standard-kategori om matchningen misslyckades.

Sedan måste du implementera `MyCustomTextAnnouncementActivity`, om du vill ändra layouten (men behålla samma vy identifierare), du behöver bara ange klassen som i följande exempel:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Om du vill ersätta standardkategorin för text-meddelanden, ersätts `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` av din implementering.

Web meddelanden och avsökningar kan anpassas på ett liknande sätt.

För web-meddelanden kan du utöka `EngagementWebAnnouncementActivity` och deklarera dina aktiviteter i den `AndroidManifest.xml` som i följande exempel:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

För avsökningar kan du utöka `EngagementPollActivity` och deklarera dina i den `AndroidManifest.xml` som i följande exempel:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementeringen från grunden
Du kan implementera kategorier för dina aktiviteter för meddelande (och avsökning) utan att utöka en av de `Engagement*Activity` klasser som tillhandahålls av nå SDK. Detta är användbart till exempel om du vill definiera en layout som inte använder samma vyer som standard layouter.

Precis som för avancerade notification anpassning rekommenderas att titta på källkoden för standardimplementeringen.

Här följer några saker att tänka på: Reach startas aktiviteten med ett specifikt syfte (motsvarande avsiktshantering filtret) plus en extra parameter som är innehåll identifierare.

Du kan göra detta för att hämta innehållsobjektet som innehåller de fält som du angav när du skapar kampanjen på webbplatsen:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

För statistik, ska du rapportera innehållet visas i den `onResume` händelse:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Sedan Glöm inte att anropa antingen `actionContent(this)` eller `exitContent(this)` på innehållsobjektet innan aktiviteten försätts i bakgrunden.

Om du inte anropa antingen `actionContent` eller `exitContent`, statistik inte skickas (dvs. inga analyser på kampanjen) och viktigare är nästa kampanjer meddelas inte förrän programmet startas.

Orientering eller andra ändringar i konfigurationen kan göra koden svårt att avgöra om aktiviteten försätts i bakgrunden eller inte, standardimplementeringen säkerställer innehållet rapporteras avslutats om användaren lämnar aktiviteten (antingen genom att trycka på `HOME` eller `BACK`) men inte om orientering ändras.

Här är intressant del av implementeringen:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Som du ser om du anropas `actionContent(this)` sedan klar aktiviteten, `exitContent(this)` på ett säkert sätt kan anropas utan att ha någon effekt.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
