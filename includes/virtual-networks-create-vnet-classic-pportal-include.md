## Erstellen eines klassischen VNet im Azure-Vorschauportal.

Führen Sie zum Erstellen eines klassischen VNet basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. Ein Browser, navigieren Sie zu http://portal.azure.com und melden Sie sich ggf. mit Ihrem Azure-Konto.
2. Klicken Sie auf **Neu** > **Netzwerk** > **Virtual Network**, beachten Sie, dass die **Wählen Sie ein Bereitstellungsmodell** Liste bereits zeigt **klassische**, und klicken Sie dann auf **Erstellen**, wie in der folgenden Abbildung dargestellt.

    ![Erstellen von VNet im Vorschauportal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)

3. Auf der **Virtual Network** Blatt, geben die **Namen** von VNet, und klicken Sie dann auf **Adressraum**. Konfigurieren Sie Ihre Adresse Speicherplatz für das VNet und seine erste Subnetz, und klicken Sie dann **OK**. Die folgende Abbildung zeigt die Einstellungen des CIDR-Blocks für das aktuelle Szenario.

    ![Blatt "Adressraum"](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)

4. Klicken Sie auf **Ressourcengruppe** und wählen Sie eine Ressourcengruppe zum Hinzufügen des VNet für, oder klicken Sie auf **neue Ressourcengruppe erstellen** VNet eine neue Ressourcengruppe hinzu. Die folgende Abbildung zeigt die Ressource die Einstellungen für eine neue Ressourcengruppe namens **TestRG**. Weitere Informationen zu Ressourcengruppen finden Sie auf [Übersicht über Azure Resource Manager](resource-group-overview.md/#resource-groups).

    ![Blatt "Ressourcengruppe erstellen"](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)

5. Ändern Sie ggf. die **Abonnement** und **Speicherort** Einstellungen für Ihr VNet. 

6. Wenn Sie nicht, finden in der VNet als Kachel in möchten der **im Startmenü**, deaktivieren Sie **an Startmenü anheften**. 

7. Klicken Sie auf **Erstellen** und beachten Sie die Kachel namens **Erstellen virtuelle Netzwerk** wie in der folgenden Abbildung dargestellt.

    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)

8. Warten Sie, bis das VNet erstellt wurde. Wenn die Kachel unten angezeigt wird, klicken Sie darauf, um weitere Subnetze hinzuzufügen.

    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)

9. Daraufhin sollte die **Konfiguration** für das VNet, wie unten dargestellt. 

    ![Erstellen eines VNet im Portal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)

10. Klicken Sie auf **Subnetze** > **Hinzufügen**, geben Sie einen **Namen** und geben Sie eine **-Adressbereich (CIDR-Block)** für Ihr Subnetz, und klicken Sie dann auf **OK**. Die folgende Abbildung zeigt die Einstellungen für das aktuelle Szenario.

    ![Erstellen von VNet im Vorschauportal](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

