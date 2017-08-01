---
title: "Anslutningsappar för Azure Logic Apps | Microsoft Docs"
description: Bygg och skapa logikappar bland alla Microsoft-hanterade anslutningsappar
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f1f1fd50-b7f9-4d13-824a-39678619aa7a
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1e2dad92647630c8b9a4e7676eee9288c87daf44
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017


---
# <a name="connectors-list"></a>Lista över anslutningsappar
> [!TIP]
> I den [fullständiga A-Z-listan](#az) (i det här avsnittet) listas alla tillgängliga anslutningsappar du kan använda i Logic Apps. [Information om anslutningen](/connectors/) visar en lista över alla utlösare och åtgärder som definierats i swagger och visar också eventuella gränser för varje anslutning.

Anslutningsappar är en viktig del när du skapar logikappar. Med sådana anslutningsappar kan du verkligen utöka dina lokala och molnbaserade program för att göra olika saker med data som du skapar och data du redan har. Anslutningsapparna finns i följande kategorier: 

* **Standardanslutningsappar**: Automatiskt tillgängliga och ingår när du använder logikappar. Vissa exempel är Service Bus, Power BI, Oracle Database, OneDrive och många fler.

* **Anslutningsappar för integrationskonton**: De här är tillgängliga när du köper ett integrationskonto. Med sådana anslutningsappar kan du omvandla och validera XML, bearbeta business-to-business-meddelanden med AS2 / X12 / EDIFACT och koda och avkoda flata filer. Om du arbetar med BizTalk Server är de här anslutningsapparna en bra metod för att utöka dina BizTalk-arbetsflöden till Azure.  

    BizTalk Server har också en [Logic Apps-adapter](https://msdn.microsoft.com/library/mt787163.aspx) för mottagning från en logikapp och sändning till en logikapp.

* **Enterprise-anslutningsappar**: Innehåller MQ och SAP. Tillgängligt för en extra kostnad. 

I informationen om [Logic Apps-priser](https://azure.microsoft.com/pricing/details/logic-apps/) och [prismodellen](../logic-apps/logic-apps-pricing.md) kan du läsa mer om kostnaderna. 

## <a name="popular-connectors"></a>Populära anslutningsappar
Det finns tusentals program och miljontals körningar som bearbetar data och information med hjälp av dessa anslutningsappar. I följande tabell anges de populäraste och några favoriter hos våra användare:

| |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][AzureBlobStorageicon]<br/>**Azure Blob<br/>Storage**][AzureBlobStoragedoc] | Om du vill automatisera alla aktiviteter med ditt lagringskonto bör du titta på den här anslutningsappen. Stöder CRUD-åtgärder (skapa, läsa, uppdatera, ta bort). | [![API Icon][Azure-Functionsicon]<br/>**Azure Functions**][azure-functionsdoc] | Skapa funktioner som kör anpassade fragment för C# eller node.js och använd sedan använda dessa funktioner i logikapparna.  |
| [![API Icon][Dynamics-365icon]<br/>**Dynamics 365<br/>CRM Online**][Dynamics-365doc] | Detta är ett av de mest efterfrågade anslutningsprogrammen. Den har utlösare och åtgärder för att automatisera arbetsflöden med leads och mycket mer. | [![API Icon][Event-Hubs-icon]<br/>**Event Hubs**][event-hubs-doc] | Använda och publicera händelser i en Event Hub. Du kan till exempel hämta utdata från din logikapp med Event Hubs och sedan skicka dem till en leverantör av realtidsanalys. |
| [![API Icon][FTPicon]<br/>**FTP**][FTPdoc] | Om FTP-servern är tillgänglig från internet kan du automatisera arbetsflöden att arbeta med filer och mappar. <br/><br/>Det finns också SFTP med SFTP-anslutningsappen. | [![API Icon][HTTPicon]<br/>**HTTP**][httpdoc] | Använd logikappar för att kommunicera med valfri slutpunkt över HTTP. |
| [![API Icon][Office-365-Outlookicon]<br/>**Office 365<br/>Outlook**][office365-outlookdoc] | Massor av utlösare och många fler åtgärder för att använda e-post och händelser för Office 365 i dina arbetsflöden. <br/><br/>Den här anslutningsappen innehåller ett *e-postmeddelande med godkännande* för att godkänna semesteransökningar, utgiftsrapporter och så vidare. <br/><br/>Office 365-användare är också tillgängliga med anslutningsappen för Office 365-användare.| [![API Icon][HTTP-Requesticon]<br/>**Begäran/svar**][HTTP-Requestdoc] | Den här anslutningsappen tillhandahåller en HTTPS-URL. När logikappen tar emot en begäran till denna URL startar logikappen. |
| [![API Icon][Salesforceicon]<br/>**Salesforce**][salesforcedoc] | Logga enkelt in med ditt Salesforce-konto för att få åtkomst till objekt som leads med mera. |  [![API Icon][Service-Busicon]<br/>**Service Bus**][Service-Busdoc] | Den populäraste anslutningsappen i Logic Apps, den innehåller utlösare och åtgärder för att göra asynkrona meddelanden och publicera/prenumerera på köer, prenumerationer och avsnitt. |
|  [![API Icon][SharePointicon]<br/>**SharePoint<br/>Online**][SharePointdoc] | Om du gör något med SharePoint och kan dra nytta av automatisering rekommenderar vi att du tittar på den här anslutningsappen. Kan användas med lokal SharePoint och SharePoint Online. | [![API Icon][SQL-Servericon]<br/>**SQL Server**][SQL-Serverdoc] | En av de mest använda anslutningsapparna. Den kan ansluta till en lokal SQL Server och en Azure SQL Database. | 
| [![API Icon][Twittericon]<br/>**Twitter**][Twitterdoc] | Logga enkelt in med ett Twitter-konto, och påbörja sedan ett arbetsflöde när en ny tweet postas. Spara sedan dessa tweets till en SQL Database eller SharePoint-lista. | | | 

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton 

Enterprise-integrationspaketet (EIP) innehåller anslutningsappar som är välkända för BizTalk Server-communityn. När du köper ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) får du också följande anslutningsappar: 

