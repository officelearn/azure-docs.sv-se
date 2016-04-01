###Erteilen Sie Ihrem Pushzertifikat den Zugriff auf Mobile Engagement.

Damit Mobile Engagement Pushbenachrichtigungen in Ihrem Namen senden darf, müssen Sie den Zugriff auf Ihr Zertifikat gestatten. Dies wird durch Konfigurieren und Eingeben Ihres Zertifikats im Mobile Engagement-Portal erreicht. Sicherstellen, dass Ihr P12-Zertifikat zu erhalten, wie in erläutert [Apple Dokumentation](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. Navigieren Sie zum Mobile Engagement-Portal. Stellen Sie sicher, Sie in der richtigen und klicken Sie dann auf die **beginnen** unten:

    ![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. Klicken Sie auf die **Einstellungen** Seite im Engagement-Portal. Klicken Sie hier auf die **systemeigener Push** Abschnitt, um Ihr p12-Zertifikat hochzuladen:

    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. Wählen Sie Ihr P12-Zertifikat aus, laden Sie es hoch, und geben Sie anschließend Ihr Kennwort ein:

    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>Versenden von Benachrichtigungen an die App

Wir erstellen jetzt eine einfache Pushbenachrichtigungskampagne, die eine Pushbenachrichtigung an die App sendet:

1. Navigieren Sie zu der **erreichen** auf Mobile Engagement-Portal.

2. Klicken Sie auf **neue Ankündigung** um die Push-Kampagne zu erstellen.

    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. Richten Sie die ersten Felder der Kampagne ein:

    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

    -   Geben Sie einen **Namen** für die Kampagne 
    -   Wählen Sie die **Lieferzeit** als **nur außerhalb app**: Dies ist der einfache Apple-pushbenachrichtigungstyp, der Text unterstützt.
    -   Geben Sie in den Benachrichtigungstext zunächst den **Titel** die erste Zeile in der Push-Vorgang sein.
    -   Geben Sie Ihre **Nachricht** werden die in der zweiten Zeile

4. Führen Sie einen Bildlauf nach unten, und wählen Sie im Abschnitt Inhalt **nur Benachrichtigung**

    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. Sie haben das Festlegen der einfachst möglichen Kampagne abgeschlossen. Jetzt einen Bildlauf nach unten, und klicken Sie auf **Erstellen** Schaltfläche, um die Push-Benachrichtigung-Kampagne zu speichern. 

6. Klicken Sie abschließend - auf **aktivieren** Push-Benachrichtigung zu senden. 

    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. Sie erhalten die Benachrichtigung auf Ihrem iOS-Gerät im Notification Center wie folgt:

    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. Wenn Sie eine Apple Watch zusammen mit diesem iOS-Gerät nutzen, wird die Benachrichtigung auf Ihrer Apple Watch angezeigt:

    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 

