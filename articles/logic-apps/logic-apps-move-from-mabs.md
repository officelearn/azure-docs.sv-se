---
title: "Flytta appar från BizTalk-tjänst till Azure Logikappar | Microsoft Docs"
description: Flytta eller migrera Azure BizTalk Services MABS till Logic Apps
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
ms.author: ladocs; jonfan; mandia
ms.openlocfilehash: 01c5376ac5ba9125eede9deb5ee0a7a006a91bb4
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="move-from-biztalk-services-to-logic-apps"></a>Flytta från BizTalk-tjänst till Logic Apps

Microsoft Azure BizTalk-tjänster (MABS) tas ur bruk. Använd det här avsnittet för att flytta dina MABS integrering lösningar till Azure Logic Apps. 

## <a name="overview"></a>Översikt

BizTalk-tjänster består av två underordnade tjänster:

1.  Microsoft BizTalk Services Hybridanslutningar
2.  EAI- och EDI bridge-baserade integrering

Om du vill ha för att flytta hybridanslutningar, sedan [Hybridanslutningar för Azure App Service](../app-service/app-service-hybrid-connections.md) beskrivs ändringar och funktioner i den här tjänsten. Azure Hybridanslutningar ersätter Hybridanslutningar för BizTalk-tjänster. Azure Hybridanslutningar är tillgänglig i Azure App Service och erbjuds i Azure-portalen. Azure Hybridanslutningar innehåller också en ny Hybridanslutningshanteraren för att hantera befintliga BizTalk-tjänst hybridanslutningar och nya hybridanslutningar som du skapar i portalen. Azure App Service-Hybridanslutningar är allmänt tillgänglig (GA).

Logic Apps är ersättningen för EAI- och EDI bridge-baserade-integration. Logic Apps innehåller samma funktioner som BizTalk-tjänst med mera. Logic Apps tillhandahåller skalbar molnlagring förbrukningsbaserad arbetsflöde och orchestration-funktioner så att du kan snabbt och enkelt skapa komplexa integration lösningar använder en webbläsare, eller använda verktyg i Visual Studio.

Följande tabell innehåller en mappning av BizTalk-tjänst-funktioner för Logic Apps.

| BizTalk Services   | Logic Apps            | Syfte                  |
| ------------------ | --------------------- | ---------------------------- |
| Anslutning          | Anslutning             | Skicka och ta emot data   |
| Platslänksbrygga             | Logikapp             | Pipeline-processor           |
| Validera fas     | XML-verifiering åtgärd      | Validera ett XML-dokument mot ett schema             |
| Utöka fas       | Data-token      | Befordra egenskaper i meddelanden eller för beslut om routning             |
| Transformera fas    | Transformera åtgärd      | Konvertera XML-meddelanden från ett format till en annan             |
| Avkoda fas       | Flat fil avkoda åtgärd      | Konvertera från flat-fil till XML             |
| Koda fas       |  Flat fil koda åtgärd      | Konvertera XML till flat-fil             |
| Meddelandet Inspector       |  Azure Functions eller API Apps      | Köra anpassad kod i din integreringar             |
| Väg-åtgärd      |  Villkor eller växel      | Skicka meddelanden till en av de angivna kopplingarna             |

Det finns ett antal olika typer av artefakt i BizTalk-tjänst.

## <a name="connectors"></a>Anslutningar
Kopplingar i BizTalk-tjänst kan bryggor skickar och tar emot data, inklusive dubbelriktat bryggor som aktiverad interaktioner som HTTP-baserade frågor och svar. I Logic Apps används samma terminologi. Kopplingar i logikappar fungerar på samma sätt, och även innehålla över 140 som kan ansluta till en stor mängd olika tekniker och tjänster, både lokalt med hjälp av lokala Data Gateway (ersätta BizTalk-Adapter-tjänst som används av BizTalk-tjänst), och i molnet SaaS- och PaaS-tjänster, till exempel OneDrive, Office 365, Dynamics CRM och mycket mer.

Datakällor i BizTalk-tjänst är begränsade till FTP-, SFTP och Service Bus-kö eller avsnittet prenumeration.

![](media/logic-apps-move-from-mabs/sources.png)

Varje brygga har en HTTP-slutpunkt som standard, som är konfigurerad med Runtime-adressen och egenskaperna för bryggan relativ adress. För att uppnå samma med Logic Apps använda den [förfrågan och svar](../connectors/connectors-native-reqres.md) åtgärder.

