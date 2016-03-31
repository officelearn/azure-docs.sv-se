## Grundlagen zu Virtual Networks

### Was ist ein Azure Virtual Network (VNet)?

Mit VNets können Sie virtuelle private Netzwerke (VPNs) in Azure bereitstellen und verwalten und die VNets optional mit anderen VNets in Azure oder mit Ihrer lokalen IT-Infrastruktur verbinden, um hybride oder standortübergreifende Lösungen zu erstellen. Jedes erstellte VNet verfügt über einen eigenen CIDR-Block und kann mit anderen VNets und lokalen Netzwerken verbunden werden, solange die CIDR-Blöcke nicht im Konflikt miteinander stehen. Sie können zudem Steuerelemente der DNS-Servereinstellungen für VNets festlegen und das VNet in Subnetze segmentieren. 

Verwenden Sie VNets für Folgendes:

- Erstellen eines dedizierten privaten virtuellen Nur-Cloud-Netzwerks
                                    
    Manchmal benötigen Sie keine standortübergreifende Konfiguration für Ihre Lösung. Wenn Sie ein VNet erstellen, können die Dienste und virtuellen Computer des VNet direkt und sicher in der Cloud miteinander kommunizieren. Auf diese Weise verbleibt der Datenverkehr sicher im VNet, Sie können jedoch trotzdem in Ihrer Lösung Endpunktverbindungen für die virtuellen Computer und Dienste konfigurieren, die eine Internetkommunikation erfordern.

- Sicheres Erweitern des Rechenzentrums
                                    
    Mit VNets können Sie herkömmliche Standort-zu-Standort (S2S)-VPNs erstellen, um die Kapazität des Rechenzentrums sicher zu skalieren. S2S-VPNs verwenden IPsec, um eine sichere Verbindung zwischen dem unternehmenseigenen VPN-Gateway und Azure bereitzustellen.

- Unterstützung von Hybrid Cloud-Szenarios
                                    
    VNets bieten flexible Möglichkeiten, verschiedene Hybrid Cloud-Szenarios zu unterstützen. Sie können cloudbasierte Anwendungen auf sichere Weise mit beliebigen lokalen Systemen wie z. B. Mainframes oder Unix-Systemen verbinden.

### Wie finde ich heraus, ob ich ein virtuelles Netzwerk benötige?

Besuchen Sie die [Überblick über virtuelle Netzwerke](virtual-networks-overview.md) finden Sie eine Entscheidungstabelle, mit denen Sie die besten des für Sie Netzwerkentwurfs.

### Wie fange ich an?

