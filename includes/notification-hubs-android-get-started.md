

>[AZURE.NOTE]Um dieses Verfahren ausführen zu können, müssen Sie ein Google-Konto verfügen, die eine verifizierter e-Mail-Adresse. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.


1. Navigieren Sie zu der <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a> Website, melden Sie sich mit Ihrem Google-Anmeldeinformationen, und klicken Sie dann auf **Create Project**.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    >[AZURE.NOTE]Wenn Sie bereits über ein vorhandenes Projekt haben, Sie werden angewiesen, die <strong>Projekte</strong> Seite nach der Anmeldung. Wenn Sie über das Dashboard ein neues Projekt erstellen möchten, erweitern Sie <strong>API Project</strong>, klicken Sie unter <strong>Other projects</strong> auf <strong>Create...</strong>, geben Sie einen Projektnamen ein, und klicken Sie auf <strong>Create project</strong>.

2. Geben Sie einen Projektnamen ein, den Vertragsbedingungen akzeptieren, und auf **Erstellen**. Falls angefordert, führen Sie die SMS-Verifizierung aus, und klicken Sie auf **Erstellen** erneut.

3. Notieren Sie sich die Projektnummer, die in der **Projekte** Abschnitt. 

    Zu einem späteren Zeitpunkt im Lernprogramm legen Sie diesen Wert im Client als PROJECT_ID-Variable fest.

4. Erweitern Sie in der linken Spalte **APIs & Auth**, klicken Sie auf **APIs** und dann einen Bildlauf nach unten, und klicken Sie auf die Umschaltfläche aktivieren **Google Cloud Messaging für Android** und akzeptieren Sie die Nutzungsbedingungen. 

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Klicken Sie auf **Anmeldeinformationen**, und klicken Sie dann auf **neuen Schlüssel erstellen** 

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. In **erstellt einen neuen Schlüssel**, klicken Sie auf **Serverschlüssel**. Klicken Sie im nächsten Fenster auf **Erstellen**.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Notieren Sie sich die **API-Schlüssel** Wert.

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

    Mit diesem API-Schlüsselwert geben Sie Azure die Möglichkeit, sich bei GCM zu authentifizieren und Pushbenachrichtigungen im Auftrag Ihrer App zu verschicken.



