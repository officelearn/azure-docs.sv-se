---
title: "Notification Hubs senaste nytt självstudiekursen - Android"
description: "Lär dig hur du använder Azure Service Bus Notification Hubs för att skicka senaste nyheterna meddelanden till Android-enheter."
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 76ec01c874fceedab7d76b2ef58e4b45b5489f58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Använda Notification Hubs för att skicka de senaste nyheterna
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt
Det här avsnittet visar hur du använder Azure Notification Hubs för att sända senaste nyheterna meddelanden till en Android-app. När du är klar kommer du att kunna registrera för att analysera nyhetskategorier som du är intresserad av och får endast push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden har skickas till grupper av användare som har tidigare ha deklarerats intresse för dem, t.ex. RSS-läsare, appar för musik fläktar, osv.

Broadcast scenarier aktiveras genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg för tar alla enheter som har registrerats för taggen emot meddelandet. Eftersom taggar är bara strängar, behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs Routning och Tagguttryck](notification-hubs-tags-segment-push-message.md).

## <a name="prerequisites"></a>Krav
Det här avsnittet bygger på appen som du skapade i [Kom igång med Notification Hubs][get-started]. Innan du börjar den här kursen ska du måste redan har slutfört [Kom igång med Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägg till kategori markeringen i appen
Det första steget är att lägga till de UI-element i din befintliga huvudaktiviteten som gör att användaren kan välja kategorier för att registrera. Vilka kategorier av en användare som lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Öppna filen res/layout/activity_main.xml och Ersätt innehållet med följande:
   
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">
   
                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>
2. Öppna filen res/values/strings.xml och Lägg till följande rader:
   
        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>
   
    Main_activity.xml grafiska layouten bör nu se ut så här:
   
    ![][A1]
3. Nu skapa en klass **meddelanden** i samma paket som din **MainActivity** klass.
   
        import java.util.HashSet;
        import java.util.Set;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;
   
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;
   
            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
   
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }
   
            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }
   
            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }
   
            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
   
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
   
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }
   
        }
   
    Den här klassen använder lokal lagring för att lagra kategorier av nyheter som den här enheten ska ta emot. Den innehåller också metoder för att registrera dig för dessa kategorier.
4. I din **MainActivity** klassen ta bort din privata fält för **NotificationHub** och **GoogleCloudMessaging**, och Lägg till ett fält för **meddelanden**:
   
        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;
5. I den **onCreate** metod, ta bort initieringen av den **hubb** fält och **registerWithNotificationHubs** metod. Lägg till följande rader som initierar en instans av den **meddelanden** klass. 

        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);

            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`och `HubListenConnectionString` redan ska anges med den `<hub name>` och `<connection string with listen access>` platshållarna med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature* som du fick tidigare.

    > [AZURE.NOTE] Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram inte är vanligtvis säker kan distribuera du endast nyckel för lyssna åtkomst med din klientapp. Lyssna åtkomst aktiverar inte går att ändra din app att registrera för meddelanden, men befintliga registreringar och går inte att skicka meddelanden. Fullständig åtkomst-nyckeln används i en skyddad backend-tjänst för att skicka meddelanden och ändra befintliga registreringar.


1. Lägg sedan till följande importer och `subscribe` metod för att hantera knappen prenumerera på händelse:
   
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;
   
        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();
   
            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");
   
            notifications.storeCategoriesAndSubscribe(categories);
        }
   
    Den här metoden skapar en lista över kategorier och använder den **meddelanden** klass som lagrar listan i enhetens lokala lagring och registrera motsvarande taggar med meddelandehubben. När kategorier ändras, återskapas registreringen med de nya kategorierna.

Appen är nu kunna lagra en uppsättning kategorier i lokal lagring på enheten och registrera med notification hub när användaren ändrar valet av kategorier.

## <a name="register-for-notifications"></a>Registrera dig för meddelanden
De här stegen registrera med notification hub vid start med hjälp av kategorier som har lagrats i lokal lagring.

> [!NOTE]
> Eftersom registrationId som tilldelats av Google Cloud Messaging (GCM) kan ändras när som helst, bör du registrera dig för meddelanden ofta att undvika fel i meddelande. Det här exemplet registrerar för meddelande varje gång appen startar. För appar som körs ofta mer än en gång om dagen, du kan förmodligen hoppa över registrering för att bevara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.
> 
> 

1. Lägg till följande kod i slutet av den **onCreate** metod i den **MainActivity** klass:
   
        notifications.subscribeToCategories(notifications.retrieveCategories());
   
    Detta säkerställer att varje gång appen startas hämtas kategorier från lokal lagring och begär en registrering för dessa kategorier. 
2. Uppdatera sedan den `onStart()` metod för den `MainActivity` klassen enligt följande:
   
    @Overrideskyddade void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
   
    Detta uppdaterar huvudaktiviteten baserat på status för tidigare sparad kategorier.

Appen är nu klar och kan lagra en uppsättning kategorier i enhetens lokala lagring som används för att registrera med notification hub när användaren ändrar valet av kategorier. Nu ska definierar vi en serverdel som kategori meddelanden kan skickas till den här appen.

## <a name="sending-tagged-notifications"></a>Skicka taggade meddelanden
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Kör appen och generera meddelanden
1. I Android Studio skapar appen och starta den på en enhet eller emulator.
   
    Observera att appen Användargränssnittet innehåller en uppsättning växlar som låter dig välja kategorier för att prenumerera på.
2. Aktivera en eller flera kategorier växlar och klicka sedan på **prenumerera**.
   
    Appen konverterar valda kategorier till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. Registrerade kategorier returneras och visas i ett popup-meddelande.
3. Skicka ett nytt meddelande genom att köra .NET-konsolapp.  Du kan också skicka taggade mall-meddelanden via felsökningsfliken i meddelandehubben i den [klassiska Azure-portalen].
   
    Meddelanden i valda kategorier visas som popup-meddelanden.

## <a name="next-steps"></a>Nästa steg
Vi lärt dig hur du broadcast senaste nyheterna efter kategori i den här självstudiekursen. Överväg att fylla i en av följande kurser som markerar andra avancerade Meddelandehubbar scenarier:

* [Använda Notification Hubs för att sända lokaliserade senaste nyheterna]
  
    Lär dig mer om att utöka senaste nyheterna appen om du vill aktivera skicka lokaliserade meddelanden.

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Använda Notification Hubs för att sända lokaliserade senaste nyheterna]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[klassiska Azure-portalen]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
