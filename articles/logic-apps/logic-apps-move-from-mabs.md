---
title: Flytta appar från BizTalk Services till Azure Logic Apps | Microsoft Docs
description: Migrera från Azure BizTalk Services (MABS) till Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/30/2017
ms.openlocfilehash: f27e82e780917e00625ef6a14ab8317d1f5b8ae8
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124807"
---
# <a name="migrate-from-biztalk-services-to-azure-logic-apps"></a>Migrera från BizTalk Services till Azure Logic Apps

Microsoft Azure BizTalk Services (MABS) tas ur bruk. Flytta MABS integreringslösningar till [Azure Logic Apps](../logic-apps/logic-apps-overview.md), följer du anvisningarna i den här artikeln. 

## <a name="introduction"></a>Introduktion

BizTalk Services består av två subservices:

* Microsoft BizTalk Services Hybridanslutningar
* EAI och EDI bridge-baserad integrering

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) ersätter BizTalk Services Hybridanslutningar. Azure Hybrid-anslutningar är tillgänglig med Azure App Service via Azure portal. Den här tjänsten tillhandahåller en Hybridanslutningshanteraren så att du kan hantera befintliga BizTalk Services hybridanslutningar och nya hybridanslutningar som du skapar i portalen. 

[Logic Apps](../logic-apps/logic-apps-overview.md) ersätter EAI och EDI bridge-baserad integrering med samma funktioner i BizTalk Services och mycket annat. Den här tjänsten tillhandahåller molnskala förbrukningsbaserad arbetsflöde och dirigering funktioner för dig att snabbt och enkelt skapa komplexa lösningar via en webbläsare eller med Visual Studio.

Den här tabellen mappar BizTalk Services-funktioner till Logic Apps.

| BizTalk Services   | Logic Apps            | Syfte                      |
| ------------------ | --------------------- | ---------------------------- |
| Koppling          | Koppling             | Skicka och ta emot data   |
| Bridge             | Logikapp             | Pipeline-processor           |
| Verifiera steg     | Åtgärd för XML-verifiering | Validera XML-dokument med ett schema | 
| Utöka steg       | Data-token           | Flytta upp egenskaper i meddelanden eller för beslut om routning |
| Omvandla steg    | Omvandla åtgärd      | Konvertera XML-meddelanden från ett format till en annan |
| Avkoda steg       | Flat fil avkoda åtgärd | Konvertera från flat fil till XML |
| Koda steg       | Flat fil koda åtgärd | Konvertera från XML till flat fil |
| Meddelandet Inspector  | Azure Functions eller API-appar | Kör anpassad kod i din integreringar |
| Väg åtgärd       | Villkor eller växel | Skicka meddelanden till en av de angivna kopplingarna |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk Services-artefakter

BizTalk Services har flera olika typer av artefakter. 

## <a name="connectors"></a>Anslutningar

BizTalk Services-kopplingar hjälp bryggor skicka och ta emot data, inklusive dubbelriktat bryggor som gör HTTP-baserade begäran/svar-interaktioner. Logic Apps använder samma terminologi och har 180 + anslutningsappar som tjänar samma syfte genom att ansluta till en mängd olika tekniker och tjänster. Till exempel kopplingar är tillgängliga för molnet SaaS och PaaS-tjänster, till exempel OneDrive, Office 365, Dynamics CRM med mera, plus lokala system via den lokala Datagatewayen, som ersätter BizTalk Adapter-tjänsten för BizTalk Services. Datakällor i BizTalk Services är begränsade till prenumeration för FTP-, SFTP och Service Bus-kö eller ämne.

![](media/logic-apps-move-from-mabs/sources.png)

Som standard har varje brygga en HTTP-slutpunkt som är konfigurerad med Runtime-adress och de relativa adressegenskaperna för bryggan. För att uppnå samma resultat med Logic Apps kan du använda den [begäranden och svar](../connectors/connectors-native-reqres.md) åtgärder.

## <a name="xml-processing-and-bridges"></a>XML-bearbetning och bryggor

