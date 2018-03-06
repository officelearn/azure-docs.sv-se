---
title: "Flytta appar från BizTalk-tjänst till Azure Logikappar | Microsoft Docs"
description: "Flytta eller migrera Azure BizTalk-tjänster (MABS) till Azure Logic Apps"
services: logic-apps
documentationcenter: 
author: jonfancey
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: jonfan; LADocs
ms.openlocfilehash: 6e00e62e60c059a16731a77e529b4b93f50802e9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="move-from-biztalk-services-to-azure-logic-apps"></a>Flytta från BizTalk-tjänst till Azure Logikappar

Microsoft Azure BizTalk-tjänster (MABS) tas ur bruk. Flytta dina lösningar för integrering av MABS till [Azure Logikappar](../logic-apps/logic-apps-overview.md), följer du anvisningarna i den här artikeln. 

## <a name="introduction"></a>Introduktion

BizTalk-tjänster består av två subservices:

* Microsoft BizTalk Services Hybridanslutningar
* EAI- och EDI bridge-baserade integrering

[Azure App Service-Hybridanslutningar](../app-service/app-service-hybrid-connections.md) ersätter Hybridanslutningar för BizTalk-tjänster. Azure Hybridanslutningar är tillgänglig med Azure App Service via Azure-portalen. Den här tjänsten tillhandahåller en Hybridanslutningshanteraren så att du kan hantera befintliga BizTalk-tjänst hybridanslutningar och nya hybridanslutningar som du skapar i portalen. 

[Logic Apps](../logic-apps/logic-apps-overview.md) ersätter EAI- och EDI bridge-baserade integrering med samma funktioner i BizTalk-tjänst med mera. Den här tjänsten tillhandahåller skalbar molnlagring förbrukningsbaserad arbetsflödet och orchestration-funktioner att snabbt och enkelt skapa komplexa integration lösningar via en webbläsare eller med Visual Studio.

Den här tabellen motsvarar Logic Apps BizTalk-tjänst-funktioner.

| BizTalk Services   | Logic Apps            | Syfte                      |
| ------------------ | --------------------- | ---------------------------- |
| Anslutning          | Anslutning             | Skicka och ta emot data   |
| Platslänksbrygga             | Logikapp             | Pipeline-processor           |
| Validera fas     | XML-verifiering åtgärd | Validera ett XML-dokument mot ett schema | 
| Utöka fas       | Data-token           | Befordra egenskaper i meddelanden eller för beslut om routning |
| Transformera fas    | Transformera åtgärd      | Konvertera XML-meddelanden från ett format till en annan |
| Avkoda fas       | Flat fil avkoda åtgärd | Konvertera från flat-fil till XML |
| Koda fas       | Flat fil koda åtgärd | Konvertera XML till flat-fil |
| Meddelandet Inspector  | Azure Functions eller API Apps | Köra anpassad kod i din integreringar |
| Väg-åtgärd       | Villkor eller växel | Skicka meddelanden till en av de angivna kopplingarna |
|||| 

## <a name="biztalk-services-artifacts"></a>BizTalk-tjänst artefakter

BizTalk-tjänst har flera typer av artefakter. 

## <a name="connectors"></a>Anslutningar

BizTalk-tjänst kopplingar hjälp bryggor skicka och ta emot data, inklusive dubbelriktat bryggor som möjliggör samverkan med HTTP-baserade frågor och svar. Logic Apps använder samma terminologi och har 180 + kopplingar som fungerar på samma sätt genom att ansluta till en mängd olika tekniker och tjänster. Till exempel kopplingar är tillgänglig för molnet SaaS och PaaS-tjänsterna, till exempel OneDrive, Office 365, Dynamics CRM och mer plus lokalt system med hjälp av lokala Data Gateway, som ersätter BizTalk Adapter-tjänst för BizTalk-tjänst. Datakällor i BizTalk-tjänst är begränsade till FTP-, SFTP och Service Bus-kö eller avsnittet prenumeration.

![](media/logic-apps-move-from-mabs/sources.png)

Som standard har varje brygga en HTTP-slutpunkt som är konfigurerad med Runtime-adressen och egenskaperna för bryggan relativ adress. För att uppnå samma resultat med Logic Apps, använda den [förfrågan och svar](../connectors/connectors-native-reqres.md) åtgärder.

## <a name="xml-processing-and-bridges"></a>XML-bearbetning och bryggor

