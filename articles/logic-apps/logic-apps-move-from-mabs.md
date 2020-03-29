---
title: Migrera appar från BizTalk Services till Azure Logic Apps
description: Så här flyttar du dina appar och lösningar från Microsoft Azure BizTalk Services (MABS) till Azure Logic Apps
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: 97399635399c12022006ac95e60c5828bf2a9dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905429"
---
# <a name="migrate-your-apps-and-solutions-from-biztalk-services-to-azure-logic-apps"></a>Migrera dina appar och lösningar från BizTalk Services till Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) går i pension. Om du vill flytta dina MABS-integrationslösningar till [Azure Logic Apps](../logic-apps/logic-apps-overview.md)följer du anvisningarna i den här artikeln. 

## <a name="introduction"></a>Introduktion

BizTalk Services består av två undertjänster:

* Hybridanslutningar för Microsoft BizTalk-tjänster
* EAI och EDI-brobaserad integration

[Hybridanslutningar för Azure App Service](../app-service/app-service-hybrid-connections.md) ersätter BizTalk Services Hybrid-anslutningar. Azure Hybrid-anslutningar är tillgängligt med Azure App Service via Azure-portalen. Den här tjänsten tillhandahåller en Hybrid Connection Manager så att du kan hantera befintliga BizTalk Services-hybridanslutningar och även nya hybridanslutningar som du skapar i portalen. 

[Logic Apps](../logic-apps/logic-apps-overview.md) ersätter EAI- och EDI-bryggbaserad integration med alla samma funktioner i BizTalk-tjänster med mera. Den här tjänsten tillhandahåller förbrukningsbaserade arbetsflödes- och orkestreringsfunktioner i molnskala för att snabbt och enkelt kunna skapa komplexa integrationslösningar via en webbläsare eller med Visual Studio.

Den här tabellen mappar BizTalk Services-funktionerna till Logic Apps.

| BizTalk-tjänster   | Logic Apps            | Syfte                      |
| ------------------ | --------------------- | ---------------------------- |
| Anslutningsprogram          | Anslutningsprogram             | Skicka och ta emot data   |
| Bridge             | Logikapp             | Pipeline-processor           |
| Validera steg     | Åtgärden XML-validering | Verifiera ett XML-dokument mot ett schema | 
| Berika scenen       | Datatoken           | Befordra egenskaper till meddelanden eller för routningsbeslut |
| Omforma steg    | Transformera åtgärd      | Konvertera XML-meddelanden från ett format till ett annat |
| Avkoda steg       | Åtgärd för fast filavkodning | Konvertera från platt fil till XML |
| Koda steg       | Åtgärden Fast filkoda | Konvertera från XML till platt fil |
| Meddelandekontroll  | Azure-funktioner eller API-appar | Kör anpassad kod i dina integrationer |
| Ruttåtgärd       | Skick eller switch | Dirigera meddelanden till en av de angivna kopplingarna |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services artefakter

BizTalk Services har flera typer av artefakter. 

## <a name="connectors"></a>Anslutningar

BizTalk Services-kopplingar hjälper bryggor att skicka och ta emot data, inklusive tvåvägsbryggor som aktiverar HTTP-baserade request/response-interaktioner. Logic Apps använder samma terminologi och har hundratals kopplingar som tjänar samma syfte genom att ansluta till ett brett utbud av tekniker och tjänster. Anslutningsappar är till exempel tillgängliga för molnbaserade SaaS- och PaaS-tjänster, till exempel OneDrive, Office365, Dynamics CRM med mera, plus lokala system via den lokala datagatewayen, som ersätter BizTalk-adaptertjänsten för BizTalk-tjänster. Källor i BizTalk Services är begränsade till FTP-, SFTP- och Service-busskö- eller ämnesprenumeration.

![](media/logic-apps-move-from-mabs/sources.png)

Som standard har varje brygga en HTTP-slutpunkt, som är konfigurerad med egenskaperna Runtime Address och egenskaperna Relativ adress för bryggan. Om du vill uppnå samma resultat med Logic Apps använder du åtgärderna [Begäran och svar.](../connectors/connectors-native-reqres.md)

## <a name="xml-processing-and-bridges"></a>XML-bearbetning och bryggor

