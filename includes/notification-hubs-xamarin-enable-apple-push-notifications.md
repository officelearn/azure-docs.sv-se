

Um Ihre App für Pushbenachrichtigungen über den Apple Push Notification Service (APNS) zu registrieren, müssen Sie ein neues Pushzertifikat, eine neue App-ID und ein neues Bereitstellungsprofil für das Projekt im Apple-Entwicklerportal erstellen. Die App-ID enthält die Konfigurationsinformationen, die es Ihrer App ermöglichen, Pushbenachrichtigungen zu senden und zu empfangen. Diese Einstellungen enthalten das Pushbenachrichtigungszertifikat zur Authentifizierung beim Apple Push Notification Service (APNS), wenn Pushbenachrichtigungen gesendet und empfangen werden. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) Dokumentation.


####Generieren der Zertifikatsignieranforderungsdatei für das Pushzertifikat

Diese Schritte führen Sie durch die Erstellung der Zertifikatsignieranforderung. Diese wird verwendet, um ein Pushzertifikat zu generieren, das mit APNS verwendet wird.

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Kann aus geöffnet werden die **Dienstprogramme** Ordner oder **andere** Ordner auf Launch Pad.

2. Klicken Sie auf **Schlüsselbund**, erweitern Sie **Zertifikatsassistent**, klicken Sie dann auf **Anfordern eines Zertifikats von einer Zertifizierungsstelle...**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Wählen Sie Ihre **e-Mail-Adresse** und **Allgemeiner Name** , stellen Sie sicher, dass **auf Datenträger speichern** ausgewählt ist, und klicken Sie dann auf **Weiter**. Lassen Sie die **Zertifizierungsstelle e-Mail-Adresse** Feld leer, da es nicht erforderlich ist.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR)-Datei in **Speichern unter**, wählen Sie den Speicherort im ****, klicken Sie dann auf **Speichern**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.


####Registrieren der App für Pushbenachrichtigungen

Erstellen Sie eine neue explizite App-ID für Ihre Anwendung bei Apple, und konfigurieren Sie sie auch für Pushbenachrichtigungen.  

1. Navigieren Sie zu der [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456) Klicken Sie im Apple Developer Center, melden Sie sich mit Ihrer Apple-ID auf **Bezeichner**, klicken Sie dann auf **App IDs**, und klicken Sie abschließend auf die **+** Symbol, um eine neue app zu registrieren.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Aktualisieren Sie die folgenden drei Felder für Ihre neue Anwendung ein, und klicken Sie dann auf **Weiter**:

    * **Namen**: Geben Sie einen beschreibenden Namen für Ihre app in den **Namen** Feld der **App ID Description** Abschnitt.
    
    * **Bundlebezeichner**: unter der **explizite App-ID** Geben Sie im Abschnitt eine **Bundle-ID** im Formular `<Organization Identifier>.<Product Name>` wie unter der [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Dieser muss mit dem übereinstimmen, was auch im XCode- oder Xamarin-Projekt für Ihre App verwendet wird.    
     
    * **Pushbenachrichtigungen**: Überprüfen der **Pushbenachrichtigungen** -Option in der **Anwendungsdienste** Abschnitt.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  Überprüfen Sie die Einstellung, auf die Ihre App-ID-Bildschirm Confirm und nachdem Sie überprüft haben sie auf **Senden**

4.  Sobald Sie die neue App-ID gesendet haben, sehen Sie die **Registrierung abschließen** Bildschirm. Klicken Sie auf **Fertig**.

5. Bestimmen Sie im Developer Center unter "App IDs" die soeben erstellte App-ID, und klicken Sie auf deren Zeile. Durch Klicken auf die Zeile mit der App-ID werden Einzelheiten zur App angezeigt. Klicken Sie auf die **Bearbeiten** unten.

6. Führen Sie einen Bildlauf zum unteren Bildschirmrand aus, und klicken Sie auf die **Create Certificate...** Schaltfläche unter dem Abschnitt **Development Push SSL Certificate**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Hierdurch wird der Assistent „Add iOS Certificate“ angezeigt.

    > [AZURE.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

7. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort, in dem Sie die CSR-Datei, für die Push-Zertifikat gespeichert. Klicken Sie dann auf **generieren**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Nachdem das Zertifikat vom Portal erstellt wurde, klicken Sie auf die **herunterladen** Schaltfläche.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Hierdurch wird das Signierzertifikat heruntergeladen und auf Ihrem Computer in Ihrem Download-Ordner gespeichert.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] In der Standardeinstellung die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

9. Doppelklicken Sie auf das heruntergeladene pushzertifikat **aps_development.cer**. Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber es wird das Präfix **Apple Development iOS Push Services:**.

10. Schlüsselbund, mit der Maustaste des neue Push-Zertifikats, das Sie gerade erstellt, in haben der **Zertifikate** Kategorie. Klicken Sie auf **exportieren**, nennen Sie die Datei, wählen Sie die **P12** format, und klicken Sie dann auf **Speichern**.

    Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats. Damit wird die Authentifizierung mit APNS aktiviert, indem Sie es zum klassischen Azure-Portal hochladen.



####Erstellen eines Bereitstellungsprofils für die App

1. In der <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, wählen **Provisioning Profiles**, wählen **alle**, und klicken Sie dann auf die **+** um ein neues Profil zu erstellen. Dadurch wird die **Add iOS Provisiong Profile** Assistenten

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Wählen Sie **iOS App Development** unter **Entwicklung** als bereitstellungsprofiltyp aus, und klicken Sie auf **Weiter**. 


3. Wählen Sie als Nächstes die app-ID, die Sie gerade erstellt haben, aus der **App-ID** Dropdown-Liste aus, und klicken Sie auf **fortsetzen**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. In der **Wählen Sie Zertifikate** Bildschirm Wählen Sie Ihre Entwicklungszertifikat zum Signieren von Code verwendet, und klicken Sie auf **Weiter**. Dabei handelt es sich um ein Signaturzertifikat, nicht um das soeben erstellte Pushzertifikat.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Wählen Sie als Nächstes die **Geräte** für Tests, und klicken Sie auf **fortsetzen**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Abschließend wählen Sie einen Namen für das Profil im **Profilname**, klicken Sie auf **generieren**.

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)