En brygga är detsamma som en process-pipelinen för BizTalk-tjänst. En brygga arbeta med data som tagits emot från en koppling, gör vissa arbetar med data och skicka resultaten till en annan dator. Logic Apps har samma genom att stödja pipeline-baserade interaktion samma mönster som BizTalk-tjänst och även andra integration mönster. Den [XML-Request-Reply Bridge](https://msdn.microsoft.com/library/azure/hh689781.aspx) i BizTalk Services kallas en VETER pipelinen, som består av faser som utför dessa uppgifter:

* V Validera
* (E) Enrich
* (T) transformera
* (E) Enrich
* (R) väg

Den här bilden visar hur bearbetning delas mellan begäran och svar, som ger kontroll över begäran och svar sökvägar separat, till exempel genom att använda olika mappar för varje sökväg:

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

XML enkelriktade bridge lägger också till avkoda och koda i början och slutet av bearbetning. Direkt bryggan innehåller ett enda Enrich steg.

### <a name="message-processing-decoding-and-encoding"></a>Meddelandebehandling avkodning och kodning

I BizTalk-tjänst kan du ta emot olika typer av XML-meddelanden och fastställa matchande schemat för det mottagna meddelandet. Detta arbete utförs i den *meddelandetyper* steget i receive process-pipelinen. Avkoda steget använder sedan identifierade meddelandetypen för att avkoda meddelandet med det angivna schemat. Om schemat är en flat fil-schemat, konverterar det här steget inkommande flat fil till XML. 

Logic Apps tillhandahåller liknande funktioner. Du får en flat-fil via olika protokoll som använder olika connector utlösare (filsystem, FTP-, HTTP och så vidare) och använda den [Flat fil avkoda](../logic-apps/logic-apps-enterprise-integration-flatfile.md) åtgärder för att omvandla inkommande data till XML. Du kan flytta dina befintliga scheman flat fil direkt till Logic Apps utan några ändringar och sedan ladda upp scheman till ditt konto för integrering.

### <a name="validation"></a>Validering

När inkommande data har konverterats till XML (eller om XML var meddelandeformat togs emot), kör verifiering för att avgöra om meddelandet följer XSD-schemat. Använd för att utföra den här uppgiften i Logikappar i [XML-verifiering](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) åtgärd. Du kan använda samma scheman från BizTalk-tjänst utan några ändringar.

### <a name="transform-messages"></a>Transformera meddelanden

I BizTalk-tjänst konverterar transformeringen mellanlagra en XML-baserade meddelandeformat till en annan. Det här arbetet genom att använda en karta med mapper TRFM-baserade. Processen påminner om i Logic Apps. Transform-åtgärden körs en karta från ditt konto för integrering. Den största skillnaden är att mappningar i Logic Apps XSLT-format. XSLT innehåller möjligheten att återanvända befintliga XSLT som du redan har, inklusive maps som skapats för BizTalk Server som innehåller functoids. 

### <a name="routing-rules"></a>Regler för Routning

BizTalk-tjänst gör ett beslut om routning på vilka endpoint eller connector skickar inkommande meddelanden eller data. Möjlighet att välja från förkonfigurerade slutpunkter är möjligt med hjälp av alternativet Routning filter:

![](media/logic-apps-move-from-mabs/route-filter.png)

I BizTalk-tjänster, om det finns två alternativ, använder en *villkoret* är det bästa sättet för att konvertera routning filter i BizTalk-tjänst. Om det finns fler än två, använder du en **växla**.

Logic Apps ger funktioner för avancerad logik samt avancerade Kontrollflöde och routning med [villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [växla instruktioner](../logic-apps/logic-apps-control-flow-switch-statement.md).

### <a name="enrich"></a>Utöka

Lägger till egenskaper i kontexten för meddelandet som är associerade med mottagna data i BizTalk-tjänst bearbetningen Enrich steget. Till exempel uppgradera en egenskap som ska användas för routning från en databassökning, eller genom att extrahera ett värde med ett XPath-uttryck. Logic Apps ger åtkomst till alla kontextuella utdata från föregående åtgärder, vilket gör det enkelt att replikera samma beteende. Till exempel med hjälp av den `Get Row` SQL-anslutningsåtgärd du returnera data från en SQL Server-databas och använda data i en beslut och åtgärd för routning. På samma sätt egenskaper på inkommande Service Bus meddelanden i kön av en utlösare är adresserbara samt XPath med hjälp av arbetsflödet definition language uttrycket.

### <a name="run-custom-code"></a>Köra anpassad kod

BizTalk-tjänst kan du [köra anpassad kod](https://msdn.microsoft.com/library/azure/dn232389.aspx) som överförs i din egen sammansättningar. Den här funktionen har implementerats av den [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector) gränssnitt. Varje steg i bryggan innehåller två egenskaper (på Ange Inspector och på Avsluta Inspector) som tillhandahåller de .NET-typ du skapade som implementerar det här gränssnittet. Anpassad kod kan du utföra mer komplexa bearbetning av data och du kan återanvända befintliga koden i sammansättningar som utför vanliga affärslogik. 

Logic Apps tillhandahåller två sätt att utföra anpassad kod: Azure Functions och API Apps. Azure Functions kan skapas och anropas från logikappar. Se [Lägg till och kör anpassad kod för logic apps via Azure Functions](../logic-apps/logic-apps-azure-functions.md). Du kan använda API Apps, en del av Azure App Service för att skapa egna utlösare och åtgärder. Lär dig mer om [skapar en anpassad API för användning med Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Om du har anpassade kod i sammansättningar som anropas från BizTalk-tjänst kan du antingen flytta koden till Azure Functions eller skapa anpassade API: er med API Apps, beroende på vad du implementera. Om du har kod som omsluter en annan tjänst som Logic Apps inte har en koppling och sedan skapa en API-App och använda åtgärderna som innehåller till exempel din API-app i din logikapp. Om du har hjälpfunktioner eller bibliotek, är Azure Functions förmodligen bäst.

### <a name="edi-processing-and-trading-partner-management"></a>EDI bearbetning och hantering av handelspartners

BizTalk-tjänst och Logic Apps innehåller EDI och B2B bearbetning med stöd för AS2 tillämplighet instruktionen 2, X12 och EDIFACT. I BizTalk Services din skapa EDI bryggor och skapa eller hantera handel partners och avtalen i den dedikerade hantering och uppföljning av portalen.
I Logic Apps får du den här funktionen via den [Enterprise Integration Pack (EIP)](../logic-apps/logic-apps-enterprise-integration-overview.md). EIP ger [integrering konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) och B2B-åtgärder för EDI och B2B-bearbetning. Du också använda ett konto för integrering för att skapa och hantera [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md). När du har skapat ett konto för integrering kan du länka en eller flera logikappar till kontot. Du kan sedan använda B2B-åtgärder för att komma åt handel partnerinformation från din logikapp. Det finns följande åtgärder:

* AS2 Encode
* AS2 avkoda
* X12 Encode
* Avkoda X12
* EDIFACT koda
* EDIFACT avkoda

Till skillnad från BizTalk-tjänst frikopplad åtgärderna från transportprotokoll. Så när du skapar dina logic apps har du mer flexibilitet i vilka kopplingar att du använder för att skicka och ta emot data. Du kan till exempel ta emot X12 filer som bifogade filer från e-post och sedan processen dessa filer i en logikapp. 

## <a name="manage-and-monitor"></a>Hantera och övervaka

För BizTalk-tjänst som en dedikerad portal spårningsfunktioner att övervaka och felsöka problem. Logic Apps ger bättre spårning och övervakning av funktioner via den [Azure-portalen](../logic-apps/logic-apps-monitor-your-logic-apps.md), och med den [Operations Management Suite B2B-lösning](../logic-apps/logic-apps-monitor-b2b-message.md), innehåller en mobilapp för att hålla koll på saker När du är på språng.

## <a name="high-availability"></a>Hög tillgänglighet

Du kan dela belastningen för hög tillgänglighet (HA) i BizTalk-tjänst genom att använda mer än en instans i en viss region. Logic Apps ger hög tillgänglighet i region utan extra kostnad. 

I BizTalk-tjänst kräver out av region katastrofåterställning för B2B bearbetning en process för säkerhetskopiering och återställning. För affärskontinuitet, Logic Apps kan mellan region aktiv/passiv [DR kapaciteten](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md), vilket gör att du synkroniserar B2B data över integrationskonton i olika regioner.

## <a name="next-steps"></a>Nästa steg

* [Vad är Logic Apps?](../logic-apps/logic-apps-overview.md)
* [Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md), eller kom snabbt igång med en [färdig mall](../logic-apps/logic-apps-create-logic-apps-from-templates.md)  
* [Visa alla tillgängliga kopplingar](../connectors/apis-list.md) som du kan använda i logikappar