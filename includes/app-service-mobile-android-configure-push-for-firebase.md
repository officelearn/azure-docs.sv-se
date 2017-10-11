
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **Bläddra bland alla** > **Apptjänster**, och klicka sedan på din Mobile Apps-serverdel. Under **inställningar**, klickar du på **App Service Push**, och klicka sedan på namnet på din meddelandehubb.
2. Gå till **Google (GCM)**, ange den **servernyckel** värde som du fick från Firebase i föregående procedur och klicka sedan på **spara**.

    ![Ange GCM API-nyckeln i portalen](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Mobile Apps serverdel har nu konfigurerats för att använda Firebase Cloud Messaging. På så sätt kan du skicka push-meddelanden i appen körs på en Android-enhet med hjälp av notification hub.

<!-- URLs. -->


<!-- images -->
