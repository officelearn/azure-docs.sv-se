---
title: Anslutningsappar för Azure Logic Apps | Microsoft Docs
description: Automatisera arbetsflöden med anslutningsappar för Azure Logic Apps, inklusive inbyggda, hanterade, lokala, integrationskonto och enterprise-anslutningsappar
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: b320696a56855baaa4af10177d25dfe9973ee73a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635449"
---
# <a name="connectors-for-azure-logic-apps"></a>Anslutningsappar för Azure Logic Apps

Kopplingar spelar en viktig del när du skapar automatiska arbetsflöden med Azure Logic Apps. Genom att använda anslutningsappar i logic apps kan du utöka funktionerna för dina lokala och molnbaserade appar du utför uppgifter med de data som du skapar och redan har. 

Medan Logic Apps-erbjudanden [~ 200 + anslutningsappar](https://docs.microsoft.com/connectors), den här artikeln beskriver populära och vanliga kopplingar som har används av tusentals appar och miljontals körningar för bearbetning av data och information. Anslutningsappar är tillgängliga som built-ins eller hanterade anslutningsappar. 

> [!NOTE]
> För en fullständig lista över kopplingar och referensinformation för varje anslutning, till exempel åtgärder, utlösare och gränser, hittar du en fullständig lista under den [översikt över Anslutningsappar](https://docs.microsoft.com/connectors).

* [**Built-INS**](#built-ins): dessa inbyggda åtgärder och utlösare kan du skapa logikappar som körs på anpassade scheman som kommunicerar med andra slutpunkter, ta emot och svara på förfrågningar och anropa Azure functions, Azure API Apps (Webbappar), egna API: er hanteras och appar som har publicerats med Azure API Management och kapslade logik som kan ta emot begäranden. Du kan också använda inbyggda åtgärder som hjälper dig att ordna och styra logikappens arbetsflöde och också arbeta med data.

* **Hanterade anslutningsappar**: dessa anslutningsappar innehåller utlösare och åtgärder för att komma åt andra tjänster och system. Vissa anslutningar kräver att du först skapa anslutningar som hanteras av Azure Logic Apps. Hanterade anslutningsappar är ordnade i dessa grupper:

  |   |   |
  |---|---|
  | [**Hanterade API-kopplingar**](#managed-api-connectors) | Skapa logikappar som använder tjänster som Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online och många fler. | 
  | [**Lokala anslutningar**](#on-premises-connectors) | När du installerar och konfigurerar den [lokal datagateway][gateway-doc], dessa kopplingar hjälper dina logic apps åtkomst till lokala system, till exempel SQL Server, SharePoint Server, Oracle DB, filresurser och andra. | 
  | [**Integrationskonton**](#integration-account-connectors) | Tillgängliga när du skapar och betala för ett integrationskonto, transformera dessa kopplingar och validera XML, koda och avkoda flata filer och bearbeta business-to-business (B2B) meddelanden med AS2 och EDIFACT X12 protokoll. | 
  | [**Anslutningsprogram för företagsprogram**](#enterprise-connectors) | Ger åtkomst till företagssystem som SAP och IBM MQ mot en extra kostnad. |
  ||| 

  Till exempel om du använder Microsoft BizTalk Server, dina logic apps kan ansluta till och kommunicera med din BizTalk Server med hjälp av den [BizTalk Server-anslutningen](#on-premises-connectors). 
  Du kan utöka eller utföra BizTalk-liknande åtgärder i dina logic apps med hjälp av den [integrationskonton](#integration-account-connectors). 

> [!NOTE] 
> Fullständig lista över kopplingar och referensinformation för varje anslutning, till exempel åtgärder och utlösare, som definieras av en Swagger-beskrivning, plus eventuella gränser du hittar en fullständig lista under den [översikt över Anslutningsappar](/connectors/). Information om priser finns i [Logic Apps prisinformation](https://azure.microsoft.com/pricing/details/logic-apps/) och [Logic Apps prismodellen](../logic-apps/logic-apps-pricing.md). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Inbyggda

Logic Apps tillhandahåller inbyggd utlösare och åtgärder så att du kan skapa schemabaserade arbetsflöden hjälper logikappar kommunicera med andra appar och tjänster, kontroll arbetsflöde genom dina logic apps och hantera eller manipulera data. 

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][schedule-icon]<br/>**schema**][recurrence-doc] | -Köra din logikapp enligt ett angivet schema, allt från grundläggande till avancerade upprepningar med den **upprepning** utlösaren. <p>-Pausa din logikapp under en angiven tid med den **fördröjning** åtgärd. <p>-Pausa logikappen till angivna datum och tid med den **Fördröj tills** åtgärd. | [![API-ikon][http-icon]<br/>**HTTP**][http-doc] | Kommunicera med valfri slutpunkt via HTTP med både utlösare och åtgärder för HTTP, HTTP + Swagger, och HTTP + Webhook. | 
| [![API-ikon][http-request-icon]<br/>**för begäran**][http-request-doc] | – Skapa din logikapp anropningsbara från andra appar eller tjänster, utlösare på Event Grid resource händelser eller utlösaren för svar på aviseringar i Azure Security Center med den **begära** utlösaren. <p>-Skicka svar till en app eller tjänst med den **svar** åtgärd. | [![API-ikon][batch-icon]<br/>**Batch**][batch-doc] | -Bearbeta meddelanden i batchar med den **Batch meddelanden** utlösaren. <p>-Anropet logikappar som har befintliga batch-utlösare med den **skicka meddelanden till batchen** åtgärd. | 
| [![API-ikon][azure-functions-icon]<br/>**Azure Functions**][azure-functions-doc] | Anropa Azure-funktioner som kör anpassade fragment (C# eller Node.js) från dina logic apps. | [![API-ikon][azure-api-management-icon]</br>**Azure API Management**][azure-api-management-doc] | Anropa utlösare och åtgärder som definierats av ditt eget API: er som du hanterar och publicera med Azure API Management. | 
| [![API-ikon][azure-app-services-icon]<br/>**Azure App Services**][azure-app-services-doc] | Anropa Azure API Apps eller Webbappar som körs på Azure App Service. Utlösare och åtgärder som definierats i de här apparna visas som alla andra förstklassig utlösare och åtgärder när Swagger ingår. | [![API-ikon][azure-logic-apps-icon]<br/>**Azure<br/>Logic Apps**][nested-logic-app-doc] | Anropa andra logikappar som börjar med en begäransutlösare. | 
||||| 

### <a name="control-workflow"></a>Kontrollen arbetsflöde

Här är inbyggda åtgärder för att strukturera och kontrollera åtgärder i logikappens arbetsflöde:

|   |   |   |   | 
|---|---|---|---| 
| [![Inbyggda ikonen][condition-icon]<br/>**villkor**][condition-doc] | Utvärdera ett villkor och kör olika åtgärder beroende på om villkoret är SANT eller FALSKT. | [![Inbyggda ikonen][for-each-icon]</br>**för varje**][for-each-doc] | Utföra samma åtgärder på alla objekt i en matris. | 
| [![Inbyggda ikonen][scope-icon]<br/>**omfång**][scope-doc] | Gruppera åtgärder i *scope*, vilket hämta sina egna status när åtgärder i omfånget slutföras. | [![Inbyggda ikonen][switch-icon]</br>**växel**][switch-doc] | Gruppera åtgärder i *fall*, som tilldelas unika värden såvida standard. Kör endast att vars tilldelade värdet matchar resultatet från ett uttryck, objekt eller token. Om det finns inga matchningar, kör du standard fall. | 
| [![Inbyggda ikonen][terminate-icon]<br/>**avsluta**][terminate-doc] | Stoppa ett aktivt körs logikappens arbetsflöde. | [![Inbyggda ikonen][until-icon]<br/>**tills**][until-doc] | Upprepa åtgärder tills det angivna villkoret är SANT eller vissa tillstånd har ändrats. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Hantera eller ändra data

Här är inbyggda åtgärder för att arbeta med data utdata och deras format:  

|   |   | 
|---|---| 
| [![Inbyggda ikonen][data-operations-icon]<br/>**dataåtgärder**][data-operations-doc] | Utför åtgärder med data: <p>- **Compose**: skapa ett enda utflöde från flera inmatningar med olika typer. <br>- **Skapa CSV tabell**: skapa en Semikolonavgränsade--värden (CSV)-tabell från en matris med JSON-objekt. <br>- **Skapa HTML-tabell**: skapa en HTML-tabell från en matris med JSON-objekt. <br>- **Filtermatris**: skapa en matris från objekten i en annan matris som uppfyller dina kriterier. <br>- **Ansluta till**: skapa en sträng från alla objekt i en matris och avgränsa de objekt med angiven avgränsare. <br>- **Parsa JSON**: skapa användarvänliga tokens från egenskaperna och deras värden i JSON innehåll så att du kan använda dessa egenskaper i arbetsflödet. <br>- **Välj**: skapa en matris med JSON-objekt genom att omvandla objekt eller värden i en annan matris och mappa dessa objekt till angivna egenskaperna. | 
| ![Inbyggda ikon][date-time-icon]<br/>**Datum tid** | Utför åtgärder med tidsstämplar som är: <p>- **Lägg till tid**: lägga till det angivna antalet enheter till en tidsstämpel. <br>- **Konvertera tidszon**: konvertera en tidsstämpel från källtidszonen tidszon mål. <br>- **Aktuell tid**: returnerar den aktuella tidsstämpeln som en sträng. <br>- **Hämta framtida tid**: returnerar den aktuella tidsstämpeln plus de angivna tidsenheterna. <br>- **Hämta tidigare tid**: returnerar den aktuella tidsstämpeln minus de angivna tidsenheterna. <br>- **Subtrahera från tiden**: ta bort ett antal tidsenheter från en tidsstämpel. |
| [![Inbyggda ikonen][variables-icon]<br/>**variabler**][variables-doc] | Utför åtgärder med variabler: <p>- **Lägga till en matrisvariabel**: Infoga ett värde som det sista objektet i en matris som lagras av en variabel. <br>- **Lägga till strängvariabeln**: Infoga ett värde som det sista tecknet i en sträng som lagras av en variabel. <br>- **Minska variabel**: minskar en variabel med ett konstant värde. <br>- **Inkrementera variabeln**: öka en variabel med ett konstant värde. <br>- **Initiera variabel**: skapa en variabel och deklarera dess datatyp och ursprungligt värde. <br>- **Ange variabel**: tilldela ett annat värde till en befintlig variabel. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Hanterade API-kopplingar

Här följer mer populära anslutningsappar för att automatisera uppgifter, processer och arbetsflöden med dessa tjänster eller system:

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][azure-service-bus-icon]<br/>**Azure Service Bus**][azure-service-bus-doc] | Hantera asynkrona meddelanden, sessioner och ämnesprenumerationer med de vanligaste anslutningsappen i Logic Apps. | [![API-ikon][sql-server-icon]<br/>**SQL Server**][sql-server-doc] | Anslut till SQL Server lokalt eller en Azure SQL Database i molnet så att du kan hantera poster, köra lagrade procedurer eller utföra frågor. | 
| [![API-ikon][office-365-outlook-icon]<br/>**Office 365<br/>Outlook**][office-365-outlook-doc] | Anslut till Office 365 e-postkontot så att du kan skapa och hantera e-postmeddelanden, uppgifter, kalenderhändelser och möten, kontakter, begäranden och mycket mer. | [![API-ikon][azure-blob-storage-icon]<br/>**Azure Blob<br/>lagring**][azure-blob-storage-doc] | Anslut till ditt lagringskonto så att du kan skapa och hantera blobbinnehåll. | 
| [![API-ikon][sftp-icon]<br/>**SFTP**][sftp-doc] | Ansluta till SFTP-servrar som du kan komma åt från internet så att du kan arbeta med filer och mappar. | [![API-ikon][sharepoint-online-icon]<br/>**SharePoint<br/>Online**][sharepoint-online-doc] | Anslut till SharePoint Online så att du kan hantera filer, bifogade filer, mappar och mycket mer. | 
| [![API-ikon][dynamics-365-icon]<br/>**Dynamics 365<br/>CRM Online**][dynamics-365-doc] | Anslut till Dynamics 365-konto så att du kan skapa och hantera poster och objekt. | [![API-ikon][ftp-icon]<br/>**FTP**][ftp-doc] | Ansluta till FTP-servrar som du kan komma åt från internet så att du kan arbeta med filer och mappar. | 
| [![API-ikon][salesforce-icon]<br/>**Salesforce**][salesforce-doc] | Anslut till ditt Salesforce-konto så att du kan skapa och hantera objekt, till exempel poster, jobb, objekt med mera. | [![API-ikon][twitter-icon]<br/>**Twitter**][twitter-doc] | Anslut till ditt Twitter-konto så att du kan hantera tweets, följare, din tidslinje och mer. Spara dina tweets till SQL-, Excel- eller SharePoint. | 
| [![API-ikon][azure-event-hubs-icon]<br/>**Azure Event Hubs**][azure-event-hubs-doc] | Använda och publicera händelser via en Händelsehubb. Till exempel hämta utdata från din logikapp med Event Hubs och sedan skicka som utdata till en leverantör av realtidsanalys. | [![API-ikon][azure-event-grid-icon]<br/>**Azure Event**</br>**rutnät**][azure-event-grid-doc] | Övervaka händelser som publicerats av en Event Grid, till exempel när ändrar Azure-resurser eller resurser från tredje part. | 
||||| 

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Lokala anslutningsappar 

Här följer några vanliga kopplingar som ger åtkomst till data och resurser i lokala system. Innan du kan skapa en anslutning till ett lokalt system, måste du först [hämta, installera och konfigurera en lokal datagateway][gateway-doc]. Den här gatewayen tillhandahåller en säker kommunikationskanal utan att behöva ställa in nödvändiga nätverkets infrastruktur. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![API-ikon][file-system-icon]<br/>**filen</br> System**][file-system-doc] | [![API-ikon][ibm-db2-icon]<br/>**IBM DB2**][ibm-db2-doc] | [![API-ikon][ibm-informix-icon]<br/>**IBM** </br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-ikon][oracle-db-icon]<br/>**Oracle DB**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-ikon][sharepoint-server-icon]<br/>**SharePoint</br> Server**][sharepoint-server-doc] | [![API-ikon][sql-server-icon]<br/>**SQL</br> Server**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
||||| 

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton 

Här är kopplingar för att skapa lösningar för business-to-business (B2B) med dina logikappar när du skapar och betala för en [integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är tillgängligt via den Enterprise-Integrationspaket (EIP) i Azure. Med det här kontot kan du skapa och lagra B2B-artefakter, till exempel samarbetspartner, avtal, kartor, scheman, certifikat och så vidare. Om du vill använda dessa artefakter, koppla dina logikappar till ditt integrationskonto. Om du använder BizTalk Server, verka anslutningsapparna välbekanta redan.

|   |   |   |   | 
|---|---|---|---| 
| [![API-ikon][as2-icon]<br/>**AS2</br> avkodning**][as2-decode-doc] | [![API-ikon][as2-icon]<br/>**AS2</br> kodning**][as2-encode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> avkodning**][edifact-decode-doc] | [![API-ikon][edifact-icon]<br/>**EDIFACT</br> kodning**][edifact-encode-doc] | 
| [![API-ikon][flat-file-decode-icon]<br/>**Flat fil</br> avkodning**][flat-file-decode-doc] | [![API-ikon][flat-file-encode-icon]<br/>**Flat fil</br> kodning**][flat-file-encode-doc] | [![API-ikon][integration-account-icon]<br/>**integrering<br/>konto**][integration-account-doc] | [![API-ikon][liquid-icon]<br/>**flytande**</br>**omvandlar**][json-liquid-transform-doc] | 
| [![API-ikon][x12-icon]<br/>**X12</br> avkodning**][x12-decode-doc] | [![API-ikon][x12-icon]<br/>**X12</br> kodning**][x12-encode-doc] | [![API-ikon][xml-transform-icon]<br/>**XML**</br>**omvandlar**][xml-transform-doc] | [![API-ikon][xml-validate-icon]<br/>**XML <br/>verifiering**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Dina logic apps kan komma åt företagssystem som SAP och IBM MQ:

|   |   | 
|---|---| 
| [![API-ikon][ibm-mq-icon]<br/>**IBM MQ**][ibm-mq-doc] | [![API-ikon][sap-icon]<br/>**SAP**][sap-connector-doc] |
||| 

## <a name="more-about-triggers-and-actions"></a>Mer information om utlösare och åtgärder

Flera anslutningsappar innehåller *utlösare* som meddelar din logikapp när specifika händelser äger rum. Så när dessa händelser inträffar, utlösaren skapar och kör en instans av din logikapp. FTP-anslutningsappen innehåller till exempel en ”när en fil läggs till eller ändras” utlösare som startar logikappen när en fil uppdateras. 

Logic Apps tillhandahåller dessa typer av utlösare:  

* *Sökningsutlösare*: dessa utlösare avsöker din tjänst med en angiven frekvens och söker efter nya data. 

  När nya data körs en ny instans av logikappen skapas och körs med de data som skickas som indata. 

* *Push-utlösare*: de här utlösarna lyssnar efter nya data på en slutpunkt eller efter en händelse ska inträffa, som skapar och kör en ny instans av din logikapp.

* *Upprepningsutlösare*: den här utlösaren skapar och kör en instans av logikappen baserat på ett angivet schema.

Anslutningsapparna tillhandahåller också *åtgärder* som utför uppgifter i logikappens arbetsflöde. Din logikapp kan till exempel läsa data och använda dessa data i senare steg i logikappen. Mer specifikt kan din logikapp hitta kunddata från en SQL-databas och bearbeta dessa data senare i logikappens arbetsflöde. 

Mer information om utlösare och åtgärder som finns i den [översikt över Anslutningsappar](connectors-overview.md). 

## <a name="custom-apis-and-connectors"></a>Anpassade API: er och anslutningsappar 

För att anropa API: er som kör anpassad kod eller som inte är tillgängliga som kopplingar kan du utöka plattformen Logic Apps genom [skapa anpassade API Apps](../logic-apps/logic-apps-create-api-app.md). Du kan också [skapa anpassade anslutningsappar](../logic-apps/custom-connector-overview.md) för *alla* REST eller SOAP-baserat API: er som tillgängliggör dessa API: er för alla logikappar i Azure-prenumerationen.
Om du vill göra anpassade API Apps eller kopplingar offentliga för allmän användning i Azure, kan du [skicka in anslutningsappar för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Du kan skicka eller rösta på idéer för Azure Logic Apps och anslutningsapparna genom att gå till den [webbplatsen för Logic Apps-Användarfeedback](https://aka.ms/logicapps-wish).

* Är dokumentationen saknas artiklar eller information som du tycker är viktigt? Om Ja, kan du hjälpa genom att lägga till i befintliga artiklar eller genom att skriva en egen. Dokumentationen är öppen källkod och finns på GitHub. Kom igång på i Azure-dokumentationen [GitHub-lagringsplatsen](https://github.com/Microsoft/azure-docs). 

## <a name="next-steps"></a>Nästa steg

* Hitta den [kopplingarnas fullständig lista](https://docs.microsoft.com/connectors)
* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa anpassade anslutningsappar för logic apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Skapa anpassade API:er för logikappar](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Anslut till datakällor lokalt från logikappar med lokala datagatewayer"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Skapa en Azure API Management-tjänstinstans för att hantera och publicera dina API: er"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrera logikappar med App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Bearbetar meddelanden inom grupper, eller i batchar"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Utvärdera ett villkor och kör olika åtgärder beroende på om villkoret är SANT eller FALSKT"
[delay-doc]: ./connectors-native-delay.md "Utföra fördröjda åtgärder"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Utföra samma åtgärder på alla objekt i en matris"
[http-doc]: ./connectors-native-http.md "Göra HTTP-anrop med HTTP-anslutningsappen"
[http-request-doc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[http-response-doc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Göra HTTP-anrop med HTTP + Swagger-anslutningsappen"
[http-webook-doc]: ./connectors-native-webhook.md "Lägga till http-webhook-åtgärder och utlösare i logikappar"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[query-doc]: ./connectors-native-query.md "Välja och filtrera matriser med frågeåtgärden"
[recurrence-doc]:  ./connectors-native-recurrence.md "Utlösa återkommande åtgärder för logikappar"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Ordna åtgärder i grupper som hämta sina egna status efter att åtgärderna i gruppen slutföras" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Ordna åtgärder i fall som är tilldelade unika värden. Kör bara fallet vars värde matchar resultatet från ett uttryck, objekt eller token. Om det finns inga matchningar, kör du standard-fall"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stoppa eller Avbryt ett arbetsflöde som är aktivt som körs för din logikapp"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Upprepa åtgärder tills det angivna villkoret är SANT eller vissa tillstånd har ändrats"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Utföra åtgärder, till exempel filtrera matriser eller skapar CSV och HTML-tabeller"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Utför åtgärder med variabler, till exempel starta, set, öka, minska, och lägga till i variabeln sträng eller matris"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobcontainern med Azure Blob Storage Connector"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md " Övervaka händelser som publicerats av en Event Grid, till exempel när ändrar Azure-resurser eller resurser från tredje part"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Anslut till Azure Event Hubs. Ta emot och skicka händelser mellan logikappar och händelsehubbar"
[box-doc]: ./connectors-create-api-box.md "Anslut till Box. Överföra, hämta, ta bort, visa filer och mycket mer "
[dropbox-doc]: ./connectors-create-api-dropbox.md "Anslut till Dropbox. Överföra, hämta, ta bort, visa filer och mycket mer "
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Ansluta till Dynamics CRM Online så att du kan arbeta med CRM Online-data"
[facebook-doc]: ./connectors-create-api-facebook.md "Anslut till Facebook. Publicera på en tidslinje, hämta ett sidflöde och mycket mer"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ansluta till ett lokalt filsystem"
[ftp-doc]: ./connectors-create-api-ftp.md "Ansluta till en FTP-/FTPS-server för FTP-aktiviteter, till exempel överföring, hämtning och borttagning av filer och mycket mer"
[github-doc]: ./connectors-create-api-github.md "Ansluta till GitHub och spåra problem"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Ansluter till Google Kalender och kan hantera kalendern."
[google-drive-doc]: ./connectors-create-api-googledrive.md "Ansluta till Google Drive så att du kan arbeta med dina data"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Anslut till Google Sheets så att du kan ändra dina blad"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ansluter till Google Tasks så att du kan hantera uppgifter"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell och mycket mer"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läsa en rad, lista tabeller och annat"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Anslut till IBM MQ lokalt eller i Azure för att skicka och ta emot meddelanden"
[instagram-doc]: ./connectors-create-api-instagram.md "Anslut till Instagram. Utlösa eller utföra åtgärder vid händelser"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Anslut till ditt MailChimp-konto. Hantera och automatisera e-post"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ansluta till Mandrill för kommunikation"
[microsoft-translator-doc]: ./connectors-create-api-microsofttranslator.md "Anslut till Microsoft Translator. Översätta text, identifiera språk och annat" 
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Anslut till ditt Office 365-konto. Skicka och ta emot e-post, hantera din kalender och dina kontakter med mera"
[office-365-users-doc]: ./connectors-create-api-office365-users.md 
[office-365-video-doc]: ./connectors-create-api-office365-video.md "Hämta videoinformation, videolistor, videokanaler och uppspelnings-URL:er för Office 365-videor"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Anslut till ditt personliga Microsoft OneDrive. Överföra, ta bort och lista filer med mera"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Anslut till företagets Microsoft OneDrive. Överföra, ta bort och lista filer och mycket mer"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Anslut till en Oracle-databas för att lägga till, infoga och ta bort rader med mera"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Anslut till din Outlook-postlåda. Hantera din e-post, dina kalender, dina kontakter och mycket mer"
[project-online-doc]: ./connectors-create-api-projectonline.md "Anslut till Microsoft Project Online. Hantera projekt, uppgifter, resurser och mycket mer"
[rss-doc]: ./connectors-create-api-rss.md "Publicera och hämta flödesobjekt, utlös åtgärder när ett nytt objekt publiceras på en RSS-feed."
[salesforce-doc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto. Hantera konton, leads, affärsmöjligheter och mycket mer"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ansluta till ett lokalt SAP-system"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagarlistor"
[sftp-doc]: ./connectors-create-api-sftp.md "Anslut till ditt SFTP-konto. Överföra, hämta och ta bort filer med mera"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Anslut till lokala SharePoint-servrar. Hantera dokument, listobjekt och annat"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Anslut till SharePoint Online. Hantera dokument, listobjekt och annat"
[slack-doc]: ./connectors-create-api-slack.md "Ansluta till Slack och skicka meddelanden till Slack-kanaler"
[smtp-doc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bifogade filer"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQLServer. Skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell."
[trello-doc]: ./connectors-create-api-trello.md "Anslut till Trello. Hantera dina projekt och organisera vad du vill med vem du vill"
[twilio-doc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, hämta tillgängliga nummer, hantera inkommande telefonnummer och mycket mer"
[twitter-doc]: ./connectors-create-api-twitter.md "Anslut till Twitter. Hämta tidslinjer, publicera tweets och mycket mer"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Anslut till Wunderlist. Hantera uppgifter och uppgiftslistor, håll dig uppdaterad och mycket mer"
[yammer-doc]: ./connectors-create-api-yammer.md "Anslut till Yammer. Skicka meddelanden, få nya meddelanden och mycket mer"
[youtube-doc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera videor och kanaler"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Läs mer om AS2 för Enterprise-integration."
[as2-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-as2-decode.md "Läs mer om AS2-avkodning för Enterprise-integration"
[as2-encode-doc]:../logic-apps/logic-apps-enterprise-integration-as2-encode.md "Läs mer om AS2-kodning för Enterprise-integration"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Läs mer om EDIFACT-avkodning för Enterprise-integration"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Läs mer om EDIFACT-kodning för Enterprise-integration"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platta filer för Enterprise-integration."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platta filer för Enterprise-integration."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Kolla upp scheman, kartor, partner med mera i ditt integrationskonto"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Lär dig mer om JSON-transformationer med Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Läs mer om X12 för Enterprise-integration"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Läs mer om X12-avkodning för Enterprise-integration"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Läs mer om X12-kodning för Enterprise-integration"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Läs mer om transformeringar för Enterprise-integration."
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Läs mer om XML-verifiering för Enterprise-integration."

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed API icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[microsoft-translator-icon]: ./media/apis-list/microsoft-translator.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[office-365-video-icon]: ./media/apis-list/office-365-video.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[wunderlist-icon]: ./media/apis-list/wunderlist.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
