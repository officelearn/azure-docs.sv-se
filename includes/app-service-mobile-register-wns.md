
1. In Visual Studio Projektmappen-Explorer mit der Maustaste die Windows Store-app-Projekt, klicken Sie auf **Store** > **App mit Store verknüpfen**. 

    ![Zuordnen der App zu Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
    
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto, geben Sie einen Namen für Ihre app im **app-Namen reservieren**, klicken Sie dann auf **reservieren**.

3. Nachdem die app-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen app, klicken Sie auf **Weiter**, und klicken Sie dann auf **zuordnen**. Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt. 

7. Wiederholen Sie die Schritte 1 und 3 für das Windows Phone Store-App-Projekt mithilfe der zuvor erstellten Registrierung für die Windows Store-App.  

7. Navigieren Sie zu der [Windows-Entwicklungscenter](https://dev.windows.com/en-us/overview), melden Sie sich mit Ihrem Microsoft-Konto, klicken Sie auf die neue app-Registrierung in **Meine apps**, erweitern Sie dann **Services** > **Pushbenachrichtigungen**. 

8. In der **Pushbenachrichtigungen** auf **Live Services-Website** unter **Microsoft Azure Mobile Services**.

9. In der **App-Einstellungen** Registerkarte, notieren Sie sich die Werte der **Clientschlüssel** und **Paket-SID**. 

    ![App-Einstellung im Developer Center](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT] Der geheime Clientschlüssel und Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

