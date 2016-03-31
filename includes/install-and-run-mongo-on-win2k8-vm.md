Diese schrittweise Anleitung erläutert die Installation und Ausführung von MongoDB auf einem virtuellen Computer, der Windows Server ausführt.

> [AZURE.IMPORTANT] MongoDB-Sicherheitsfunktionen wie Authentifizierung und IP-Adressen-Anbindung werden nicht standardmäßig aktiviert. Die Sicherheitsfunktionen sollten aktiviert werden, bevor MongoDB in einer Produktionsumgebung eingesetzt wird.  Finden Sie unter [Sicherheit und Authentifizierung](http://www.mongodb.org/display/DOCS/Security+and+Authentication) Weitere Informationen.

1. Nachdem Sie den virtuellen Computer über Remotedesktop verbunden sind, öffnen Sie Internet Explorer über die **Starten** Menü auf dem virtuellen Computer.

2. Wählen Sie die **Tools** Schaltfläche in der oberen rechten Ecke.  In **Internetoptionen**, wählen die **Sicherheit** Registerkarte, und wählen Sie dann die **Vertrauenswürdige Sites** Symbol, und klicken Sie dann auf die **Sites** Schaltfläche. Hinzufügen _http://\*.mongodb.org_ zur Liste der vertrauenswürdigen Sites.

3. Klicken Sie auf [Downloads - MongoDB] [MongoDownloads].

4. Suchen Sie die **Current Stable Release**, wählen Sie das neueste **64-Bit-** Version in der Spalte Windows herunterladen und Ausführen des MSI-Installationsprogramms.

5. MongoDB wird normalerweise unter "C:\Programme\MongoDB" installiert. Suchen Sie auf dem Desktop nach "Umgebungsvariablen", und fügen Sie der PATH-Variablen den Pfad der MongoDB-Binärdateien hinzu. Die Binärdateien befinden sich beispielsweise auf Ihrem Computer unter "C:\Programme\MongoDB\Server\3.0\bin".

6. Erstellen von Daten- und Protokollverzeichnisse für MongoDB auf dem Datenträger (Laufwerk **F:**, z. B.) Sie in den vorhergehenden Schritten erstellt haben. Aus **Start**, Option **Command Prompt** um ein Eingabeaufforderungsfenster zu öffnen.  Geben Sie Folgendes ein:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs

7. Geben Sie Folgendes rein, um die Datenbank auszuführen:

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    Alle protokollmeldungen geleitet werden, die *F:\MongoLogs\mongolog.log* Server mongod.exe startet und Journaldateien Datei. Es kann einige Minuten dauern, bis MongoDB die Journaldateien reserviert hat und mit dem Lauschen auf Verbindungen beginnt.

8. Die MongoDB-Administrator-Shell öffnen Sie ein weiteres Befehlsfenster aus **Start** und geben Sie Folgendes ein:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    Die Datenbank wird vom Insert erstellt.

9. Alternativ können Sie "mongod.exe" als Dienst installieren:

        C:\mongodb\bin>mongod --logpath F:\MongoLogs\mongolog.log --logappend --dbpath F:\MongoData\ --install

    Dadurch wird der Dienst "Mongo DB" mit der Beschreibung "Mongo DB" erstellt. Die **--Logpath** Option muss verwendet werden, um eine Protokolldatei anzugeben, da der laufende Dienst kein Befehlsfenster zur Anzeige der Ausgabe hat.  Die **--Logappend** Option gibt an, dass ein Neustart des Diensts eine Ausgabe, die an die vorhandene Protokolldatei angehängt wird.  Die **--Dbpath** Option gibt den Speicherort des Datenverzeichnisses an. Weitere dienstbezogene Befehlszeilenoptionen finden Sie unter [dienstbezogene Befehlszeilenoptionen] [MongoWindowsSvcOptions].

    Führen Sie zum Starten des Diensts den folgenden Befehl aus:

        C:\mongodb\bin>net start MongoDB

10. Nach der Installation und Ausführung von MongoDB müssen Sie einen Port in Windows Firewall öffnen, um eine Remote-Verbindung mit MongoDB herzustellen.  Aus der **Start** Klicken Sie im Menü **Administratortools** und anschließend **Windows-Firewall mit erweiterter Sicherheit**.

11. Wählen Sie im linken Bereich **Eingehende Regeln**.  In der **Aktionen** Bereich Wählen Sie auf der rechten **neue Regel...**.

    ![Windows-Firewall][Image1]

    In den **Assistenten für neue eingehende Regel**, Option **Port** und klicken Sie dann auf **Weiter**.

    ![Windows-Firewall][Image2]

    Wählen Sie **TCP** und anschließend **bestimmte lokale Ports**.  Geben Sie einen Port "27017" (der Standardport MongoDB lauscht), und klicken Sie auf **Weiter**.

    ![Windows-Firewall][Image3]

    Wählen Sie **Verbindung zulassen,** und klicken Sie auf **Weiter**.

    ![Windows-Firewall][Image4]

    Klicken Sie auf **Weiter** erneut.

    ![Windows-Firewall][Image5]

    Geben Sie einen Namen für die Regel ein, z. B. "MongoPort", und klicken Sie auf **Fertig stellen**.

    ![Windows-Firewall][Image6]

12. Wenn Sie beim Erstellen des virtuellen Computers keinen Endpunkt für MongoDB konfiguriert haben, können Sie dies jetzt tun. Sie benötigen sowohl die Firewall-Regel als auch den Endpunkt, um eine Remote-Verbindung zu MongoDB herstellen zu können. Klicken Sie im Verwaltungsportal auf **virtuelle Computer**, klicken Sie auf den Namen des neuen virtuellen Computers aus, und klicken Sie dann auf **Endpunkte**.

    ![Endpunkte][Image7]

13. Klicken Sie auf **Hinzufügen** am unteren Rand der Seite. Wählen Sie **eigenständigen Endpunkt hinzufügen** und klicken Sie auf **Weiter**.

    ![Endpunkte][Image8]

14. Fügen Sie einen Endpunkt mit dem Namen "Mongo", Protokoll **TCP**, und die beiden **öffentlichen** und **privaten** Ports festlegen auf "27017". Dies ermöglicht einen Fernzugriff auf MongoDB.

    ![Endpunkte][Image9]

> [AZURE.NOTE] Port 27017 ist der Standardport verwendet MongoDB. Sie können dies durch Ändern der _– Port_ Unterbefehl beim Starten des mongod.exe-Servers. Achten Sie darauf, dass Sie in der Firewall die gleiche Portnummer sowie den "Mongo"-Endpunkt entsprechend den Anweisungen oben angeben.


[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png


