

##Generieren der Zertifikatsignieranforderungsdatei

Der Apple Push Notification Service (APNS) verwendet Zertifikate zur Authentifizierung Ihrer Pushbenachrichtigungen. Befolgen Sie diese Anweisungen, um das erforderliche Pushzertifikat zum Senden und Empfangen von Benachrichtigungen zu erstellen. Weitere Informationen zu diesen Konzepten finden Sie in der offiziellen [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) Dokumentation.

Erstellen Sie die Zertifikatsignieranforderungsdatei (CSR-Datei), die von Apple zur Generierung eines signierten Pushzertifikats verwendet wird.

1. Führen Sie auf Ihrem Mac das Tool "Schlüsselbundverwaltung" aus. Kann aus geöffnet werden die **Dienstprogramme** Ordner oder **andere** Ordner auf Launch Pad.

2. Klicken Sie auf **Schlüsselbund**, erweitern Sie **Zertifikatsassistent**, klicken Sie dann auf **Anfordern eines Zertifikats von einer Zertifizierungsstelle...**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Wählen Sie Ihre **e-Mail-Adresse** und **Allgemeiner Name** , stellen Sie sicher, dass **auf Datenträger speichern** ausgewählt ist, und klicken Sie dann auf **Weiter**. Lassen Sie die **Zertifizierungsstelle e-Mail-Adresse** Feld leer, da es nicht erforderlich ist.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Geben Sie einen Namen für die Zertifikatsignieranforderungsdatei (CSR)-Datei in **Speichern unter**, wählen Sie den Speicherort im ****, klicken Sie dann auf **Speichern**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Hierdurch wird die CSR-Datei am gewählten Ort gespeichert, wobei Schreibtisch der Standardort ist. Merken Sie sich den für diese Datei gewählten Speicherort.

Anschließend werden Sie Ihre App bei Apple registrieren, Pushbenachrichtigungen aktivieren und diese exportierte CSR hochladen, um ein Pushzertifikat zu erstellen.

##Registrieren der App für Pushbenachrichtigungen

Um Pushbenachrichtigungen an eine iOS-App senden zu können, muss diese bei Apple registriert und auch für Pushbenachrichtigungen angemeldet werden.  

1. Wenn Sie Ihre app noch nicht registriert haben, navigieren Sie zu der <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> im Apple Developer Center, melden Sie sich mit Ihrer Apple-ID, klicken Sie auf **Bezeichner**, klicken Sie dann auf **App IDs**, und klicken Sie abschließend auf die **+** Symbol, um eine neue app zu registrieren.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Aktualisieren Sie die folgenden drei Felder für Ihre neue Anwendung ein, und klicken Sie dann auf **Weiter**:

    * **Namen**: Geben Sie einen beschreibenden Namen für Ihre app in den **Namen** Feld der **App ID Description** Abschnitt.
    
    * **Bundlebezeichner**: unter der **explizite App-ID** Geben Sie im Abschnitt eine **Bundle-ID** im Formular `<Organization Identifier>.<Product Name>` wie unter der [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Die *Organisations-ID* und *Product Name* Sie verwenden, muss die Organisation und entsprechen Produkt Sie verwenden, wenn Sie Ihr XCode-Projekt erstellen. In der folgenden Screenshot werden *NotificationHubs* dient als organisationskennung und *GetStarted* wird als Produktname verwendet. Wenn Sie sicherstellen, dass diese Werte mit den Werten übereinstimmen, die Sie in Ihrem XCode-Projekt verwenden, können Sie das richtige Veröffentlichungsprofil mit XCode verwenden. 
    
    * **Pushbenachrichtigungen**: Überprüfen der **Pushbenachrichtigungen** -Option in der **Anwendungsdienste** Abschnitt.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

    Hierdurch wird Ihre App-ID generiert, und Sie werden zur Bestätigung der Daten aufgefordert. Klicken Sie auf **Senden**


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


    Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. Bestimmen Sie im Developer Center unter "App IDs" die soeben erstellte App-ID, und klicken Sie auf deren Zeile.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Durch Klicken auf die App-ID werden Einzelheiten zur App angezeigt. Klicken Sie auf die **Bearbeiten** unten.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Führen Sie einen Bildlauf zum unteren Bildschirmrand aus, und klicken Sie auf die **Create Certificate...** Schaltfläche unter dem Abschnitt **Development Push SSL Certificate**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Hierdurch wird der Assistent "Add iOS Certificate" zur Anzeige gebracht.

    > [AZURE.NOTE] In diesem Lernprogramm wird ein Entwicklungszertifikat verwendet. Derselbe Prozess wird auch zum Registrieren eines Produktionszertifikats durchgeführt. Achten Sie darauf, dass Sie denselben Zertifikattyp beim Senden von Benachrichtigungen verwenden.

5. Klicken Sie auf **Choose File**, navigieren Sie zum Speicherort der CSR-Datei, die Sie in der ersten Aufgabe erstellt und dann auf **generieren**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Nachdem das Zertifikat vom Portal erstellt wurde, klicken Sie auf die **herunterladen** und klicken auf **Fertig**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Hierdurch wird das Zertifikat heruntergeladen und auf Ihrem Computer in Ihrem Downloadordner gespeichert.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] In der Standardeinstellung die heruntergeladene Datei ein Entwicklungszertifikat namens **aps_development.cer**.

7. Doppelklicken Sie auf das heruntergeladene pushzertifikat **aps_development.cer**.

    Hierdurch wird das neue Zertifikat im Schlüsselbund installiert, wie unten gezeigt:

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] Der Name in Ihrem Zertifikat kann verschieden sein, aber es wird das Präfix **Apple Development iOS Push Services:**.

8. Schlüsselbund, mit der Maustaste des neue Push-Zertifikats, das Erstellen der **Zertifikate** Kategorie. Klicken Sie auf **exportieren**, nennen Sie die Datei, wählen Sie die **P12** format, und klicken Sie dann auf **Speichern**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Notieren Sie sich den Dateinamen und den Speicherort des exportierten P12-Zertifikats, das zum Aktivieren der Authentifizierung mit APNS verwendet wird.

    >[AZURE.NOTE] In diesem Lernprogramm wird eine QuickStart.p12-Datei erstellt. Name und Ort Ihrer Datei können verschieden sein.


##Erstellen eines Bereitstellungsprofils für die App

1. In der <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>, wählen **Provisioning Profiles**, wählen **alle**, und klicken Sie dann auf die **+** um ein neues Profil zu erstellen. Dadurch wird die **Add iOS Provisiong Profile** Assistenten

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Wählen Sie **iOS App Development** unter **Entwicklung** als bereitstellungsprofiltyp aus, und klicken Sie auf **Weiter**. 


3. Wählen Sie als Nächstes die app-ID, die Sie gerade erstellt haben, aus der **App-ID** Dropdown-Liste aus, und klicken Sie auf **fortsetzen**

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. In der **Wählen Sie Zertifikate** Bildschirm Wählen Sie Ihr üblichen Entwicklungszertifikat zum Signieren von Code verwendet, und klicken Sie auf **Weiter**. Dabei handelt es sich nicht um das soeben erstellte Pushzertifikat.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Wählen Sie als Nächstes die **Geräte** für Tests, und klicken Sie auf **fortsetzen**

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Abschließend wählen Sie einen Namen für das Profil im **Profilname**, klicken Sie auf **generieren**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. Nachdem das neue Bereitstellungsprofil erstellt wurde, klicken Sie darauf, um es herunterzuladen und auf Ihrem Xcode-Entwicklungscomputer zu installieren. Klicken Sie dann auf **Fertig**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)




