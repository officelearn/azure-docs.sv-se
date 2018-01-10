

1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.
2. När du har skapat ditt projekt klickar du på **Lägg till Firebase i din Android-app** och följer anvisningarna.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Klicka på ditt projektkugghjul i Firebase-konsolen och klicka sedan på **Projektinställningar**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Klickar du på den **allmänna** i dina Projektinställningar och sedan ladda ned den **google services.json** fil som innehåller Server API-nyckel och klient-ID.
5. Klicka på den **Cloud Messaging** i dina Projektinställningar och kopiera värdet för den **äldre servernyckel**. Det här värdet används för att konfigurera åtkomstprincipen för notification hub.