Besuchen Sie [Dokumentation zu virtuellen Netzwerken](http://azure.microsoft.com/documentation/services/virtual-network/) für den Einstieg. Auf dieser Seite befinden sich Links zu allgemeinen Konfigurationsschritten sowie Informationen zu den verschiedenen Aspekten, die Sie berücksichtigen müssen, wenn Sie Ihr virtuelles Netzwerk entwerfen.

### Welche Dienste können in VNets genutzt werden?

VNets können mit einer Vielzahl unterschiedlicher Azure-Dienste, z. B. Cloud Services (PaaS), Virtual Machines und Web-Apps, verwendet werden. Es gibt jedoch auch einige Dienste, die in einem VNet nicht unterstützt werden. Überprüfen Sie den spezifischen Dienst, den Sie verwenden möchten, und vergewissern Sie sich, ob er kompatibel ist.

### Können VNets ohne standortübergreifende Konnektivität verwendet werden?

Ja. Ein VNet kann auch ohne Standort-zu-Standort-Konnektivität genutzt werden. Dies ist insbesondere von Nutzen, wenn Sie Domänencontroller und SharePoint-Farmen in Azure ausführen möchten.

## Konfiguration von virtuellen Netzwerken

### Welche Tools werden zum Erstellen eines VNet verwendet?

Sie können die folgenden Tools zum Erstellen oder Konfigurieren eines virtuellen Netzwerks verwenden:

- Sie können das Verwaltungsportal verwenden. Finden Sie unter [Gewusst wie: Verwalten der Eigenschaften eines virtuellen Netzwerks (VNet)](virtual-networks-settings.md).

- Sie können eine Netzwerkkonfigurationsdatei (NETCFG-Datei) verwenden. Finden Sie unter [Konfigurieren eines virtuellen Netzwerks mithilfe einer Netzwerk-Konfigurationsdatei](virtual-networks-using-network-configuration-file.md).

### Welche Adressbereiche kann ich in meinen VNets verwenden?

Können öffentliche IP-Adressbereiche und alle IP-Adressbereich gemäß [RFC 1918](http://tools.ietf.org/html/rfc1918).

### Können öffentliche IP-Adressen in VNets verwendet werden?

Ja. Weitere Informationen zu öffentlichen IP-Adressbereichen, finden Sie unter [öffentlicher IP-Adressraum in einem virtuellen Netzwerk (VNet)](virtual-networks-public-ip-within-vnet.md). Beachten Sie, dass auf Ihre öffentlichen IPs nicht direkt über das Internet zugegriffen werden kann.

### Ist die Anzahl der Subnetze im virtuellen Netzwerk begrenzt?

Sie können eine unbegrenzte Anzahl von Subnetzen in einem VNet verwenden. Alle Subnetze müssen vollständig im Adressraum des virtuellen Netzwerks enthalten sein und sollten sich nicht überschneiden.

### Unterliegen die in den Subnetzen verwendeten IP-Adressen bestimmten Beschränkungen?

Einige IP-Adressen innerhalb jedes Subnetzes sind in Azure reserviert. Die erste und letzte IP-Adresse der Subnetze sind aus Gründen der Protokollkonformität reserviert. Darüber hinaus sind drei weitere für Azure-Dienste verwendete IP-Adressen reserviert.

### Wie ist die minimale und maximale Größe von VNets und Subnetzen?

Das kleinste unterstützte Subnetz weist die Netzmaske /29 und das größte die Netzmaske /8 (gemäß CIDR-Subnetzdefinitionen) auf. 

### Können VLANs mithilfe von VNets in Azure integriert werden?

Nr. VNets sind Layer-3-Overlays. Layer-2-Semantik wird in Azure nicht unterstützt.

### Können benutzerdefinierte Routingrichtlinien für VNets und Subnetze angegeben werden?

Ja. Sie können benutzerdefinierte Routen (UDR) verwenden. Weitere Informationen zu UDR finden Sie auf [benutzerdefinierte Routen und IP-Weiterleitung](virtual-networks-udr-overview.md).

### Unterstützen VNets Multicasting oder Broadcasting?

Nr. Wir unterstützen keine Multicast- oder Broadcastanforderungen.

### Welche Protokolle werden innerhalb von VNets unterstützt?

Sie können innerhalb von VNets IP-basierte Standardprotokolle verwenden. Verkapselte Multicast-, Broadcast- und IP-in-IP-Pakete sowie GRE (Generic Routing Encapsulation)-Pakete werden jedoch blockiert. Unterstützte Standardprotokolle sind:

- TCP
- UDP
- ICMP

### Kann ich meine Standardrouter innerhalb eines VNet mit "ping" abfragen?

Nein.

### Können Verbindungsdiagnosen mit "tracert" durchgeführt werden?

Nein.

### Können Subnetze hinzugefügt werden, nachdem das VNet erstellt wurde?

Ja. Subnetze können VNets jederzeit hinzugefügt werden, solange die Subnetzadresse nicht Teil eines anderen Subnetzes im VNet ist.

### Kann die Größe des VNet nach dessen Erstellung geändert werden?

Sie können mit PowerShell-Cmdlets oder der NETCFG-Datei ein Subnetz hinzufügen, entfernen, erweitern oder verkleinern, wenn in diesem Subnetz keine virtuellen Computer oder Dienste bereitgestellt werden. Sie können auch beliebige Präfixe hinzufügen, entfernen, erweitern oder verkleinern, solange die Subnetze, die virtuelle Computer oder Dienste enthalten, nicht von der Änderung betroffen sind.

### Können Subnetze nach dem Erstellen geändert werden?

Ja. Sie können die in einem VNet verwendeten CIDR-Blöcke hinzufügen, entfernen und ändern.

### Kann eine Verbindung mit dem Internet hergestellt werden, wenn Dienste in einem VNet ausgeführt werden?

Ja. Alle Dienste, die innerhalb eines VNet bereitgestellt werden, können eine Verbindung mit dem Internet herstellen. Jeder in Azure bereitgestellte Clouddienst verfügt über eine öffentlich adressierbare, zugewiesene VIP-Adresse. Sie müssen Eingabeendpunkte für PaaS-Rollen und Endpunkte für virtuelle Computer definieren, damit diese Dienste Verbindungen über das Internet annehmen können.

### Unterstützen VNets IPv6?

Nr. Sie können keine IPv6 mit VNets zu diesem Zeitpunkt.

### Kann sich ein VNet über mehrere Regionen erstrecken?

Nr. Eine VNet ist in einer einzelnen Region beschränkt.

### Kann ein VNet mit einem anderen VNet in Azure verbunden werden?

Ja. Sie können VNet-zu-VNet-Kommunikation mithilfe der REST-APIs oder Windows PowerShell erstellen. Finden Sie unter [konfigurieren eine VNet-zu-VNet-Verbindung](virtual-networks-configure-vnet-to-vnet-connection.md).

## Namensauflösung (DNS)

### Welche DNS-Optionen sind für VNets verfügbar?

Entscheiden Sie anhand der Tabelle auf die [namensauflösung für virtuelle Computer und Rolleninstanzen](virtual-networks-name-resolution-for-vms-and-role-instances.md) Seite führt Sie durch alle DNS-Optionen.

### Können DNS-Server für ein VNet angegeben werden?

Ja. Sie haben die Möglichkeit, IP-Adressen von DNS-Servern in den Einstellungen des VNet anzugeben. Diese gelten als DNS-Standardserver für alle virtuellen Computer im VNet.

### Wie viele DNS-Server können angegeben werden?

Sie können bis zu 12 DNS-Server angeben.

### Können DNS-Server geändert werden, nachdem das Netzwerk erstellt wurde?

Ja. Sie können die Liste der DNS-Server für das VNet jederzeit ändern. Wenn Sie die Liste der DNS-Server ändern, müssen Sie jeden virtuellen Computer im VNet neu starten, damit der neue DNS-Server übernommen wird.


### Was ist der von Azure bereitgestellte DNS-Dienst, und wie wird er bei VNets verwendet?

Der von Azure bereitgestellte DNS-Dienst ist ein von Microsoft angebotener mehrinstanzenfähiger DNS-Dienst. In Azure werden alle Ihre virtuellen Computer und Rolleninstanzen in diesem Dienst registriert. Dieser Dienst stellt die Namensauflösung nach dem Hostnamen für virtuelle Computer und Rolleninstanzen, die im gleichen Clouddienst enthalten sind, und nach dem FQDN für virtuelle Computer und Rolleninstanzen im gleichen VNet zur Verfügung. 

> [AZURE.NOTE] Es ist eine Einschränkung zu diesem Zeitpunkt auf die ersten 100 Cloud-Dienste im virtuellen Netzwerk für die Auflösung zwischen Mandanten Namen, die mithilfe von Azure bereitgestellten DNS. Diese Einschränkung gilt nicht, wenn Sie einen eigenen DNS-Server verwenden.

### Können DNS-Einstellungen für einzelne virtuelle Computer und Dienste überschrieben werden?

Ja. Sie können DNS-Server pro Clouddienst festlegen, um die standardmäßigen Netzwerkeinstellungen zu überschreiben. Es wird jedoch empfohlen, nach Möglichkeit den netzwerkweiten DNS-Server zu verwenden.

### Können benutzerdefinierte DNS-Suffixe angegeben werden?

Nr. Sie können keine benutzerdefiniertes DNS-Suffix für die VNets angeben.

## VNets und virtuelle Computer

### Können virtuelle Computer in einem VNet bereitgestellt werden?

Ja.

### Können virtuelle Linux-Computer in einem VNet bereitgestellt werden?

Ja. Sie können alle von Azure unterstützten Linux-Distributionen bereitstellen.

### Worin besteht der Unterschied zwischen einer öffentlichen VIP-Adresse und einer internen IP-Adresse?

- Eine interne IP-Adresse ist eine IP-Adresse, die den einzelnen virtuellen Computern eines VNet über DHCP zugewiesen wird. Sie ist nicht öffentlich. Wenn Sie ein VNet erstellt haben, wird die interne IP-Adresse aus dem Bereich zugewiesen, den Sie in den Subnetzeinstellungen des VNet angegeben haben. Auch wenn Sie nicht über ein VNet verfügen, wird dennoch eine interne IP-Adresse zugewiesen. Die interne IP-Adresse bleibt dem virtuellen Computer während seiner gesamten Lebensdauer zugewiesen, sofern seine Zuordnung nicht aufgehoben wird.

- Eine öffentliche VIP-Adresse ist die öffentliche IP-Adresse, die dem Clouddienst oder Load Balancer zugewiesen wird. Sie wird der NIC des virtuellen Computers nicht direkt zugewiesen. Die VIP-Adresse wird für den Clouddienst, dem sie zugewiesen ist, beibehalten, bis die Zuordnung aller virtuellen Computer in diesem Clouddienst aufgehoben wird oder alle virtuellen Computer gelöscht werden. Zu diesem Zeitpunkt wird sie freigegeben.

### Welche IP-Adresse wird meinem virtuellen Computer zugewiesen?

- **Interne IP-Adresse -** Wenn Sie einen virtuellen Computer mit einem VNet bereitstellen, erhält der virtuelle Computer eine interne IP-Adresse aus einem Pool interner IP-Adressen, die Sie angeben. Virtuelle Computer kommunizieren im VNet mithilfe interner IP-Adressen. In Azure wird eine dynamische interne IP-Adresse zugewiesen, Sie können jedoch eine statische Adresse für Ihren virtuellen Computer anfordern. Weitere Informationen zu statischen internen IP-Adressen finden Sie unter [wie einer statischen internen IP-Adresse](virtual-networks-reserved-private-ip.md).

- **VIP -** Ihren virtuellen Computer ist auch eine VIP-Adresse zugeordnet, obwohl eine VIP-Adresse niemals direkt mit dem virtuellen Computer zugewiesen ist. Eine VIP-Adresse ist eine öffentliche IP-Adresse, die dem Clouddienst zugewiesen werden kann. Sie können optional eine VIP-Adresse für Ihren Clouddienst reservieren. Finden Sie unter [Reservierte öffentliche IP-Adresse](virtual-networks-reserved-public-ip.md).

- **ILPIP:** Sie können auch eine öffentliche IP-Adresse (ILPIP) von auf Instanzebene konfigurieren. ILPIPs sind dem virtuellen Computer direkt zugeordnet und nicht dem Clouddienst. Weitere Informationen zu ILPIPs finden Sie unter [auf Instanzebene öffentliche IP-Übersicht](virtual-networks-instance-level-public-ip.md).

### Kann ich eine interne IP-Adresse für einen virtuellen Computer reservieren, den ich zu einem späteren Zeitpunkt erstelle?

Nr. Sie können nicht auf eine interne IP-Adresse reservieren. Wenn eine interne IP-Adresse verfügbar ist, wird sie einem virtuellen Computer oder einer Rolleninstanz durch den DHCP-Server zugewiesen. Dieser virtuelle Computer ist möglicherweise der Computer, dem Sie die interne IP-Adresse zuweisen möchten, möglicherweise aber auch nicht. Sie können jedoch die interne IP-Adresse eines bereits erstellten virtuellen Computers in eine verfügbare interne IP-Adresse ändern. 

### Ändern sich interne IP-Adressen für virtuelle Computer in einem VNet?

Ja. Interne IP-Adressen bleiben dem virtuellen Computer während seiner gesamten Lebensdauer zugewiesen, sofern diese Zuordnung nicht aufgehoben wird. Nach dem Aufheben der Zuordnung eines virtuellen Computers wird die interne IP-Adresse freigegeben, sofern Sie keine statische interne IP-Adresse definiert haben. Wenn der virtuelle Computer einfach beendet wird (und nicht in den Status versetzt **angehalten (freigegeben)**) bleibt die IP-Adresse des virtuellen Computers zugewiesen.

### Können IP-Adressen manuell NICs in virtuellen Computern zugewiesen werden?

Nr. Sie müssen nicht Schnittstelleneigenschaften von virtuellen Computern ändern. Änderungen können dazu führen, dass die Verbindung mit dem virtuellen Computer unterbrochen wird.

### Was geschieht mit den IP-Adressen, wenn ein virtueller Computer heruntergefahren wird?

Nichts. Die IP-Adressen (sowohl die öffentliche VIP-Adresse als auch die interne IP-Adresse) bleiben dem Clouddienst oder virtuellen Computer weiterhin zugewiesen. 

> [AZURE.NOTE] Wenn Sie den virtuellen Computer einfach herunterfahren möchten, verwenden Sie nicht das Verwaltungsportal dazu. Derzeit wird über die Schaltfläche zum Herunterfahren die Zuordnung des virtuellen Computers aufgehoben.

### Können virtuelle Computer ohne erneute Bereitstellung zwischen Subnetzen in einem VNet verschoben werden?

Ja. Weitere Informationen finden Sie [hier](virtual-networks-move-vm-role-to-subnet.md).

### Kann eine statische MAC-Adresse für einen virtuellen Computer konfiguriert werden?

Nr. Eine MAC-Adresse kann nicht statisch konfiguriert werden.

### Bleibt die MAC-Adresse eines virtuellen Computers nach ihrer Erstellung unverändert?

Nein, aber sie wird nur verändert, wenn der virtuelle Computer in den Status „Beendet (Zuordnung aufgehoben)“ versetzt wird. Wenn Sie die Größe des virtuellen Computers ändern, neu starten oder im Falle von Dienstkorrekturen oder einer geplanten Wartung des Hostservers, wird die MAC-Adresse beibehalten.

### Kann ich von einem virtuellen Computer in einem VNet eine Verbindung mit dem Internet herstellen?

Ja. Alle Dienste, die innerhalb eines VNet bereitgestellt werden, können eine Verbindung mit dem Internet herstellen. Zusätzlich verfügt jeder in Azure bereitgestellte Clouddienst über eine öffentlich adressierbare zugewiesene VIP-Adresse. Sie müssen Eingabeendpunkte für PaaS-Rollen und Endpunkte für virtuelle Computer definieren, damit diese Dienste Verbindungen über das Internet annehmen können.

## VNets und Dienste

### Welche Dienste können in VNets genutzt werden?

Sie können innerhalb von VNets ausschließlich Compute Services verwenden. Compute Services sind auf Clouddienste (Web- und Workerrollen) und virtuelle Computer beschränkt.

### Können Web-Apps mit dem virtuellen Netzwerk verwendet werden?

Ja. Sie können Web-Apps in einem VNet mit ASE (App Service Environment, App Service-Umgebung) bereitstellen. Web-Apps können außerdem eine sichere Verbindung herstellen und auf Ressourcen im Azure-VNet zugreifen, wenn Sie für Ihr VNet „Punkt-zu-Site“ konfiguriert haben. Weitere Informationen finden Sie unter den folgenden Links:


- [Erstellen von Web-Apps in einer App Service-Umgebung](app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Web Apps Virtual Network Integration (in englischer Sprache)](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Using VNet Integration and Hybrid Connections with Web Apps (in englischer Sprache)](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrieren einer Web-App in einem Azure Virtual Network](web-sites-integrate-with-vnet.md)

### Können Clouddienste mit Web- und Workerrollen (PaaS) in einem VNet bereitgestellt werden?

Ja. Sie können PaaS-Dienste innerhalb von VNets bereitstellen.

### Wie werden PaaS-Rollen für ein VNet bereitgestellt?

Dazu geben Sie den Namen des VNet und die Rollen-/Subnetzzuordnungen im Netzwerkkonfigurationsabschnitt der Dienstkonfiguration an. Sie müssen keine Binärdateien aktualisieren.

### Können Dienste in und aus VNets verschoben werden?

Nr. Sie können keine Dienste in und aus VNets verschieben. Sie müssen den entsprechenden Dienst löschen und erneut bereitstellen, um ihn in ein anderes VNet zu verschieben.

## VNets und Sicherheit

### Welches Sicherheitsmodell wird für VNets verwendet?

VNets werden von anderen VNets und anderen in der Azure-Infrastruktur gehosteten Diensten vollständig isoliert ausgeführt. Die Vertrauensstellungsgrenze entspricht der Begrenzung des VNet.

### Können ACLs oder NSGs für VNets definiert werden?

Nr. Sie können keine ACLs oder NSGs VNets zuordnen. ACLs können jedoch für Eingabeendpunkte virtueller Computer definiert werden, die in einem VNet bereitgestellt wurden. NSGs können Subnetzen oder NICs zugeordnet werden. 

### Gibt es ein Whitepaper zum Thema VNet-Sicherheit?

Ja. Sie können es herunterladen [hier](http://go.microsoft.com/fwlink/?LinkId=386611).

## APIs, Schemas und Tools

### Können VNets programmgesteuert verwaltet werden?

Ja. VNets und die standortübergreifende Konnektivität können mithilfe von REST-APIs verwaltet werden. Weitere Informationen finden Sie [hier](http://go.microsoft.com/fwlink/?LinkId=296833).

### Sind Tools zur Unterstützung von VNets verfügbar?

Ja. Sie können PowerShell und Befehlszeilentools für zahlreiche Plattformen verwenden. Weitere Informationen finden Sie [hier](http://go.microsoft.com/fwlink/?LinkId=317721).


