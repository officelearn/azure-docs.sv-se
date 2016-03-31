Azure Marketplace bietet eine breite Auswahl an beliebten Web-Apps, die von Microsoft, anderen Unternehmen oder Open Source-Softwareinitiativen entwickelt wurden. Aus dem Azure Marketplace erstellte Web-apps erfordern keine Installation von Software, die außer dem Browser zur Verbindung mit dem [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715). 

In diesem Lernprogramm lernen Sie Folgendes:

- Erstellen einer neuen Web-App mit dem Azure Marketplace

- Bereitstellen der Web-App über das Azure-Vorschauportal
 
Sie erstellen einen WordPress-Blog unter Verwendung einer Standardvorlage. In der folgenden Abbildung wird die fertige Anwendung dargestellt:


![Wordpress-Blog][13]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren, bevor Sie sich für ein Azure-Konto anmelden möchten, wechseln Sie zu [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751), wo Sie eine kurzlebige Starter-Web-app sofort in App Service erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen einer Web-App im Portal

1. Melden Sie sich beim Azure-Vorschauportal an.

2. Öffnen Sie den Azure Marketplace entweder durch Klicken auf die **Marketplace** Symbol:

    ![Marketplace-Symbol][marketplace]

    Oder durch Klicken auf das **Neu** Symbol oben rechts im Dashboard, und wählen **Marketplace** auf den Eintrag der Liste.
    
    ![Neu erstellen][5]
    
3. Wählen Sie **Web + Mobile**. Suchen Sie nach **WordPress** und klicken Sie auf die **WordPress** Symbol.

    ![WordPress-Symbol in der Liste][7]
    
5. Wählen Sie nach dem Lesen der Beschreibung der WordPress-app, **Erstellen**.

6. Klicken Sie auf **Web-app**, und geben Sie die erforderlichen Werte zum Konfigurieren der Web-app.
    
    ![App konfigurieren][8]

7. Klicken Sie auf **Datenbank**, und geben Sie die erforderlichen Werte für die MySQL-Datenbank konfigurieren. 

    ![Datenbank konfigurieren][database]

8. Geben Sie einen Namen für die neue Ressourcengruppe an.

    ![Ressourcengruppe festlegen][groupname]

8. Klicken Sie gegebenenfalls auf **Abonnement**, und geben Sie das Abonnement zu verwenden. 

7. Wenn Sie die Definition der Web-app abgeschlossen haben, klicken Sie auf **Erstellen**, und warten Sie, während die neue Web-app erstellt wird.

   Sobald die App erstellt ist, sehen Sie eine Ressourcengruppe mit der Web-App und der Datenbank.

   ![Gruppe anzeigen][resourcegroup]

## Starten und Verwalten Ihrer WordPress-Web-App
    
1. Klicken Sie auf die neue Web-App, um deren Details anzuzeigen.

    ![Dashboard starten][10]

2. Auf der **Essentials** auf entweder **Durchsuchen** oder den Link unter **Url** Willkommensseite der Web-app zu öffnen.

    ![Website-URL][browse]

3. Wenn Sie WordPress nicht installiert haben, geben Sie die erforderlichen WordPress, und klicken Sie auf Konfigurationsinformationen **WordPress installieren** Konfiguration abzuschließen und die Web-app-Anmeldeseite zu öffnen.

4. Klicken Sie auf **Anmeldung** und geben Sie Ihre Anmeldeinformationen.  

5. Sie besitzen nun eine neue WordPress-Web-App, die der nachfolgenden Web-App ähnlich ist.    

    ![Ihre WordPress-Website][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png


