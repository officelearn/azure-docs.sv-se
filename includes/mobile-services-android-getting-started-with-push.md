1. In Ihrer **app** Projekt, öffnen Sie die Datei `AndroidManifest.xml`. Ersetzen Sie im Code in den nächsten beiden Schritten _`**my_app_package**`_ mit dem Namen des app-Pakets für Ihr Projekt, das ist der Wert der `package` -Attribut des der `manifest` Tag. 

2. Fügen Sie die folgenden neuen Berechtigungen nach dem vorhandenen `uses-permission`-Element ein:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Fügen Sie den folgenden Code nach dem Starttag `application` ein: 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Fügen Sie diese Zeile unter *Abhängigkeiten* in der **build.gradle** Datei in das Anwendungsverzeichnis, und synchronisieren Sie Gradle mit dem Projekt erneut: 

        compile(group: 'com.microsoft.azure', name: 'azure-notifications-handler', version: '1.0.1', ext: 'jar')


5. Öffnen Sie die Datei *ToDoItemActivity.java*, und fügen Sie die folgenden Import-Anweisung hinzu:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


6. Fügen Sie der Klasse die folgende private Variable: Ersetzen Sie _`<PROJECT_NUMBER>`_ durch die Projektnummer, die Ihre app im vorherigen Vorgang durch Google zugewiesen:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. Ändern Sie die Definition von der *MobileServiceClient* aus **private** zu **öffentlichen, statischen**, sodass sie jetzt wie folgt aussieht:

        public static MobileServiceClient mClient;



8. Als Nächstes müssen wir eine neue Klasse zum Behandeln von Benachrichtigungen hinzufügen. Öffnen Sie im Projekt-Explorer die **Src** = > **wichtigsten** = > **Java** Knoten und mit der rechten Maustaste den paketnamenknoten: Klicken Sie auf **Neu**, klicken Sie dann auf **Java-Klasse**.

9. In **Namen** Typ `MyHandler`, klicken Sie dann auf **OK**. 


    ![](./media/mobile-services-android-get-started-push/android-studio-create-class.png)


10. Ersetzen Sie in der Datei "MyHandler" die Klassendeklaration durch 

        public class MyHandler extends NotificationsHandler {


11. Fügen Sie die folgenden import-Anweisungen für die `MyHandler`-Klasse hinzu:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

    
12. Fügen Sie als Nächstes die folgenden Member für die `MyHandler`-Klasse hinzu:

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;


13. In der `MyHandler` -Klasse verwenden, fügen Sie den folgenden Code zum Überschreiben der **OnRegistered** -Methode, die das Gerät mit dem mobilen Dienst Notification Hub registriert.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
    
            new AsyncTask<Void, Void, Void>() {
                            
                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
                        return null;
                    }
                    catch(Exception e) { 
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }



14. In der `MyHandler` -Klasse verwenden, fügen Sie den folgenden Code zum Überschreiben der **OnReceive** Methode, wodurch die Benachrichtigung beim Empfang angezeigt wird.

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
    
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
            mNotificationManager = (NotificationManager)
                      ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, ToDoActivity.class), 0);
    
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.drawable.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                             .bigText(msg))
                  .setContentText(msg);
    
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }


15. Aktualisieren Sie in der Datei TodoActivity.java die **OnCreate** Methode der *ToDoActivity* Klasse, um die benachrichtigungsbehandlungsklasse zu registrieren. Stellen Sie sicher, dass dieser Code nach dem Hinzufügen der *MobileServiceClient* instanziiert wird.


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Your app is now updated to support push notifications.

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q


