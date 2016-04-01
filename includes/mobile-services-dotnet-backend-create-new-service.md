
Führen Sie die folgenden Schritte aus, um einen neuen mobilen Dienst zu erstellen.

1.  Melden Sie sich bei der [klassischen Azure-Portal](https://manage.windowsazure.com/). Klicken Sie am unteren Rand des Navigationsbereichs, **+ NEW**. Erweitern Sie **Compute** und **Mobile Service**, klicken Sie dann auf **Erstellen**.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png)

    Dies zeigt die **Erstellen Sie einen mobilen Service** Dialogfeld.

2.  In der **Erstellen Sie einen mobilen Service** Seite **eine freie 20 MB-SQL-Datenbank erstellen**, wählen **.NET** Common Language Runtime, geben Sie dann einen Unterdomänennamen für den neuen mobilen Dienst im Namen der **URL** Textbox. Klicken Sie auf den Pfeil nach rechts, um die nächste Seite aufzurufen.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png)

    Dies zeigt die **Geben Sie Einstellungen für die Datenbank** Seite.

    > [AZURE.NOTE] Im Rahmen dieses Lernprogramms erstellen Sie eine neue Instanz der SQL-Datenbank und Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Falls Sie bereits eine Datenbank in der gleichen Region wie den neuen mobilen Dienst haben, wählen Sie stattdessen **verwendet eine vorhandene Datenbank** und wählen Sie dann die Datenbank. Die Verwendung einer Datenbank in einer anderen Region wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann.

3.  In **Namen**, geben Sie den Namen der neuen Datenbank, und geben Sie dann **Benutzername**, dies ist der name für die neue SQL-Datenbankserver, geben das Kennwort und klicken Sie auf die Schaltfläche mit dem Häkchen, um den Vorgang abzuschließen.
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png)

Sie haben nun einen neuen mobilen Dienst erstellt, der von Ihren mobilen Apps verwendet werden kann.


