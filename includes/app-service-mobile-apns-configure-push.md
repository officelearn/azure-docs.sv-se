

1.  Starten Sie auf Ihrem Mac **Schlüsselbund**. Öffnen Sie **Kategorie** > **Meine Zertifikate**. Suchen Sie das zu exportierende SSL-Zertifikat (das Sie zuvor heruntergeladen haben), und legen Sie den Inhalt offen. Wählen Sie nur das Zertifikat ohne privaten Schlüssel, und [exportieren](https://support.apple.com/kb/PH20122?locale=en_US).

2. Klicken Sie in der Azure-Portal auf **Alle durchsuchen** > **Anwendungsdienste** > Ihre Mobile App-Back-End > **Einstellungen** > **Mobile** > **Push** > **Konfigurieren erforderlichen Einstellungen** > **+ Notification Hub**, und geben Sie einen Namen und einen Namespace für den Notification Hub, und klicken Sie dann auf die **OK** Schaltfläche.

    ![][1]

3. In der **Erstellen Notification Hub** Blatt, klicken Sie auf die **Erstellen** Schaltfläche.
     
    Bevor Sie mit dem nächsten Schritt fortfahren, klicken Sie auf **Benachrichtigungen**, um sicherzustellen, dass Ihr Notification Hub-Setup abgeschlossen ist. 

4. Klicken Sie in der Azure-Portal auf **Alle durchsuchen** > **Anwendungsdienste** > Ihre Mobile App-Back-End > **Einstellungen** > **Mobile** > **Push** > **Apple Push Notification Services** > **Zertifikat hochladen**. Hochladen der. p12-Datei, die richtige Auswahl **Modus** (entspricht, ob die SSL-Clientzertifikats, die zuvor von Ihnen generierte Entwicklung oder Verteilung galt). Ihr Dienst ist jetzt so konfiguriert, dass er mit Pushbenachrichtigungen unter iOS arbeitet.

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


