## Erstellen eines VNet im Azure-Vorschauportal.

Gehen Sie folgendermaßen vor, um mit dem Azure-Vorschauportal ein VNet basierend auf dem oben beschriebenen Szenario zu erstellen.

1. Ein Browser, navigieren Sie zu http://portal.azure.com und melden Sie sich ggf. mit Ihrem Azure-Konto.
2. Klicken Sie auf **Neu** > **Netzwerk** > **Virtual Network**, klicken Sie dann auf **-Ressourcen-Manager** aus der **Wählen Sie ein Bereitstellungsmodell** aus, und klicken Sie dann auf **Erstellen**, wie in der folgenden Abbildung dargestellt.

    ![Erstellen von VNet im Vorschauportal](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Auf der **virtuelles Netzwerk erstellen** Blade, konfigurieren Sie die VNet-Einstellungen, wie in der folgenden Abbildung dargestellt.

    ![Blatt "Erstellen eines virtuellen Netzwerks"](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Klicken Sie auf **Ressourcengruppe** und wählen Sie eine Ressourcengruppe zum Hinzufügen des VNet für, oder klicken Sie auf **neu erstellen** VNet eine neue Ressourcengruppe hinzu. Die folgende Abbildung zeigt die Ressource die Einstellungen für eine neue Ressourcengruppe namens **TestRG**. Weitere Informationen zu Ressourcengruppen finden Sie auf [Übersicht über Azure Resource Manager](resource-group-overview.md/#resource-groups).

    ![Ressourcengruppe](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Ändern Sie ggf. die **Abonnement** und **Speicherort** Einstellungen für Ihr VNet. 

6. Wenn Sie nicht, finden in der VNet als Kachel in möchten der **im Startmenü**, deaktivieren Sie **an Startmenü anheften**. 

7. Klicken Sie auf **Erstellen** und beachten Sie die Kachel namens **Erstellen virtuelle Netzwerk** wie in der folgenden Abbildung dargestellt.

    ![Kachel "Erstellen eines virtuellen Netzwerks"](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Warten, für das VNet erstellt werden soll, und dann in der **Virtual Network** Blatt, klicken Sie auf **Alle Einstellungen** > **Subnetze** > **Hinzufügen** wie unten dargestellt.

    ![Hinzufügen eines Subnetzes im Vorschauportal](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Geben Sie die subnetzeinstellungen für die *Back-End-* Subnetz, wie unten dargestellt, und klicken Sie dann auf **OK**. 

    ![Subnetzeinstellungen](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Eine Liste der Subnetze wird angezeigt, wie in der folgenden Abbildung dargestellt.

    ![Liste der Subnetze im VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)

