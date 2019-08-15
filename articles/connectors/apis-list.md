---
title: Anslutningar för Azure Logic Apps
description: Automatisera arbets flöden med kopplingar för Azure Logic Apps, inklusive inbyggt, hanterat, lokalt, integrations konto och företags anslutningar
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 126c3fb348b1d53769a818ae4b21fcdbbef65615
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68517244"
---
# <a name="connectors-for-azure-logic-apps"></a>Anslutningar för Azure Logic Apps

Anslutningsprogram ger snabb åtkomst från Azure Logic Apps till händelser, data och åtgärder i andra appar, tjänster, system, protokoll och plattformar. Genom att använda anslutningsprogram i dina logikappar kan du utöka funktionerna för dina molnappar och lokala appar så att du kan utföra olika åtgärder för de data som du skapar och redan har.

Även om Logic Apps erbjuder [hundratals kopplingar](https://docs.microsoft.com/connectors), beskriver den här artikeln populära och ofta använda anslutningar som används av tusentals appar och miljoner körningar för bearbetning av data och information. Om du vill hitta en fullständig lista över kopplingar och varje kopplings referensinformation, till exempel utlösare, åtgärder och gränser, granskar du kopplings referens sidorna under [kopplings översikt](https://docs.microsoft.com/connectors). Lär dig även mer om [utlösare och åtgärder](#triggers-actions), [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md)och [Logic Apps pris information](https://azure.microsoft.com/pricing/details/logic-apps/). 

> [!NOTE]
> Om du vill integrera med en tjänst eller ett API som inte har någon koppling kan du antingen anropa tjänsten direkt via ett protokoll som HTTP eller skapa en [anpassad anslutning](#custom).

Kopplingar är tillgängliga antingen som inbyggda utlösare och åtgärder eller som hanterade anslutningar:

<a name="built-in"></a>

* [**Inbyggda program**](#built-ins): Dessa inbyggda utlösare och åtgärder är "interna" för att Azure Logic Apps och hjälpa dig att skapa Logi Kap par som körs på anpassade scheman, kommunicera med andra slut punkter, ta emot och svara på förfrågningar och anropa Azure Functions, Azure API Apps (Web Apps), dina egna API: er hanteras och publiceras med Azure API Management och kapslade Logic-appar som kan ta emot begär Anden. Du kan också använda inbyggda åtgärder som hjälper dig att organisera och kontrol lera din Logic app-arbetsflöde och även arbeta med data.

  > [!NOTE]
  > Logic Apps i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kan komma åt resurser direkt i ett virtuellt Azure-nätverk.
  > När du använder en ISE, inbyggda utlösare och åtgärder som visar **kärn** etiketten körs i samma ISE som dina Logic Apps. Logic Apps, inbyggda utlösare och inbyggda åtgärder som körs i din ISE använder en pris sättnings plan som skiljer sig från den förbruknings pris planen.
  >
  > Mer information om hur du skapar ISEs finns i [ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). 
  > Mer information om priser finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md).

<a name="managed-connectors"></a>

* **Hanterade anslutningsappar**: De här anslutningarna distribueras och hanteras av Microsoft och ger utlösare och åtgärder för att komma åt moln tjänster, lokala system eller både, inklusive Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint med mera. Vissa anslutningar har specifikt stöd för kommunikation mellan företag och företag (B2B) och kräver ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkat till din Logic app. Innan du använder vissa anslutningar måste du först skapa anslutningar, som hanteras av Azure Logic Apps. 

  Om du till exempel använder Microsoft BizTalk Server kan dina Logi Kap par ansluta till och kommunicera med dina BizTalk Server med hjälp av den [BizTalk Server lokala anslutningen](#on-premises-connectors). 
  Du kan sedan utöka eller utföra BizTalk-liknande åtgärder i dina Logi Kap par genom att använda kopplingarna för [integrations konton](#integration-account-connectors).

  Anslutningarna klassificeras som antingen standard eller Enterprise. 
  [Företags anslutningar](#enterprise-connectors) ger till gång till företags system som SAP, IBM MQ och IBM 3270 för ytterligare kostnader. För att avgöra om en koppling är standard eller företag, se teknisk information på varje kopplings referens sida under [anslutnings översikt](https://docs.microsoft.com/connectors). 

  Du kan också identifiera anslutningar genom att använda dessa kategorier, även om vissa kopplingar kan korsa flera kategorier. 
  SAP är till exempel en Enterprise Connector och en lokal anslutning:

  |   |   |
  |---|---|
  | [**Hanterade API-kopplingar**](#managed-api-connectors) | Skapa Logi Kap par som använder tjänster som Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online och många fler. |
  | [**Lokala anslutningar**](#on-premises-connectors) | När du har installerat och konfigurerat den [lokala][gateway-doc]datagatewayen kan de här anslutningarna hjälpa dina Logi Kap par att komma åt lokala system som SQL Server, SharePoint Server, Oracle DB, fil resurser och andra. |
  | [**Integrations konto anslutningar**](#integration-account-connectors) | De här kopplingarna är tillgängliga när du skapar och betalar för ett integrations konto, omvandlar och validerar XML, kodar och avkodar flata filer och bearbetar Business-to-Business (B2B)-meddelanden med AS2-, EDIFACT-och X12-protokoll. |
  |||

  > [!NOTE]
  > Logic Apps i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kan komma åt resurser direkt i ett virtuellt Azure-nätverk. När du använder en ISE, standard-och företags anslutningar som visar **ISE** -etiketten körs i samma ISE som dina Logic Apps. Kopplingar som inte visar ISE-etiketten körs i den globala Logic Appss tjänsten.
  >
  > För lokala system som är anslutna till ett virtuellt Azure-nätverk, injicera du ISE i det nätverket så att dina Logi Kap par kan komma åt dessa system genom att använda antingen en anslutning som har en **ISE** -etikett, en HTTP-åtgärd eller en [anpassad anslutning](#custom). Logic Apps och kopplingar som körs i din ISE använder en pris sättnings plan som skiljer sig från den förbruknings bara pris planen. 
  >
  > Mer information om hur du skapar ISEs finns i [ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
  > Mer information om priser finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md).

  För en fullständig lista över kopplingar och varje kopplings referensinformation, till exempel åtgärder och utlösare, som definieras av en OpenAPI (tidigare Swagger), plus eventuella begränsningar, kan du hitta hela listan under [anslutnings översikten](/connectors/). Information om priser finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md)och [Logic Apps pris information](https://azure.microsoft.com/pricing/details/logic-apps/). 

<a name="built-ins"></a>

## <a name="built-ins"></a>Inbyggda program

Logic Apps innehåller inbyggda utlösare och åtgärder så att du kan skapa schemabaserade arbets flöden, hjälpa dina Logi Kap par att kommunicera med andra appar och tjänster, styra arbets flödet via dina Logi Kap par och hantera eller manipulera data.

|   |   |   |   | 
|---|---|---|---| 
| [![**Schema** för][schedule-icon]<br/>API-ikon][recurrence-doc] | – Kör din Logic app enligt ett angivet schema, från grundläggande till komplex upprepning, med **upprepnings** utlösaren. <p>– Pausa din Logic app under en angiven varaktighet med **fördröjnings** åtgärden. <p>– Pausa din Logi Kap par fram till angivet datum och tid med **fördröjningen innan** åtgärden. | [![API-][http-icon]<br/>ikon**http**][http-doc] | Kommunicera med valfri slut punkt via HTTP med både utlösare och åtgärder för HTTP, HTTP + Swagger och HTTP + webhook. | 
| [![API-][http-request-icon]<br/>ikon][http-request-doc] | – Gör ditt Logic app-anrop från andra appar eller tjänster, Utlös på Event Grid resurs händelser eller Utlös på svar till Azure Security Center aviseringar med utlösaren för **begäran** . <p>– Skicka svar till en app eller tjänst med **svars** åtgärden. | [![API-][batch-icon]<br/>ikon**batch**][batch-doc] | – Bearbeta meddelanden i batchar med utlösaren **batch-meddelanden** . <p>– Anropa Logi Kap par som har befintliga batch-utlösare med åtgärden **skicka meddelanden till batch** . | 
| [![API-][azure-functions-icon]<br/>ikon**Azure Functions**][azure-functions-doc] | Anropa Azure Functions som kör anpassade kod avsnitt (C# eller Node. js) från dina Logic Apps. | [![API-][azure-api-management-icon]</br>ikon**Azure-API Management**][azure-api-management-doc] | Anropa utlösare och åtgärder som definierats av dina egna API: er som du hanterar och publicerar med Azure API Management. | 
| [![API-][azure-app-services-icon]<br/>ikon**Azure App tjänster**][azure-app-services-doc] | Anropa Azure API Apps eller Web Apps som finns på Azure App Service. Utlösare och åtgärder som definieras av dessa appar visas som alla andra utlösare och åtgärder för första klass när Swagger ingår. | [![API-][azure-logic-apps-icon]<br/>ikon**Azure<br/>-Logic Apps**][nested-logic-app-doc] | Anropa andra Logi Kap par som börjar med en begär ande utlösare. | 
||||| 

### <a name="control-workflow"></a>Kontroll arbets flöde

Logic Apps innehåller inbyggda åtgärder för att strukturera och kontrol lera åtgärder i din Logic Apps-arbets flöde:

|   |   |   |   | 
|---|---|---|---| 
| [![**Villkor** för inbyggd ikon][condition-icon]<br/>][condition-doc] | Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt. | [![Inbyggd ikon][for-each-icon]</br>**för varje**][for-each-doc] | Utför samma åtgärder på varje objekt i en matris. | 
| [![Inbyggda ikon][scope-icon]<br/>**omfång**][scope-doc] | Gruppera åtgärder i *omfattningar*, som får sin egen status efter att åtgärderna i omfånget har körts. | [![**Växel** för inbyggd ikon][switch-icon]</br>][switch-doc] | Gruppera åtgärder i *fall*, som tilldelas unika värden förutom standard fallet. Kör bara det fall vars tilldelade värde matchar resultatet från ett uttryck, ett objekt eller en token. Kör standard fallet om inga matchningar finns. | 
| [![Inbyggd ikon][terminate-icon]<br/>**Avsluta**][terminate-doc] | Stoppa ett aktivt Logic app-arbetsflöde som körs. | [![Inbyggd ikon][until-icon]<br/>**tills**][until-doc] | Upprepa åtgärder tills det angivna villkoret är sant eller så har status ändrats. | 
||||| 

### <a name="manage-or-manipulate-data"></a>Hantera eller manipulera data

Logic Apps innehåller inbyggda åtgärder för att arbeta med data utdata och deras format:  

|   |   | 
|---|---| 
| [![Inbyggda ikon][data-operations-icon]<br/>**data åtgärder**][data-operations-doc] | Utför åtgärder med data: <p>- **Skriv**: Skapa en enda utmatning från flera indata med olika typer. <br>- **Skapa CSV-tabell**: Skapa en kommaavgränsad tabell (CSV) från en matris med JSON-objekt. <br>- **Skapa HTML-tabell**: Skapa en HTML-tabell från en matris med JSON-objekt. <br>- **Filtrera matris**: Skapa en matris från objekt i en annan matris som uppfyller dina kriterier. <br>- **Anslut**: Skapa en sträng från alla objekt i en matris och avgränsa dessa objekt med angiven avgränsare. <br>- **Parsa JSON**: Skapa användarvänliga token från egenskaper och deras värden i JSON-innehåll så att du kan använda dessa egenskaper i ditt arbets flöde. <br>- **Välj**: Skapa en matris med JSON-objekt genom att transformera objekt eller värden i en annan matris och mappa dessa objekt till angivna egenskaper. | 
| ![Inbyggd ikon][date-time-icon]<br/>**Datum tid** | Utföra åtgärder med tidsstämplar: <p>- **Lägg till i tid**: Lägg till det angivna antalet enheter i en tidsstämpel. <br>- **Konvertera tidszon**: Konvertera en tidsstämpel från käll tids zonen till mål tids zonen. <br>- **Aktuell tid**: Returnera den aktuella tidstämpeln som en sträng. <br>- **Hämta framtida tid**: Returnera den aktuella tidsstämpeln och de angivna tidsenheterna. <br>- **Hämta tidigare tid**: Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. <br>- **Subtrahera från tid**: Subtrahera ett antal tidsenheter från en tidsstämpel. |
| [![Inbyggda Icon][variables-icon]<br/>-**variabler**][variables-doc] | Utföra åtgärder med variabler: <p>- **Lägg till i mat ris variabel**: Infoga ett värde som det sista objektet i en matris som lagras av en variabel. <br>- **Lägg till i sträng variabel**: Infoga ett värde som det sista specialtecknet i en sträng som lagras av en variabel. <br>- **Minska variabeln**: Minska en variabel med ett konstant värde. <br>- **Increment-variabel**: Öka en variabel med ett konstant värde. <br>- **Initiera variabel**: Skapa en variabel och deklarera dess datatyp och initialt värde. <br>- **Ange variabel**: Tilldela ett annat värde till en befintlig variabel. |
|  |  | 

<a name="managed-api-connectors"></a>

## <a name="managed-api-connectors"></a>Hanterade API-kopplingar

Logic Apps tillhandahåller de här populära standard anslutningarna för automatisering av uppgifter, processer och arbets flöden med dessa tjänster eller system.

|   |   |   |   | 
|---|---|---|---| 
| [![API-][azure-service-bus-icon]<br/>ikon**Azure Service Bus**][azure-service-bus-doc] | Hantera asynkrona meddelanden, sessioner och ämnesprenumerationer med det anslutningsprogram som oftast används i Logic Apps. | [![API-][sql-server-icon]<br/>ikon**SQL Server**][sql-server-doc] | Anslut till din SQL Server lokalt eller en Azure SQL Database i molnet så att du kan hantera poster, köra lagrade procedurer eller köra frågor. | 
| [![API-][office-365-outlook-icon]<br/>ikon**Office<br/>365 Outlook**][office-365-outlook-doc] | Anslut till ditt Office 365-e-postkonto så att du kan skapa och hantera e-post, uppgifter, Kalender händelser och möten, kontakter, förfrågningar och mycket annat. | [![API-][azure-blob-storage-icon]<br/>ikon**Azure<br/>Blob Storage**][azure-blob-storage-doc] | Anslut till ditt lagrings konto så att du kan skapa och hantera BLOB-innehåll. | 
| [![API-][sftp-icon]<br/>ikon**SFTP**][sftp-doc] | Anslut till SFTP-servrar som du kan komma åt från Internet så att du kan arbeta med dina filer och mappar. | [![API-][sharepoint-online-icon]<br/>ikon**SharePoint<br/>online**][sharepoint-online-doc] | Anslut till SharePoint Online så att du kan hantera filer, bilagor, mappar med mera. | 
| [![API-][dynamics-365-icon]<br/>ikon**Dynamics<br/>365 CRM Online**][dynamics-365-doc] | Anslut till ditt Dynamics 365-konto så att du kan skapa och hantera poster, artiklar med mera. | [![API-][ftp-icon]<br/>ikon**FTP**][ftp-doc] | Anslut till FTP-servrar som du kan komma åt från Internet så att du kan arbeta med dina filer och mappar. | 
| [![API-][salesforce-icon]<br/>ikon**Salesforce**][salesforce-doc] | Anslut till ditt Salesforce-konto så att du kan skapa och hantera objekt, till exempel poster, jobb, objekt med mera. | [![API-][twitter-icon]<br/>ikon**Twitter**][twitter-doc] | Anslut till ditt Twitter-konto så att du kan hantera tweets, följare, din tids linje och mer. Spara din tweets i SQL, Excel eller SharePoint. | 
| [![API-][azure-event-hubs-icon]<br/>ikon**Azure-Event Hubs**][azure-event-hubs-doc] | Använda och publicera händelser via en Event Hub. Du kan till exempel hämta utdata från din Logic app med Event Hubs och sedan skicka utdata till en leverantör av real tids analys. | [![API-][azure-event-grid-icon]<br/>ikon**Azure Event**</br>**Grid**][azure-event-grid-doc] | Övervaka händelser som publicerats av en Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras. | 
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Lokala anslutningsappar 

Här följer några vanliga standard anslutningar som Logic Apps ger åtkomst till data och resurser i lokala system. Innan du kan skapa en anslutning till ett lokalt system måste du först [Ladda ned, installera och konfigurera en lokal datagateway][gateway-doc]. Den här gatewayen tillhandahåller en säker kommunikations kanal utan att behöva konfigurera den nödvändiga nätverks infrastrukturen. 

|   |   |   |   |   | 
|---|---|---|---|---| 
| ![API-ikon][biztalk-server-icon]<br/>**BizTalk**</br> **Server** | [![API-][file-system-icon]<br/>ikon**fil</br> system**][file-system-doc] | [![API-][ibm-db2-icon]<br/>ikon**IBM DB2**][ibm-db2-doc] | [![API-][ibm-informix-icon]<br/>ikon**IBM** </br> **Informix**][ibm-informix-doc] | ![API-ikon][mysql-icon]<br/>**MySQL** | 
| [![API-][oracle-db-icon]<br/>ikon**Oracle DB**][oracle-db-doc] | ![API-ikon][postgre-sql-icon]<br/>**PostgreSQL** | [![API-][sharepoint-server-icon]<br/>ikon**SharePoint</br> Server**][sharepoint-server-doc] | [![API-][sql-server-icon]<br/>ikon**SQL</br> Server**][sql-server-doc] | ![API-ikon][teradata-icon]<br/>**Teradata** | 
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton

Logic Apps tillhandahåller standard anslutningar för att skapa B2B-lösningar (Business-to-Business) med dina Logi Kap par när du skapar och betalar för ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är tillgängligt via Enterprise-INTEGRATIONSPAKET (EIP) i Azure. Med det här kontot kan du skapa och lagra B2B-artefakter som handels partner, avtal, kartor, scheman, certifikat och så vidare. Om du vill använda dessa artefakter associerar du dina Logi Kap par med ditt integrations konto. Om du för närvarande använder BizTalk Server kan dessa anslutningar verka bekant redan.

|   |   |   |   | 
|---|---|---|---| 
| [![API-][as2-icon]<br/>ikon**AS2</br>**  -avkodning][as2-doc] | [![API-][as2-icon]<br/>ikon**AS2</br> -kodning**][as2-doc] | [![API-][edifact-icon]<br/>ikon**EDIFACT</br>**  -avkodning][edifact-decode-doc] | [![API-][edifact-icon]<br/>ikon**EDIFACT</br> -kodning**][edifact-encode-doc] | 
| [![API-][flat-file-decode-icon]<br/>ikon**Flat</br> fil** -avkodning][flat-file-decode-doc] | [![API-][flat-file-encode-icon]<br/>ikon**Flat</br> fil kodning**][flat-file-encode-doc] | [![API-][integration-account-icon]<br/>ikon**integrations<br/>konto**][integration-account-doc] | [![API-][liquid-icon]<br/>ikon**flytande**</br>**transformeringar**][json-liquid-transform-doc] | 
| [![API-][x12-icon]<br/>ikon**X12</br>**  -avkodning][x12-decode-doc] | [![API-][x12-icon]<br/>ikon**X12</br> -kodning**][x12-encode-doc] | [![][xml-transform-icon]<br/>**XML-** </br>transformeringar för API-ikon][xml-transform-doc] | [![API-][xml-validate-icon]<br/>ikon **<br/>XML-verifiering**][xml-validate-doc] |  
||||| 

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Logic Apps tillhandahåller de här företags anslutningarna för åtkomst till företags system, till exempel SAP och IBM MQ:

|   |   |   | 
|---|---|---| 
| [![API-][ibm-3270-icon]<br/>ikon**IBM 3270**][ibm-3270-doc] | [![API-][ibm-mq-icon]<br/>ikon**IBM MQ**][ibm-mq-doc] | [![API-][sap-icon]<br/>ikon**SAP**][sap-connector-doc] |
|||| 

<a name="triggers-actions"></a>

## <a name="triggers-and-actions---more-info"></a>Utlösare och åtgärder – mer information

Kopplingar kan tillhandahållautlösare, *åtgärder*eller både och. En utlösare är det första steget i alla logiska appar, vanligt vis genom att ange den händelse som utlöser utlösaren och börjar köra din Logic app. FTP-anslutningen har till exempel en utlösare som startar din Logic app "när en fil läggs till eller ändras". Vissa utlösare söker regelbundet efter den angivna händelsen eller data och utlöses sedan när de identifierar den angivna händelsen eller informationen. Andra utlösare väntar men utlöses direkt när en enskild händelse inträffar eller när nya data är tillgängliga. Utlösare skickas också tillsammans med alla data som krävs till din Logic app. Din Logic app kan läsa och använda dessa data i hela arbets flödet.
Twitter-anslutningen har exempelvis en utlösare, "när en ny tweet publiceras", som skickar Tweetens innehåll till din Logic app-arbetsflöde. 

När en utlösare aktive ras skapar Azure Logic Apps en instans av din Logic app och börjar köra *åtgärderna* i din Logic app-arbetsflöde. Åtgärder är de steg som följer utlösaren och utför uppgifter i din Logic app-arbetsflöde. Du kan till exempel skapa en Logic-app som hämtar kund information från en SQL-databas och bearbetar dessa data i senare åtgärder. 

Här är de allmänna typerna av utlösare som Azure Logic Apps tillhandahåller:

* *Upprepnings*utlösare: Den här utlösaren körs enligt ett angivet schema och är inte nära kopplad till en viss tjänst eller ett visst system.

* Avsöknings utlösare: Den här utlösaren avsöker regelbundet en specifik tjänst eller ett system baserat på det angivna schemat, söker efter nya data eller om en specifik händelse har inträffat. Om nya data är tillgängliga eller om den här händelsen har inträffat skapar utlösaren och kör en ny instans av din Logic app, som nu kan använda de data som skickas som indata.

* *Push*-utlösare: Den här utlösaren väntar och lyssnar efter nya data eller för att en händelse ska inträffa. När nya data är tillgängliga eller när händelsen inträffar skapar utlösaren och kör en ny instans av din Logic app, som nu kan använda de data som skickas som indata.

<a name="custom"></a>

## <a name="connector-configuration"></a>Anslutnings konfiguration

Varje kopplings utlösare och åtgärder tillhandahåller egna egenskaper som du kan konfigurera. Många anslutningar kräver också att du först skapar en *anslutning* till mål tjänsten eller systemet och anger autentiseringsuppgifter för autentisering eller annan konfigurations information innan du kan använda en utlösare eller åtgärd i din Logic app. Du måste till exempel auktorisera en anslutning till ett Twitter-konto för att komma åt data eller för att publicera åt dig. 

För anslutningar som använder OAuth innebär en anslutning att du loggar in på tjänsten, till exempel Office 365, Salesforce eller GitHub, där din åtkomsttoken krypteras och lagras på ett säkert sätt i ett Azure-hemligt arkiv. Andra anslutningar, till exempel FTP och SQL, kräver en anslutning som har konfigurations information, till exempel Server adressen, användar namnet och lösen ordet. Dessa anslutnings konfigurations uppgifter krypteras och lagras på ett säkert sätt. 

Anslutningarna kan komma åt mål tjänsten eller systemet så länge tjänsten eller systemet tillåter det. För tjänster som använder Azure Active Directory (AD) OAuth-anslutningar, till exempel Office 365 och Dynamics, Azure Logic Apps uppdaterar åtkomsttoken på obestämd tid. Andra tjänster kan ha gränser för hur länge Azure Logic Apps kan använda en token utan att uppdatera. I allmänhet är vissa åtgärder ogiltiga för alla åtkomsttoken, till exempel att ändra ditt lösen ord.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Anpassade API: er och anslutningar

Om du vill anropa API: er som kör anpassad kod eller som inte är tillgängliga som kopplingar kan du utöka Logic Apps-plattformen genom att [skapa anpassade API Apps](../logic-apps/logic-apps-create-api-app.md). Du kan också [skapa anpassade anslutningar](../logic-apps/custom-connector-overview.md) för *alla* rest-eller SOAP-baserade API: er som gör dessa API: er tillgängliga för alla Logic-appar i din Azure-prenumeration.
Om du vill göra anpassade API Apps eller anslutningar offentliga för alla som ska användas i Azure kan du [skicka in anslutningar för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Logic Apps i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kan komma åt resurser direkt i ett virtuellt Azure-nätverk.
> Om du har anpassade anslutningar som kräver den lokala datagatewayen och du har skapat dessa anslutningar utanför en ISE kan Logic Apps i en ISE också använda dessa anslutningar.
>
> Anpassade anslutningar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt komma åt lokala data källor som är anslutna till ett virtuellt Azure-nätverk som är värd för ISE. Därför behöver Logic Apps i en ISE förmodligen inte datagatewayen när de kommunicerar med dessa resurser.
>
> Mer information om hur du skapar ISEs finns i [ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Nästa steg

* Hitta [kopplingens fullständiga lista](https://docs.microsoft.com/connectors)
* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa anpassade anslutningar för Logic Apps](https://docs.microsoft.com/connectors/custom-connectors/)
* [Skapa anpassade API:er för logikappar](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Anslut till datakällor lokalt från logikappar med lokala datagatewayer"

<!--Built-ins doc links-->
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Skapa en Azure API Management-tjänstinstans för att hantera och publicera dina API: er"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrera logikappar med App Service API Apps"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Bearbeta meddelanden i grupper eller som batchar"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt"
[delay-doc]: ./connectors-native-delay.md "Utföra fördröjda åtgärder"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Utför samma åtgärder på varje objekt i en matris"
[http-doc]: ./connectors-native-http.md "Göra HTTP-anrop med HTTP-anslutningsappen"
[http-request-doc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[http-response-doc]: ./connectors-native-reqres.md "Lägga till åtgärder för HTTP-begäranden och svar i logikappar"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Göra HTTP-anrop med HTTP + Swagger-anslutningsappen"
[http-webook-doc]: ./connectors-native-webhook.md "Lägg till HTTP webhook-åtgärder och utlösare i Logi Kap par"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[query-doc]: ./connectors-native-query.md "Välja och filtrera matriser med frågeåtgärden"
[recurrence-doc]:  ./connectors-native-recurrence.md "Utlösa återkommande åtgärder för logikappar"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organisera åtgärder i grupper som får sin egen status efter att åtgärderna i gruppen har körts" 
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organisera åtgärder i fall, som tilldelas unika värden. Kör bara det fall vars värde matchar resultatet från ett uttryck, ett objekt eller en token. Om inga matchningar finns kör du standard fallet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stoppa eller Avbryt ett aktivt pågående arbets flöde för din Logic app"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Upprepa åtgärder tills det angivna villkoret är sant eller så har status ändrats"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Utföra data åtgärder, till exempel för att filtrera matriser eller skapa CSV-och HTML-tabeller"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Utföra åtgärder med variabler, till exempel initiera, ange, öka, minska och lägga till i sträng eller mat ris variabel"

<!--Managed API doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobcontainern med Azure Blob Storage Connector"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Övervaka händelser som publicerats av en Event Grid, till exempel när Azure-resurser eller resurser från tredje part ändras"
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
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ansluta till 3270-appar i IBM-stordatorer"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell och mycket mer"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läsa en rad, lista tabeller och annat"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ansluta till IBM MQ lokalt eller i Azure för att skicka och ta emot meddelanden"
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
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagar listor"
[sftp-doc]: ./connectors-create-api-sftp.md "Anslut till ditt SFTP-konto. Överföra, hämta och ta bort filer med mera"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Anslut till lokala SharePoint-servrar. Hantera dokument, listobjekt och annat"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Anslut till SharePoint Online. Hantera dokument, listobjekt och annat"
[slack-doc]: ./connectors-create-api-slack.md "Ansluta till Slack och skicka meddelanden till Slack-kanaler"
[smtp-doc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bifogade filer"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQL Server. Skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell."
[trello-doc]: ./connectors-create-api-trello.md "Anslut till Trello. Hantera dina projekt och organisera vad du vill med vem du vill"
[twilio-doc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, hämta tillgängliga nummer, hantera inkommande telefonnummer och mycket mer"
[twitter-doc]: ./connectors-create-api-twitter.md "Anslut till Twitter. Hämta tidslinjer, publicera tweets och mycket mer"
[wunderlist-doc]: ./connectors-create-api-wunderlist.md "Anslut till Wunderlist. Hantera uppgifter och uppgiftslistor, håll dig uppdaterad och mycket mer"
[yammer-doc]: ./connectors-create-api-yammer.md "Anslut till Yammer. Skicka meddelanden, få nya meddelanden och mycket mer"
[youtube-doc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera videor och kanaler"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Läs mer om AS2 för Enterprise-integration."
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
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
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
