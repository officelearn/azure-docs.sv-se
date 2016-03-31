[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) unterstützt die fortlaufende Bereitstellung von Source Code Control und repositorytools wie BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial und TFS in Web-Apps. Sie können diese Tools verwenden, um den Inhalt und den Code für Ihre Anwendung zu verwalten und bei Bedarf schnell und einfach Änderungen an Ihrer Azure-Web-App vorzunehmen.

In diesem Artikel erfahren Sie, wie Git verwenden, direkt von Ihrem lokalen Computer in Web-Apps veröffentlichen (in Azure, heißt diese Methode der Veröffentlichung **Lokales Git**). Außerdem wird erläutert, wie Sie die fortlaufende Bereitstellung von Repositorywebsites aus ermöglichen, z. B. von BitBucket, CodePlex, Dropbox, GitHub oder Mercurial. Informationen zum Verwenden von TFS für die fortlaufende Bereitstellung finden Sie unter [Continuous delivery to Azure using Visual Studio Team Services].

> [AZURE.NOTE] Viele der in diesem Artikel beschriebenen Git-Befehle werden automatisch ausgeführt, beim Erstellen einer Web-app mit der [Azure-Befehlszeilentools für Mac und Linux](/develop/nodejs/how-to-guides/command-line-tools/).

## <a id="Step1"></a>Schritt 1: Installieren von Git

Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Finden Sie unter [Installing Git] zu betriebssystemspezifischen Distributionen und zur Installation.

> [AZURE.NOTE] In einigen Betriebssystemen, eine Befehlszeilenversion und eine GUI Version von Git verfügbar sind. Die in diesem Artikel bereitgestellten Anweisungen verwenden die Befehlszeilenversion.

## <a id="Step2"></a>Schritt 2: Erstellen eines lokalen Repositorys

Führen Sie die folgenden Aufgaben durch, um ein neues Git-Repository zu erstellen.

1. Erstellen Sie ein Verzeichnis mit dem Namen "MyGitRepository" für Ihr Git-Repository und die Dateien Ihrer Web-App.

2. Öffnen Sie ein Befehlszeilentool wie z. B. **GitBash** (Windows) oder **Bash** (Unix Shell). Auf OS X-Systemen können Sie die Befehlszeile über zugreifen der **Terminal** Anwendung.

3. Wechseln Sie in der Befehlszeile in das Verzeichnis "MyGitRepository".

        cd MyGitRepository

4. Verwenden Sie den folgenden Befehl, um ein neues Git-Repository zu initialisieren.

        git init

    Dies sollte eine Meldung wie zurückgeben **leeres Git-Repository unter [Pfad] initialisiert**.

## <a id="Step3"></a>Schritt 3:Hinzufügen einer Webseite

Web-Apps unterstützen in verschiedenen Programmiersprachen erstellte Anwendungen. In diesem Beispiel verwenden Sie eine statische HTML-Datei.

1. Erstellen Sie mit einem Texteditor eine neue Datei namens **index.html** im Stammverzeichnis des Git-Repositorys (das Verzeichnis "mygitrepository", die Sie zuvor erstellt haben).

2. Geben Sie den folgenden Text als Inhalt der Datei index.html ein, und speichern Sie die Datei.

        Hello Git!

3. Stellen Sie mit der Befehlszeile sicher, dass Sie sich im Stammverzeichnis Ihres Git-Repositorys befinden. Verwenden Sie folgenden Befehl zum Hinzufügen der **index.html** Datei im Repository:

        git add index.html 

    > [AZURE.NOTE] Sie können Hilfeinformationen für jeden Git-Befehl durch Eingabe-Help "oder"--Help nach dem Befehl. Geben Sie als Parameteroptionen für den Befehl "add" beispielsweise "git add -help" ein, um Befehlszeilenhilfe zu erhalten, oder "git add --help", um ausführlichere Hilfe anzuzeigen.

