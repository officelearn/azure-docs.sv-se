

1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat projektet, Välj **lägga till Firebase till din Android-app**. Följ sedan instruktionerna som tillhandahålls.

    ![Lägg till Firebase i din Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

    ![Välj Projektinställningar](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Välj den **allmänna** fliken i dina Projektinställningar. Hämta den **google services.json** fil som innehåller Server API-nyckel och klient-ID.
5. Välj den **Cloud Messaging** i dina Projektinställningar och sedan kopiera värdet för den **äldre servernyckel**. Du kan använda det här värdet för att konfigurera åtkomstprincipen för notification hub.
