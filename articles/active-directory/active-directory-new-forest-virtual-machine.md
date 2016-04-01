<properties 
    pageTitle="Installieren einer Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk | Microsoft Azure" 
    description="Ein Lernprogramm, in dem erläutert wird, wie Sie eine neue Active Directory-Gesamtstruktur auf einer virtuellen Maschine (VM) in einem virtuellen Azure-Netzwerk erstellen" 
    services="active-directory, virtual-network" 
    documentationCenter="" 
    authors="markusvi" 
    manager="stevenpo" 
    tags=""/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/20/2015" 
    ms.author="markusvi"/>


# Installieren einer neuen Active Directory-Gesamtstruktur auf einem virtuellen Azure-Netzwerk

In diesem Thema veranschaulicht, wie zum Erstellen einer neuen Windows Server Active Directory-Umgebung auf virtuellen Azure-Netzwerken auf einem virtuellen Computer (VM) auf ein [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md). In diesem Fall ist das virtuelle Azure-Netzwerk nicht mit einem lokalen Netzwerk verbunden. 

Folgende Themen könnten für Sie ebenfalls von Interesse sein:

- Ein Video, das diese Schritte veranschaulicht, finden Sie unter [installieren eine neue Active Directory-Gesamtstruktur auf Windows Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Optional können Sie [Konfigurieren eines Standort-zu-Standort-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) und eine neue Gesamtstruktur installieren oder eine lokale Gesamtstruktur in Windows Azure erweitern. Diese Schritte finden Sie unter [Installieren Sie ein Replikat Active Directory-Domänencontroller in Azure Virtual Network](../virtual-networks-install-replica-active-directory-domain-controller.md).
-  Konzeptionelle Anleitungen zum Installieren von Active Directory-Domänendienste (AD DS) auf virtuellen Azure-Netzwerken finden Sie unter [Richtlinien für die Bereitstellung von Windows Server Active Directory auf Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## Szenario (Diagramm)

In diesem Szenario müssen externe Benutzer auf Anwendungen zugreifen, die auf in die Domäne eingebundenen Servern ausgeführt werden. Die virtuellen Computer, auf denen die Anwendungsserver ausgeführt werden, und die virtuellen Computer, auf denen Domänencontroller ausgeführt werden, werden in einem virtuellen Azure-Netzwerk in einem eigenen Clouddienst ausgeführt. Sie sind auch zur Verbesserung der Fehlertoleranz in einer Verfügbarkeitsgruppe enthalten.

![][1]
7
## Unterschiede zwischen der virtuellen und der lokalen Umgebung

Die Installation eines Domänencontrollers in Azure unterscheidet sich nicht wesentlich von der lokalen Installation. Die Hauptunterschiede sind in der folgenden Tabelle aufgeführt. 

Konfigurieren  | Lokal  | Virtuelles Azure-Netzwerk 
------------- | -------------  | ------------
**IP-Adresse des Domänencontrollers**  | Statische IP-Adresse in den Netzwerkadaptereigenschaften zuweisen   | Ausführen des Cmdlets "Set-AzureStaticVNetIP" zum Zuweisen einer statischen IP-Adresse
**DNS-Client-Resolver**  | Bevorzugte und alternative Adresse des DNS-Servers in den Netzwerkadaptereigenschaften von Domänenmitgliedern eingeben   | Adresse des DNS-Servers in den Eigenschaften des virtuellen Netzwerks eingeben
**Speicherort der Active Directory-Datenbank**  | Standardspeicherort C:\ kann optional geändert werden  | Standardspeicherort C:\ muss geändert werden



## Erstellen eines virtuellen Azure-Netzwerks

1. Melden Sie sich am klassischen Azure-Portal an.
2. Erstellen Sie ein virtuelles Netzwerk. Klicken Sie auf **Netzwerke** > **Erstellen eines virtuellen Netzwerks**. Geben Sie bei Ausführen des Assistenten die Werte aus der folgenden Tabelle ein. 

    Seite des Assistenten…  | Einzugebende Werte
    ------------- | -------------
    **Details zum virtuellen Netzwerk**  | <p>Name: Geben Sie einen Namen für Ihr virtuelles Netzwerk</p><p>Region: Wählen Sie die nächstgelegene Region aus.</p>
    **DNS und VPN**  | <p>Lassen Sie das Feld für den DNS-Server leer.</p><p>Wählen Sie keine VPN-Option aus.</p>
    **Adressräume des virtuellen Netzwerks**  | <p>Subnet-Name: Geben Sie einen Namen für Ihr Subnetz</p><p>Start-IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## Erstellen von virtuellen Computern (VMs) für die Serverrollen Domänencontroller und DNS
 
Wiederholen Sie die folgenden Schritte, um virtuelle Computer zum Hosten der DC-Rolle nach Bedarf zu erstellen. Sie sollten mindestens zwei virtuelle DC bereitstellen, um Fehlertoleranz und Redundanz zu gewährleisten. Wenn das virtuelle Azure-Netzwerk über mindestens zwei Domänencontroller verfügt, die auf ähnliche Weise konfiguriert sind (d. h. beide sind GCs, führen DNS-Server aus und beide führen keine FSMO-Rolle aus usw.), stellen Sie die virtuellen Computer, auf denen diese DCs ausgeführt werden, in eine Verfügbarkeitsgruppe, um eine verbesserte Fehlertoleranz zu erreichen.

Um die virtuellen Computer mithilfe von Windows PowerShell anstelle der Benutzeroberfläche zu erstellen, finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und vorabkonfigurieren Windows-basierten virtuellen Maschinen](../virtual-machines-ps-create-preconfigure-windows-vms.md).

1. Klicken Sie im klassischen Portal auf **Neu** > **Compute** > **Virtual Machine** > **From Gallery**. Verwenden Sie die folgenden Werte, um den Assistenten abzuschließen. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.

    Seite des Assistenten…  | Einzugebende Werte
    ------------- | -------------
    **Image auswählen**  | Windows Server 2012 R2 Datacenter
    **Konfiguration des virtuellen Computers**  | <p>Der Name des virtuellen Computers: Geben Sie ein einteiliger Name (z. B. AzureDC1).</p><p>Neuer Benutzername: Geben Sie den Namen eines Benutzers. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</p><p>Neues Kennwort/bestätigen: Geben Sie ein Kennwort</p>
    **Konfiguration des virtuellen Computers**  | <p>Cloud-Dienst: Wählen Sie <b>erstellen einen neuen Clouddienst</b> für den ersten virtuellen Computer und wählen, gleichen Cloud-Dienstnamen, wenn Sie weitere virtuelle Computer erstellen, die DC-Rolle hostet.</p><p>Cloud Service DNS Name: Geben Sie einen global eindeutigen Namen</p><p>Region/Affinity Group/Virtual Network: Geben Sie den Namen des virtuellen Netzwerks (z. B. WestUSVNet).</p><p>Speicherkonto: Wählen Sie <b>ein automatisch generiertes Speicherkonto verwenden</b> für den ersten virtuellen Computer und wählen Sie dann diese denselben speicherkontonamen, wenn Sie weitere virtuelle Computer erstellen, die DC-Rolle hostet.</p><p>Verfügbarkeitsgruppe: Wählen Sie <b>verfügbarkeitsgruppe erstellen</b>.</p><p>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeit festgelegt, wenn Sie den ersten virtuellen Computer erstellen, und Sie dann wählen, dass die gleichen Namen, wenn Sie weitere virtuelle Computer erstellen.</p>
    **Konfiguration des virtuellen Computers**  | <p>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen.</p>
2. Fügen Sie an jeden virtuellen Computer, der die DC-Serverrolle ausführt, einen Datenträger an. Der zusätzliche Speicherplatz ist erforderlich, um die AD-Datenbank, Protokolle und SYSVOL zu speichern. Geben Sie eine Größe für den Datenträger (z. B. 10 GB), und lassen Sie die **Hostcacheeinstellungen** festgelegt **keine**. Die Schritte finden Sie unter [Gewusst wie: Anfügen eines Datenträgers auf einem virtuellen Computer](../storage-windows-attach-disk.md).
3. Nachdem Sie sich zuerst mit dem virtuellen Computer anmelden, öffnen Sie **Server-Manager** > **Datei- und Speicherdienste** zum Erstellen eines Volumes auf diesem Datenträger mit NTFS.
4. Reservieren Sie eine statische IP-Adresse für virtuelle Computer, auf denen die DC-Rolle ausgeführt wird. Um eine statische IP-Adresse reservieren möchten, laden Sie Microsoft Web Platform Installer und [Installieren Sie Azure PowerShell](../powershell-install-configure.md) und führen Sie das Set-AzureStaticVNetIP-Cmdlet. Beispiel:

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

Weitere Informationen zum Festlegen einer statischen IP-Adresse finden Sie unter [Konfiguration einer statischen internen IP-Adresse eines virtuellen Computers](../virtual-network/virtual-networks-reserved-private-ip.md).

## Installieren von Windows Server Active Directory

Verwenden Sie dieselbe Routine zum [Installieren Sie AD DS](https://technet.microsoft.com/library/jj574166.aspx) die Verwendung von lokalen (d. h., Sie können die Benutzeroberfläche, eine Antwortdatei oder Windows PowerShell). Für die Installation einer neuen Gesamtstruktur brauchen Sie Administratoranmeldeinformationen. Damit die Active Directory-Datenbank, Protokolle und SYSVOL auf dem zusätzlich an den virtuellen Computer angeschlossenen Datenträger gespeichert werden können, müssen Sie den Standardspeicherort vom Betriebssystemlaufwerk auf den zusätzlichen Datenträger verlagern. 

Wenn die DC-Installation beendet ist, schließen Sie den virtuellen Computer wieder an und melden sich beim DC an. Denken Sie daran, die Domänenanmeldeinformationen anzugeben.

## Zurücksetzen des DNS-Servers für das virtuelle Azure-Netzwerk

1. Setzen Sie die Einstellung für die DNS-Weiterleitung auf dem neuen Domänencontroller/DNS-Server zurück. 
  1. Klicken Sie im Server-Manager auf **Tools** > **DNS**. 
  2. In **DNS-Manager**, mit der rechten Maustaste auf den Namen des DNS-Servers, und klicken Sie auf **Eigenschaften**. 
  3. Auf der **Weiterleitungen** Registerkarte, klicken Sie auf die IP-Adresse der Weiterleitung, und klicken Sie auf **Bearbeiten**.  Wählen Sie die IP-Adresse aus, und klicken Sie auf **Löschen**. 
  4. Klicken Sie auf **OK** um den Editor zu schließen und **Ok** erneut aus, um die DNS-Server-Eigenschaften zu schließen. 
2. Aktualisieren Sie die DNS-Servereinstellung für das virtuelle Netzwerk. 
  1. Klicken Sie auf **virtuelle Netzwerke** > doppelklicken Sie auf das virtuelle Netzwerk, das Sie erstellt haben > **konfigurieren** > **DNS-Server**, geben Sie den Namen und die DIP eines der virtuellen Computer, der die Domänencontroller/DNS-Serverrolle ausgeführt wird, und klicken Sie auf **Speichern**. 
  2. Wählen Sie den virtuellen Computer, und klicken Sie auf **Neustart** zum Auslösen des virtuelle Computers die DNS-Resolver-Einstellungen der IP-Adresse des neuen DNS-Servers konfiguriert. 


## Erstellen von virtuellen Computern für Domänenmitglieder

1. Wiederholen Sie die folgenden Schritte, um virtuelle Computer zu erstellen, die als Anwendungsserver ausgeführt werden. Übernehmen Sie den Standardwert für eine Einstellung, sofern kein anderer Wert empfohlen wird oder erforderlich ist.

    Seite des Assistenten…  | Einzugebende Werte
    ------------- | -------------
    **Image auswählen**  | Windows Server 2012 R2 Datacenter
    **Konfiguration des virtuellen Computers**  | <p>Der Name des virtuellen Computers: Geben Sie ein einteiliger Name (z. B. AppServer1).</p><p>Neuer Benutzername: Geben Sie den Namen eines Benutzers. Dieser Benutzer wird Mitglied der lokalen Administratorgruppe auf dem virtuellen Computer. Sie benötigen diesen Namen, um sich zum ersten Mal bei dem virtuellen Computer anzumelden. Das integrierte Konto mit dem Namen "Administrator" funktioniert nicht.</p><p>Neues Kennwort/bestätigen: Geben Sie ein Kennwort</p>
    **Konfiguration des virtuellen Computers**  | <p>Cloud-Dienst: Wählen Sie **erstellen einen neuen Clouddienst** für den ersten virtuellen Computer und wählen, gleichen Cloud-Dienstnamen, wenn Sie weitere virtuelle Computer erstellen, die Anwendung gehostet wird.</p><p>Cloud Service DNS Name: Geben Sie einen global eindeutigen Namen</p><p>Region/Affinity Group/Virtual Network: Geben Sie den Namen des virtuellen Netzwerks (z. B. WestUSVNet).</p><p>Speicherkonto: Wählen Sie **ein automatisch generiertes Speicherkonto verwenden** für den ersten virtuellen Computer und wählen Sie dann diese denselben speicherkontonamen, wenn Sie weitere virtuelle Computer erstellen, die Anwendung gehostet wird.</p><p>Verfügbarkeitsgruppe: Wählen Sie **verfügbarkeitsgruppe erstellen**.</p><p>Verfügbarkeitsgruppenname: Geben Sie einen Namen für die Verfügbarkeit festgelegt, wenn Sie den ersten virtuellen Computer erstellen, und Sie dann wählen, dass die gleichen Namen, wenn Sie weitere virtuelle Computer erstellen.</p>
    **Konfiguration des virtuellen Computers**  | <p>Wählen Sie <b>VM-Agent installieren</b> und alle anderen erforderlichen Erweiterungen.</p>
2. Nachdem alle virtuellen Computer bereitgestellt wurden, melden Sie sich an, und stellen eine Verbindung zur Domäne her. Im **Server-Manager**, klicken Sie auf **lokalen Server** > **Arbeitsgruppe** > **ändern...** und wählen Sie dann **Domäne** und geben Sie den Namen der lokalen Domäne. Geben Sie die Anmeldeinformationen eines Domänenbenutzers ein, und starten Sie den virtuellen Computer neu, um das Beitreten zu der Domäne abzuschließen.

Um die virtuellen Computer mithilfe von Windows PowerShell anstelle der Benutzeroberfläche zu erstellen, finden Sie unter [Verwenden von Azure PowerShell zum Erstellen und vorabkonfigurieren Windows-basierten virtuellen Maschinen](../virtual-machines-ps-create-preconfigure-windows-vms.md).

Weitere Informationen zur Verwendung von Windows PowerShell finden Sie unter [Erste Schritte mit Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx) und [Azure Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/jj554330.aspx).


## Siehe auch

-  [Installieren einer neuen Active Directory-Gesamtstruktur in einem virtuellen Azure-Netzwerk](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Richtlinien für die Bereitstellung von Windows Server Active Directory auf virtuellen Computern in Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Erstellen eines virtuellen Netzwerks komplett in der Cloud](../virtual-network/virtual-networks-create-vnet.md)
-  [Konfigurieren eines Standort-zu-Standort-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Installieren eines Active Directory-Replikatdomänencontrollers in Microsoft Azure](../virtual-networks-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: (01) Grundlagen zu virtuellen Computern](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) Erstellen virtueller Netzwerke und Herstellen standortübergreifender Verbindungen](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md)
-  [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Einrichten einer statischen IP-Adresse für einen virtuellen Computer in Azure](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [How to assign Static IP to Azure VM (in englischer Sprache)](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Installieren einer neuen Active Directory-Gesamtstruktur](https://technet.microsoft.com/library/jj574166.aspx)
-  [Einführung in die Virtualisierung von Active Directory-Domänendiensten (AD DS) (Ebene 100).](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

 