4. Übernehmen Sie dann die Änderungen am Repository mithilfe des folgenden Befehls:

        git commit -m "Adding index.html to the repository"

    Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        [master (root-commit) 369a79c] Adding index.html to the repository
         1 file changed, 1 insertion(+)
         create mode 100644 index.html

## <a id="Step4"></a>Aktivieren des Web-App-Repositorys

Führen Sie die folgenden Schritte durch, um ein Git-Repository für Ihre Web-App zu aktivieren.

1. Melden Sie sich auf die [Azure Portal].

2. Klicken Sie im Blatt für Ihre Web-app auf **Settings > fortlaufende Bereitstellung**. Klicken Sie auf **Quelle auswählen**, klicken Sie dann auf **lokale Repository**, und klicken Sie dann auf **OK**.  

    ![Lokales Git-Repository](./media/publishing-with-git/azure1-local-git.png)

4. Wenn Sie zum ersten Mal ein Repository in Azure einrichten, müssen Sie dafür Anmeldeinformationen erstellen. Sie verwenden diese, um eine Anmeldung beim Azure-Repository vorzunehmen und Änderungen aus Ihrem lokalen Git-Repository mithilfe von Push zu übertragen. Klicken Sie auf dem Blatt Ihrer Web-app auf **Einstellungen > Anmeldeinformationen für die Bereitstellung**, konfigurieren Sie Ihre Bereitstellung Benutzername und Kennwort. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>Bereitstellen des Projekts