## <a name="xml-processing-and-bridges"></a>XML-bearbetning och bryggor
En brygga i BizTalk-tjänst är detsamma som en pipeline för bearbetning. En brygga kan ta data från en koppling och göra några arbetar med data och skicka den till en annan dator. Logic Apps har samma genom att stödja pipeline-baserade interaktion samma mönster som BizTalk-tjänst och innehåller också ett antal andra integration mönster. Den [XML-Request-Reply Bridge](https://msdn.microsoft.com/library/azure/hh689781.aspx) i BizTalk Services kallas en VETER pipeline som består av faser som kan:

* V Validera
* (E) Enrich
* (T) transformera
* (E) Enrich
* (R) väg

Som det visas i följande bild, bearbetning delas mellan begäran och svar och ger kontroll över begäran och svar sökvägar separat (till exempel med hjälp av olika kartor för varje):

![](media/logic-apps-move-from-mabs/xml-request-reply.png)

Dessutom XML enkelriktade bridge läggs avkoda och koda faser i början och slutet av bearbetning och direkt bryggan innehåller ett enda Enrich steg.

### <a name="message-processing-and-decodingencoding"></a>Bearbetning av meddelandet och avkoda/kodning
I BizTalk-tjänst du XML-meddelanden med olika typer och fastställa matchande schemat för det mottagna meddelandet. Detta utförs i den **meddelandetyper** steget i receive process-pipelinen. Sedan används fasen avkoda identifierade meddelandetypen för att avkoda den med hjälp av det angivna schemat. Om schemat är en platt fil-schemat, konverterar inkommande platt fil till XML. 

Logic Apps tillhandahåller liknande funktioner. Du får en platt fil över en mängd olika protokoll som använder olika connector utlösare (filsystem, FTP-, HTTP och så vidare) och använda den [Flat fil avkoda](../logic-apps/logic-apps-enterprise-integration-flatfile.md) åtgärder för att omvandla inkommande data till XML. Du kan flytta dina befintliga scheman flat fil direkt till logikappar utan ändringar och sedan ladda upp scheman till ditt konto för integrering.

### <a name="validation"></a>Validering
När inkommande data har konverterats till XML (eller om XML var meddelandeformat togs emot), kör verifiering för att avgöra om meddelandet följer XSD-schemat. Om du vill göra detta i Logic Apps, Använd den [XML-verifiering](../logic-apps/logic-apps-enterprise-integration-xml-validation.md) åtgärd. Igen, kan du använda samma scheman från BizTalk-tjänst utan några ändringar.

### <a name="transform-messages"></a>Transformera meddelanden
I BizTalk-tjänst konverterar transformeringen mellanlagra en XML-baserade meddelandeformat till en annan. Detta görs genom att använda en karta med mapper TRFM-baserade. Processen påminner om i Logic Apps. Transform-åtgärden körs en karta från ditt konto för integrering. Den största skillnaden är att mappningar i Logic Apps XSLT-format. XSLT innehåller möjligheten att återanvända befintliga XSLT som du redan har, inklusive maps som skapats för BizTalk Server som innehåller functoids. 

### <a name="routing-rules"></a>Regler för Routning
BizTalk-tjänst gör ett beslut om routning på vilka endpoint/connector för att skicka inkommande meddelanden/data. Möjlighet att välja en av ett antal förkonfigurerade slutpunkter är möjligt med hjälp av alternativet Routning filter:

![](media/logic-apps-move-from-mabs/route-filter.png)

Logic Apps ger funktioner för mer avancerad logik med [villkoret](../logic-apps/logic-apps-use-logic-app-features.md) och [växel](../logic-apps/logic-apps-switch-case.md), aktivera avancerade Kontrollflöde och routning. Konvertera routning filter i BizTalk-tjänst bäst uppnås med hjälp av en **villkoret** *om* det finns två alternativ. Om det finns fler än två, använder du en **växla**.

### <a name="enrich"></a>Utöka
Den Enrich delen i BizTalk-tjänst som bearbetar lägger till egenskaper kontexten meddelande som är associerade med mottagna data. Till exempel uppgradera en egenskap som ska användas för routning (diskuteras nedan) från en databassökning, eller genom att extrahera ett värde med ett XPath-uttryck. Logic Apps ger åtkomst till alla kontextuella utdata från föregående åtgärder, vilket gör det enkelt att replikera samma beteende. Till exempel med hjälp av den `Get Row` SQL-anslutningsåtgärd du returnera data från en SQL Server-databas och använda data i en beslut och åtgärd för routning. På samma sätt egenskaper på inkommande Service Bus meddelanden i kön av en utlösare är adresserbara samt XPath med hjälp av arbetsflödet definition language uttrycket.

### <a name="use-custom-code"></a>Använd anpassad kod
BizTalk-tjänst ger möjlighet att [köra anpassad kod](https://msdn.microsoft.com/library/azure/dn232389.aspx) på din egen sammansättningar. Det har implementerats av den [IMessageInspector](https://msdn.microsoft.com/library/microsoft.biztalk.services.imessageinspector.aspx) gränssnitt. Varje steg i bryggan innehåller två egenskaper (på Ange Inspector och på Avsluta Inspector) som tillhandahåller de .net-typ du skapade som implementerar det här gränssnittet. Anpassad kod kan du utföra mer komplexa bearbetning av data, samt återanvända befintliga kod i sammansättningar som utför vanliga affärslogik. 

Logic Apps tillhandahåller två sätt att utföra anpassad kod: Azure Functions och API Apps. Azure Functions kan skapas och anropas från logikappar. Se [Lägg till och kör anpassad kod för logic apps via Azure Functions](../logic-apps/logic-apps-azure-functions.md). Du kan använda API Apps, en del av Azure App Service för att skapa egna utlösare och åtgärder. Lär dig mer om [skapar en anpassad API för användning med Logic Apps](../logic-apps/logic-apps-create-api-app.md). 

Om du har anpassade kod i assmeblies som anropas från BizTalk-tjänst kan antingen flytta koden till Azure Functions eller skapa anpassade API: er med API Apps; beroende på vad du implementera. Till exempel om du har kod som kapslar in en annan tjänst att Logic Apps inte har en koppling sedan skapa en API-App och använder de åtgärder som din API-app finns i din logikapp. Om du har hjälpfunktioner eller bibliotek, är Azure Functions förmodligen bäst.

### <a name="edi-processing-and-trading-partner-management"></a>EDI bearbetning och hantering av handelspartners
BizTalk-tjänster omfattar EDI och B2B bearbetning med stöd för AS2 tillämplighet instruktionen 2, X12 och EDIFACT. Det gör även Logic Apps. I BizTalk Services din skapa EDI bryggor och skapa/hantera handel partners och avtalen i den dedikerade hantering och uppföljning av portalen.

I Logic Apps den här funktionen ingår i den [Enterprise-Integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md). Det här består av Integration konto och B2B-åtgärder för EDI och B2B-bearbetning. Den [integrering konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) används för att skapa och hantera [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md). När du skapar ett konto för Integration kan associera du en eller flera logikappar till kontot. När associerat, kan du använda B2B-åtgärder för att komma åt handel partnerinformation i din logikapp. Det finns följande åtgärder:

* AS2 Encode
* AS2 avkoda
* X12 Encode
* Avkoda X12
* EDIFACT koda
* EDIFACT avkoda

Till skillnad från BizTalk-tjänst frikopplad åtgärderna från transportprotokoll. Så när du skapar dina logic apps har du mer flexibilitet för vilka kopplingar du använder för att skicka och ta emot data. Till exempel filer möjligt för att ta emot X12 som bifogade filer från e-post och sedan processen dessa filer i en logikapp. 

## <a name="manage-and-monitor"></a>Hantera och övervaka
Dedikerad portal för BizTalk-tjänst som samt hantering av handelspartners spårningsfunktioner att övervaka och felsöka problem. 

Logic Apps ger bättre spårning och övervakning av funktioner i den [Azure-portalen](../logic-apps/logic-apps-monitor-your-logic-apps.md), och med den [Operations Management Suite B2B-lösning](../logic-apps/logic-apps-monitor-b2b-message.md), även en mobilapp för att hålla koll på saker när du är på språng.

## <a name="high-availability"></a>Hög tillgänglighet
För att uppnå hög tillgänglighet (HA) i BizTalk-tjänst måste använda du mer än en instans i en viss region för att dela belastningen. Hög tillgänglighet i region med logic apps är inbyggda och kommer utan extra kostnad. En process för säkerhetskopiering och återställning krävs för out av region katastrofåterställning för B2B bearbetning i BizTalk-tjänst. I Logic Apps mellan region aktiv/passiv [DR kapaciteten](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md) tillhandahålls; som tillåter synkronisering av B2B-data över Integrationskonton i olika regioner för verksamhetskontinuitet.

## <a name="next"></a>Nästa
* [Vad är Logic Apps?](logic-apps-overview.md)
* [Skapa din första logikapp](quickstart-create-first-logic-app-workflow.md), eller kom snabbt igång med en [färdig mall](logic-apps-create-logic-apps-from-templates.md)  
* [Visa alla tillgängliga anslutningsprogram](../connectors/apis-list.md) du kan använda i en logikapp
