1. Wechseln Sie wieder zum Failovercluster-Manager.  Erweitern Sie **Rollen** und markieren Sie dann die Availability Group.  Auf der **Ressourcen** Registerkarte rechten Maustaste auf den Namen des Listeners aus, und klicken Sie auf Eigenschaften.

1. Klicken Sie auf die **Abhängigkeiten** Registerkarte. Wenn mehrere Ressourcen aufgeführt sind, überprüfen Sie, ob die IP-Adressen OR-Abhängigkeiten aufweisen (statt AND-Abhängigkeiten).  Klicken Sie auf **OK**.

1. Mit der rechten Maustaste des Listenernamens, und klicken Sie auf **Online schalten**.

1. Sobald der Listener online ist die **Ressourcen** Registerkarte auf die Availability Group und auf **Eigenschaften**.

    ![Konfigurieren der Verfügbarkeitsgruppenressource](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

1. Erstellen Sie eine Abhängigkeit für die Ressource des Listenernamens (nicht den Ressourcennamen der IP-Adresse). Klicken Sie auf **OK**.

    ![Hinzufügen von Abhängigkeiten zum Listenernamen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

1. Starten Sie **SQL Server Management Studio** und Verbinden mit dem primären Replikat.

1. Navigieren Sie zu **hohe Verfügbarkeit mit AlwaysOn** | **Verfügbarkeitsgruppen** | **<AvailabilityGroupName>** | **Verfügbarkeitsgruppenlistener**. 

3. Jetzt sollte der Listenername angezeigt werden, den Sie im Failovercluster-Manager erstellt haben. Mit der rechten Maustaste des Listenernamens, und klicken Sie auf **Eigenschaften**.

1. In der **Port** Feld Geben Sie die Portnummer für den verfügbarkeitsgruppenlistener mithilfe der zuvor verwendete $EndpointPort (in diesem Lernprogramm wurde 1433 der Standard), klicken Sie dann auf **OK**.


