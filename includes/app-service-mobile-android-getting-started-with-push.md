1. I din **app** projekt, öppna filen `AndroidManifest.xml`. Ersätt i koden i följande två steg  *`**my_app_package**`*  med namnet på app-paket för projektet. Detta är värdet för den `package` attribut för den `manifest` tagg.
2. Lägg till följande nya behörigheter efter den befintliga `uses-permission` element:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. Lägg till följande kod efter den `application` startkoden:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. Öppna filen *ToDoActivity.java*, och Lägg till följande importuttryck:

        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. Lägg till följande privata variabeln i klassen. Ersätt  *`<PROJECT_NUMBER>`*  med projektnumret Google har tilldelats din app i föregående procedur.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. Ändra definitionen av *MobileServiceClient* från **privata** till **offentliga statiska**, så det nu ser ut så här:

        public static MobileServiceClient mClient;
7. Lägg till en ny klass för att hantera meddelanden. Öppna i Projektutforskaren den **src** > **huvudsakliga** > **java** noder och högerklicka på noden paket namn. Klicka på **ny**, och klicka sedan på **Java-klass**.
8. I **namn**, typen `MyHandler`, och klicka sedan på **OK**.

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)

9. Ersätt klassdeklarationen med i filen MyHandler:

        public class MyHandler extends NotificationsHandler {
10. Lägg till följande importuttryck för den `MyHandler` klass:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. Sedan till den här medlemmen till den `MyHandler` klass:

        public static final int NOTIFICATION_ID = 1;
12. I den `MyHandler` klassen och Lägg till följande kod för att åsidosätta den **onRegistered** metod som registrerar din enhet med meddelandehubben mobiltjänst.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);

           new AsyncTask<Void, Void, Void>() {

               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. I den `MyHandler` klassen och Lägg till följande kod för att åsidosätta den **onReceive** metod, vilket gör meddelandet ska visas när den tas emot.

        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");

               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags

               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();

               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. Uppdatera tillbaka i TodoActivity.java-filen i **onCreate** metod för den *ToDoActivity* klassen för att registrera meddelande hanterare klassen. Se till att lägga till den här koden efter den *MobileServiceClient* instansieras.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Appen har uppdaterats för att stödja push-meddelanden.
