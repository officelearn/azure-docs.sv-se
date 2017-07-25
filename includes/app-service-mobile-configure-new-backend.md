
1. Klicka på **Apptjänster** > Välj din mobilappsserverdel > klicka på **Quickstart** > din klientplattform (iOS, Android, Xamarin, Cordova).

![Azure-portalen med snabbstart för mobila appar markerad][quickstart]

2. Om anslutningen till databasen inte är konfigurerad måste du skapa en dataanslutning.

![Azure-portalen med Mobilappar Anslut till BD][connect]

  * Skapa ny SQL Database och server.

  ![Azure-portalen med Mobile Apps skapa ny BD och server][server]

  * Vänta tills anslutningen har skapats.

  ![Skapa Azure-portalen med Mobile Apps-dataanslutning][notification]

  * Dataanslutningen måste lyckas.

  ![Skapa Azure-portalen med Mobile Apps-dataanslutning][already-connection]

3. Under **2. Skapa ett tabell-API**, Välj Node.js för **Språk för serverdel**. Godkänn bekräftelsen och klicka på **Skapa TodoItem-tabell**. En ny *TodoItem*-tabell skapas i din databas. Kom ihåg att när du byter en befintlig serverdel till Node.js, så skrivs allt innehåll över! För att skapa en .NET-serverdel [följer du dessa instruktioner][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