I BizTalk Services är en brygga detsamma som en process-pipelinen. En brygga arbeta med data som tas emot från en koppling, gör vissa arbeta med data och skicka resultaten till ett annat system. Logic Apps gör samma sak som stöder samma pipeline-baserade interaktion mönster som BizTalk Services och även tillgång till andra integration-mönster. Den [XML-begäran / svar-brygga](https://msdn.microsoft.com/library/azure/hh689781.aspx) i BizTalk Services kallas för en en VETER-pipeline som består av steg som utför dessa uppgifter:

* (V) verifiera
* (E) berika
* (T) transformering
* (E) berika
* (R) väg

Den här bilden visar hur bearbetning delas mellan begäran och svar, vilket ger kontroll över begäran och svar-sökvägar separat, till exempel genom att använda olika mappar för varje sökväg:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

En XML-enkelriktad brygga lägger dessutom till stegen avkoda och koda i början och slutet av bearbetning. Direkt-brygga innehåller ett enda Enrich steg.

### <a name="message-processing-decoding-and-encoding"></a>Meddelandebearbetning, avkoda och koda

I BizTalk Services, kan du ta emot olika typer av XML-meddelanden och fastställa matchande schemat för det mottagna meddelandet. Detta arbete utförs i den *meddelandetyper* steget i receive process-pipelinen. Avkoda scenen använder sedan identifierade meddelandetypen för att avkoda meddelandet med det angivna schemat. Om schemat är ett schema för platt fil, konverterar det här skedet inkommande flat fil till XML. 

Logic Apps tillhandahåller liknande funktioner. Du får en flat fil via olika protokoll olika connector-utlösare (File System, FTP, HTTP och så vidare) och använda den [avkodning av Flat fil](../logic-apps/logic-apps-enterprise-integration-flatfile.md) åtgärder för att omvandla inkommande data till XML. Du kan flytta dina befintliga scheman för platt fil direkt till Logic Apps utan några ändringar och sedan ladda upp scheman till ditt Integrationskonto.

### <a name="validation"></a>Validering

När inkommande konverteras till XML (eller om XML var meddelandeformat togs emot), kör verifieringen för att avgöra om meddelandet följer XSD-schemat. Om du vill utföra den här uppgiften i Logic Apps, använda den [XML-verifiering](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) åtgärd. Du kan använda samma scheman från BizTalk Services utan ändringar.

### <a name="transform-messages"></a>Omvandla meddelanden

I BizTalk Services konverterar transformeringen scenen en XML-baserade meddelandeformat till en annan. Det här arbetet genom att tillämpa en karta, med hjälp av TRFM-baserade mappningen. Processen påminner om i Logic Apps. Transformering åtgärden kör en karta från ditt Integrationskonto. Den största skillnaden är att kartor i Logic Apps i XSLT-format. XSLT omfattar möjligheten att återanvända befintliga XSLT som du redan har, inklusive maps som skapats för BizTalk Server och som innehåller functoids. 

### <a name="routing-rules"></a>Routningsregler

BizTalk Services blir ett beslut om routning på vilken slutpunkt eller anslutningen att skicka inkommande meddelanden eller data. Möjligheten att välja från förkonfigurerade slutpunkter är möjlig med hjälp av routingalternativ som filter:

![](media/logic-apps-move-from-mabs/route-filter.png)

I BizTalk Services, om det finns två alternativ, använder en *villkor* är det bästa sättet för att konvertera routningsfilter i BizTalk Services. Om det finns fler än två, använder du en **växla**.

Logic Apps tillhandahåller funktioner för avancerad logik plus avancerade Kontrollflöde och routning med [villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [switch-satser](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Utöka

I BizTalk Services-bearbetning, lägger Enrich scenen till egenskaper meddelandekontexten som är associerade med mottagna data. Till exempel uppgraderar en egenskap som ska användas för routning från en databassökning eller genom att extrahera ett värde med ett XPath-uttryck. Logic Apps ger tillgång till alla kontextuella data utdata från föregående åtgärder, vilket gör det enkelt att replikera samma beteende. Till exempel med hjälp av den `Get Row` SQL-anslutningsåtgärd du returnera data från en SQL Server-databas och använda data i en beslut och åtgärd för routning. På samma sätt egenskaper på inkommande Service Bus meddelanden i kön av en utlösare är adresserbara, samt XPath med xpath workflow definition language uttrycket.

### <a name="run-custom-code"></a>Kör anpassad kod

BizTalk Services kan du [kör anpassad kod](https://msdn.microsoft.com/library/azure/dn232389.aspx) som laddas upp i din egen sammansättningar. Den här funktionen har implementerats av den [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) gränssnitt. Varje steg i bryggan innehåller två egenskaper (på Ange Inspector och på Avsluta Inspector) som tillhandahåller .NET-typ du har skapat som implementerar det här gränssnittet. Anpassad kod kan du utföra mer komplexa bearbetning av data och låter dig återanvända befintliga koden i sammansättningar som utför vanliga affärslogik. 

Logic Apps tillhandahåller två huvudsakliga sätt att köra anpassad kod: Azure Functions och API Apps. Azure Functions kan skapas och anropa från logikappar. Se [Lägg till och kör anpassad kod för logic apps via Azure Functions](../logic-apps/logic-apps-azure-functions.md). Använd API-appar, en del av Azure App Service för att skapa dina egna utlösare och åtgärder. Läs mer om [skapar en anpassad API ska användas med Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Om du har anpassad kod i sammansättningar som anropas från BizTalk Services, kan du antingen flytta den här koden till Azure Functions eller skapa anpassade API: er med API Apps, beroende på vad du implementerar. Om du har kod som omsluter en annan tjänst som Logic Apps inte har en anslutning och sedan skapa en API-App och använder åtgärderna som innehåller till exempel din API-app i din logikapp. Om du har hjälpfunktioner eller bibliotek, är Azure Functions sannolikt det bästa valet.

### <a name="edi-processing-and-trading-partner-management"></a>EDI-bearbetning och hantering av handelspartners

BizTalk Services och Logic Apps innehåller EDI- och B2B-bearbetning med stöd för AS2 (Applicability Statement 2) X12 och EDIFACT. I BizTalk Services, din skapa EDI-bryggor och skapa eller hantera handelspartners och avtal i dedikerade spårnings- och -portalen.
I Logic Apps kan du få den här funktionen via den [Enterprise-Integrationspaket (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP ger [Integrationskontot](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och B2B-åtgärder för EDI- och B2B-bearbetning. Du också använda ett Integrationskonto för att skapa och hantera [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md). När du skapar ett Integrationskonto kan länka du en eller flera logikappar till kontot. Du kan sedan använda B2B-åtgärder för att komma åt handel partnerinformation från din logikapp. Följande åtgärder tillhandahålls:

* AS2-kodning
* AS2-avkodning
* X12 koda
* X12 avkoda
* EDIFACT-kodning
* EDIFACT-avkodning

Till skillnad från BizTalk Services, är dessa åtgärder fristående från transportprotokoll. Så när du skapar dina logic apps har du mer flexibilitet i vilka kopplingar att du använder för att skicka och ta emot data. Du kan till exempel ta emot X12 filer som bifogade filer från e-post och sedan bearbeta dessa filer i en logikapp. 

## <a name="manage-and-monitor"></a>Hantera och övervaka

I BizTalk Services tillhandahåller en dedikerad portal spårningsfunktioner att övervaka och felsöka problem. Logic Apps tillhandahåller bättre spårning och övervakningsfunktioner via den [Azure-portalen](../logic-apps/logic-apps-monitor-your-logic-apps.md), och innehåller en mobilapp för att hålla ett öga på saker när du är på resande fot.

## <a name="high-availability"></a>Hög tillgänglighet

Du kan dela belastningen med hjälp av fler än en instans i en viss region för hög tillgänglighet (HA) i BizTalk Services. Logic Apps tillhandahåller regional hög tillgänglighet utan extra kostnad. 

I BizTalk Services kräver out regional haveriberedskap för B2B-bearbetning en säkerhetskopierings- och återställningsprocessen. För affärskontinuitet, Logic Apps tillhandahåller interregionala aktiv/passiv [DR-funktionen](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), vilket gör att du synkroniserar B2B data över integrationskonton i olika regioner.

## <a name="next-steps"></a>Nästa steg

* [Vad är Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), eller kom snabbt igång med en [färdig mall](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Visa alla tillgängliga anslutningsappar](../connectors/apis-list.md) som du kan använda i logic apps