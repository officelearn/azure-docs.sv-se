<properties writer="kathydav" editor="tysonn" manager="timlt" />

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com) an. Sehen Sie sich die [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) bieten, wenn Sie noch kein Abonnement haben.

2. Klicken Sie auf der Befehlsleiste am unteren Rand des Fensters auf **Neu**.

3. Unter **Compute**, klicken Sie auf **virtuellen Computer**, und klicken Sie dann auf **aus Galerie**.

    ![Neuen virtuellen Computer erstellen][Image1]

4. Unter den **SUSE** Gruppieren, wählen Sie ein OpenSUSE-VM-Image, und klicken Sie dann auf Pfeil, um fortzufahren.

5. Auf der ersten **Konfiguration des virtuellen Computers** Seite:

    - Geben Sie einen **Namen des virtuellen Computers**, z. B. "Testlinuxvm".
    - Überprüfen Sie die **Ebene** und wählen Sie eine **Größe**. Die Ebene bestimmt, zwischen welchen Größen Sie wählen können. Die Größe wirkt sich auf seine Betriebskosten sowie auf die Konfigurationsoptionen, wie die Anzahl der Datenträger, die Sie anschließen können, aus. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../articles/virtual-machines-size-specs.md).
    - Geben Sie einen **Neuer Benutzername**, oder übernehmen Sie den Standardnamen **Azureuser**. Dieser Name wird der Sudoer-Listendatei hinzugefügt.
    - Entscheiden, welche Art **Authentifizierung** verwenden. Hinweise zu allgemeinen Kennwortrichtlinien finden Sie unter [sichere Kennwörter](http://msdn.microsoft.com/library/ms161962.aspx).

6. Auf der nächsten **Konfiguration des virtuellen Computers** Seite:

    - Verwenden Sie den Standardnamen **erstellen einen neuen Clouddienst**.
    - In den **DNS-Namen** Geben Sie einen gültigen DNS-Namen als Teil der Adresse, z. B. "Testlinuxvm".
    - In der **Region/Affinity Group/Virtual Network** Wählen Sie eine Region, in der dieses virtuelle Image gehostet wird.
    - Unter **Endpunkte**, behalten Sie den SSH-Endpunkt. Sie können nun weitere Endpunkte hinzufügen oder nach dem Erstellen des virtuellen Computers vorhandene Endpunkte hinzufügen, ändern oder löschen.

    >[AZURE.NOTE] Wenn Sie eine virtuelle Maschine ein virtuelles Netzwerk verwenden möchten Sie **müssen** das virtuelle Netzwerk beim Erstellen des virtuellen Computers angeben. Sie können einen virtuellen Computer nicht einem virtuellen Netzwerk hinzufügen, nachdem Sie den virtuellen Computer erstellt haben. Weitere Informationen finden Sie unter [virtuelle Netzwerke im Überblick](virtual-networks-overview.md).

7.  Für die letzte **Konfiguration des virtuellen Computers** Seite, behalten Sie die Standardeinstellungen, und klicken Sie dann auf das Häkchen, um fertig zu stellen.

Im Verwaltungsportal den neuen virtuellen Computer unter **virtuelle Computer**. Während der Status, als gemeldet wird **(Bereitstellung)**, die virtuelle Maschine wird eingerichtet. Wenn der Status wird angegeben als **Ausführen**, können Sie mit dem nächsten Schritt fortfahren.

##Herstellen einer Verbindung mit dem virtuellen Computer

In Abhängigkeit von dem Betriebssystem auf dem Computer, über den Sie eine Verbindung herstellen, verwenden Sie für die Verbindung mit dem virtuellen Computer SSH oder PuTTY:

- Verwenden Sie auf einem Computer mit Linux SSH. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

    Geben Sie das Kennwort des Benutzers ein.

- Verwenden Sie auf einem Computer mit Windows PuTTY. Wenn Sie nicht installiert ist, können Sie ihn aus der [PuTTY-Downloadseite][PuTTYDownload].

    Speichern Sie **putty.exe** in ein Verzeichnis auf Ihrem Computer. Öffnen Sie eine Befehlszeile und navigieren Sie zu diesem Ordner ausführen **putty.exe**.

    Geben den Hostnamen ein, z. B. "testlinuxvm.cloudapp.NET""und"22"für die **Port**.

    ![PuTTY-Bildschirm][Image6]  

##Aktualisieren des virtuellen Computers (optional)

1. Nachdem Sie eine Verbindung mit dem virtuellen Computer hergestellt haben, haben Sie die Möglichkeit, Systemaktualisierungen und Patches zu installieren. Zum Ausführen der Aktualisierung geben Sie Folgendes ein:

    `$ sudo zypper update`

2. Wählen Sie **Software**, dann **Onlineupdates** zum Auflisten der verfügbaren Updates. Wählen Sie **annehmen** zum Starten der Installation und alle verfügbaren neuen Patches (außer den optionalen) anzuwenden.

3. Wählen Sie nach Abschluss der Installation, **Fertig stellen**.  Ihr System ist jetzt auf dem aktuellen Stand.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png


