
In diesem Abschnitt wird erläutert, wie Sie SQL Server Express installieren, TCP/IP aktivieren, einen statischen Port festlegen und eine Datenbank erstellen, die mit Hybridverbindungen verwendet werden kann.  

###SQL Server Express installieren

Um eine lokale SQL Server- oder SQL Server Express-Datenbank mit einer Hybridverbindung verwenden zu können, muss TCP/IP an einem statischen Port aktiviert werden. Standardinstanzen von SQL Server verwenden den statischen Port 1433, benannte Instanzen dagegen nicht. Aus diesem Grund installieren wir die Standardinstanz. Wenn Sie bereits die Standardinstanz von SQL Server Express installiert haben, können Sie diesen Abschnitt überspringen.

1. Führen Sie zum Installieren von SQL Server Express die **SQLEXPRWT_x64_ENU.exe** oder **SQLEXPR_x86_ENU.exe** -Datei, die Sie heruntergeladen haben. Der SQL Server-Installationscenter-Assistent wird geöffnet.
    
2. Wählen Sie **eigenständige neue SQL Server-Installation oder Hinzufügen von Features zu einer vorhandenen Installation**, befolgen Sie die Anweisungen, übernehmen die Standardeinstellungen, bis Sie sich an den **Instanzkonfiguration** Seite.
    
3. Auf der **Instanzkonfiguration** Seite **Standardinstanz**, akzeptieren Sie die Standardeinstellungen auf der **Serverkonfiguration** Seite.

    >[AZURE.NOTE]Wenn Sie bereits eine Standardinstanz von SQL Server installiert haben, können Sie mit dem nächsten Abschnitt überspringen und diese Instanz mit Hybridverbindungen verwenden. 
    
5. Auf der **Datenbankmodulkonfiguration** Seite **Authentifizierungsmodus**, wählen Sie **im gemischten Modus (SQL Server-Authentifizierung und Windows-Authentifizierung)**, und geben Sie ein sicheres Kennwort für das integrierte **sa** Administratorkonto an.
    
    In diesem Tutorial verwenden Sie die SQLServer-Authentifizierung. Merken Sie sich unbedingt das Kennwort, das Sie angeben, weil Sie es später benötigen werden.
    
6. Beenden Sie den Assistenten, um die Installation abzuschließen.

###Aktivieren von TCP/IP und Festlegen eines statischen Ports

In diesem Abschnitt wird mithilfe des SQL Server-Konfigurations-Managers, der zusammen mit SQL Server Express installiert wurde, TCP/IP aktiviert und eine statische IP-Adresse festgelegt. 

1. Führen Sie die Schritte [TCP/IP-Netzwerkprotokolls für SQL Server aktivieren](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) TCP/IP-Zugriff auf die Instanz zu aktivieren.

2. (Optional) Wenn Sie nicht die Standardinstanz verwenden können, müssen Sie den Schritte in [konfigurieren ein Servers zur Überwachung eines bestimmten TCP-Ports ](https://msdn.microsoft.com/library/ms177440.aspx) auf einen statischen Port für die Instanz festzulegen. Wenn Sie diesen Schritt ausführen, stellen Sie die Verbindung unter Verwendung des neuen definierten Ports und nicht über Port 1433 her.

3. (Optional:) Fügen Sie bei Bedarf Ausnahmen in der Firewall für den Remotezugriff auf den SQL Server-Prozess (sqlservr.exe) hinzu.

###Erstellen einer neuen Datenbank in der lokalen SQL Server-Instanz

1. Stellen Sie in SQL Server Management Studio eine Verbindung mit der SQL Server-Instanz her, die Sie gerade installiert haben. (Wenn der **Verbindung mit Server herstellen** Dialogfeld nicht automatisch angezeigt wird, navigieren Sie zu **Objekt-Explorer** Klicken Sie im linken Bereich auf **Verbinden**, und klicken Sie dann auf **Datenbankmodul**.)     

    ![Verbindung mit dem Server herstellen](./media/hybrid-connections-create-on-premises-database/A04SSMSConnectToServer.png)
    
    Für **Servertyp**, wählen Sie **Datenbankmodul**. Für **Servername**, können Sie **Localhost** oder den Namen des Computers, auf dem SQL Server installiert. Wählen Sie **SQL Server-Authentifizierung**, und geben Sie das Kennwort für das sa-Anmeldung, die Sie zuvor erstellt haben. 
    
2. Zum Erstellen einer neuen Datenbank mithilfe von SQL Server Management Studio mit der rechten Maustaste **Datenbanken** im Objekt-Explorer, und klicken Sie dann auf **neue Datenbank**.
    
3. In der **neue Datenbank** Dialogfelds `OnPremisesDB`, und klicken Sie dann auf **OK**. 
    
4. Im Objekt-Explorer erweitern **Datenbanken**, sehen Sie, dass die neue Datenbank erstellt wird.

###Erstellen einer neuen SQL Server-Anmeldung und Festlegen von Berechtigungen

Schließlich erstellen Sie eine neue SQL Server-Anmeldung mit eingeschränkten Berechtigungen. Der Azure-Dienst stellt eine Verbindung mit dem lokalen SQL Server mit dieser Anmeldung statt mit der integrierten sa-Anmeldung her, die über uneingeschränkte Berechtigungen für den Server verfügt.

1. In SQL Server Management Studio Objekt-Explorer mit der rechten Maustaste die **OnPremisesDB** Datenbank, und klicken Sie auf **neue Abfrage**.

2.  Fügen Sie die folgende TSQL-Abfrage im Abfragefenster ein.

        USE [master]
        GO
        
        /* Replace the PASSWORD in the following statement with a secure password. 
           If you save this script, make sure that you secure the file to 
           securely maintain the password. */ 
        CREATE LOGIN [HybridConnectionLogin] WITH PASSWORD=N'<**secure_password**>', 
            DEFAULT_DATABASE=[OnPremisesDB], DEFAULT_LANGUAGE=[us_english], 
            CHECK_EXPIRATION=OFF, CHECK_POLICY=ON
        GO
    
        USE [OnPremisesDB]
        GO
    
        CREATE USER [HybridConnectionLogin] FOR LOGIN [HybridConnectionLogin] 
        WITH DEFAULT_SCHEMA=[dbo]
        GO

        GRANT CONNECT TO [HybridConnectionLogin]
        GRANT CREATE TABLE TO [HybridConnectionLogin]
        GRANT CREATE SCHEMA TO [HybridConnectionLogin]
        GO  
   
3. Ersetzen Sie im obigen Skript die Zeichenfolge `<**secure_password**>` durch ein sicheres Kennwort für das neue *HybridConnectionsLogin*.

4. **Führen Sie** die Abfrage auf die neue Anmeldung erstellen und die erforderlichen Berechtigungen in der lokalen Datenbank erteilen.