|  |  |  |  |
| --- | --- | --- | --- |
| [![API Icon][as2icon]<br/>**AS2-</br>avkodning**][as2decode] | [![API Icon][as2icon]<br/>**AS2-</br>kodning**][as2encode] | [![API Icon][x12icon]<br/>**EDIFACT-</br>avkodning**][EDIFACTdecode] | [![API Icon][x12icon]<br/>**EDIFACT-</br>kodning**][EDIFACTencode] |
[![API Icon][flatfileicon]<br/>**Flatfils</br>kodning**][flatfiledoc] | [![API Icon][flatfiledecodeicon]<br/>**Flatfils</br>avkodning**][flatfiledecodedoc] | [![API Icon][integrationaccounticon]<br/>**Integrations<br/>konto**][integrationaccountdoc] | [![API Icon][xmltransformicon]<br/>**Omvandla<br/>XML**][xmltransformdoc] |
| [![API Icon][x12icon]<br/>**X12-</br>avkodning**][x12decode] | [![API Icon][x12icon]<br/>**X12-</br>kodning**][x12encode] | [![API Icon][xmlvalidateicon]<br/>**XML-<br/>verifiering**][xmlvalidatedoc] | |

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Anslut dina företagsprogram med dina logikappar.

|  |  |
| --- | --- |
|[![API Icon][MQicon]<br/>**MQ**][mqdoc]|[![API Icon][SAPicon]<br/>**SAP**][sapconnector]|


## <a name="az"></a>Fullständig lista, A–Z

[Information om anslutningen](/connectors/) visar en lista över alla utlösare och åtgärder som definierats i swagger och visar också eventuella gränser för varje anslutning.

