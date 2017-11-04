
1. I vyn lösning (eller **Solution Explorer** i Visual Studio), högerklicka på den **komponenter** mappen, klicka på **få fler komponenter...** , söka efter den **Google Cloud Messaging-klienten** komponenten och Lägg till den i projektet.
2. Öppna filen ToDoActivity.cs projektet och Lägg till följande med instruktionen till klassen:
   
        using Gcm.Client;
3. I den **ToDoActivity** klassen och Lägg till följande nya kod: 
   
        // Create a new instance field for this activity.
        static ToDoActivity instance = new ToDoActivity();
   
        // Return the current activity instance.
        public static ToDoActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }
        // Return the Mobile Services client.
        public MobileServiceClient CurrentClient
        {
            get
            {
                return client;
            }
        }
   
    På så sätt kan du få åtkomst till den mobila klient instansen från tjänstprocessen för push-hanterare.
4. Lägg till följande kod i den **OnCreate** metod, efter den **MobileServiceClient** skapas:
   
       // Set the current instance of TodoActivity.
       instance = this;
   
       // Make sure the GCM client is set up correctly.
       GcmClient.CheckDevice(this);
       GcmClient.CheckManifest(this);
   
       // Register the app for push notifications.
       GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);

Din **ToDoActivity** nu har förberetts för att lägga till push-meddelanden.

