
1. Melden Sie sich auf dem lokalen Computer, auf dem [Azure-Verwaltungsportal](http://manager.windowsazure.com) (Dies ist das alte Portal).

2. Wählen Sie am unteren Rand des Navigationsbereichs, **+ NEW** > **Anwendungsdienste** > **BizTalk Service** > **Erstellen Sie benutzerdefinierte**.

3. Geben Sie einen **BizTalk-Dienstname** und wählen Sie eine **Edition**. 

    In diesem Lernprogramm wird **mobile1**. Sie müssen einen eindeutigen Namen für Ihren neuen BizTalk Service angeben.

4. Nachdem der BizTalk Service erstellt wurde, wählen Sie die **Hybridverbindungen** und anschließend auf **Hinzufügen**.

    ![Hybridverbindung hinzufügen](./media/hybrid-connections-create-new/3.png)

    Daraufhin wird eine neue Hybridverbindung erstellt.

5. Geben Sie einen **Namen** und **Hostnamen** für den Hybrid-Verbindung, und legen **Port** zu `1433`. 
  
    ![Hybridverbindung konfigurieren](./media/hybrid-connections-create-new/4.png)

    Der Hostname ist der Name des lokalen Servers. Konfigurieren Sie die Hybridverbindung für den Zugriff auf SQL Server unter dem Port 1433. Wenn Sie eine benannte SQL-Server-Instanz nutzen, verwenden Sie stattdessen den zuvor definierten statischen Port.

6. Nachdem die neue Verbindung erstellt wurde, wird der Status von der der neuen Verbindung wird **lokale Einrichtung unvollständig**.

7. Navigieren Sie zurück zu Ihrem mobilen Dienst, klicken Sie auf **konfigurieren**, scrollen Sie zum **hybridverbindungen** und klicken Sie auf **hybridverbindung hinzufügen**, wählen Sie die hybridverbindung, die Sie gerade erstellt haben, und klicken Sie auf **OK**.

    Jetzt kann Ihr mobiler Dienst die neue Hybridverbindung verwenden.

Nun müssen Sie den Hybrid Connection Manager auf dem lokalen Computer installieren.

