Die folgenden Schritte veranschaulichen, wie Sie mithilfe von SQL Server Management Studio (SSMS) über das Internet eine Verbindung mit der SQL Server-Instanz herstellen. Mit den gleichen Schritten ermöglichen Sie jedoch auch den Zugriff der lokal und in Azure ausgeführten Anwendungen auf den virtuellen SQL Server-Computer.

Bevor Sie eine Verbindung mit der Instanz von SQL Server über einen anderen virtuellen Computer oder über das Internet herstellen können, müssen Sie folgende Aufgaben abschließen, die in den nächsten Abschnitten beschrieben werden:

- [Erstellen eines TCP-Endpunkts für den virtuellen Computer](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Öffnen der TCP-Ports in der Windows-Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Konfigurieren von SQL Server für das Lauschen des TCP-Protokolls](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus](#configure-sql-server-for-mixed-mode-authentication)
- [Erstellen von Anmeldenamen für die SQL Server-Authentifizierung](#create-sql-server-authentication-logins)
- [Bestimmen des DNS-Namens des virtuellen Computers](#determine-the-dns-name-of-the-virtual-machine)
- [Verbinden mit dem Datenbankmodul von einem anderen Computer aus](#connect-to-the-database-engine-from-another-computer)

Der Verbindungspfad wird von folgendem Diagramm zusammengefasst:

![Verbinden mit einem virtuellen SQL Server-Computer](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### Erstellen eines TCP-Endpunkts für den virtuellen Computer

Für den Zugriff auf SQL Server über das Internet benötigt der virtuelle Computer einen Endpunkt, um nach eingehender TCP-Kommunikation zu lauschen. Dieser Azure-Konfigurationsschritt leitet den eingehenden Datenverkehr des TCP-Ports zu einem TCP-Port, auf den der virtuelle Computer zugreifen kann.

>[AZURE.NOTE] Wenn Sie innerhalb der gleichen Cloud-Dienst oder virtuellen Netzwerk verbinden, müssen Sie keinen Endpunkt mit öffentlichem Zugriff erstellen. In diesem Fall können Sie mit dem nächsten Schritt fortfahren. Weitere Informationen finden Sie unter [Verbindungsszenarien](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. Klicken Sie auf der Azure-Verwaltungsportal auf **virtuelle Computer**.
    
2. Klicken Sie auf den neu erstellten virtuellen Computer. Informationen zu Ihrem virtuellen Computer werden angezeigt.
    
3. Wählen Sie im oberen Bereich der Seite, die **ENDPUNKTE** Seite, und klicken Sie dann unten auf der Seite auf **Hinzufügen**.
    
4. Auf der **Fügen Sie einen Endpunkt zu einer virtuellen Maschine** auf **eigenständigen Endpunkt hinzufügen**, und klicken Sie dann auf den Pfeil "Weiter", um den Vorgang fortzusetzen.
    
5. Auf der **Geben Sie die Details des Endpunkts** Seite, die folgenden Informationen angeben.

    - In den **Namen** Geben einen Namen für den Endpunkt.
    - In der **Protokoll** Wählen Sie im **TCP**. Sie können eingeben **57500** in den **öffentlicher PORT** Feld. Auf ähnliche Weise können Sie SQL Server Standard-Überwachungsport eingeben **1433** in die **privater Port** Feld. Viele Organisationen verwenden unterschiedliche Portnummern, um böswillige Sicherheitsangriffe zu vermeiden. 

6. Klicken Sie auf das Häkchen, um fortzufahren. Der Endpunkt wurde erstellt.

### Öffnen der TCP-Ports in der Windows-Firewall für die Standardinstanz des Datenbankmoduls

1. Stellen Sie mit Windows Remotedesktop eine Verbindung zum virtuellen Computer her. Geben Sie nach der Anmeldung auf dem Startbildschirm **WF.msc**, und drücken Sie dann die EINGABETASTE. 

    ![Starten des Firewall-Programms](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. In der **Windows-Firewall mit erweiterter Sicherheit**, im linken Bereich mit der rechten Maustaste **Eingehende Regeln**, und klicken Sie dann auf **neue Regel** im Aktionsbereich.

    ![Neue Regel](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. In der **für neue eingehende Regel** Dialogfeld **Regeltyp**, Option **Port**, und klicken Sie dann auf **Weiter**.

4. In der **Protokolle und Ports** Dialogfeld verwenden Sie den Standardnamen **TCP**. In der **bestimmte lokale Ports** Feld, und geben Sie die Portnummer der Instanz des Datenbankmoduls (**1433** für die Standardinstanz oder Ihrer Wahl für den privaten Port im Endpunktschritt). 

    ![TCP-Port 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Klicken Sie auf **Weiter**.

6. In der **Aktion** Wählen Sie im Dialogfeld **Verbindung zulassen,**, und klicken Sie dann auf **Weiter**.

    **Sicherheitshinweis:** auswählen **Verbindung zulassen, wenn sie sicher ist** können zusätzliche Sicherheit sorgen. Wählen Sie diese Option, wenn Sie zusätzliche Sicherheitsoptionen in Ihrer Umgebung konfigurieren möchten.

    ![Verbindungen zulassen](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. In der **Profil** Wählen Sie im Dialogfeld **öffentlichen**, **Private**, und **Domäne**. Klicken Sie dann auf **Weiter**. 

    **Sicherheitshinweis:**  auswählen **öffentlichen** ermöglicht den Zugriff über das Internet. Sie sollten möglichst ein restriktiveres Profil auswählen.

    ![Öffentliches Profil](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. In den **Namen** Dialogfeld Geben Sie einen Namen und eine Beschreibung für diese Regel, und klicken Sie dann auf **Fertig stellen**.

    ![Regelname](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Öffnen Sie gegebenenfalls zusätzliche Ports für andere Komponenten. Weitere Informationen finden Sie unter [Konfigurieren der Windows-Firewall für SQL Server-Zugriff](http://msdn.microsoft.com/library/cc646023.aspx).


### Konfigurieren von SQL Server für das Lauschen des TCP-Protokolls

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server-Konfigurations-Manager** und drücken Sie die EINGABETASTE.
    
    ![SSCM öffnen](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. Erweitern Sie in SQL Server-Konfigurations-Manager im Konsolenbereich **SQL Server-Netzwerkkonfiguration**.

3. Klicken Sie im linken Bereich auf **Protokolle für MSSQLSERVER** (standardinstanzenname.) Klicken Sie im Detailbereich mit der rechten Maustaste auf TCP, dies sollte standardmäßig für Katalogimages aktiviert sein. Klicken Sie für eigene Images auf **aktivieren** (wenn der Status deaktiviert ist.)

    ![TCP aktivieren](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. Klicken Sie im linken Bereich auf **SQL Server-Dienste**. Klicken Sie im Detailbereich mit der rechten Maustaste **SQL Server (_Instanzname_)** (die Standardinstanz ist **SQL Server (MSSQLSERVER)**), und klicken Sie dann auf **Neustart**, um zu beenden und die Instanz von SQL Server neu starten. 

    ![Datenbankmodul neu starten](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Schließen Sie den SQL Server-Konfigurations-Manager.

Weitere Informationen zur Aktivierung von Protokollen für die SQL Server-Datenbankmodul finden Sie unter [Aktivieren oder Deaktivieren eines Server-Netzwerkprotokolls](http://msdn.microsoft.com/library/ms191294.aspx).

### Konfigurieren von SQL Server für die Authentifizierung mit gemischtem Modus

Das SQL Server-Datenbankmodul kann ohne Domänenumgebung keine Windows-Authentifizierung nutzen. Um eine Verbindung zum Datenbankmodul von einem anderen Computer aus herzustellen, konfigurieren Sie SQL Server für die Authentifizierung mit gemischtem Modus. Die Authentifizierung mit gemischtem Modus ermöglicht sowohl die SQL Server-Authentifizierung als auch die Windows-Authentifizierung.

>[AZURE.NOTE] Konfigurieren der Authentifizierung im gemischten Modus ist möglicherweise nicht erforderlich, wenn Sie ein virtuelles Netzwerk in Azure mit einer konfigurierten domänenumgebung konfiguriert haben.

1. Geben Sie während der Verbindung mit dem virtuellen Computer auf der Startseite **SQL Server 2014 Management Studio** und klicken Sie auf das ausgewählte Symbol.

    ![SSMS starten](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

    Wenn Sie Management Studio erstmalig öffnen, muss dieses eine Management Studio-Benutzerumgebung erstellen. Dies kann einige Zeit dauern.

2. Management Studio zeigt die **Verbindung mit Server herstellen** im Dialogfeld. In der **Servername** Geben Sie den Namen des virtuellen Computers für die Verbindung mit dem Datenbankmodul mit dem Objekt-Explorer. (Anstelle des Namen des virtuellen Computers können Sie auch **(lokal)** oder einen einzigen Punkt als den **Servername**. Wählen Sie **Windows-Authentifizierung**, und lassen Sie **_Your_VM_name_\your_local_administrator** in den **Benutzername** Feld. Klicken Sie auf **Verbinden**.

    ![Verbindung mit dem Server herstellen](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. In SQL Server Management Studio Objekt-Explorer mit der rechten Maustaste in des Namens der Instanz von SQL Server (der Name des virtuellen Computers), und klicken Sie dann auf **Eigenschaften**.

    ![Servereigenschaften](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. Auf der **Sicherheit** Seite **Serverauthentifizierung**, Option **SQL Server-und Windows-Authentifizierungsmodus**, und klicken Sie dann auf **OK**.

    ![Authentifizierungsmodus auswählen](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. Klicken Sie im Dialogfeld SQL Server Management Studio **OK** um den notwendigen Neustart von SQL Server zu bestätigen.

6. Klicken Sie im Objekt-Explorer auf Ihre Server, und klicken Sie dann auf **Neustart**. (Wenn SQL Server-Agent ausgeführt wird, muss dieser ebenfalls neu gestartet werden.)

    ![Neu starten](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. Klicken Sie im Dialogfeld SQL Server Management Studio **Ja** sicher zustimmen, dass Sie SQL Server neu starten möchten.

### Erstellen von Anmeldenamen für die SQL Server-Authentifizierung

Um von einem anderen Computer aus eine Verbindung zum Datenbankmodul herzustellen, müssen Sie mindestens einen Anmeldenamen für die SQL Server-Authentifizierung erstellen.

1. Erweitern Sie in SQL Server Management Studio-Objekt-Explorer den Ordner der Serverinstanz, in der der neue Anmeldename erstellt werden soll.

2. Mit der rechten Maustaste die **Sicherheit** Ordner, zeigen Sie auf **Neu**, und wählen Sie **Anmeldung**.

    ![Neue Anmeldung](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. In der **Anmeldung - neu** Dialogfeld auf die **Allgemein** Seite, geben Sie den Namen des neuen Benutzers in der **Anmeldename** Feld.

4. Wählen Sie **SQL Server-Authentifizierung**.

5. In der **Kennwort** Geben Sie ein Kennwort für den neuen Benutzer. Geben Sie das Kennwort erneut in das **Kennwort bestätigen** Feld.

6. Um kennwortrichtlinienoptionen für Komplexität und Erzwingung zu erzwingen, wählen **Kennwortrichtlinie erzwingen** (empfohlen). Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

7. Um kennwortrichtlinienoptionen für den Ablauf zu erzwingen, wählen **Ablauf des Kennwortes erzwingen** (empfohlen). Kennwortrichtlinie erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde.

8. Wählen Sie zum Erzwingen des Benutzers ein neues Kennwort nach dem ersten Erstellen des Anmeldenamens **Benutzer muss Kennwort bei der nächsten Anmeldung ändern** (wird empfohlen, falls dieser Anmeldenamen von einer anderen Person verwendet wird. Wenn Sie den Anmeldenamen selbst verwenden werden, sollten Sie diese Option nicht auswählen). Ablauf des Kennwortes erzwingen muss ausgewählt werden, um dieses Kontrollkästchen zu aktivieren. Dies ist eine Standardoption, wenn die SQL Server-Authentifizierung ausgewählt wurde. 

9. Aus der **Standarddatenbank** Wählen Sie eine Standarddatenbank für die Anmeldung. **Master** ist der Standardwert für diese Option. Wenn Sie noch nicht mit eine Benutzerdatenbank erstellt haben, lassen Sie diese Einstellung auf **master**.

10. In der **Default Language** auflisten, lassen Sie **Standard** als Wert.
    
    ![Anmeldenameneigenschaften](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. Falls es sich um den ersten Anmeldenamen handelt, den Sie erstellen, werden Sie diesen Anmeldenamen wahrscheinlich als SQL Server-Administrator bezeichnen. In diesem Fall auf die **Serverrollen** Seite Kontrollkästchen **Sysadmin**. 

    **Sicherheitshinweis:** Mitglieder der festen Serverrolle Sysadmin haben vollständige Kontrolle über die Datenbank-Engine. Sie sollten die Mitgliedschaft zu dieser Rolle sorgfältig beschränken.

    ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Klicken Sie auf OK.

Weitere Informationen zu SQL Server-Anmeldenamen finden Sie unter [Erstellen Sie eine Anmeldung](http://msdn.microsoft.com/library/aa337562.aspx).

### Bestimmen des DNS-Namens des virtuellen Computers

Um sich von einem anderen Computer aus mit dem SQL Server-Datenbankmodul zu verbinden, müssen Sie den DNS-Namen (Domain Name System) des virtuellen Computers kennen. (Dies ist der Name, den das Internet verwendet, um den virtuellen Computer zu identifizieren. Sie können die IP-Adresse verwenden, aber die IP-Adresse ändert sich möglicherweise, wenn Azure aufgrund von Redundanz oder Wartungsarbeiten Ressourcen verschiebt. Der DNS-Name bleibt bestehen, da er zu einer neuen IP-Adresse weitergeleitet werden kann.)  

1. Wählen Sie im Azure-Verwaltungsportal (oder aus dem vorherigen Schritt) **virtuelle Computer**. 

2. Auf der **Instanzen virtueller Computer** Seite der **auf einen Blick** Spalte suchen und kopieren Sie den DNS-Namen für den virtuellen Computer.

    ![DNS-Name](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
    

### Verbinden mit dem Datenbankmodul von einem anderen Computer aus
 
1. Öffnen Sie auf einem mit dem Internet verbundenen Computer SQL Server Management Studio.
2. In der **mit Server verbinden** oder **Verbindung mit Datenbankmodul herstellen** im Feld der**Servername** Geben Sie den DNS-Namen des virtuellen Computers (in der vorherigen Aufgabe festgelegt) und Portnummer eines öffentlichen Endpunkts im Format *DNSName, Portnummer* wie z. B. **tutorialtestVM.cloudapp.net,57500**.
Um die Portnummer zu erhalten, melden Sie sich beim Azure-Verwaltungsportal an und suchen den virtuellen Computer. Klicken Sie auf dem Dashboard auf **ENDPUNKTE** und verwenden die **öffentlicher PORT** zugewiesene **MSSQL**.
    ![Öffentlicher Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. In der **Authentifizierung** Wählen Sie im **SQL Server-Authentifizierung**.
5. In der **Anmeldung** Geben Sie den Namen einer Anmeldung, die Sie vorher erstellt haben.
6. In der **Kennwort** Geben Sie das Kennwort der Anmeldung, die Sie vorher erstellen.
7. Klicken Sie auf **Verbinden**.

    ![Verbinden mit SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)


