---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 654bc3745768fccea41d7c3991142bf7183b54be
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811548"
---
1. I din **app** projekt, öppna filen `AndroidManifest.xml`. Lägg till följande kod efter den `application` starttaggen:

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

2. Öppna filen `ToDoActivity.java`, och gör följande ändringar:

    - Lägg till import-sats:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - Ändra definitionen av `MobileServiceClient` från **privata** till **privata statiska**, så att den nu ser ut så här:

        ```java
        private static MobileServiceClient mClient;
        ```

    - Lägg till `registerPush` metoden:

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

    - Uppdatera den **onCreate** -metoden för den `ToDoActivity` klass. Se till att lägga till den här koden efter den `MobileServiceClient` instantieras.

        ```java
        registerPush();
        ```

3. Lägg till en ny klass för att hantera meddelanden. I Project Explorer öppnar du den **app** > **java** > **your projekt namnområde** noder och högerklicka på noden paket namn. Klicka på **New**, och klicka sedan på **Java-klass**. Skriv namn `ToDoMessagingService`, och klicka sedan på OK. Ersätt sedan klassdeklarationen med:

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

4. Lägg till en annan klass för att hantera token uppdateringar. Skapa `ToDoInstanceIdService` java klassen och Ersätt klassdeklarationen med:

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

Din app har uppdaterats för att stödja push-meddelanden.
