

1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**. Följ sedan instruktionerna som visas.

    ![Lägg till Firebase till din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Välj fliken **Allmänt** i projektinställningarna. Ladda ned filen **google-services.json** som innehåller server-API-nyckeln och klient-ID:t.
5. Välj fliken med **molnmeddelanden** i projektinställningarna och kopiera sedan värdet för **Äldre servernyckel**. Du använder det här värdet när du konfigurerar åtkomstprincipen för meddelandehubben.
