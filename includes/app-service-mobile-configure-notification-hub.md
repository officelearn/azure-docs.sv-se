Funktionen Mobilappar i Azure App Service använder [Azure Notification Hubs] att skicka push-meddelanden, så att du ska konfigurera en meddelandehubb för din mobila app.

1. I den [Azure-portalen], gå till **Apptjänster**, och klicka sedan på din app-serverdel. Under **inställningar**, klickar du på **Push**.
2. Klicka på **Anslut** att lägga till en resurs för notification hub i appen. Du kan antingen skapa ett nav eller ansluta till en befintlig.

    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nu har du anslutit en meddelandehubb i projektet Mobile Apps serverdel. Du kommer senare att konfigurera den här meddelandehubben för att ansluta till ett plattform meddelandesystem (PNS) för att skicka till enheter.

[Azure-portalen]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
