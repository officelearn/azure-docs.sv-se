
1. Wenn Sie Ihre app noch nicht registriert haben, navigieren Sie zu der [Submit an app page] im Entwicklungscenter für Windows Store-apps, melden Sie sich mit Ihrem Microsoft-Konto, und klicken Sie dann auf **Anwendungsname**.

    ![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Wählen Sie **Erstellen einer neuen app durch einen eindeutigen Namen reservieren** und klicken Sie auf **Weiter**, geben Sie einen Namen für Ihre app im **Anwendungsname**, klicken Sie auf **Anwendungsname reservieren**, und klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie beim Abschluss des Lernprogramms erstellt haben [Get started with Mobile Services].

4. Im Projektmappen-Explorer mit der Maustaste die Windows Store-app-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**. 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

    Dies zeigt die **Zuordnen Ihrer Anwendung im Windows Store** Assistenten.

5. Klicken Sie im Assistenten auf **Anmelden** und melden Sie sich mit Ihrem Microsoft-Konto, wählen Sie die Anwendung, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter**, und klicken Sie dann auf **zuordnen**.

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.   

6. (Optional) Für universelle Windows-Apps wiederholen Sie die Schritte 4 und 5 für das Windows Phone Store-Projekt. 

6. Klicken Sie in der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Services**. 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

7. Klicken Sie auf der Seite Dienste auf **Live Services-Website** unter **Azure Mobile Services**.

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

8. Klicken Sie auf **API-Einstellungen**, aktivieren Sie **Mobile oder desktop-Client-app**, geben Sie die URL des mobilen Diensts als die **Zieldomäne**, geben Sie den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` in **URL umleiten**, klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. In **App-Einstellungen**, notieren Sie sich die Werte der **Client-ID**, **Clientschlüssel**, und **Paket-Sicherheits-ID (SID)**. 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]Der geheime Clientschlüssel und die Paket-SID sind wichtige sicherheitsrelevante Anmeldeinformationen. Geben Sie diese Informationen nicht weiter und verteilen Sie sie nicht mit Ihrer Anwendung.

10. Melden Sie sich auf die [klassischen Azure-Portal](https://manage.windowsazure.com/), klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre app.

11. Klicken Sie auf die **Identität** Geben die **Clientschlüssel** und **Paket-SID** Werte von WNS in Schritt 4 erhalten haben, und klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Klicken Sie auf die **Identität** Registerkarte. Beachten Sie, dass die **Clientschlüssel** und **Paket-SID** -Werte sind bereits im vorherigen Schritt festgelegt. Geben Sie den **Client-ID** Notizen, und klicken Sie dann auf zuvor vorgenommenen **Speichern**.

    ![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582


