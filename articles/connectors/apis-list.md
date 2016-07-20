<properties
    pageTitle="Lista över Microsoft-hanterade anslutningar för användning i  logikappar i Microsoft Azure | Microsoft Azure Apptjänst"
    description="Hämta en fullständig lista över de Microsoft-hanterade anslutningar som du kan använda för att skapa logikappar i Azure Apptjänst"
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="deonhe"/>

# Lista över hanterade anslutningar

>[AZURE.NOTE] Den här versionen av artikeln gäller förhandsschemaversionen för logikappar från 2015-08-01. Om du vill se förhandsschemaversionen från 2014-12-01 klickar du på [Lista över anslutningar](../app-service-logic/app-service-logic-connectors-list.md). 

Prisinformation och en lista över vad som ingår i varje tjänstnivå finns i [Priser för Azure Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto går du till [Prova logikapp](https://tryappservice.azure.com/?appservice=logic). Du kan genast skapa en tillfällig startlogikapp i Apptjänsten. Inget kreditkort krävs, och du gör inga åtaganden.

Välj en ikon och lär dig att snabbt använda de här anslutningarna för att skapa appar som anropar de här tjänsterna. Anslutningarna kan sedan användas för att skapa logikappar, PowerApps och flöden.

|Anslutningar||||
|-----------|-----------|-----------|-----------|
|[![API Icon][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API Icon][boxicon]<br/>**Box**][boxDoc]|[![API Icon][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![API Icon][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![API Icon][facebookicon]<br/>**Facebook**][facebookdoc]|[![API Icon][ftpicon]<br/>**FTP**][ftpdoc]|[![API Icon][githubicon]<br/>**GitHub**][githubdoc]|[![API Icon][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![API Icon][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![API Icon][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Users**][office365usersdoc]|
|[![API Icon][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|[![API Icon][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API Icon][onedriveicon]<br/>**OneDrive<br/>for Business**][onedriveforbusinessdoc]|[![API Icon][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![API Icon][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![API Icon][rssicon]<br/>**RSS**][rssdoc]|[![API Icon][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API Icon][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![API Icon][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API Icon][sftpicon]<br/>**SFTP**][sftpdoc]|[![API Icon][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![API Icon][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![API Icon][smtpicon]<br/>**SMTP**][smtpdoc]|[![API Icon][sqlicon]<br/>**SQL Azure**][sqldoc]|[![API Icon][trelloicon]<br/>**Trello**][trellodoc]|[![API Icon][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![API Icon][twittericon]<br/>**Twitter**][twitterdoc]|[![API Icon][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![API Icon][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Om du har skapat logikappar med hjälp av förhandsschemat från 2014-12-01 märker du säkert att anslutningarna för företagsintegration som t.ex. de för BizTalk inte finns med i listan ovan. Vi vet att de är viktiga och vi jobbar hårt för att göra dem tillgängliga så fort som möjligt. Vi kan inte ge dig ett exakt datum då de kommer att vara tillgängliga men det är en av våra främsta prioriteringar. Under tiden kan du komma åt dina [v1 API:er och BizTalk-API:er från Logikappar](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Tack för att du har förståelse. Håll ögonen öppna.


### Anslutningar kan vara utlösare
Flera anslutningar innehåller utlösare som kan meddela din app när specifika händelser äger rum. FTP-anslutningen innehåller till exempel utlösaren OnUpdatedFile . Du kan skapa antingen en logikapp, en PowerApp eller ett flöde som lyssnar av utlösaren och vidtar en åtgärd när utlösaren aktiveras.

Det finns två typer av utlösare:  

* Sökningsutlösare: Dessa utlösare avsöker din tjänst med en frekvens som är angiven för att söka efter nya data. När det finns nya data körs en ny instans av din app med dessa data som indata. För att förhindra att samma data används flera gånger kan utlösaren rensa data som har lästs in och skickats till appen.
* Push-utlösare: de här utlösarna lyssnar efter data på en slutpunkt eller efter att en händelse ska inträffa. Sedan utlöser de en ny instans av appen. Twitter-anslutningen är ett exempel på en sådan.


### Anslutningar kan vara åtgärder
Anslutningar kan också användas som åtgärder i dina appar. Åtgärder är användbara för att söka efter data som sedan kan användas då appen körs. Du kan till exempel behöva söka efter kunddata från en SQL-databas vid bearbetningen av en order. Eller så kan du behöva skriva, uppdatera eller ta bort data i en destinationstabell. Du kan göra detta med de åtgärder som tillhandahålls av anslutningarna. Åtgärder mappar till åtgärder som har definierats i Swagger-metadata.


[Nyheter](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Skapa en logikapp redan nu](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Kom igång med PowerApps redan nu](../power-apps/powerapps-get-started-azure-portal.md)  
[Migrera befintliga logikappar till den senaste schemaversionen](connectors-schema-migration.md) 

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Anslut till Azure blob för att hantera filer i din blobbehållare."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Sök i Bing efter webbmaterial, bilder, nyheter och videor."
[boxDoc]: ./connectors-create-api-box.md "Ansluter till Box och kan ladda upp, hämta, ta bort, lista och utföra många andra filaktiviteter."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Anslut till Dynamics CRM Online och gör mer med dina CRM Online-data."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Ansluter till Dropbox och kan hämta, ta bort, lista och utföra många andra filaktiviteter."
[exceldoc]: ./connectors-create-api-excel.md "Anslut till Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Anslut till Facebook och skicka till en tidslinje, hämta ett sidflöde och mycket annat."
[ftpdoc]: ./connectors-create-api-ftp.md "Ansluter till en FTP-/FTPS-server och utför olika FTP-aktiviteter, som t.ex. att ladda upp, hämta och ta bort filer samt mycket annat."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Anslut till GoogleDrive och interagera med dina data."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Office 365-kopplingen kan skicka och ta emot mejl, hantera din kalender och hantera dina kontakter med hjälp av ditt Office 365-konto."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Ansluter till ditt privata Microsoft OneDrive och laddar upp, tar bort och listar filer samt mycket annat."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Ansluter till ditt Microsoft OneDrive för företag och laddar upp, tar bort och listar filer samt mycket annat."
[outlookdoc]: ./connectors-create-api-outlook.md "Anslut till din postlåda i Outlook och få åtkomst till din e-post och mycket annat."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Ansluter till Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "Med RSS-anslutningen kan användarna publicera och hämta flödesobjekt. Användarna kan också utlösa åtgärder när en nytt objekt publiceras i flödet."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto och hantera konton, leads, affärsmöjligheter och mycket annat."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Ansluter till Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Kan skicka meddelanden från Service Bus-köer och -ämnen och ta emot meddelanden från Service Bus-köer och -prenumerationer."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Ansluter till SharePoint Online och hanterar dokument och listobjekt."
[slackdoc]: ./connectors-create-api-slack.md "Anslut till Slack och skicka meddelanden till Slack-kanaler."
[sftpdoc]: ./connectors-create-api-sftp.md "Ansluter till SFTP och kan ladda upp, hämta och ta bort filer samt mycket annat."
[githubdoc]: ./connectors-create-api-github.md "Ansluter till GitHub och kan spåra problem."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Skicka bättre e-post."
[smtpdoc]: ./connectors-create-api-smtp.md "Ansluter till en SMTP-server och kan skicka e-post med bifogade filer."
[sqldoc]: ./connectors-create-api-sqlazure.md "Ansluter till SQL Azure-databasen. Du kan skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell."
[trellodoc]: ./connectors-create-api-trello.md "Trello är det kostnadsfria, flexibla och visuella sättet att organisera saker och ting med folk."
[twiliodoc]: ./connectors-create-api-twilio.md "Ansluter till Twilio och kan skicka och hämta meddelanden, hämta tillgängliga nummer, hantera inkommande telefonnummer och mycket annat."
[twitterdoc]: ./connectors-create-api-twitter.md "Ansluter till Twitter och hämtar tidslinjer, skickar tweets och mycket annat."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Synkronisera din vardag."
a[yammerdoc]: ./connectors-create-api-yammer.md "Anslut till Yammer och skicka meddelanden och hämta nya meddelanden."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png



<!--HONumber=Jun16_HO2-->


