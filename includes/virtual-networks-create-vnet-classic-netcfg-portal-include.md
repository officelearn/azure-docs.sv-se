## Erstellen eines VNet mithilfe einer Netzwerkkonfigurationsdatei im Azure-Portal

Azure verwendet eine XML-Datei, um alle für ein Abonnement verfügbaren VNets zu definieren. Sie können diese Datei herunterladen und bearbeiten, um vorhandene VNets zu ändern oder zu löschen und neue zu erstellen. In diesem Dokument erfahren Sie, wie Sie die Datei, die auch als Netzwerkkonfigurationsdatei oder netcgf-Datei bezeichnet wird, herunterladen und bearbeiten können, um ein neues VNet zu erstellen. Überprüfen Sie die [Konfigurationsschema für virtuelle Azure-Netzwerk](https://msdn.microsoft.com/library/azure/jj157100.aspx) Weitere Informationen zu Netzwerk-Konfigurationsdatei.

Führen Sie zum Erstellen eines VNet unter Verwendung einer netcfg-Datei im Azure-Portal die folgenden Schritte aus.

1. In einem Browser zu http://manage.windowsazure.com navigieren, und melden Sie sich ggf. mit Ihrem Azure-Konto.
2. Blättern Sie in der Liste der Dienste, und klicken Sie auf **Netzwerke** wie unten dargestellt.

    ![Virtuelle Azure-Netzwerke](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure1.gif)

3. Klicken Sie auf den unteren Rand der Seite, auf die **EXPORTIEREN** Schaltfläche, wie unten dargestellt.

    ![Schaltfläche "Exportieren"](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure2.png)

4. Auf der **Export Netzwerkkonfiguration** Seite, wählen Sie das Abonnement, das Sie die Konfiguration des virtuellen Netzwerks aus exportieren möchten, und klicken Sie dann auf das Häkchen in der unteren linken Ecke der Seite.
5. Führen Sie die Browser-Anweisungen zum Speichern der **NetworkConfig.xml** Datei. Notieren Sie sich den Speicherort der Datei.
6. Öffnen Sie die Datei, die Sie in Schritt 5 mit einer beliebigen XML- oder Text-Editor-Anwendung gespeichert haben, und suchen Sie nach der **<VirtualNetworkSites>** Element. Wenn Sie alle Netzwerke, die bereits erstellt haben, wird jedes Netzwerk als eigene angezeigt **<VirtualNetworkSite>** Element.
7. Um das virtuelle Netzwerk in diesem Szenario beschriebenen zu erstellen, fügen Sie den folgenden XML-Code direkt unter dem **<VirtualNetworkSites>** Element:

        <VirtualNetworkSite name="TestVNet" Location="Central US">
          <AddressSpace>
            <AddressPrefix>192.168.0.0/16</AddressPrefix>
          </AddressSpace>
          <Subnets>
            <Subnet name="FrontEnd">
              <AddressPrefix>192.168.1.0/24</AddressPrefix>
            </Subnet>
            <Subnet name="BackEnd">
              <AddressPrefix>192.168.2.0/24</AddressPrefix>
            </Subnet>
          </Subnets>
        </VirtualNetworkSite>

8.  Speichern Sie die Netzwerkkonfigurationsdatei.
9.  Klicken Sie in der Azure-Portal auf der unteren linken Ecke der Seite auf **Neu**, klicken Sie dann auf **Netzwerkdienste**, klicken Sie dann auf **VIRTUAL NETWORK**, und klicken Sie dann auf **IMPORTKONFIGURATION** wie in der folgenden Abbildung dargestellt.

    ![Konfiguration importieren](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure3.gif)

10.  Auf die **die Netzwerk-Konfigurationsdatei importieren** auf **Datei suchen**, navigieren Sie zu dem Ordner, der Sie die Datei in Schritt 8 gespeichert, wählen Sie die Datei, und klicken Sie dann auf **Öffnen**. Das Webseite sollte ähnlich wie die folgende Abbildung aussehen. Klicken Sie in der unteren rechten Ecke der Seite auf den Pfeil, um mit dem nächsten Schritt fortzufahren.

    ![Import network configuration file page](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure4.png)

11.   Auf der **Netzwerk** Seite, beachten Sie den Eintrag für Ihr neues VNet, wie in der folgenden Abbildung dargestellt.

    ![Building your network page](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure5.png)

12.   Klicken Sie auf das Häkchen in der unteren rechten Ecke der Seite, um das VNet zu erstellen. Nach einigen Sekunden wird das VNet in der Liste der verfügbaren VNets angezeigt, wie in der folgenden Abbildung dargestellt.

    ![New virtual network](./media/virtual-networks-create-vnet-classic-portal-xml-include/vnet-create-portal-netcfg-figure6.png)

