
###Skapa ett Google Cloud Messaging-projekt med API-nyckel

>[AZURE.NOTE] För att kunna slutföra den här proceduren behöver du ett Google-konto med en verifierad e-postadress. Gå till <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a> om du vill skapa ett nytt Google-konto.

1. Navigera till [Google Cloud-konsolen](https://console.developers.google.com/project) och logga in med dina Google-kontouppgifter.

2. Klicka på **Gå till projekt** och sedan på **Skapa projekt**.
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Ange ett projektnamn.

4. Anteckna projektnumret som visas under textrutan **Projektnamn**. Du behöver numret senare i guiden för att fylla i Android-manifestfilen.
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)   
5. Klicka på **Skapa**.

6. Kontrollera i den vänstra kolumnen att **Översikt** är markerad och klicka på **Google Cloud Messaging** under mobila API:er. På nästa sida klickar du på **Aktivera**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. På nästa sida klickar du på **Gå till autentiseringsuppgifter** och på sidan därefter väljer du **Google Cloud Messaging** i den första listrutan och **Webbserver** i den andra och klickar på **Vilka autentiseringsuppgifter behöver jag?**

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. På sidan **Lägg till autentiseringsuppgifter till ditt projekt** klickar du på **Skapa API-nyckel**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Anteckna **API-NYCKEL**-värdet. Du använder API-nyckelvärdet senare för att konfigurera i avsnittet ”Systemspecifik push-avisering”. Klicka på **Klar**.



<!--HONumber=Jun16_HO2-->


