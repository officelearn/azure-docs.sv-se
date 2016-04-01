### Installation mithilfe von Composer

1. [Installieren von Git][install-git]. Unter Windows muss die ausführbare Git-Datei zu Ihrer PATH-Umgebungsvariablen hinzugefügt werden. 

2. Erstellen Sie eine Datei mit dem Namen **composer.json** im Stammverzeichnis des Projekts und fügen Sie den folgenden Code hinzu:

    ```
    {
        "repositories": [
            {
                "type": "pear",
                "url": "http://pear.php.net"
            }
        ],
        "require": {
            "pear-pear.php.net/mail_mime" : "*",
            "pear-pear.php.net/http_request2" : "*",
            "pear-pear.php.net/mail_mimedecode" : "*",
            "microsoft/windowsazure": "*"
        }
    }
    ```

3. Herunterladen **[composer.phar][composer-phar]** in Ihrem Projektverzeichnis.

4. Öffnen Sie eine Eingabeaufforderung und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

    ```
    php composer.phar install
    ```

### Manuelle Installation

Gehen Sie folgendermaßen vor, um die PHP-Client-Bibliotheken für Azure manuell herunterzuladen:

> [AZURE.NOTE] Die PHP-Clientbibliotheken für Azure besteht eine Abhängigkeit zu den [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), [Mail_mime](http://pear.php.net/package/Mail_mime), und [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR-Paketen. Die empfohlene Methode zum Auflösen dieser Abhängigkeiten ist, um die Installation dieser Pakete mithilfe der [PEAR-Paket-Manager](http://pear.php.net/manual/en/installation.php).
 
1. Laden Sie eine ZIP-Datei, die die Bibliotheken aus enthält [GitHub][php-sdk-github]. Alternativ können Sie auch das Repository verzweigen und auf dem lokalen Computer klonen. Für die letztgenannte Option sind ein GitHub-Konto und eine lokale Installation von Git erforderlich.
    
2. Kopieren Sie das Verzeichnis `WindowsAzure` der heruntergeladenen ZIP-Datei in Ihre Anwendungsverzeichnisstruktur.

Weitere Informationen zum Installieren von PHP-Clientbibliotheken für Azure (einschließlich Informationen zur Installation eines PEAR-Pakets) finden Sie unter [Herunterladen des Azure SDK für PHP][download-SDK-PHP].

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar


