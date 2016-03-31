Mit den folgenden Schritten werden Ihre App im Windows Store registriert und Ihr mobiler Dienst für die Unterstützung von Pushbenachrichtigungen konfiguriert. Außerdem wird Ihrer App Code hinzugefügt, um einen Gerätekanal bei Ihrem Notification Hub zu registrieren. Visual Studio 2013 verwendet die von Ihnen angegebenen Anmeldeinformationen, um eine Verbindung mit Azure und dem Windows Store herzustellen. 

1. Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, mit der rechten Maustaste in des Windows Store-app-Projekts, klicken Sie auf **Hinzufügen** dann **Pushbenachrichtigung...**. 

    ![Assistent zum Hinzufügen von Pushbenachrichtigungen in Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

    Dadurch wird der Assistent zum Hinzufügen von Pushbenachrichtigungen gestartet.

2. Klicken Sie auf **Weiter**, melden Sie sich bei Ihrem Windows Store-Konto, und geben Sie einen Namen im **Reservieren Sie einen neuen Namen** und klicken Sie auf **reservieren**.

    Dadurch wird eine neue App-Registrierung erstellt.

3. Klicken Sie auf die neue Eintragung in die **Anwendungsname** und dann auf **Weiter**.

4. In der **Wählen Sie einen Dienst** Seite, klicken Sie auf den Namen des mobilen Diensts, und klicken Sie dann **Weiter** und **Fertig stellen**. 

    Der von Ihrem mobilen Dienst verwendete Notification Hub wird mit der Registrierung des Windows-Benachrichtigungsdiensts (WNS) aktualisiert. Jetzt können Sie Azure Notification Hubs zum Senden von Benachrichtigungen von Mobile Services an Ihre App unter Verwendung von WNS nutzen. 

    >[AZURE.NOTE]Dieses Lernprogramm demonstriert sendende von Benachrichtigungen von einem mobilen Dienst-Back-End. Sie können dieselbe Notification Hub-Registrierung verwenden, um Benachrichtigungen von einem beliebigen Back-End-Dienst zu senden. Weitere Informationen finden Sie unter [Übersicht über Notification Hubs](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Beim Ausführen des Assistenten eine neue **Push-Installation ist fast abgeschlossen** Seite in Visual Studio geöffnet ist. Auf dieser Seite wird eine alternative Methode zum Konfigurieren des Projekts für Ihren mobilen Dienst für den Versand von Benachrichtigungen beschrieben, die von diesem Lernprogramm abweicht. 

    Der Code, der Ihrer universellen Windows-App-Lösung vom Assistenten zum Hinzufügen von Pushbenachrichtigungen hinzugefügt wird, ist plattformspezifisch. Weiter unten in diesem Abschnitt beseitigen Sie diese Redundanz, indem Sie den Clientcode von Mobile Services freigeben, wodurch die Verwaltung der universellen App vereinfacht wird.  

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/