* [Übertragen lokaler Dateien zu Azure mithilfe von Push (Lokales Git)](#Step6)
* [Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step7)
* [Bereitstellen einer Visual Studio-Projekts aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial](#Step75)

Führen Sie die folgenden Schritte durch, um Ihre Web-App mit einem lokalen Git in Azure zu veröffentlichen:

1. Klicken Sie im Blatt für Ihre Web-app auf **Einstellungen > Eigenschaften** für die **Git-URL**.

    ![](./media/publishing-with-git/azure3-repo-details.png)

    **Git-URL** ist die remotereferenz zur Bereitstellung von Ihrem lokalen Repository auf. Sie verwenden diese URL in den folgenden Schritten.

1. Stellen Sie unter Verwendung der Befehlszeile sicher, dass das Stammverzeichnis Ihres lokalen Git-Repositorys, das die vorher erstellte Datei "index.html" enthält, das aktuelle Verzeichnis ist.

2. Verwendung `git remote` den in aufgeführten Remotebezug hinzufügen **Git URL** aus Schritt 1. Der Befehl sieht etwa wie folgt aus:

        git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] Der **remote** Befehl fügt einen benannten Verweis auf einem remote-Repository. In diesem Beispiel wird ein Verweis mit dem Namen "azure" für das Repository Ihrer Web-App erstellt.

1. Verwenden Sie Folgendes an der Befehlszeile, um die aktuellen Repository-Inhalte aus dem lokalen Repository per Push in das "azure"-Remote-Repository zu übertragen:

        git push azure master

    Sie werden zum Eingeben des Kennworts aufgefordert, das vorher beim Zurücksetzen Ihrer Anmeldeinformationen für die Bereitstellung im Portal erstellt wurde. Geben Sie das Kennwort ein (beachten Sie, dass Gitbash keine Sternchen in der Konsole anzeigt, wenn Sie Ihr Kennwort eingeben). Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

        Counting objects: 6, done.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (6/6), 486 bytes, done.
        Total 6 (delta 0), reused 0 (delta 0)
        remote: New deployment received.
        remote: Updating branch 'master'.
        remote: Preparing deployment for commit id '369a79c929'.
        remote: Preparing files for deployment.
        remote: Deployment successful.
        To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
        * [new branch]      master -> master

    > [AZURE.NOTE] Das Repository für Ihre Web-app pushanforderungen erwartet erstellt die <strong>master</strong> Verzweigung des Repositorys, die dann als Inhalt der Web-app verwendet werden.

2. Kehren Sie zum Blatt Ihrer Web-App im Azure-Portal zurück. **Keine Bereitstellung gefunden** sollte geändert werden, um **aktive Bereitstellung** mit einen Protokolleintrag des letzten pushvorgangs. 

    ![](./media/publishing-with-git/azure4-deployed.png)

2. Klicken Sie auf den Link unter **URL** am oberen Rand Blatt zu überprüfen, ob die **index.html** bereitgestellt wurde. Eine Seite mit "Hallo Git!" wird angezeigt.

    ![Eine Webseite mit "Hallo Git!"][hello-git]

3. Ändern Sie mithilfe eines Text-Editors die **index.html** Datei, sodass sie "Juhu!" enthält, und speichern Sie die Datei.

4. Verwenden Sie die folgenden Befehle an der Befehlszeile, um **Hinzufügen** und **Commit** ändert, und klicken Sie dann **Push** die Änderungen an den remote-Repository:

        git add index.html
        git commit -m "Celebration"
        git push azure master

    Sobald die **Push** Befehl abgeschlossen wurde, aktualisieren Sie den Browser (möglicherweise müssen Sie drücken STRG + F5, für den Browser ordnungsgemäß zu aktualisieren), und beachten Sie, dass der Inhalt der Seite jetzt die letzte übernommene Änderung wiedergibt.

### <a id="Step7"></a>Bereitstellen von Dateien aus einer Repository-Website wie BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Push lokale Dateien ermöglicht zu Azure mithilfe von Local Git Ihnen manuell übertragen von Updates aus einem lokalen Projekt zur Web-app in Azure, bei der Bereitstellung aus BitBucket, CodePlex, Dropbox, GitHub oder mercurial zu einem fortlaufenden Bereitstellungsprozess, in Azure in die neuesten Updates aus Ihrem Projekt ziehen.

Zwar führen beide Methoden dazu, dass Ihr Projekt in Web-Apps bereitgestellt wird, die fortlaufende Bereitstellung ist jedoch nützlich, wenn mehrere Personen an einem Projekt arbeiten und Sie sicherstellen möchten, dass die aktuelle Version stets veröffentlicht wird, unabhängig davon, wer das letzte Update durchgeführt hat. Die fortlaufende Bereitstellung bietet sich auch an, wenn Sie eines der oben erwähnten Tools als zentrales Repository für Ihre Anwendung verwenden.

Das Bereitstellen von Dateien über GitHub, CodePlex oder BitBucket setzt voraus, dass Sie Ihr lokales Projekt auf einem dieser Dienste veröffentlicht haben. Weitere Informationen zum Veröffentlichen Ihres Projekts auf diesen Diensten finden Sie unter [Create a Repo (GitHub)], [Using Git with CodePlex], [Create a Repo (BitBucket)], [Using Dropbox to Share Git Repositories], oder [Quick Start - Mercurial].

1. Kopieren Sie die Dateien Ihrer Web-App zunächst in das ausgewählte Repository, das für die fortlaufende Bereitstellung verwendet wird.

2. Klicken Sie im Blatt Ihrer Web-app im Portal auf **Settings > kontinuierliche Bereitstellung**. Klicken Sie auf **Quelle auswählen**, klicken Sie dann auf **GitHub**, z. B..  

    ![](./media/publishing-with-git/azure6-setup-github.png)
    
2. In der **kontinuierliche Bereitstellung** Blatt, klicken Sie auf **Autorisierung**, klicken Sie dann auf **Autorisieren**. Das Azure-Portal leitet Sie an die Repository-Site weiter, um die Autorisierung abzuschließen. 

4. Wenn Sie fertig sind, wechseln Sie zurück zum Azure-Portal, und klicken Sie auf **OK** in die **Autorisierung** Blatt.

5. In der **kontinuierliche Bereitstellung** Blatt, wählen Sie die Organisation, Projekt und Verzweigung aus bereitgestellt werden soll. Wenn Sie fertig sind, klicken Sie auf **OK**.
  
    ![](./media/publishing-with-git/azure7-setup-github-configure.png)

    > [AZURE.NOTE] Wenn Sie fortlaufende Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

Azure erstellt eine Zuordnung für das ausgewählte Repository und überträgt die Dateien mithilfe von Pull aus dem angegebenen Pfad. Nach Abschluss dieses Prozesses die **Bereitstellung** Abschnitt des Blatts Ihrer Web-app angezeigt wird ein **aktive Bereitstellung** Nachricht, die Bereitstellung war erfolgreich.

7. An diesem Punkt wurde Ihr Projekt aus dem Repository Ihrer Wahl in Ihrer Web-App bereitgestellt. Um sicherzustellen, dass die Web-app aktiv ist, klicken Sie auf die **URL** am oberen Rand des Portals. Der Browser sollte zur Web-App wechseln.

8. Um sicherzustellen, dass die fortlaufende Bereitstellung aus dem Repository Ihrer Wahl stattfindet, übertragen Sie eine Änderung per Push in das Repository. Ihre Web-App sollte aktualisiert werden, um die Änderung kurz nach dem Push ins Repository wiederzugeben. Sie können überprüfen, ob die Aktualisierung abgerufen wurde die **Bereitstellungen** Blatt der Web-app.

### <a id="Step75"></a>Bereitstellen von Visual Studio-Projekten aus BitBucket, CodePlex, Dropbox, GitHub oder Mercurial

Die Übertragung einer Visual Studio-Projektmappe in Web-Apps in Azure App Service per Push erfordert lediglich das einfache Übertragen einer Datei "index.html". Der Web-App-Bereitstellungsprozess optimiert alle Details, darunter NuGet-Wiederherstellungsabhängigkeiten und die Erstellung der Binärdateien der Anwendung. Sie können die Best Practices der Quellcodeverwaltung befolgen und Code ausschließlich in Ihrem Git-Repository verwalten und den Rest von der Web-App-Bereitstellung erledigen lassen.

Die Schritte zum Übertragen von Visual Studio-Projektmappe in Web-Apps ist identisch mit der in der [im vorherigen Abschnitt](#Step7), vorausgesetzt, dass Sie Ihre Projektmappe und das Repository wie folgt konfigurieren:

-   Im Stammverzeichnis Ihres Repositorys hinzufügen eine `.gitignore` Datei, und geben Sie alle Dateien und Ordner, die Sie z. B. aus dem Repository ausschließen möchten die `Obj`, `Bin`, und `packages` Ordner (finden Sie unter [Gitignore-Dokumentation](http://git-scm.com/docs/gitignore) für Informationen über die Formatierung). Beispiel:

        [Oo]bj/
        [Bb]in/
        *.user
        /TestResults
        *.vspscc
        *.vssscc
        *.suo
        *.cache
        *.csproj.user
        packages/*
        App_Data/
        /apps
        msbuild.log
        _app/
        nuget.exe

    >[AZURE.NOTE] Bei Verwendung von GitHub können Sie optional eine Visual Studio-spezifische .gitignore-Datei generieren, wenn Sie Ihr Repository alle allgemeinen temporären Dateien, Buildergebnisse, usw. enthält erstellen. Anschließend können Sie sie Ihren Anforderungen entsprechend anpassen.

-   Fügen Sie Ihrem Repository die gesamte Verzeichnisstruktur des Projekts hinzu, wobei sich die .sln-Datei im Stammverzeichnis des Repositorys befinden muss.

-   In Visual Studio-Projektmappe [Aktivieren NuGet Package Restore](http://docs.nuget.org/Consume/Package-Restore) damit Visual Studio automatisch fehlende Pakete wiederherstellt.

Nachdem Sie Ihr Repository wie beschrieben eingerichtet und Ihre Web-App in Azure für fortlaufende Veröffentlichung aus einem der Online-Git-Repositorys konfiguriert haben, können Sie Ihre ASP.NET-Anwendung lokal in Visual Studio entwickeln und Ihren Code fortlaufend bereitstellen, indem Sie einfach Ihre Änderungen mithilfe von Push an Ihr Online-Git-Repository übertragen.

## Deaktivieren der fortlaufenden Bereitstellung

Fortlaufende Bereitstellung kann deaktiviert werden, aus der **Bereitstellungen** Blatt. Klicken Sie auf dem Blatt Ihrer Web-app auf **Settings > fortlaufende Bereitstellung**. Klicken Sie dann auf **trennen**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)  

Nach Beantwortung **Ja** die Bestätigungsnachricht können Sie zum Blatt Ihrer Web-app zurückkehren und auf **Settings > fortlaufende Bereitstellung** wenn Sie die Veröffentlichung aus einer anderen Quelle einrichten möchten.

## <a id="Step8"></a>Problembehandlung

Die folgenden Fehler und Probleme treten häufiger auf, wenn Git zum Veröffentlichen auf einer Web-App in Azure verwendet wird:

****

**Symptom**: kein Zugriff auf "SiteURL": Fehler beim Verbinden mit [ScmAddress]

**Ursache**: Dieser Fehler kann auftreten, wenn die Web-app nicht ausgeführt wird.

**Auflösung**: Starten Sie die Web-app im Azure-Portal. Die Git-Bereitstellung funktioniert nur dann, wenn die Web-App ausgeführt wird. 


****

**Symptom**: Host "Hostname" konnte nicht aufgelöst werden.

**Ursache**: Dieser Fehler kann auftreten, wenn die beim Erstellen des 'Azure'-Remotewebsite eingegebenen Adressinformationen falsch waren.

**Auflösung**: Verwenden der `git remote -v` Befehl aus, um alle Remotewebsites jeweils zusammen mit den zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.

****

**Symptom**: keine Refs gemeinsam und none angegeben; nichts tun. Sie sollten vielleicht eine Verzweigung wie 'master' angeben.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie beim Ausführen eines Git push-Vorgangs und den von Git verwendeten "Push.Default"-Wert nicht festgelegt haben keine Verzweigung angeben.

**Auflösung**: Ausführen den Push-Vorgang erneut, den master-Pfad angeben. Beispiel:

    git push azure master

****

**Symptom**: Src Refspec [verzweigungsname] stimmt nicht überein.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, auf eine andere Master Verzweigung auf "Azure" remote zu übertragen.

**Auflösung**: Ausführen den Push-Vorgang erneut, den master-Pfad angeben. Beispiel:

    git push azure master

****

**Symptom**: Fehler: Änderungen an remote-Repository, aber Ihre Web-app nicht aktualisiert.

**Ursache**: Dieser Fehler kann auftreten, wenn Sie bereitstellen eine Node.js-Anwendung mit einer package.json-Datei, die zusätzliche erforderliche Module angibt.

**Auflösung**: zusätzliche Meldungen mit "Npm ERR!" sollte vor dieser Fehler protokolliert und können zusätzlichen Kontext für den Fehler bereitstellen. Folgende bekannte Ursachen für diesen Fehler und die entsprechende "Npm ERR!" Nachricht:

* **Ungültige package.json-Datei**: Npm ERR! Couldn't read dependencies.

* **Systemeigenes Modul, die nicht binäre Distribution für Windows**:

    * npm ERR! \'cmd "/ C" "Node-Gyp neu erstellen" \ "Fehler bei 1

        OR

    * npm ERR! [modulename@version] Preinstall: \'make || Gmake\'


## Zusätzliche Ressourcen

* [Verwenden von PowerShell für Azure]
* [Verwenden des Azure-Befehlszeilentools für Mac und Linux]
* [Git-Dokumentation]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren, bevor Sie sich für ein Azure-Konto anmelden möchten, wechseln Sie zu [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751), wo Sie eine kurzlebige Starter-Web-app sofort in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[How to use PowerShell for Azure]: ../articles/install-configure-powershell.md
[How to use the Azure Command-Line Tools for Mac and Linux]: ../articles/xplat-cli-install.md
[Git Documentation]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Create a Repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Create a Repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Using Dropbox to Share Git Repositories]: https://gist.github.com/trey/2722927
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md


