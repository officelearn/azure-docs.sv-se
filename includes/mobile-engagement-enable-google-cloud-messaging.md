>[AZURE.NOTE] Um dieses Verfahren ausführen zu können, müssen Sie ein Google-Konto verfügen, die eine verifizierter e-Mail-Adresse. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

1. Navigieren Sie zu der [Google Cloud Console](https://console.developers.google.com/project) und melden Sie sich mit Ihrem Google-Anmeldeinformationen, und klicken Sie dann auf **Create Project**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)   

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   

2. Geben Sie einen Projektnamen ein, den Vertragsbedingungen akzeptieren, und auf **Erstellen**. Falls angefordert, führen Sie die SMS-Verifizierung aus, und klicken Sie auf **Erstellen** erneut.

3. Notieren Sie sich die Projektnummer, die in der **Projekte** Abschnitt. Sie benötigen ihn später in diesem Tutorial, um die Android-Manifestdatei aufzufüllen. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)   

4. Erweitern Sie in der linken Spalte **APIs & Auth**, klicken Sie auf **APIs** und dann einen Bildlauf nach unten, und klicken Sie auf **Cloud Messaging for Android**. Klicken Sie auf der nächsten Seite **Enable API** und akzeptieren Sie die Nutzungsbedingungen. 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Klicken Sie auf **Anmeldeinformationen**, und klicken Sie dann auf **Add Credential**->**API-Schlüssel** 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. In **erstellt einen neuen Schlüssel**, klicken Sie auf **Serverschlüssel**. Klicken Sie im nächsten Fenster auf **Erstellen**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png) 

7. Notieren Sie sich die **API-Schlüssel** Wert. Sie verwenden diesen API-Schlüsselwert später für die Konfiguration im Abschnitt „Systemeigener Push“.



