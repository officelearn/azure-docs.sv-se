Diese schrittweise Anleitung erläutert die Installation und Ausführung von MongoDB auf einem virtuellen Computer, der CentOS Linux ausführt.

> [AZURE.WARNING] MongoDB-Sicherheitsfunktionen wie Authentifizierung und IP-Adressen-Anbindung werden nicht standardmäßig aktiviert. Die Sicherheitsfunktionen sollten aktiviert werden, bevor MongoDB in einer Produktionsumgebung eingesetzt wird.  Finden Sie unter [Sicherheit und Authentifizierung](http://www.mongodb.org/display/DOCS/Security+and+Authentication) Weitere Informationen.

1. Konfigurieren Sie das Package Management System (YUM) so, dass Sie MongoDB installieren können. Erstellen einer */etc/yum.repos.d/10gen.repo* zu Informationen über das Repository, und fügen Sie die folgende Datei:

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2. Speichern Sie die repo-Datei und führen Sie dann den folgenden Befehl aus, um die lokale Paketdatenbank zu aktualisieren:

        $ sudo yum update

3. Führen Sie zur Installation des Pakets den folgenden Befehl aus. Hiermit installieren Sie die aktuelle stabile Version von MongoDB und die zugehörigen Werkzeuge:

        $ sudo yum install mongo-10gen mongo-10gen-server

    Warten Sie, während MongoDB heruntergeladen und installiert wird.

4. Erstellen Sie ein Datenverzeichnis. Standardmäßig speichert MongoDB Daten in der */Data/Db* Directory, aber Sie müssen dieses Verzeichnis erstellen. Führen Sie zum Erstellen aus:

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Weitere Informationen zum Installieren von MongoDB auf Linux finden Sie unter [Quickstart Unix][QuickstartUnix].

5. Führen Sie zum Starten der Datenbank aus:

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    Alle protokollmeldungen geleitet werden, die */srv/datadrive/data/mongod.log* Datei MongoDB-Server startet und Journaldateien vorbelegt. Es kann einige Minuten dauern, bis MongoDB die Journaldateien reserviert hat und mit dem Lauschen auf Verbindungen beginnt.

6. Starten Sie die administrative Shell von MongoDB, indem Sie ein eigenes SSH- oder PuTTY-Fenster öffnen und das Folgende ausführen:

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    Die Datenbank wird vom Insert erstellt.

7. Wenn MongoDB installiert ist, müssen Sie einen Endpunkt konfigurieren, damit ein Fernzugriff auf MongoDB möglich ist. Klicken Sie im Verwaltungsportal auf **virtuelle Computer**, dann klicken Sie auf den Namen des neuen virtuellen Computers, und klicken Sie dann **Endpunkte**.
    
    ![Endpunkte][Image7]

8. Klicken Sie auf **Endpunkt hinzufügen** am unteren Rand der Seite.
    
    ![Endpunkte][Image8]

9. Fügen Sie einen Endpunkt mit den folgenden Einstellungen hinzu:

 - **Namen**: Mongo
 - **Protokoll**: TCP
 - **Öffentlicher Port**: 27017
 - **Privater Port**: 27017
 
 Dies ermöglicht einen Fernzugriff auf MongoDB.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png


