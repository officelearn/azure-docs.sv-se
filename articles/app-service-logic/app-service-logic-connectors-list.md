<properties
    pageTitle="Lista över tillgängliga kopplingar och API Apps | Microsoft Azure Apptjänst"
    description="Läs mer om kopplingar och API Apps i Azure Apptjänst"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>



# Lista över kopplingar och API Apps att använda i dina Logic Apps
>[AZURE.NOTE] Den här versionen av artikeln gäller förhandsvisning av schemaversionen för Logic Apps 2014-12-01. Versionsinformation för Logic Apps General Availability (GA) finns i [Lista över anslutningsappar](../connectors/apis-list.md).

Läs mer om alla tillgängliga kopplingar och API Apps som skapats av Microsoft för att inom dina Logic Apps.

Prisinformation och en lista över vad som ingår i varje tjänstnivå finns i [Priser för Azure Apptjänst](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Om du vill komma igång med Logic Apps innan du registrerar dig för ett Azure-konto går du till [Prova logikapp](https://tryappservice.azure.com/?appservice=logic). Du kan skapa en kortvarig startlogikapp omedelbart i Apptjänsten. Inget kreditkort krävs, och du gör inga åtaganden.

## Core-kopplingar
I följande tabell visas alla tillgängliga kopplingar och API Apps som skapats av Microsoft och som är tillgängliga som Core-kopplingar:

Namn | Beskrivning
--- | ---
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Kan skicka meddelanden från Service Bus-köer och -ämnen och ta emot meddelanden från Service Bus-köer och -prenumerationer.
[Bing-översättare](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Använd Bing för att översätta text till ett annat språk.
[HTTP](app-service-logic-connector-http.md) | HTTP-lyssnaren öppnar en slutpunkt som fungerar som en HTTP-server och lyssnar efter inkommande begäranden för HTTP eller HTTPS. HTTP-åtgärden kräver inte en API-app och stöds internt inom Logic Apps.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Office 365-kopplingen kan skicka och ta emot e-post, hantera din kalender och hantera kontakter med ditt Office 365-konto.
[Slack](app-service-logic-connector-slack.md) | Anslut till Slack och skicka meddelanden till Slack-kanaler.


## Enterprise Integration-kopplingar
I följande tabell visas alla tillgängliga kopplingar och API Apps som skapats av Microsoft och som är tillgängliga som Enterprise Integration-kopplingar:

Namn  | Beskrivning
------------- | -------------
[AS2-koppling](app-service-logic-connector-as2.md) | Kan ta emot och skicka meddelanden med transportprotokollet AS2. Data transporteras på ett säkert och tillförlitligt sätt med hjälp av digitala certifikat och kryptering.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Tar emot och skickar meddelanden med EDIFACT-protokollet i kommunikation mellan företag.
[Flat fil-kodaren BizTalk](app-service-logic-flatfile-encoder.md) | Ger samverkan mellan flat fil-data (som excel och csv) och XML-data. Den här API-appen kan konvertera en flat fil-instans till XML och vice versa.
[JSON-kodaren BizTalk](app-service-logic-connector-jsonencoder.md) | En kodare och avkodare som hjälper din apps interop mellan JSON -och XML-data. Den kan konvertera en viss JSON-instans till XML och vice versa.
[BizTalk-regler](app-service-logic-use-biztalk-rules.md) | Använd BizTalk-regler för att definiera och styra affärslogik i en organisation. Affärsreglerna kan uppdateras utan att kompilera eller omdistribuera associerade program.
[BizTalk-handelspartnerhantering](app-service-logic-connector-tpm.md) | Definierar och bevarar business-to-business-relationer med hjälp av partners, avtal, scheman och certifikat som används i avtalen. Dessa relationer tillämpas med AS2-, EDIFACT- och X12 API Apps.
[Transformeringstjänsten BizTalk](app-service-logic-transform-xml-documents.md) | Konverterar data från ett format till ett annat. Du kan även överföra en befintlig karta (.trfm-fil), visa länkar mellan käll- och målscheman och använda 'Test' funktioner med exempel på inkommande XML-innehåll. Olika inbyggda funktioner är också tillgängliga, till exempel strängändringar, villkorlig tilldelning och mer.
[BizTalk X12](app-service-logic-connector-x12.md) | Tar emot och skickar meddelanden med X12-protokollet i kommunikation mellan företag.
[BizTalk-XML-verifieraren](app-service-logic-xml-validator.md) | Verifierar XML-data mot fördefinierade XML-scheman. Du kan använda befintliga scheman eller generera scheman baserat på en flat fil-instans, en JSON-instans eller befintliga kopplingar.
[BizTalk-XPath-extraktor](app-service-logic-xpath-extract.md) | Söker efter och hämtar data från XML-innehåll baserat på en XPath som du väljer.
[DB2-koppling](app-service-logic-connector-db2.md) | Ansluter till en IBM DB2-databas lokalt och på en virtuell Azure-dator som kör ett Windows-operativsystem. Kan mappa Web API- och OData-API-åtgärder till Informix Structured Query Language-kommandon. <br/><br/>Inga utlösare. Åtgärderna är bland annat att markera, infoga, uppdatera, ta bort och anpassa tabelluttryck<br/><br/>Denna koppling inkluderar även Microsoft Client för DRDA för att ansluta till en Informix-server i ett TCP/IP-nätverk.
[Fil](app-service-logic-connector-file.md) | Med den här anslutningen kan du ansluta till det lokala filsystemet eller nätverket och göra olika filaktiviteter, inklusive att överföra, ta bort, lista filer och mycket annat.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Ansluter till en FTP-/FTPS-server och gör olika FTP-aktiviteter, inklusive att överföra, hämta och ta bort filer och mycket annat.
[Informix](app-service-logic-connector-informix.md) | Ansluter till en IBM Informix-databas, lokalt och på en virtuell Azure-dator som kör ett Windows-operativsystem. Kan mappa Web API- och OData-API-åtgärder till Informix Structured Query Language-kommandon.<br/><br/>Inga utlösare. Åtgärderna är bland annat att markera, infoga, uppdatera, ta bort och anpassa tabelluttryck.<br/><br/>Vid lokal användning kan VPN eller Azure ExpressRoute  användas. Denna koppling inkluderar även en Microsoft Client för DRDA för att ansluta till en Informix-server i ett TCP/IP-nätverk.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Ansluter till en lokal SQLServer eller en Azure SQL-databas. Du kan skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell.
MQ | Ansluter till en IBM WebSphere MQ-server version 8, lokalt och på en virtuell Azure-dator som kör ett Windows-operativsystem. Vid lokal användning kan VPN eller Azure ExpressRoute  användas. Kopplingen inkluderar även Microsoft Client för MQ.<br/><br/>Inga utlösare. Inga åtgärder.<br/><br/>**Obs**! Kan för närvarande inte användas med Logic Apps.

## Kopplingar som utlösare
Flera kopplingar skapar utlösare för Logic Apps. Dessa utlösare är av två typer:

1. Avsökningsutlösare: Dessa utlösare avsöker din tjänst med en frekvens som är angiven för att söka efter nya data. När det finns nya data körs en ny instans av din logikapp med data som indata. För att förhindra att samma data används flera gånger kan utlösaren rensa data som har lästs in och skickats till logikappen. Exempel på sådana kopplingar är File, SQL och Azure Storage.
2. Push-utlösare: dessa utlösare lyssnar efter data på en slutpunkt eller för att en händelse ska inträffa. Sedan utlöser de en ny instans av logikappen. Exempel på sådana kopplingar är HTTP-lyssnare och Twitter.

## Kopplingar som åtgärder
Kopplingar kan också användas som åtgärder i din logikapp. Åtgärder är användbara för att söka efter data i logikappen som sedan kan användas i körningen. Du kan till exempel behöva söka efter data från en SQL-databas för ytterligare information om en kund vid bearbetning av en order. Eller så kan du behöva skriva, uppdatera eller ta bort data i en destination. Du kan göra detta med de åtgärder som tillhandahålls av kopplingarna. Åtgärder som mappar till åtgärder i API Apps (som definieras av deras Swagger-metadata).

## Skapa egna kopplingar och API Apps
[Kopplingar och referenser för API Apps](http://aka.ms/appservicesconnectorreference)  
[Utlösare för API Apps i Azure Apptjänst](../app-service-api/app-service-api-dotnet-triggers.md)  
[Referens för logikapp](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Mer om kopplingar och API Apps
[Vad är kopplingar och BizTalk-API Apps](app-service-logic-what-are-biztalk-api-apps.md)  
[Använda hybridanslutningshanteraren i Azure Apptjänst](app-service-logic-hybrid-connection-manager.md)  
[Hantera och övervaka inbyggda API Apps och kopplingar](app-service-logic-monitor-your-connectors.md)



<!--HONumber=Sep16_HO3-->


