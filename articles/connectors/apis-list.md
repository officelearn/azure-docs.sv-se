---
title: Anslutningsprogram för Azure Logic Apps
description: Automatisera arbetsflöden med anslutningsappar för Azure Logic Apps, till exempel inbyggda, hanterade, lokala, integrationskonto, ISE och företagskopplingar
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247337"
---
# <a name="connectors-for-azure-logic-apps"></a>Anslutningsprogram för Azure Logic Apps

Anslutningsappar ger snabb åtkomst från Azure Logic Apps till händelser, data och åtgärder i andra appar, tjänster, system, protokoll och plattformar. Genom att använda anslutningsprogram i dina logikappar kan du utöka funktionerna för dina molnappar och lokala appar så att du kan utföra olika åtgärder för de data som du skapar och redan har.

Logic Apps erbjuder [hundratals kopplingar,](https://docs.microsoft.com/connectors)men den här artikeln beskriver de *populära och vanligaste* kopplingarna som används av tusentals appar och miljontals körningar för bearbetning av data och information. Om du vill hitta den fullständiga listan över kopplingar och varje kopplings referensinformation, till exempel utlösare, åtgärder och begränsningar, granskar du [anslutningsreferenssidorna](https://docs.microsoft.com/connectors)under Översikt över Kopplingar . Läs också mer om [utlösare och åtgärder,](#triggers-actions) [logic apps prismodell](../logic-apps/logic-apps-pricing.md)och [Logic Apps prisinformation](https://azure.microsoft.com/pricing/details/logic-apps/).

> [!TIP]
> Om du vill integrera med en tjänst eller ett API som inte har anslutningsapp kan du antingen direkt anropa tjänsten via ett protokoll som HTTP eller skapa en [anpassad anslutningsapp](#custom).

## <a name="connector-types"></a>Kopplingstyper

Kopplingar är tillgängliga som inbyggda utlösare och åtgärder eller som hanterade kopplingar.

<a name="built-in"></a>

* [**Inbyggd:**](#built-ins)Inbyggda utlösare och åtgärder är "inbyggda" i Azure Logic Apps och hjälper dig att utföra dessa uppgifter för dina logikappar:

  * Kör på anpassade och avancerade scheman.

  * Organisera och kontrollera logikappens arbetsflöde, till exempel loopar och villkor, och även för att arbeta med variabler och dataåtgärder.

  * Kommunicera med andra slutpunkter.

  * Ta emot och svara på förfrågningar.

  * Anropa Azure-funktioner, Azure API Apps (Web Apps), dina egna API:er som hanteras och publiceras med Azure API Management och kapslade logikappar som kan ta emot begäranden.

<a name="managed-connectors"></a>

* [**Hanterade anslutningsappar:**](#managed-api-connectors)Distribuerade och hanterade av Microsoft, dessa kopplingar ger utlösare och åtgärder för åtkomst till molntjänster, lokala system eller båda, inklusive Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint med mera. Vissa anslutningsappar stöder specifikt B2B-kommunikationsscenarier (Business-to-Business) och kräver ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkat till logikappen. Innan du använder vissa kopplingar kan du behöva först skapa anslutningar som hanteras av Azure Logic Apps.

  Om du till exempel använder Microsoft BizTalk Server kan dina logikappar ansluta till och kommunicera med BizTalk Server med hjälp av [den lokala BizTalk-servern.](#on-premises-connectors) Du kan sedan utöka eller utföra BizTalk-liknande åtgärder i logikapparna med hjälp av [anslutningsapparna](#integration-account-connectors).

  Kopplingar klassificeras som standard eller Enterprise. [Företagsanslutningar](#enterprise-connectors) ger åtkomst till företagssystem som SAP, IBM MQ och IBM 3270 mot en extra kostnad. Information om du vill ta reda på om en koppling är Standard eller Enterprise finns i den tekniska informationen i varje kopplings referenssida under [Översikt över Kopplingar](https://docs.microsoft.com/connectors).

  Du kan också identifiera kopplingar med hjälp av dessa kategorier, även om vissa kopplingar kan korsa flera kategorier. SAP är till exempel en Enterprise-anslutning och en lokal anslutningsapp:

  |   |   |
  |---|---|
  | [**Hanterade kopplingar**](#managed-api-connectors) | Skapa logikappar som använder tjänster som Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online och många fler. |
  | [**Lokala anslutningsappar**](#on-premises-connectors) | När du har installerat och konfigurerat den [lokala datagatewayen][gateway-doc]hjälper dessa kopplingar dina logikappar att komma åt lokala system som SQL Server, SharePoint Server, Oracle DB, filresurser och andra. |
  | [**Anslutningar för integrationskonton**](#integration-account-connectors) | Dessa kopplingar är tillgängliga när du skapar och betalar för ett integrationskonto och omvandlar och validerar XML-meddelanden, kodar och avkodar platta filer och bearbetar B2B-meddelanden (Business-to-business) med AS2-, EDIFACT- och X12-protokoll. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>Ansluta från en integrationstjänstmiljö

För logikappar som behöver direkt åtkomst till resurser i ett virtuellt Azure-nätverk kan du skapa en isolerad [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) där du kan skapa, distribuera och köra dina logikappar på dedikerade resurser. När du bläddrar bland de anslutningsappar som du vill använda för logikappar i en ISE i Logic App Designer visas en **CORE-etikett** på inbyggda utlösare och åtgärder, medan **ISE-etiketten** visas på vissa kopplingar:

* **CORE**: Inbyggda utlösare och åtgärder med den här etiketten körs i samma ISE som dina logikappar, till exempel:

  ![Exempel på ISE-anslutning](./media/apis-list/example-core-connector.png)

* **ISE**: Hanterade kopplingar med den här etiketten körs i samma ISE som dina logikappar, till exempel:

  ![Exempel på ISE-anslutning](./media/apis-list/example-ise-connector.png)

  Om du har ett lokalt system som är anslutet till ett virtuellt Azure-nätverk kan dina logikappar komma åt det systemet direkt utan den [lokala datagatewayen](../logic-apps/logic-apps-gateway-connection.md). I stället kan du antingen använda systemets **ISE-anslutning** om det finns en HTTP-åtgärd eller en [anpassad anslutning](#custom). För lokala system som inte har **ISE-kopplingar** använder du lokal datagateway. Information om hur du granskar tillgängliga ISE-kopplingar finns i [ISE-kopplingar](#ise-connectors).

* Alla andra kopplingar utan **CORE-** eller **ISE-etiketten,** som du kan fortsätta att använda, körs i den globala Logic Apps-tjänsten med flera innehavare, till exempel:

  ![Exempel på anslutning med flera innehavare](./media/apis-list/example-multi-tenant-connector.png)

Logikappar som körs i en ISE och deras kopplingar, oavsett var dessa kopplingar körs, följer en fast prisplan jämfört med den förbrukningsbaserade prisplanen. Mer information finns på följande sidor:

* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Prisinformation för Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>Inbyggd

Logic Apps tillhandahåller inbyggda utlösare och åtgärder så att du kan skapa schemabaserade arbetsflöden, hjälpa dina logikappar att kommunicera med andra appar och tjänster, styra arbetsflödet via dina logikappar och hantera eller manipulera data.

|   |   |   |   |
|---|---|---|---|
| [![API-ikon][schedule-icon]<br>**Schema**][schedule-doc] | - Kör en logikapp på en angiven upprepning, allt från grundläggande till avancerade scheman med [ **utlösaren Återkommande** ][schedule-recurrence-doc]. <p>- Kör en logikapp som måste hantera data i kontinuerliga segment med [ **utlösaren skjutfönster** ][schedule-sliding-window-doc]. <p>- Pausa logikappen under en angiven tid med åtgärden [ **Fördröjning** ][schedule-delay-doc]. <p>- Pausa logikappen tills det angivna datumet och tiden med [ **åtgärden Fördröjning tills.** ][schedule-delay-until-doc] | [![][batch-icon]<br>**API-ikon Batch**][batch-doc] | - Bearbeta meddelanden i batchar med **batch-meddelanden** utlösaren. <p>- Anropa logikappar som har befintliga batchutlösare med åtgärden **Skicka meddelanden till batch.** |
| [![][http-icon]<br>**API-ikon HTTP**][http-doc] | Anropa HTTP- eller HTTPS-slutpunkter med utlösare och åtgärder för HTTP. Andra HTTP-inbyggda utlösare och åtgärder inkluderar [HTTP + Swagger][http-swagger-doc] och [HTTP + Webhook][http-webhook-doc]. | [![API-ikon][http-request-icon]<br>**Begäran**][http-request-doc] | - Gör din logikapp callable från andra appar eller tjänster, utlösa på Event Grid resurshändelser, eller utlösa på svar till Azure Security Center-aviseringar med **begäran utlösaren.** <p>- Skicka svar till en app eller tjänst med **svarsåtgärden.** |
| [![API-ikonen][azure-api-management-icon]<br>**Azure API <br>Management**][azure-api-management-doc] | Anropa utlösare och åtgärder som definieras av dina egna API:er som du hanterar och publicerar med Azure API Management. | [![API-ikonen][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Anropa Azure API-appar, eller webbappar, som finns på Azure App Service. De utlösare och åtgärder som definieras av dessa appar visas som alla andra förstklassiga utlösare och åtgärder när Swagger ingår.|
| [![API-ikonen][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | Anropa andra logikappar som börjar med **utlösaren Begäran.** |
|||||

### <a name="run-code-from-logic-apps"></a>Kör kod från logikappar

Logic Apps innehåller inbyggda åtgärder för att köra din egen kod i logikappens arbetsflöde:

|   |   |   |   |
|---|---|---|---|
| [![API-ikonen][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | Anropa Azure-funktioner som kör anpassade kodavsnitt (C# eller Node.js) från dina logikappar. | [![][inline-code-icon]<br>**Infogad API-ikon**][azure-functions-doc] | Lägg till och kör JavaScript-kodavsnitt från dina logikappar. |
|||||

### <a name="control-workflow"></a>Kontrollera arbetsflödet

Logic Apps innehåller inbyggda åtgärder för att strukturera och styra åtgärderna i logikappens arbetsflöde:

|   |   |   |   |
|---|---|---|---|
| [![Inbyggt][condition-icon]<br>**ikonvillkor**][condition-doc] | Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt. | [![Inbyggd ikon][for-each-icon]<br>**för varje**][for-each-doc] | Utför samma åtgärder på alla objekt i en matris. |
| [![Inbyggt][scope-icon]<br>**ikonomfattningsomfattning**][scope-doc] | Gruppera åtgärder i *scope ,* som får sin egen status när åtgärderna i omfånget har körts. | [![Inbyggd][switch-icon]<br>**ikonväxel**][switch-doc] | Gruppera åtgärder i *ärenden*som tilldelas unika värden förutom standardfallet. Kör bara det fall vars tilldelade värde matchar resultatet från ett uttryck, objekt eller en token. Om det inte finns några matchningar kör du standardfallet. |
| [![Inbyggd ikon][terminate-icon]<br>**avsluta**][terminate-doc] | Stoppa ett arbetsflöde som körs aktivt i logikapp. | [![Inbyggd ikon][until-icon]<br>**tills**][until-doc] | Upprepa åtgärder tills det angivna villkoret är sant eller något tillstånd har ändrats. |
|||||

### <a name="manage-or-manipulate-data"></a>Hantera eller manipulera data

Logic Apps innehåller inbyggda åtgärder för att arbeta med datautdata och deras format:

|   |   |
|---|---|
| [![Inbyggda][data-operations-icon]<br>**ikondataåtgärder**][data-operations-doc] | Utför åtgärder med data: <p>- **Komponera:** Skapa en enda utdata från flera indata med olika typer. <br>- **Skapa CSV-tabell:** Skapa en CSV-tabell (kommaavgränsad värde) från en matris med JSON-objekt. <br>- **Skapa HTML-tabell:** Skapa en HTML-tabell från en matris med JSON-objekt. <br>- **Filtermatris:** Skapa en matris från objekt i en annan matris som uppfyller dina villkor. <br>- **Gå med**: Skapa en sträng från alla objekt i en matris och separera dessa objekt med den angivna avgränsaren. <br>- **Tolka JSON**: Skapa användarvänliga token från egenskaper och deras värden i JSON-innehåll så att du kan använda dessa egenskaper i arbetsflödet. <br>- **Markera**: Skapa en matris med JSON-objekt genom att omvandla objekt eller värden i en annan matris och mappa dessa objekt till angivna egenskaper. |
| ![Inbyggd ikon][date-time-icon]<br>**Datum tid** | Utför åtgärder med tidsstämplar: <p>- **Lägg till i tid**: Lägg till det angivna antalet enheter i en tidsstämpel. <br>- **Konvertera tidszon:** Konvertera en tidsstämpel från källtidszonen till måltidszonen. <br>- **Aktuell tid**: Returnera den aktuella tidsstämpeln som en sträng. <br>- **Hämta framtida tid:** Returnera aktuell tidsstämpel plus de angivna tidsenheterna. <br>- **Hämta tidigare tid**: Returnera aktuell tidsstämpel minus de angivna tidsenheterna. <br>- **Subtrahera från tid**: Subtrahera ett antal tidsenheter från en tidsstämpel. |
| [![Inbyggda][variables-icon]<br>**ikonvariabler**][variables-doc] | Utför åtgärder med variabler: <p>- **Lägg till i matrisvariabel:** Infoga ett värde som det sista objektet i en matris som lagras av en variabel. <br>- **Lägg till strängvariabel:** Infoga ett värde som det sista tecknet i en sträng som lagras av en variabel. <br>- **Minskning av en**variabel med ett konstant värde. <br>- **Ökningsvariabel:** Öka en variabel med ett konstant värde. <br>- **Initiera variabel:** Skapa en variabel och deklarera dess datatyp och initiala värde. <br>- **Ange variabel**: Tilldela ett annat värde till en befintlig variabel. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Hanterade kopplingar

Logic Apps tillhandahåller dessa populära standardkopplingar för att automatisera uppgifter, processer och arbetsflöden med dessa tjänster eller system:

|   |   |   |   |
|---|---|---|---|
| [![API-ikonen][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Hantera asynkrona meddelanden, sessioner och ämnesprenumerationer med det anslutningsprogram som oftast används i Logic Apps. | [![][sql-server-icon]<br>**API-ikon SQL Server**][sql-server-doc] | Anslut till din SQL Server lokalt eller en Azure SQL-databas i molnet så att du kan hantera poster, köra lagrade procedurer eller utföra frågor. |
| [![API-ikonen][azure-blob-storage-icon]<br>**Azure<br>Blob Storage**][azure-blob-storage-doc] | Anslut till ditt lagringskonto så att du kan skapa och hantera blob-innehåll. | [![API-ikonen][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | Anslut till ditt Office 365-e-postkonto så att du kan skapa och hantera e-postmeddelanden, uppgifter, kalenderhändelser och möten, kontakter, förfrågningar med mera. |
| [![API-ikonen][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | Anslut till SFTP-servrar som du kan komma åt från Internet med hjälp av SSH så att du kan arbeta med dina filer och mappar. | [![][sharepoint-online-icon]<br>**API-ikonen<br>SharePoint Online**][sharepoint-online-doc] | Anslut till SharePoint Online så att du kan hantera filer, bifogade filer, mappar med mera. | 
| [![API-ikonen][dynamics-365-icon]<br>**Dynamics 365<br> **][dynamics-365-doc] | Anslut till ditt Dynamics 365-konto så att du kan skapa och hantera poster, objekt med mera. | [![API-ikonen][azure-queues-icon]<br>**Azure-köer <br>**][azure-queues-doc] | Anslut till ditt Azure Storage-konto så att du kan skapa och hantera köer och meddelanden |
| [![][ftp-icon]<br>**API-ikonen FTP**][ftp-doc] | Anslut till FTP-servrar som du kan komma åt från internet så att du kan arbeta med dina filer och mappar. | [![API-ikon][file-system-icon]<br>**Filsystem <br>**][file-system-doc] | Anslut till den lokala filresursen så att du kan skapa och hantera filer. |
| [![API-ikonen][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | Konsumera och publicera händelser via en eventhub. Du kan till exempel hämta utdata från logikappen med eventhubbar och sedan skicka utdata till en analysleverantör i realtid. | [![API-ikonen][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Övervaka händelser som publiceras av ett eventrutnät, till exempel när Azure-resurser eller resurser från tredje part ändras. |
| [![API-ikonen][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Anslut till ditt Salesforce-konto så att du kan skapa och hantera objekt som poster, jobb, objekt med mera. | [![API-ikonen][twitter-icon]<br>**Twitter**][twitter-doc] | Anslut till ditt Twitter-konto så att du kan hantera tweets, följare, din tidslinje med mera. Spara dina tweets i SQL, Excel eller SharePoint. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Lokala anslutningsappar

Här är några vanliga standardkopplingar som Logic Apps tillhandahåller för åtkomst till data och resurser i lokala system. Innan du kan skapa en anslutning till ett lokalt system måste du först [hämta, installera och konfigurera en lokal datagateway][gateway-doc]. Den här gatewayen tillhandahåller en säker kommunikationskanal utan att behöva konfigurera den nödvändiga nätverksinfrastrukturen.

|   |   |   |   |   |
|---|---|---|---|---|
| [![API-ikonen][biztalk-server-icon]<br>**BizTalk** <br> **Server**][biztalk-server-doc] | [![API-ikon][file-system-icon]<br>**Filsystem <br>**][file-system-doc] | [![][ibm-db2-icon]<br>**API-ikon IBM DB2**][ibm-db2-doc] | [![][ibm-informix-icon]<br>**API-ikonen IBM** <br> **Informix**][ibm-informix-doc] | [![API-ikonen][mysql-icon]<br>**MySQL**][mysql-doc] |
| [![API-ikon][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc] | [![API-ikonen][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc] | [![][sharepoint-server-icon]<br>**Api-ikonen <br>SharePoint Server**][sharepoint-server-doc] | [![][sql-server-icon]<br>**API-ikon SQL <br>Server**][sql-server-doc] | [![][teradata-icon]<br>**API-ikonen Teradata**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Anslutningar för integrationskonton

Logic Apps tillhandahåller Standard-kopplingar för att skapa B2B-lösningar (Business To Business-to-Business) med dina logikappar när du skapar och betalar för ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), som är tillgängligt via EIP (Enterprise Integration Pack) i Azure. Med det här kontot kan du skapa och lagra B2B-artefakter som handelspartner, avtal, kartor, scheman, certifikat och så vidare. Om du vill använda dessa artefakter associerar du dina logikappar med ditt integrationskonto. Om du för närvarande använder BizTalk Server kan dessa kopplingar redan kännas välbekanta.

|   |   |   |   |
|---|---|---|---|
| [![][as2-icon]<br>**API-ikon <br>AS2-avkodning**][as2-doc] | [![][as2-icon]<br>**AS2-kodning <br>för API-ikon**][as2-doc] | [![API-ikon][edifact-icon]<br>**EDIFACT-avkodning <br>**][edifact-decode-doc] | [![API-ikon][edifact-icon]<br>**EDIFACT-kodning <br>**][edifact-encode-doc] |
| [![API-ikon][flat-file-decode-icon]<br>**Platt fil <br>avkodning**][flat-file-decode-doc] | [![API-ikon][flat-file-encode-icon]<br>**Platt filkodning <br>**][flat-file-encode-doc] | [![API-ikonen][integration-account-icon]<br>**Integrationskonto <br>**][integration-account-doc] | [![API-ikonen][liquid-icon]<br>**Flytande** <br> **transformeringar**][json-liquid-transform-doc] |
| [![API-ikon][x12-icon]<br>** <br>X12 avkodning**][x12-decode-doc] | [![API-ikon][x12-icon]<br>** <br>X12-kodning**][x12-encode-doc] | [![XML-transformeringar][xml-transform-icon]<br> **för** **API-ikonen** <br>][xml-transform-doc] | [![][xml-validate-icon]<br>**XML-validering <br>av API-ikon**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Enterprise-anslutningsappar

Logic Apps tillhandahåller dessa Enterprise-anslutningsappar för åtkomst till företagssystem, till exempel SAP och IBM MQ:

|   |   |   |
|---|---|---|
| [![API-ikonen][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![][ibm-mq-icon]<br>**API-ikonen IBM MQ**][ibm-mq-doc] | [![][sap-icon]<br>**API-ikon SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE-kopplingar

För logikappar som du skapar och kör en isolerad [integrationstjänstmiljö (ISE)](#integration-service-environment)identifierar Logic App Designer inbyggda utlösare och åtgärder som körs i DIN ISE med hjälp av **CORE-etiketten.** Hanterade kopplingar som körs i en ISE visar **ISE-etiketten,** medan kopplingar som körs i den globala Logic Apps-tjänsten med flera innehavare inte visar någon av etiketterna. I den här listan visas de kopplingar som för närvarande har ISE-versioner:

|   |   |   |   |   |
|---|---|---|---|---|
[![][as2-icon]<br>**API-ikon AS2**][as2-doc] | [![API-ikonen][azure-blob-storage-icon]<br>**Azure<br>Blob Storage**][azure-blob-storage-doc] | [![API-ikonen][azure-cosmos-db-icon]<br>**Azure <br> Cosmos DB**][azure-cosmos-db-doc] | [![API-ikonen][azure-event-hubs-icon]<br>**Azure Event <br>Hubs**][azure-event-hubs-doc] | [![API-ikonen][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc] |
| [![API-ikonen][azure-service-bus-icon]<br>**Azure Service <br>Bus**][azure-service-bus-doc] | [![API-ikonen][azure-sql-data-warehouse-icon]<br>**Azure <br>SQL Data Warehouse**][azure-sql-data-warehouse-doc] | [![API-ikonen][azure-table-storage-icon]<br>**Azure Table <br>Storage**][azure-table-storage-doc] | [![API-ikonen][azure-queues-icon]<br>**Azure-köer <br>**][azure-queues-doc] | [![API-ikon][edifact-icon]<br>**EDIFACT**][edifact-doc] |
| [![API-ikon][file-system-icon]<br>**Filsystem <br>**][file-system-doc] | [![][ftp-icon]<br>**API-ikonen FTP**][ftp-doc] | [![API-ikonen][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc] | [![][ibm-db2-icon]<br>**API-ikon IBM DB2**][ibm-db2-doc] | [![][ibm-mq-icon]<br>**API-ikonen IBM MQ**][ibm-mq-doc] |
| [![][sap-icon]<br>**API-ikon SAP**][sap-connector-doc] | [![API-ikonen][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | [![][smtp-icon]<br>**API-ikonen SMTP**][smtp-doc] | [![][sql-server-icon]<br>**API-ikon SQL <br>Server**][sql-server-doc] | [![API-ikon][x12-icon]<br>**X12**][x12-doc] |
||||||

Mer information finns i de här ämnena:

* [Åtkomst till virtuella Azure-nätverksresurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Prissättningsmodell för Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Utlösare och åtgärdstyper

Kopplingar kan ge *utlösare,* *åtgärder*eller båda. En *utlösare* är det första steget i en logikapp, som vanligtvis anger den händelse som utlöser utlösaren och börjar köra logikappen. FTP-kopplingen har till exempel en utlösare som startar logikappen "när en fil läggs till eller ändras". Vissa utlösare kontrollerar regelbundet den angivna händelsen eller data och avfyras sedan när de upptäcker den angivna händelsen eller data. Andra utlösare väntar men utlöses direkt när en viss händelse inträffar eller när nya data är tillgängliga. Utlösare skickar också alla nödvändiga data till logikappen. Logikappen kan läsa och använda dessa data i hela arbetsflödet. Twitter-kopplingen har till exempel en utlösare, "När en ny tweet publiceras", som skickar tweetens innehåll till logikappens arbetsflöde.

När en utlösare har avfyrats skapar Azure Logic Apps en instans av logikappen och börjar köra *åtgärderna* i logikappens arbetsflöde. Åtgärder är de steg som följer utlösaren och utför uppgifter i logikappens arbetsflöde. Du kan till exempel skapa en logikapp som hämtar kunddata från en SQL-databas och bearbeta dessa data i senare åtgärder.

Här är de allmänna typerna av utlösare som Azure Logic Apps tillhandahåller:

* *Återkommande utlösare:* Den här utlösaren körs enligt ett angivet schema och är inte tätt associerad med en viss tjänst eller ett visst system.

* *Avsökningsutlösare*: Den här utlösaren avsöker regelbundet en viss tjänst eller ett visst system baserat på det angivna schemat, söker efter nya data eller om en viss händelse har inträffat. Om nya data är tillgängliga eller om den specifika händelsen har inträffat skapar och kör utlösaren en ny instans av logikappen, som nu kan använda de data som skickas som indata.

* *Tryckutlösare:* Den här utlösaren väntar och lyssnar efter nya data eller för att en händelse ska inträffa. När nya data är tillgängliga eller när händelsen inträffar skapar och kör utlösaren en ny instans av logikappen, som nu kan använda de data som skickas som indata.

<a name="connections"></a>

## <a name="connector-configuration"></a>Anslutningskonfiguration

Varje kopplings utlösare och åtgärder ger sina egna egenskaper som du kan konfigurera. Många kopplingar kräver också att du först skapar en *anslutning* till måltjänsten eller målsystemet och anger autentiseringsuppgifter eller annan konfigurationsinformation innan du kan använda en utlösare eller åtgärd i logikappen. Du måste till exempel godkänna en anslutning till ett Twitter-konto för åtkomst till data eller för att göra inlägg för din räkning.

För kopplingar som använder OAuth för Azure Active Directory (Azure AD) innebär att skapa en anslutning att du loggar in på tjänsten, till exempel Office 365, Salesforce eller GitHub, där åtkomsttoken [krypteras](../security/fundamentals/encryption-overview.md) och lagras på ett säkert sätt i ett Hemligt Azure-arkiv. Andra kopplingar, till exempel FTP och SQL, kräver en anslutning som har konfigurationsinformation, till exempel serveradress, användarnamn och lösenord. Dessa information om anslutningskonfigurationen krypteras och lagras på ett säkert sätt. Läs mer om [kryptering i Azure](../security/fundamentals/encryption-overview.md).

Anslutningar kan komma åt måltjänsten eller målsystemet så länge som tjänsten eller systemet tillåter. För tjänster som använder Azure AD OAuth-anslutningar, till exempel Office 365 och Dynamics, uppdaterar Azure Logic Apps åtkomsttoken på obestämd tid. Andra tjänster kan ha gränser för hur länge Azure Logic Apps kan använda en token utan att uppdatera. I allmänhet ogiltigförklarar vissa åtgärder alla åtkomsttoken, till exempel att ändra ditt lösenord.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>Anpassade API:er och kopplingar

Om du vill anropa API:er som kör anpassad kod eller inte är tillgängliga som kopplingar kan du utöka Logic Apps-plattformen genom [att skapa anpassade API-appar](../logic-apps/logic-apps-create-api-app.md). Du kan också [skapa anpassade kopplingar](../logic-apps/custom-connector-overview.md) för *alla* REST- eller SOAP-baserade API:er, vilket gör dessa API:er tillgängliga för alla logikappar i din Azure-prenumeration. Om du vill göra anpassade API-appar eller kopplingar offentliga för alla som kan använda i Azure kan du [skicka kopplingar för Microsoft-certifiering](../logic-apps/custom-connector-submit-certification.md).

> [!NOTE]
> Logikappar som du distribuerar och kör i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) kan komma åt resurser direkt i ett virtuellt Azure-nätverk. Om du har anpassade kopplingar som kräver den lokala datagatewayen och du har skapat dessa kopplingar utanför en ISE, kan logikappar i en ISE också använda dessa kopplingar.
>
> Anpassade kopplingar som skapas i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock komma åt lokala datakällor som är anslutna till ett virtuellt Azure-nätverk som är värd för ISE. Logikappar i en ISE behöver därför troligen inte datagatewayen när du kommunicerar med dessa resurser.
>
> Mer information om hur du skapar ISE finns i [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

## <a name="next-steps"></a>Nästa steg

* Visa [hela kopplingslistan](https://docs.microsoft.com/connectors)
* [Skapa din första logiska app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Skapa anpassade kopplingar för logikappar](https://docs.microsoft.com/connectors/custom-connectors/)
* [Skapa anpassade API:er för logikappar](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Anslut till datakällor lokalt från logikappar med lokala datagatewayer"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Skapa en Azure API Management-tjänstinstans för hantering och publicering av dina API:er"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "Integrera logikappar med App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Bearbeta meddelanden i grupper eller som batchar"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Utvärdera ett villkor och köra olika åtgärder baserat på om villkoret är sant eller falskt"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Utföra samma åtgärder på alla objekt i en matris"
[http-doc]: ./connectors-native-http.md "Anropa HTTP- eller HTTPS-slutpunkter från logikapparna"
[http-request-doc]: ./connectors-native-reqres.md "Ta emot HTTP-begäranden i logikapparna"
[http-response-doc]: ./connectors-native-reqres.md "Svara på HTTP-begäranden från logikapparna"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Anropa REST-slutpunkter från dina logikappar"
[http-webhook-doc]: ./connectors-native-webhook.md "Vänta på specifika händelser från HTTP- eller HTTPS-slutpunkter"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Välja och filtrera matriser med frågeåtgärden"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Kör logikappar baserat på ett schema"
[schedule-delay-doc]: ./connectors-native-delay.md "Fördröja körningen av nästa åtgärd"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Fördröja körningen av nästa åtgärd"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Köra logikappar enligt ett återkommande schema"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Kör logikappar som behöver hantera data i sammanhängande segment"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organisera åtgärder i grupper som får sin egen status när åtgärderna i gruppen har körts klart"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organisera åtgärder i ärenden som tilldelas unika värden. Kör bara det fall vars värde matchar resultatet från ett uttryck, objekt eller token. Om det inte finns några matchningar kör du standardfallet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stoppa eller avbryta ett arbetsflöde som körs aktivt för logikappen"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Upprepa åtgärder tills det angivna villkoret är sant eller något tillstånd har ändrats"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Utföra dataåtgärder som att filtrera matriser eller skapa CSV- och HTML-tabeller"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Utföra åtgärder med variabler, till exempel initiera, ange, öka, minska och lägga till sträng- eller matrisvariabel"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Hantera filer i blobcontainern med Azure Blob Storage Connector"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "Anslut till Azure Cosmos DB så att du kan komma åt dokument och lagrade procedurer"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Övervaka händelser som publiceras av ett händelserutnät, till exempel när Azure-resurser eller resurser från tredje part ändras"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Ansluta till Azure Event Hubs så att du kan ta emot och skicka händelser mellan logikappar och eventhubbar"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera, hämta och ta bort filer"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "Anslut till ditt Azure Storage-konto så att du kan skapa och hantera köer och meddelanden"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Skicka meddelanden från Service Bus-köer och Service Bus-ämnen och ta emot meddelanden från Service Bus-köer och Service Bus-prenumerationer"
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "Anslut till Azure SQL Data Warehouse så att du kan visa dina data"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Anslut till ditt Azure Storage-konto så att du kan skapa, uppdatera och fråga tabeller med mera"
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "Anslut till BizTalk Server så att du kan köra BizTalk-baserade program sida vid sida med Azure Logic Apps"
[box-doc]: ./connectors-create-api-box.md "Anslut till Box. Ladda upp, hämta, ta bort, lista dina filer med mera"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Anslut till Dropbox. Ladda upp, hämta, ta bort, lista dina filer med mera"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "Ansluta till Dynamics CRM Online så att du kan arbeta med CRM Online-data"
[facebook-doc]: ./connectors-create-api-facebook.md "Anslut till Facebook. Publicera på en tidslinje, hämta ett sidflöde med mera"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Ansluta till ett lokalt filsystem"
[ftp-doc]: ./connectors-create-api-ftp.md "Ansluta till en FTP-/FTPS-server för FTP-aktiviteter, till exempel överföring, hämtning och borttagning av filer och mycket mer"
[github-doc]: ./connectors-create-api-github.md "Ansluta till GitHub och spåra problem"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Ansluter till Google Kalender och kan hantera kalender"
[google-drive-doc]: ./connectors-create-api-googledrive.md "Anslut till GoogleDrive så att du kan arbeta med dina data"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Ansluta till Google Kalkylark så att du kan ändra dina ark"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Ansluter till Google-uppgifter så att du kan hantera dina uppgifter"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Ansluta till 3270 appar på IBM-stordatorer"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Anslut till IBM DB2 i molnet eller lokalt. Uppdatera en rad, hämta en tabell med mera"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Anslut till Informix i molnet eller lokalt. Läs en rad, lista tabellerna med mera"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Ansluta till IBM MQ lokalt eller i Azure för att skicka och ta emot meddelanden"
[instagram-doc]: ./connectors-create-api-instagram.md "Anslut till Instagram. Utlösa eller agera på händelser"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "Anslut till ditt MailChimp-konto. Hantera och automatisera e-post"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Ansluta till Mandrill för kommunikation"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "Anslut till din lokala MySQL-databas så att du kan läsa och skriva data"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Anslut till ditt Office 365-konto så att du kan skicka och ta emot e-postmeddelanden, hantera din kalender och dina kontakter med mera"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "Anslut till din personliga Microsoft OneDrive så att du kan ladda upp, ta bort, lista filer med mera"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Anslut till ditt företag Microsoft OneDrive så att du kan ladda upp, ta bort, lista dina filer med mera"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Anslut till en Oracle-databas så att du kan lägga till, infoga, ta bort rader med mera"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Anslut till outlook-postlådan så att du kan hantera e-post, kalendrar, kontakter med mera"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "Anslut till postgresQL-databasen så att du kan läsa data från tabeller"
[project-online-doc]: ./connectors-create-api-projectonline.md "Anslut till Microsoft Project Online så att du kan hantera dina projekt, aktiviteter, resurser med mera"
[rss-doc]: ./connectors-create-api-rss.md "Publicera och hämta feedobjekt, utlösa åtgärder när ett nytt objekt publiceras i en RSS-feed"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Anslut till ditt Salesforce-konto. Hantera konton, leads, affärsmöjligheter med mera"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Ansluta till ett lokalt SAP-system"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Anslut till SendGrid. Skicka e-post och hantera mottagarlistor"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Anslut till ditt SFTP-konto med hjälp av SSH. Ladda upp, hämta, ta bort filer med mera"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "Anslut till den lokala SharePoint-servern. Hantera dokument, listobjekt med mera"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "Anslut till SharePoint Online. Hantera dokument, listobjekt med mera"
[slack-doc]: ./connectors-create-api-slack.md "Ansluta till Slack och skicka meddelanden till Slack-kanaler"
[smtp-doc]: ./connectors-create-api-smtp.md "Ansluta till en SMTP-server och skicka e-post med bilagor"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Ansluter till SparkPost för kommunikation"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Anslut till Azure SQL Database eller SQL Server. Skapa, uppdatera, hämta och ta bort poster i en SQL-databastabell"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Ansluta till Teradata-databasen för att läsa data från tabeller"
[trello-doc]: ./connectors-create-api-trello.md "Anslut till Trello. Hantera dina projekt och organisera vad som helst med vem som helst"
[twilio-doc]: ./connectors-create-api-twilio.md "Anslut till Twilio. Skicka och hämta meddelanden, få tillgängliga nummer, hantera inkommande telefonnummer med mera"
[twitter-doc]: ./connectors-create-api-twitter.md "Anslut till Twitter. Hämta tidslinjer, skicka tweets med mera"
[yammer-doc]: ./connectors-create-api-yammer.md "Anslut till Yammer. Skicka meddelanden, få nya meddelanden med mera"
[youtube-doc]: ./connectors-create-api-youtube.md "Anslut till YouTube. Hantera dina videor och kanaler"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Koda och avkoda meddelanden som använder AS2-protokollet"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Koda och avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Avkoda meddelanden som använder EDIFACT-protokollet"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Koda meddelanden som använder EDIFACT-protokollet"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platt fil för företagsintegrering"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Läs mer om platt fil för företagsintegrering"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Hantera metadata för integrationskontoartefakter"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Omvandla JSON med flytande mallar"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Koda och avkoda meddelanden som använder X12-protokollet"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Avkoda meddelanden som använder X12-protokollet"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Koda meddelanden som använder X12-protokollet"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Omvandla XML-meddelanden"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Validera XML-meddelanden"

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
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
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
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
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
[sftp-ssh-icon]: ./media/apis-list/sftp.png
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
