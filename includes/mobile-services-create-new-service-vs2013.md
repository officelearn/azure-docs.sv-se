

Mit den folgenden Schritten wird ein neuer mobiler Dienst in Azure erstellt und Ihrem Projekt Code hinzugefügt, der Ihre App mit diesem neuen Dienst verbindet. Visual Studio 2013 stellt in Ihrem Namen eine Verbindung mit Azure her, um mit Ihren Anmeldedaten den neuen mobilen Dienst zu erstellen. Wenn Sie einen neuen mobilen Dienst erstellen, müssen Sie eine Azure-SQL-Datenbank angeben, die vom mobilen Dienst zum Speichern von Anwendungsdaten verwendet wird. 


1. Öffnen Sie in Visual Studio 2013 den Projektmappen-Explorer, mit der rechten Maustaste in des Windows Store-app-Projekts, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **verbundener Dienst**.  

2. Klicken Sie im Dialogfeld Dienste-Manager auf **Erstellen Service...**, und wählen Sie dann **& Lt; Verwalten von... & Gt;** aus  **Abonnement** im Dialogfeld mobilen Service erstellen.  

    ![Dienst erstellen – Abonnements verwalten](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3. Klicken Sie in Microsoft Azure-Abonnements verwalten auf **Signieren in…** Melden Sie sich bei Ihrem Azure-Konto (falls erforderlich), wählen Sie ein verfügbares Abonnement aus, und klicken Sie dann **Schließen**.

    Wenn Ihr Abonnement bereits über mindestens einen mobilen Dienst verfügt, werden die Dienstnamen angezeigt. 

5. In der **Mobildienst erstellen** Dialogfeld Ihre **Abonnement**, die **JavaScript** Back-End **Common Language Runtime** und ein **Region** für Ihren mobilen Dienst, und geben Sie dann eine **Namen** für Ihren mobilen Dienst.

    >[AZURE.NOTE]Namen für Mobile Dienste müssen eindeutig sein. Ein rotes X neben angezeigt **Namen** bei ist der angegebene Name nicht verfügbar. 

6. In **Datenbank**, Option **& Lt; Erstellen Sie eine kostenlose SQL-Datenbank & Gt;**, geben die **Benutzername**, **Serverkennwort**, und **serverkennwortbestätigung** Klicken Sie dann auf **Erstellen**.

    ![neuen mobilen Dienst in VS 2013 erstellen](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)


    > [AZURE.NOTE]
    > Als Teil dieses Lernprogramms erstellen Sie eine neue freie SQL-Datenbankinstanz und einen entsprechenden Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Sie können jeweils nur eine freie Datenbankinstanz haben. Wenn Sie bereits eine Datenbank in der Region des neuen mobilen Dienstes haben, können Sie stattdessen die vorhandene Datenbank auswählen. Wenn Sie eine vorhandene Datenbank auswählen, stellen Sie sicher, dass Sie die richtigen Anmeldeinformationen angeben. Falls Sie falsche Anmeldeinformationen angeben, wird der mobile Dienst in einem fehlerhaften Zustand erstellt.

7. Nachdem Sie der mobile Dienst erstellt wird, wählen Sie den neu erstellten mobilen Dienst aus der Liste im Dienst-Manager, und klicken Sie auf **OK**.

    Nach Abschluss des Assistenten werden die erforderlichen NuGet-Pakete installiert, und es wird ein Verweis auf die Clientbibliothek für mobile Dienste zum Projekt hinzugefügt. Außerdem wird der Projektquellcode aktualisiert.