I BizTalk Services är en brygga jämförbar med en bearbetningspipeline. En brygga kan ta data som tas emot från en anslutningsapp, göra en del arbete med data och skicka resultaten till ett annat system. Logic Apps gör samma sak genom att stödja samma pipeline-baserade interaktionsmönster som BizTalk Services och även tillhandahålla andra integrationsmönster. [XML Request-Reply Bridge](https://msdn.microsoft.com/library/azure/hh689781.aspx) i BizTalk Services kallas en VETER-pipeline, som består av faser som utför dessa uppgifter:

* (V) Validera
* (E) Berika
* (T) Transformera
* (E) Berika
* (R) Rutt

Den här bilden visar hur bearbetningen delas mellan begäran och svar, vilket ger kontroll över begäran och svarssökvägarna separat, till exempel genom att använda olika kartor för varje sökväg:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Dessutom lägger en XML enkelriktad brygga till avkodnings- och kodningsfaser i början och slutet av bearbetningen. Pass-Through-bron innehåller en enda Enrich-scen.

### <a name="message-processing-decoding-and-encoding"></a>Meddelandebearbetning, avkodning och kodning

I BizTalk Services kan du ta emot olika typer av XML-meddelanden och bestämma matchningsschemat för det mottagna meddelandet. Det här arbetet utförs i fasen *Meddelandetyper* i pipelinen för mottagningsbearbetning. Avkoda scenen använder sedan den identifierade meddelandetypen för att avkoda meddelandet med hjälp av det angivna schemat. Om schemat är ett platt filschema konverteras den inkommande platta filen till XML i det här steget. 

Logic Apps ger liknande funktioner. Du får en platt fil över olika protokoll med hjälp av de olika anslutningsutlösare (Filsystem, FTP, HTTP och så vidare) och använder åtgärden [Platt filkodning](../logic-apps/logic-apps-enterprise-integration-flatfile.md) för att konvertera inkommande data till XML. Du kan flytta dina befintliga platta filscheman direkt till Logic Apps utan några ändringar och sedan ladda upp scheman till ditt integrationskonto.

### <a name="validation"></a>Validering

När inkommande data har konverterats till XML (eller om XML var det mottagna meddelandeformatet) körs valideringen för att avgöra om meddelandet följer XSD-schemat. Om du vill utföra den här uppgiften i Logic Apps använder du åtgärden [XML-validering.](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) Du kan använda samma scheman från BizTalk Services utan några ändringar.

### <a name="transform-messages"></a>Omvandla meddelanden

I BizTalk Services konverterar omformningsfasen ett XML-baserat meddelandeformat till ett annat. Detta arbete görs genom att använda en karta med hjälp av TRFM-baserade mapper. I Logic Apps är processen liknande. Åtgärden Transform kör en karta från ditt integrationskonto. Den största skillnaden är att kartor i Logic Apps är i XSLT-format. XSLT innehåller möjligheten att återanvända befintliga XSLT som du redan har, inklusive kartor som skapats för BizTalk Server som innehåller functoids. 

### <a name="routing-rules"></a>Dirigeringsregler

BizTalk Services fattar ett routningsbeslut om vilken slutpunkt eller koppling som ska skickas för inkommande meddelanden eller data. Möjligheten att välja bland förkonfigurerade slutpunkter är möjlig med alternativet routningsfilter:

![](media/logic-apps-move-from-mabs/route-filter.png)

I BizTalk Services, om det bara finns två alternativ, är ett *villkor* det bästa sättet att konvertera routningsfilter i BizTalk Services. Om det finns fler än två använder du en **switch**.

Logic Apps tillhandahåller sofistikerade logikfunktioner plus avancerat kontrollflöde och routning med [villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [växelsatser](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Berika

I BizTalk Services-bearbetningen lägger fasen Enrich till egenskaper i meddelandekontexten som är associerad med de mottagna data. Till exempel att befordra en egenskap som ska användas för routning från en databassökning eller genom att extrahera ett värde med hjälp av ett XPath-uttryck. Logic Apps ger åtkomst till alla kontextuella datautdata från föregående åtgärder, vilket gör det enkelt att replikera samma beteende. Med hjälp av `Get Row` SQL-anslutningsåtgärden returnerar du data från en SQL Server-databas och använder data i en beslutsåtgärd för routning. På samma sätt är egenskaper för inkommande Service Bus-köade meddelanden av en utlösare adresserbara, liksom XPath med språkuttrycket för xpath-arbetsflödesdefinitionen.

### <a name="run-custom-code"></a>Kör anpassad kod

BizTalk Services kan du [köra anpassad kod](https://msdn.microsoft.com/library/azure/dn232389.aspx) som laddas upp i dina egna sammansättningar. Den här funktionen implementeras av [IMessageInspector-gränssnittet.](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) Varje steg i bryggan innehåller två egenskaper (På Retur-inspektör och Vid avslutningsinspektör) som anger den .NET-typ som du skapade som implementerar det här gränssnittet. Med anpassad kod kan du utföra mer komplex bearbetning på data och du kan återanvända befintlig kod i sammansättningar som utför gemensam affärslogik. 

Logic Apps innehåller två primära sätt att köra anpassad kod: Azure Functions och API-appar. Azure Functions kan skapas och anropas från logikappar. Se [Lägga till och kör anpassad kod för logikappar via Azure Functions](../logic-apps/logic-apps-azure-functions.md). Använd API-appar, en del av Azure App Service, för att skapa egna utlösare och åtgärder. Läs mer om [hur du skapar ett anpassat API som ska användas med Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Om du har anpassad kod i sammansättningar som du anropar från BizTalk Services kan du antingen flytta den här koden till Azure Functions eller skapa anpassade API:er med API-appar, beroende på vad du implementerar. Om du till exempel har kod som radbryter en annan tjänst som Logic Apps inte har en koppling för, skapar du en API-app och använder de åtgärder som api-appen tillhandahåller i logikappen. Om du har hjälpfunktioner eller bibliotek är Azure Functions förmodligen den bästa passformen.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-bearbetning och hantering av handelspartner

BizTalk-tjänster och logic apps inkluderar EDI- och B2B-bearbetning med stöd för AS2 (Applicability Statement 2), X12 och EDIFACT. I BizTalk Services skapar du EDI-bryggor och skapar eller hanterar handelspartner och avtal i den dedikerade spårnings- och hanteringsportalen.
I Logic Apps får du den här funktionen via [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP tillhandahåller [integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och B2B-åtgärder för EDI- och B2B-bearbetning. Du använder också ett integrationskonto för att skapa och hantera [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal.](../logic-apps/logic-apps-enterprise-integration-agreements.md) När du har skapat ett integrationskonto kan du länka en eller flera logikappar till kontot. Du kan sedan använda B2B-åtgärder för att komma åt partnerinformation från din logikapp. Följande åtgärder tillhandahålls:

* AS2 Koda
* AS2 Avkoda
* X12 Koda
* X12 Avkoda
* EDIFACT-koda
* EDIFACT Avkoda

Till skillnad från BizTalk Services är dessa åtgärder frikopplade från transportprotokollen. Så när du skapar dina logikappar har du större flexibilitet i vilka kopplingar som du använder för att skicka och ta emot data. Du kan till exempel ta emot X12-filer som bilagor från e-post och sedan bearbeta dessa filer i en logikapp. 

## <a name="manage-and-monitor"></a>Hantera och övervaka

I BizTalk Services tillhandahöll en dedikerad portal spårningsfunktioner för att övervaka och felsöka problem. Logic Apps erbjuder rikare spårnings- och övervakningsfunktioner för att [övervaka logikappar i Azure-portalen](../logic-apps/monitor-logic-apps.md)och innehåller en mobilapp för att hålla ett öga på saker när du är på resande fot.

## <a name="high-availability"></a>Hög tillgänglighet

För hög tillgänglighet (HA) i BizTalk Services kan du dela bearbetningsbelastningen med mer än en instans i en viss region. Logic Apps tillhandahåller ha i regionen utan extra kostnad. 

I BizTalk Services kräver katastrofåterställning utanför regionen för B2B-bearbetning en säkerhetskopierings- och återställningsprocess. För kontinuitet i verksamheten tillhandahåller Logic Apps aktiva/passiva [DR-funktioner](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)över flera regioner, vilket gör att du kan synkronisera B2B-data över integrationskonton i olika regioner.

## <a name="next-steps"></a>Nästa steg

* [Vad är Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), eller kom snabbt igång med en [färdig mall](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Visa alla tillgängliga anslutningsappar](../connectors/apis-list.md) som du kan använda i logikappar