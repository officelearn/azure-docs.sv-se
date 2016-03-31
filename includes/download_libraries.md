## Azure-Clientbibliotheken für Java – manueller Download

Azure-Bibliotheken für Java sind unter verteilt die [Apache-Lizenz, Version 2.0][license]. Klicken Sie auf [hier][zip-download] für eine ZIP-Datei der Bibliotheken und aller Abhängigkeiten.  Dies wird von Microsoft Open Technologies, Inc. zur Verfügung  Finden Sie in den Dateien license.txt und ThirdPartyNotices.txt-Datei in die ZIP-Datei für die Lizenz und Weitere Informationen.

## Azure-Bibliotheken für Java – Maven

Wenn Ihr Projekt bereits für die Verwendung von Maven für den Build-Prozess eingerichtet ist, fügen Sie die folgende Abhängigkeit zu Ihrer pom.xml-Datei hinzu: Hinweis: Informationen zum Erstellen von Maven-Projekte in Eclipse, die die Azure-Bibliotheken für Java verwendet, finden Sie unter [Erste Schritte mit Azure Management Libraries for Java][maven-getting-started].

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>0.6.0</version>
    </dependency>


In der `<version>` -Element, ersetzen Sie die Versionsnummern in diesem Beispiel mit gültigen Versionsnummern abgerufen werden können die [Azure-Bibliothekenrepository auf Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

