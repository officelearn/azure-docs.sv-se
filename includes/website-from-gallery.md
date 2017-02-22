Via Azure Marketplace får du tillgång till en mängd olika populära webbappar som har utvecklats av Microsoft och tredjepartsföretag samt programvara med öppen källkod, Webbappar som skapats från Azure Marketplace kräver inte installation av någon annan programvara än webbläsaren som används för att ansluta till [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715). 

I den här självstudiekursen lär du dig:

* Hur du skapar en ny webbapp via Azure Marketplace.
* Hur du distribuerar webbappen via Azure Preview Portal.

Du ska skapa en WordPress-blogg som använder en standardmall. Följande bild visar det färdiga programmet:

![WordPress-blogg][13]

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

## <a name="create-a-web-app-in-the-portal"></a>Skapa en webbapp på portalen
1. Logga in på Azure Preview Portal.
2. Öppna Azure Marketplace antingen genom att klicka på **Marketplace**-ikonen:
   
    ![Marketplace-ikon][marketplace]
   
    Eller genom att klicka på ikonen **Nytt** längst upp till höger på instrumentpanelen och välja **Marketplace** längst ned i listan.
   
    ![Skapa ny][5]
3. Välj **Webb + mobilt**. Leta upp **WordPress** och klicka på **WordPress**-ikonen.
   
    ![WordPress från lista][7]
4. När du har läst igenom beskrivningen av WordPress-appen väljer du **Skapa**.
5. Klicka på **Webbapp** och ange de värden som krävs för att konfigurera webbappen.
   
    ![konfigurera appen][8]
6. Klicka på **Databas** och ange de värden som krävs för att konfigurera MySQL-databasen. 
   
    ![konfigurera databasen][database]
7. Ange ett namn för en ny resursgrupp.
   
    ![Ange resursgrupp][groupname]
8. Om det behövs klickar du på **PRENUMERATION** och anger vilken prenumeration du vill använda. 
9. När du har definierat webbappen klickar du på **Skapa** och väntar medan den nya webbappen skapas.
   
   När appen har skapats ser du resursgruppen som innehåller webbappen och databasen.
   
   ![visa grupp][resourcegroup]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Starta och hantera WordPress-webbappen
1. Klicka på den nya webbappen för att visa information om appen.
   
    ![starta instrumentpanelen][10]
2. På sidan **Information** klickar du antingen på **Bläddra** eller på länken under **URL** för att öppna webbappens välkomstsida.
   
    ![webbadress][browse]
3. Om du inte har installerat WordPress anger du konfigurationsinformationen som krävs av WordPress och klickar på **Installera WordPress** för att slutföra konfigurationen och öppna webbappens inloggningssida.
4. Klicka på **Logga in** och ange dina autentiseringsuppgifter.  
5. Nu har du en ny WordPress-webbapp som liknar webbappen nedan.    
   
    ![din WordPress-plats][13]

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


<!--HONumber=Jan17_HO3-->


