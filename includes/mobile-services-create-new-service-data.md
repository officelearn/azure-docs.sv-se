

Als Nächstes erstellen Sie einen mobilen Dienst, um die In-Memory-Liste für die Datenspeicherung zu ersetzen. Führen Sie die folgenden Schritte aus, um einen neuen mobilen Dienst zu erstellen.

1. Melden Sie sich bei der [klassischen Azure-Portal](https://manage.windowsazure.com/). 
2.  Klicken Sie am unteren Rand des Navigationsbereichs, **+ NEW**.

    ![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.  Erweitern Sie **Compute** und **Mobile Service**, klicken Sie dann auf **Erstellen**.

    ![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)

    Dies zeigt die **neuen Mobildienst** Dialogfeld.

4.  In der **Erstellen eines mobilen Diensts** Seite **Erstellen Sie eine freie 20 MB SQL Database**, geben Sie dann einen Unterdomänennamen für den neuen mobilen Dienst in der **URL** Textfeld ein, und warten Sie, bis der name überprüft. Nachdem der Name fertig überprüft wurde, klicken Sie auf den Pfeil nach rechts, um die nächste Seite aufzurufen.   

    ![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)

    Dies zeigt die **Geben Sie Einstellungen für die Datenbank** Seite.

    
    > [AZURE.NOTE] Im Rahmen dieses Lernprogramms erstellen Sie eine neue Instanz der SQL-Datenbank und Server. Sie können diese neue Datenbank wiederverwenden und wie jede andere SQL-Datenbankinstanz verwalten. Falls Sie bereits eine Datenbank in der gleichen Region wie den neuen mobilen Dienst haben, wählen Sie stattdessen **verwendet eine vorhandene Datenbank** und wählen Sie dann die Datenbank. Die Verwendung einer Datenbank in einer anderen Region wird nicht empfohlen, da zusätzliche Kosten für Bandbreite anfallen können und es zu höherer Latenz kommen kann.

5.  In **Namen**, geben Sie den Namen der neuen Datenbank, und geben Sie dann **Benutzername**, dies ist der name für die neue SQL-Datenbankserver, geben das Kennwort und klicken Sie auf die Schaltfläche mit dem Häkchen, um den Vorgang abzuschließen.

    ![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
    > [AZURE.NOTE] Wenn das eingegebene Kennwort nicht den Mindestanforderungen entspricht oder nicht übereinstimmen, wird eine Warnung angezeigt.  
    >
    > Es wird empfohlen, sich den Anmeldenamen und das Kennwort des Administrators zu notieren. Sie benötigen diese Informationen, um die SQL-Datenbankinstanz oder den Server in der Zukunft wiederzuverwenden.

Sie haben nun einen neuen mobilen Dienst erstellt, der von Ihren mobilen Apps verwendet werden kann. Als Nächstes fügen Sie eine neue Tabelle zum Speichern von App-Daten hinzu. Diese Tabelle wird von der App anstelle der In-Memory-Sammlung verwendet.



