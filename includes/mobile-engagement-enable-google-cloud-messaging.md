
###Skapa ett Google Cloud Messaging-projekt med API-nyckel

>[AZURE.NOTE] För att kunna slutföra den här proceduren behöver du ett Google-konto med en verifierad e-postadress. Gå till <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> om du vill skapa ett nytt Google-konto.

1. Navigera till [Google Cloud-konsolen](https://console.developers.google.com/project) och logga in med dina Google-kontouppgifter.

2. Gå till **Alla projekt** och klicka sedan på **Skapa projekt**.

3. Ange ett **Projektnamn** och klicka på **Skapa**

4. När projektet har skapats se till att du noterar **Projektnummer** som kommer att vara ett långt numeriskt värde. Du hittar det under **avsnittet IAM & Admin** i **Inställningar** i projektet och du kommer att behöva detta senare. 
 
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Nu skapar vi en nyckel för Google Cloud Messaging-plattformen som ska användas av vår plattform för att skicka meddelanden till Android-enheter. Gå till avsnittet **API-hanterare** och klicka på **Google Cloud Messaging** under **Mobila API:er**. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. På nästa sida klickar du på knappen **Aktivera**. Du uppmanas via instrumentpanelen att skapa autentiseringsuppgifter. Klicka på knappen **Gå till autentiseringsuppgifter**. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Välj **Google Cloud Messaging** från den första listrutan och **Webbserver** från den andra, och klicka sedan på **Vilka autentiseringsuppgifter behöver jag?**

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. På sidan **Lägg till autentiseringsuppgifter till ditt projekt** klickar du på **Skapa API-nyckel**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Anteckna **API-NYCKEL**-värdet. Du använder API-nyckelvärdet senare för att konfigurera i avsnittet ”Systemspecifik push-avisering”. Klicka på **Klar**.



<!--HONumber=sep16_HO1-->