| | | | | | | | | | | | | |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| [**1**](#1) | [**A**](#a) | [**B**](#b) | [**C**](#c) | [**D**](#d) | [**E**](#e) | [**F**](#f) | [**G**](#g) | [**H**](#h) | [**I**](#i) | [**J**](#j) | [**L**](#l) | [**M**](#m) |
| [**N**](#n) | [**O**](#o) | [**P**](#p) | [**R**](#r) | [**S**](#s) | [**T**](#t) | [**U**](#u) | [**V**](#v) | [**W**](#w) | [**X**](#x) | [**Y**](#y) | [**Z**](#z) | | 

| | |
|---|---|
|<a name="1"></a>10to8-schemaläggning<br/><br/><a name="a"></a>Act!<br/>Adobe Creative Cloud<br/>appFigures<br/>[AS2][as2doc]<br/>Asana<br/>Azure Active Directory (AD)<br/>Azure API Management<br/>Azure App Services<br/>Azure Application<br/>Azure Automation<br/>[Azure Blob Storage][azureblobstoragedoc]<br/>Azure Data Lake<br/>Azure DocumentDB (Cosmos DB)<br/>[Azure Functions][azure-functionsdoc]<br/>[Azure Logic Apps][nested-logic-appdoc]<br/>AzureML<br/>Azure Queues<br/>Azure Resource Manager<br/>[Azure SQL Database][sql-serverdoc]<br/><br/><a name="b"></a>Basecamp 2<br/>Basecamp 3<br/>Batch<br/>Benchmark Email<br/>Bing Search<br/>Bitbucket<br/>Bitly<br/>BizTalk Server<br/>Blogger<br/>Box<br/>Buffer<br/><br/><a name="c"></a>Calendly<br/>Campfire<br/>Capsule CRM<br/>Chatter<br/>Cognito-formulär<br/>Prissättning för Cognitive Services – API för visuellt innehåll<br/>Ansikts-API för Cognitive Services<br/>Cognitive Services LUIS<br/>Cognitive Services-textanalys<br/>Common Data Service<br/>Konvertering av innehåll<br/>Kontroll-avsluta<br/>[Custom APIs / web apps][api/web-appdoc]<br/><br/><a name="d"></a>Dataåtgärder<br/>[DB2][db2doc]<br/>Disqus<br/>DocuSign<br/>Do Until<br/>Dropbox<br/>[Dynamics 365 CRM Online][Dynamics-365doc]<br/>Dynamics 365 for Financials<br/>Dynamics 365 for Operations<br/>Dynamics NAV<br/><br/><a name="e"></a>Easy Redmine<br/>EDIFACT<br/>[Event Hubs][event-hubs-doc]<br/>Eventbrite<br/><br/><a name="f"></a>Facebook<br/>[Filsystem][filesystemdoc]<br/>[Flat fil][flatfiledoc]<br/>FreshBooks<br/>Freshdesk<br/>Freshservice<br/>[FTP][ftpdoc]<br/><br/><a name="g"></a>GitHub<br/>Gmail<br/>Google Calendar<br/>Google Contacts<br/>Google Drive<br/>Google-blad<br/>Google-uppgifter<br/>GoToMeeting<br/>GoToTraining<br/>GoToWebinar<br/><br/><a name="h"></a>Harvest<br/>HelloSign<br/>HipChat<br/>[HTTP][httpdoc]<br/>[HTTP + Swagger][http-swaggerdoc]<br/>[HTTP-webhook][webhookdoc]<br/><br/><a name="i"></a>[Informix][informixdoc]<br/>Infusionsoft<br/>Inoreader<br/>Insightly<br/>Instagram<br/>Instapaper<br/>Integrationskonto<br/>Intercom | <a name="j"></a>JotForm<br/>JIRA<br/><br/><a name="l"></a>LeanKit<br/>LiveChat<br/><br/><a name="m"></a>MailChimp<br/>Mandrill<br/>Medel<br/>Microsoft Forms<br/>Microsoft Teams<br/>Microsoft Translator<br/>[MQ][mqdoc]<br/>MSN Väder<br/>Muhimbi PDF<br/>MySQL<br/><br/><a name="n"></a>Nexmo<br/><br/><a name="o"></a>[Office 365 Outlook][office365-outlookdoc]<br/>Office 365-användare<br/>Office 365 Video<br/>OneDrive<br/>OneDrive för företag<br/>OneNote (företag)<br/>[Oracle Database][oracle-db-doc]<br/>Outlook Customer Manager<br/>Outlook-uppgifter<br/>Outlook.com<br/><br/><a name="p"></a>PagerDuty<br/>Parserr<br/>Paylocity<br/>Pinterest<br/>Pipedrive<br/>Pivotal Tracker<br/>Planner<br/>PostgreSQL<br/>Power BI<br/>Project Online<br/><br/><a name="r"></a>Redmine<br/>[Begäran/svar][http-requestdoc]<br/>RSS<br/><br/><a name="s"></a>[Salesforce][salesforcedoc]<br/>[SAP Application Server][sapconnector]<br/>[SAP Message Server][sapconnector]<br/>[Schema][recurrencedoc]<br/>Omfång<br/>SendGrid<br/>Skicka meddelanden till en batch<br/>[Service Bus][service-busdoc]<br/>SFTP<br/>[SharePoint Online][sharepointdoc]<br/>[SharePoint Server][sharepointdoc]<br/>Slack<br/>Smartsheet<br/>SMTP<br/>SparkPost<br/>[SQL Server][sql-serverdoc]<br/>Stripe<br/>SurveyMonkey<br/>Switch Case<br/><br/><a name="t"></a>Teamwork Projects<br/>Teradata<br/>Todoist<br/>Toodledo<br/>[Omvandla XML][xmltransformdoc]<br/>Trello<br/>Twilio<br/>[Twitter][twitterdoc]<br/>Typeform<br/><br/><a name="u"></a>UserVoice<br/><br/><a name="v"></a>Variabler<br/>Vimeo<br/>Visual Studio Team Services<br/><br/><a name="w"></a>WebMerge<br/>WordPress<br/>Wunderlist<br/><br/><a name="x"></a>[X12][x12doc]<br/>[XML-verifiering][xmlvalidatedoc]<br/><br/><a name="y"></a>Yammer<br/>YouTube<br/><br/><a name="z"></a>Zendesk |

> [!TIP]
> För att komma igång med Azure Logic Apps innan du registrerar dig för ett Azure-konto går du till [Prova Logic Apps](https://tryappservice.azure.com/?appservice=logic). Du kan skapa en kortvarig startlogikapp omedelbart. Inget kreditkort krävs, och du gör inga åtaganden.

## <a name="connectors-as-triggers-and-actions"></a>Anslutningsappar som utlösare och åtgärder

En **utlösare** startar eller kör en instans på din logikapp. Flera anslutningsappar innehåller utlösare som kan meddela din app när specifika händelser äger rum. FTP-anslutningsappen har till exempel utlösaren `OnUpdatedFile` som meddelar din logikapp när en fil har uppdaterats. 

Logic Apps innehåller följande typer av utlösare:  

* *Sökningsutlösare*: Dessa utlösare avsöker din tjänst med en angiven frekvens för att söka efter nya data. 

    När det finns nya data körs en ny instans av din logikapp med data som indata. 

* *Push-utlösare*: de här utlösarna lyssnar efter data på en slutpunkt eller efter att en händelse ska inträffa och utlöser därefter en ny instans av din logikapp.

* *Upprepningsutlösare*: Den här utlösaren instantierar en instans av din logikapp på en app i ett förutbestämt schema.

Anslutningsapparna tillhandahåller också **åtgärder** som du kan använda i ditt arbetsflöde. Din logikapp kan till exempel söka efter data och sedan använda dessa data senare i din logikapp. Mer specifikt kan du låsa upp kunddata från en SQL Database och sean använda dessa kunddata för att bygga ditt arbetsflöde. 

> [!TIP]
> I [översikten över anslutningsappar](connectors-overview.md) finns mer information om utlösare och åtgärder. 


## <a name="message-manipulation-actions"></a>Åtgärder för att manipulera meddelanden

Logikappar innehåller inbyggda åtgärder som kan ändra eller manipulera nyttolastdata. Den inbyggda anslutningsappen **Dataåtgärder** innehåller följande åtgärder: 

| | |
|---|---|
| **Skriva** | Skapa eller generera värden eller objekt att använda senare eller när du skapar ditt arbetsflöde. Du kan exempelvis redigera ett JSON-objekt med värden från flera steg, eller beräkna en konstant för senare referens i en logikappkörning. |
| **Skapa CSV-tabell**<br/>**Skapa HTML-tabell** | Omvandla en matrisresultatuppsättning till en CSV- eller HTML-tabell. Lägg exempelvis till en åtgärd för att lista poster för CRM och lägg till ett filter för poster som lagts till idag. Skicka sedan resultatet som en HTML-tabell i ett e-postmeddelande. |
| **Filtrera matris** (fråga) | Filtrera ett resultat på de poster som intresserar dig. Du kan exempelvis söka på alla tweets med `#Azure` och sedan ”filtrera” de returnerade tweetsen så att de endast visar resultat som är `Tweeted_by_followers > 50`. |
| **Anslut dig** | Anslut dig till en matris med en avgränsare. Till exempel returnerar åtgärden för att identifiera nyckelfraser en matris med nyckelfraser. Du kan "ansluta" dig till dem med en `,` eller liknande. Så istället för `["Some", "Phrase"]` har du `"Some, Phrase"`. |
| **Parsa JSON** | Parsa ut och få åtkomst till värden från ett JSON-objekt i designern. Om till exempel Azure-funktionen returnerar en JSON-nyttolast kan du parsa den för att få åtkomst till JSON-egenskaperna senare i ett annat steg. Åtgärden validerar också att JSON matchar det angivna schemat vid körning. | 
| **Välj** | Välj vissa egenskaper i en matris för vidare bearbetning. Om du ”listar poster” från SQL och den returnerar 15 kolumner ska du bara välja några av kolumnerna för ytterligare bearbetning. Utdata är en matris som endast innehåller de egenskaper du väljer. |

## <a name="custom-connectors-and-azure-certification"></a>Anpassade anslutningsappar och Azure-certifiering 

För att anropa API:er som kör anpassad kod eller som inte är tillgängliga som kopplingar kan du [utöka plattformen Logic Apps](../logic-apps/logic-apps-create-api-app.md) genom att skapa REST-baserade API Apps som anpassade kopplingar. 

Om du vill göra dina anpassade API Apps offentliga och använda dem i Azure ska du skicka dina nomineringar till [programmet Microsoft Azure Certified](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/).

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och sedan vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

Saknar vi ett avsnitt om anslutningsprogram eller någon annan viktig information? Om Ja, hjälpa oss genom att bidra till våra befintliga avsnitt eller skriv ett nytt. Vår dokumentation är öppen källkod och finns på GitHub. Kom igång på vår [GitHub-lagringsplats](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Nästa steg
* [Skapa din första logiska app](../logic-apps/logic-apps-create-a-logic-app.md)
* [Skapa anpassade API:er för logikappar](../logic-apps/logic-apps-create-api-app.md)
* [Övervaka dina logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)

<!--Connectors Documentation-->

[api/web-appdoc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrera logikappar med App Service API Apps"
[azureblobstoragedoc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobbehållaren med Azure Blob Storage Connector"
[azure-functionsdoc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[db2doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell och mycket mer"
[Dynamics-365doc]: ./connectors-create-api-crmonline.md "Ansluta till Dynamics CRM Online så att du kan arbeta med CRM Online-data"
[event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Anslut till Azure Event Hubs. Ta emot och skicka händelser mellan logikappar och händelsehubbar"
[filesystemdoc]: ../logic-apps/logic-apps-using-file-connector.md "Ansluta till ett lokalt filsystem"
[ftpdoc]: ./connectors-create-api-ftp.md "Ansluta till en FTP-/FTPS-server för FTP-aktiviteter, till exempel överföring, hämtning och borttagning av filer och mycket mer"
[httpdoc]: ./connectors-native-http.md "Göra HTTP-anrop med HTTP-anslutningsappen"
[http-requestdoc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[http-swaggerdoc]: ./connectors-native-http-swagger.md "Göra HTTP-anrop med HTTP + Swagger-anslutningsappen"
[informixdoc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läsa en rad, lista tabeller och annat"
[nested-logic-appdoc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[office365-outlookdoc]: ./connectors-create-api-office365-outlook.md "Anslut till ditt Office 365-konto. Skicka och ta emot e-post, hantera din kalender och dina kontakter med mera"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Anslut till en Oracle-databas för att lägga till, infoga och ta bort rader med mera"
[mqdoc]: ./connectors-create-api-mq.md "Ansluta till MQ lokalt eller Azure och skicka och ta emot meddelanden"
[recurrencedoc]:  ./connectors-native-recurrence.md "Utlösa återkommande åtgärder för logikappar"
[salesforcedoc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto. Hantera konton, leads, affärsmöjligheter och mycket mer"
[sapconnector]: ../logic-apps/logic-apps-using-sap-connector.md "Ansluta till ett lokalt SAP-system"
[service-busdoc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Anslut till SharePoint Online. Hantera dokument, listobjekt och annat"
[sql-serverdoc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQL Server. Skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell."
[twitterdoc]: ./connectors-create-api-twitter.md "Anslut till Twitter. Hämta tidslinjer, publicera tweets och mycket mer"
[webhookdoc]: ./connectors-native-webhook.md "Lägga till webhook-åtgärder och utlösare i logikappar"

[as2doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Läs mer om AS2 för Enterprise-integration."
[x12doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Läs mer om X12 för Enterprise-integration"
[flatfiledoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platta filer för Enterprise-integration."
[flatfiledecodedoc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platta filer för Enterprise-integration."
[xmlvalidatedoc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Läs mer om XML-verifiering för Enterprise-integration."
[xmltransformdoc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Läs mer om transformeringar för Enterprise-integration."
[as2decode]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Läs mer om AS2-avkodning för Enterprise-integration"
[as2encode]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Läs mer om AS2-kodning för Enterprise-integration"
[X12decode]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Läs mer om X12-avkodning för Enterprise-integration"
[X12encode]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Läs mer om X12-kodning för Enterprise-integration"
[EDIFACTdecode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Läs mer om EDIFACT-avkodning för Enterprise-integration"
[EDIFACTencode]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Läs mer om EDIFACT-kodning för Enterprise-integration"
[integrationaccountdoc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Kolla upp scheman, kartor, partner med mera i ditt integrationskonto"


[boxDoc]: ./connectors-create-api-box.md "Anslut till Box. Överföra, hämta, ta bort, visa filer och mycket mer "
[delaydoc]: ./connectors-native-delay.md "Utföra fördröjda åtgärder"
[dropboxdoc]: ./connectors-create-api-dropbox.md "Anslut till Dropbox. Överföra, hämta, ta bort, visa filer och mycket mer "
[facebookdoc]: ./connectors-create-api-facebook.md "Anslut till Facebook. Publicera på en tidslinje, hämta ett sidflöde och mycket mer"
[githubdoc]: ./connectors-create-api-github.md "Ansluta till GitHub och spåra problem"
[google-drivedoc]: ./connectors-create-api-googledrive.md "Ansluta till Google Drive så att du kan arbeta med dina data"
[google-sheetsdoc]: ./connectors-create-api-googlesheet.md "Ansluta till Google Kalkylark så att du kan göra ändringar i kalkylblad"
[google-tasksdoc]: ./connectors-create-api-googletasks.md "Ansluter till Google Tasks så att du kan hantera uppgifter"
[google-calendardoc]: ./connectors-create-api-googlecalendar.md "Ansluter till Google Kalender och kan hantera kalendern."
[http-responsedoc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[instagramdoc]: ./connectors-create-api-instagram.md "Anslut till Instagram. Utlösa eller utföra åtgärder vid händelser"
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Anslut till ditt MailChimp-konto. Hantera och automatisera e-post"
[mandrilldoc]: ./connectors-create-api-mandrill.md "Ansluta till Mandrill för kommunikation"
[microsoft-translatordoc]: ./connectors-create-api-microsofttranslator.md "Anslut till Microsoft Translator. Översätta text, identifiera språk och annat" 
[office365-usersdoc]: ./connectors-create-api-office365-users.md 
[office365-videodoc]: ./connectors-create-api-office365-video.md "Hämta videoinformation, videolistor, videokanaler och uppspelnings-URL:er för Office 365-videor"
[onedrivedoc]: ./connectors-create-api-onedrive.md "Anslut till ditt personliga Microsoft OneDrive. Överföra, ta bort och lista filer med mera"
[onedrive-for-businessdoc]: ./connectors-create-api-onedriveforbusiness.md "Anslut till företagets Microsoft OneDrive. Överföra, ta bort och lista filer och mycket mer"
[outlook.comdoc]: ./connectors-create-api-outlook.md "Anslut till din Outlook-postlåda. Hantera din e-post, dina kalender, dina kontakter och mycket mer"
[project-onlinedoc]: ./connectors-create-api-projectonline.md "Anslut till Microsoft Project Online. Hantera projekt, uppgifter, resurser och mycket mer"
[querydoc]: ./connectors-native-query.md "Välja och filtrera matriser med frågeåtgärden"
[rssdoc]: ./connectors-create-api-rss.md "Publicera och hämta flödesobjekt, utlös åtgärder när ett nytt objekt publiceras på en RSS-feed."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagarlistor"
[sftpdoc]: ./connectors-create-api-sftp.md "Anslut till ditt SFTP-konto. Överföra, hämta och ta bort filer med mera"
[slackdoc]: ./connectors-create-api-slack.md "Ansluta till Slack och skicka meddelanden till Slack-kanaler"
[smtpdoc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bifogade filer"
[sparkpostdoc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[trellodoc]: ./connectors-create-api-trello.md "Anslut till Trello. Hantera dina projekt och organisera vad du vill med vem du vill"
[twiliodoc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, hämta tillgängliga nummer, hantera inkommande telefonnummer och mycket mer"
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Anslut till Wunderlist. Hantera uppgifter och uppgiftslistor, håll dig uppdaterad och mycket mer"
[yammerdoc]: ./connectors-create-api-yammer.md "Anslut till Yammer. Skicka meddelanden, få nya meddelanden och mycket mer"
[youtubedoc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera videor och kanaler"


<!--Icon references-->
[appFiguresicon]: ./media/apis-list/appfigures.png
[Asanaicon]: ./media/apis-list/asana.png
[Azure-Automation-icon]: ./media/apis-list/azure-automation.png
[AzureBlobStorageicon]: ./media/apis-list/azureblob.png
[Azure-Data-Lake-icon]: ./media/apis-list/azure-data-lake.png
[Azure-DocumentDBicon]: ./media/apis-list/azure-documentdb.png
[Azure-MLicon]: ./media/apis-list/azureml.png
[Azure-Resource-Manager-icon]: ./media/apis-list/azure-resource-manager.png
[Azure-Queues-icon]: ./media/apis-list/azure-queues.png
[Basecamp-3icon]: ./media/apis-list/basecamp.png
[Bitbucket-icon]: ./media/apis-list/bitbucket.png
[Bitlyicon]: ./media/apis-list/bitly.png
[BizTalk-Servericon]: ./media/apis-list/biztalk.png
[Bloggericon]: ./media/apis-list/blogger.png
[Boxicon]: ./media/apis-list/box.png
[Campfireicon]: ./media/apis-list/campfire.png
[Cognitive-Services-Text-Analyticsicon]: ./media/apis-list/cognitiveservicestextanalytics.png
[DB2icon]: ./media/apis-list/db2.png
[Dropboxicon]: ./media/apis-list/dropbox.png
[Dynamics-365icon]: ./media/apis-list/dynamicscrmonline.png
[Dynamics-365-for-Financialsicon]: ./media/apis-list/madeira.png
[Dynamics-365-for-Operationsicon]: ./media/apis-list/dynamicsax.png
[Easy-Redmineicon]: ./media/apis-list/easyredmine.png
[Event-Hubs-icon]: ./media/apis-list/eventhubs.png
[Facebookicon]: ./media/apis-list/facebook.png
[FTPicon]: ./media/apis-list/ftp.png
[GitHubicon]: ./media/apis-list/github.png
[Google-Calendaricon]: ./media/apis-list/googlecalendar.png
[Google-Driveicon]: ./media/apis-list/googledrive.png
[Google-Sheetsicon]: ./media/apis-list/googlesheet.png
[Google-Tasksicon]: ./media/apis-list/googletasks.png
[HideKeyicon]: ./media/apis-list/hidekey.png
[HipChaticon]: ./media/apis-list/hipchat.png
[Informixicon]: ./media/apis-list/informix.png
[Insightlyicon]: ./media/apis-list/insightly.png
[Instagramicon]: ./media/apis-list/instagram.png
[Instapapericon]: ./media/apis-list/instapaper.png
[JIRAicon]: ./media/apis-list/jira.png
[MailChimpicon]: ./media/apis-list/mailchimp.png
[Mandrillicon]: ./media/apis-list/mandrill.png
[Microsoft-Translatoricon]: ./media/apis-list/microsofttranslator.png
[MQicon]: ./media/apis-list/mq.png
[Office-365-Outlookicon]: ./media/apis-list/office365.png
[Office-365-Usersicon]: ./media/apis-list/office365users.png
[Office-365-Videoicon]: ./media/apis-list/office365video.png
[OneDriveicon]: ./media/apis-list/onedrive.png
[OneDrive-for-Businessicon]: ./media/apis-list/onedriveforbusiness.png
[Oracle-DB-icon]: ./media/apis-list/oracle-db.png
[Outlook.comicon]: ./media/apis-list/outlook.png
[PagerDutyicon]: ./media/apis-list/pagerduty.png
[Pinteresticon]: ./media/apis-list/pinterest.png
[Project-Onlineicon]: ./media/apis-list/projectonline.png
[Redmineicon]: ./media/apis-list/redmine.png
[RSSicon]: ./media/apis-list/rss.png
[Common-Data-Serviceicon]: ./media/apis-list/runtimeservice.png
[Salesforceicon]: ./media/apis-list/salesforce.png
[SAPicon]: ./media/apis-list/sap.png
[SendGridicon]: ./media/apis-list/sendgrid.png
[Service-Busicon]: ./media/apis-list/servicebus.png
[SFTPicon]: ./media/apis-list/sftp.png
[SharePointicon]: ./media/apis-list/sharepointonline.png
[Slackicon]: ./media/apis-list/slack.png
[Smartsheeticon]: ./media/apis-list/smartsheet.png
[SMTPicon]: ./media/apis-list/smtp.png
[SparkPosticon]: ./media/apis-list/sparkpost.png
[SQL-Servericon]: ./media/apis-list/sql.png
[Todoisticon]: ./media/apis-list/todoist.png
[Trelloicon]: ./media/apis-list/trello.png
[Twilioicon]: ./media/apis-list/twilio.png
[Twittericon]: ./media/apis-list/twitter.png
[Vimeoicon]: ./media/apis-list/vimeo.png
[Visual-Studio-Team-Servicesicon]: ./media/apis-list/visualstudioteamservices.png
[WordPressicon]: ./media/apis-list/wordpress.png
[Wunderlisticon]: ./media/apis-list/wunderlist.png
[Yammericon]: ./media/apis-list/yammer.png
[YouTubeicon]: ./media/apis-list/youtube.png

<!-- Primitive Icons -->
[API/Web-Appicon]: ./media/apis-list/api.png
[Azure-Functionsicon]: ./media/apis-list/function.png
[Delayicon]: ./media/apis-list/delay.png
[FileSystemIcon]: ./media/apis-list/filesystem.png
[HTTPicon]: ./media/apis-list/http.png
[HTTP-Requesticon]: ./media/apis-list/request.png
[HTTP-Responseicon]: ./media/apis-list/response.png
[HTTP-Swaggericon]: ./media/apis-list/http_swagger.png
[Nested-Logic-Appicon]: ./media/apis-list/workflow.png
[Recurrenceicon]: ./media/apis-list/recurrence.png
[Queryicon]: ./media/apis-list/query.png
[Webhookicon]: ./media/apis-list/webhook.png

<!-- EIP Icons -->
[as2icon]: ./media/apis-list/as2.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png
[integrationaccounticon]: ./media/apis-list/integrationaccount.png

