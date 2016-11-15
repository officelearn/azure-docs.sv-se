
1. Navigera till [Google Cloud-konsolen](https://console.developers.google.com/project) och logga in med dina Google-kontouppgifter. 
2. Klicka på **Skapa projekt**, ange ett projektnamn och klicka sedan på **Skapa**. Utför SMS-verifieringen om den efterfrågas och klicka på **Skapa** en gång till.
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     Ange ditt nya **projektnamn** och klicka på **Skapa projekt**.
3. Klicka på **Verktyg och mer**-knappen och klicka sedan på **Projektinformation**. Anteckna **projektnumret**. Du kommer behöva ange det värdet som `SenderId`-variabeln i klientappen.
   
       ![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. I instrumentpanelen för projektet under **Mobila API:er** klickar du på **Google Cloud Messaging** och på nästa sida klickar du på **Aktivera API** och accepterar villkoren. 
   
    ![Aktivera GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Aktivera GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. I instrumentpanelen för projektet klickar du på **Autentiseringsuppgifter** > **Skapa autentiseringsuppgifter** > **API-nyckel**. 
   
       ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. I **Skapa en ny nyckel** klickar du på **Servernyckel**, skriver in ett namn för din nyckel och klickar sedan på **Skapa**.
7. Anteckna **API-NYCKEL**-värdet.
   
    Du kommer att använda API-nyckelvärdet för att autentisera med GCM och skicka push-meddelanden för din app.



<!--HONumber=Nov16_HO2-->


