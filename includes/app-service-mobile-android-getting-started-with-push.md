---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: e66e7d4646b650d2d811d3807db04605dfddeded
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187857"
---
1. Öppna filen `AndroidManifest.xml`i **appprojektet** . Lägg till följande `application` kod efter öppningstaggen:

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. Öppna filen `ToDoActivity.java`och gör följande ändringar:

    - Lägg till importsatsen:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Ändra definitionen `MobileServiceClient` av från **privat** till **privat statisk**, så det nu ser ut så här:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Lägg `registerPush` till metod:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - Uppdatera **metoden onCreate** `ToDoActivity` för klassen. Se till att lägga `MobileServiceClient` till den här koden när den har instansierats.

        ```java
        registerPush();
        ```

3. Lägg till en ny klass för att hantera meddelanden. Öppna **appen** > **java** > **your-project-namespace-noder** i Project Explorer och högerklicka på noden för paketnamn. Klicka på **Ny**och sedan på **Java-klass**. Skriv i `ToDoMessagingService`Namn och klicka sedan på OK. Ersätt sedan klassdeklarationen med:

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. Lägg till en annan klass för att hantera tokenuppdateringar. Skapa `ToDoInstanceIdService` java-klass och ersätt klassdeklarationen med:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Din app uppdateras nu för att stödja push-meddelanden.
