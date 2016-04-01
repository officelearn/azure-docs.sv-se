In diesem Schritt erstellen Sie manuell den Verfügbarkeitsgruppenlistener im Failovercluster-Manager und in SQL Server Management Studio (SSMS).

1. Öffnen Sie den Failovercluster-Manager über den Knoten, der das primäre Replikat hostet.

1. Wählen Sie die **Netzwerke** Knoten, und notieren Sie sich den Clusternetzwerknamen. Dieser Name wird in der Variablen $ClusterNetworkName im PowerShell-Skript verwendet.

1. Erweitern Sie den Clusternamen, und klicken Sie dann auf **Rollen**.

1. In der **Rollen** rechten Maustaste auf den Namen und wählen Sie dann **Ressource hinzufügen** > **Clientzugriffspunkt**.

    ![Hinzufügen eines Clientzugriffspunkts für die Verfügbarkeitsgruppe](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

1. In den **Namen** Feld, einen Namen für den neuen Listener zu erstellen, und klicken Sie dann **Weiter** zweimal, und klicken Sie dann auf **Fertig stellen**. Schalten Sie den Listener oder die Ressource jetzt noch nicht online.

1. Klicken Sie auf die **Ressourcen** Registerkarte, und erweitern Sie den Clientzugriffspunkt, die Sie gerade erstellt haben. Sie sehen die **IP-Adresse** Ressourcen für jedes der Clusternetzwerke im Cluster. Wenn dies eine reine Azure-Lösung ist, wird nur eine IP-Adressressource angezeigt.

1. Wenn Sie eine Hybridlösung konfigurieren, fahren Sie mit diesem Schritt fort. Wenn Sie eine reine Azure-Lösung konfigurieren, fahren Sie mit dem nächsten Schritt fort. 
     - Mit der rechten Maustaste in der IP-Adressressource, die Ihrem lokalen Subnetz entspricht, und wählen Sie dann **Eigenschaften**. Notieren Sie den Namen der IP-Adresse und des Netzwerks.
     - Wählen Sie **statische IP-Adresse**, eine nicht verwendete IP-Adresse zuweisen, und klicken Sie dann auf **OK**.

1. Klicken Sie mit der rechten Maustaste auf die IP-Adressressource, die Ihrem Azure-Subnetz entspricht, und wählen Sie dann "Eigenschaften" aus.
    >[AZURE.NOTE] Fällt der Listener später aufgrund einer in Konflikt stehende IP-Adresse von DHCP ausgewählten online geschaltet werden kann, können Sie eine gültige statische IP-Adresse im Eigenschaftenfenster konfigurieren.

1. In der gleichen **IP-Adresse** im Eigenschaftenfenster ändern die **Namen IP-Adresse**. Die Namen dieser IP-Adresse wird in verwendet die **$IPResourceName** Variable des PowerShell-Skripts. Wiederholen Sie diesen Schritt für jede IP-Ressource, wenn die Lösung mehrere Azure-VNets umfasst.

