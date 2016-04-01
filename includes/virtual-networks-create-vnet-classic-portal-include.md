## Erstellen eines VNet im Azure-Portal.

Führen Sie zum Erstellen eines VNet basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. In einem Browser zu http://manage.windowsazure.com navigieren, und melden Sie sich ggf. mit Ihrem Azure-Konto.
2. Klicken Sie auf **Neu** > **Netzwerkdienste** > **VIRTUAL NETWORK** > **Erstellen Sie benutzerdefinierte** wie in der folgenden Abbildung dargestellt.

    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure1.gif)

3. Auf der **Details des virtuellen Netzwerks** Geben Sie die **Namen** Wählen Sie das VNet seine **Speicherort**, und klicken Sie dann auf den Pfeil in der unteren rechten Ecke der Seite auf, wechseln zu Schritt 2. Die folgende Abbildung zeigt die Einstellungen für dieses Szenario.

    ![Seite "Details zu Virtual Network"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure2.png)

4. Auf der **DNS-Server und VPN-Konnektivität** geben den Namen und die IP-Adresse für bis zu 9 DNS-Server verwenden. Wenn Sie keinen DNS-Server angeben, verwendet Ihr VNet die interne Namensauflösung von Azure. In diesem Szenario werden keine DNS-Server konfiguriert.
5. Wenn Sie eine Punkt-zu-Standort-VPN-Zugriff in Ihrem VNet bereitstellen möchten, aktivieren Sie die **Punkt-zu-Standort-VPN konfigurieren** Kontrollkästchen. Wenn Sie kein Punkt-zu-Site-VPN konfigurieren, können Sie es auch nach der Erstellung jederzeit zu Ihrem VNet hinzufügen. In diesem Szenario wird kein Punkt-zu-Site-VPN konfiguriert.
6. Wenn Sie Standort-zu-Standort-VPN-Konnektivität zwischen Ihrem VNet und einem anderen VNet oder Ihrem lokalen Netzwerk bereitstellen möchten, aktivieren Sie die **Konfigurieren eines Standort-zu-Standort-VPN** Kontrollkästchen angeben, wenn Sie verwenden möchten, und **ExpressRoute** oder Hinweis und der Name des Netzwerks für die Verbindung. Wenn Sie kein Standort-zu-Standort-VPN konfigurieren, können Sie es auch nach der Erstellung jederzeit zu Ihrem VNet hinzufügen. In diesem Szenario wird kein Standort-zu-Site-VPN konfiguriert.
7. Klicken Sie auf den Pfeil unten rechts auf der Seite, um mit Schritt 3 fortzufahren. Auf der folgenden Abbildung sind die Einstellungen für dieses Szenario dargestellt.

    ![Seite "DNS-Server und VPN-Konnektivität"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure3.png)

8. Auf der **Virtual Network Address Spaces** Seite **Start-IP-**, klicken Sie auf *10.0.0.0* ändern die VNet-Adressraum, und geben Sie dann die Start-Adressraum verwenden möchten. Geben Sie für unser Szenario *192.168.0.0*. 
9. Unter **CIDR (Anzahl Adressen)** Wählen Sie die Anzahl der Bits für die Subnetzmaske. Wählen Sie für unser Szenario *16 (65536)*.
10. Unter **SUBNETZE**, klicken Sie auf *Subnet-1* und benennen Sie das Subnetz bei Bedarf. In diesem Szenario, benennen Sie sie in *Front-End-*.

    >[AZURE.NOTE] Wenn Sie außerhalb der im Textfeld "Name" für ein Subnetz auf Sie werden nicht möglich, den Namen zu bearbeiten, wenn das Subnetz erneut. Um dieses Problem zu beheben, müssen Sie das Subnetz durch Klicken auf die Schaltfläche "X" rechts davon entfernen und dann ein neues Subnetz hinzufügen, wie in Schritt 13 unten beschrieben.

11. Unter **Start-IP-** für das erste Subnetz, geben Sie die IP-Startadresse für das Subnetz. Geben Sie für unser Szenario *192.168.1.0*.
12. Unter **CIDR (Anzahl Adressen)** Wählen Sie die Anzahl der Bits für die Subnetzmaske für das erste Subnetz. Wählen Sie für unser Szenario *24 (256)*.
13. Klicken Sie auf **Subnetz hinzufügen** ein neues Subnetz, bei Bedarf hinzu. Fügen Sie in diesem Szenario ein Subnetz hinzu, und wiederholen Sie die Schritte 10 bis 12, um das VNet wie in der folgenden Abbildung dargestellt zu konfigurieren.

    ![Seite "Adressräume von Virtual Network"](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure4.png)

14. Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite, um das VNet zu erstellen. Nach einigen Sekunden wird das VNet in der Liste der verfügbaren VNets angezeigt, wie in der folgenden Abbildung dargestellt.

    ![Neues virtuelles Netzwerk](./media/virtual-networks-create-vnet-classic-portal-include/vnet-create-portal-figure5.png)

