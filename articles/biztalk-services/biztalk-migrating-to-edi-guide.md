---
title: Migrera BizTalk Server EDI-lösningar till BizTalk Services tekniska Guide | Microsoft Docs
description: Migrera EDI till MABS; Microsoft Azure-tjänster för BizTalk
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843492"
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrera BizTalk Server EDI-lösningar till BizTalk-tjänst: tekniska Guide

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Skapad av: Tim Wieman och Nitin Mehrotra

Granskare: Karthik Bharthy

Skrivits: Microsoft Azure BizTalk-tjänst – februari 2014-versionen.

## <a name="introduction"></a>Introduktion
Elektroniska Data Interchange (EDI) är en av de vanligaste medel genom vilka företag exchange-data elektroniskt, kallas även för Business-to-Business eller B2B transaktioner. BizTalk Server har haft EDI-stöd för över tio år, sedan den första BizTalk Server-versionen. Med BizTalk-tjänster fortsätter Microsoft stöd för EDI-lösningar på Microsoft Azure-plattformen. B2B transaktioner är främst externa för en organisation och därför är det enklare att implementera om det har implementerats på en plattform i molnet. Microsoft Azure tillhandahåller den här funktionen via BizTalk-tjänst.

Vissa kunder visar BizTalk-tjänst som en ”greenfield” plattform för nya EDI-lösningar kan ha många kunder aktuella BizTalk Server EDI-lösningar som de vill migrera till Azure. Eftersom BizTalk Services EDI konstruerad är baserat på samma nyckel entiteter som BizTalk Server EDI-arkitektur (handel partners, enheter, avtal), det möjligt att migrera BizTalk Server EDI artefakter till BizTalk-tjänst.

Det här dokumentet beskrivs några av skillnaderna ingår i migrera BizTalk Server EDI-artefakter till BizTalk-tjänst. Det här dokumentet förutsätter kunskaper om BizTalk Server EDI-bearbetning och handel Partner avtal. Mer information om BizTalk Server EDI finns [handel Partner med hjälp av BizTalk hanteringsservern](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Vilken version av BizTalk Server EDI artefakter kan migreras till BizTalk-tjänst?
BizTalk Server EDI-modulen förbättrats avsevärt för BizTalk Server 2010, när den har remodeled för att inkludera partner, profiler och avtal. BizTalk-tjänst använder samma modell för att organisera handelspartner och business-avdelningar inom de handelspartner. Därför kan är migrera EDI artefakter från BizTalk Server 2010 och senare versioner till BizTalk-tjänst, en mycket mer direkt vidarebefordra process. Om du vill migrera EDI-artefakter som är associerade med versioner före BizTalk Server 2010 måste du först uppgradera till BizTalk Server 2010 och sedan migrera EDI-artefakter till BizTalk-tjänst.

## <a name="scenariosmessage-flow"></a>Scenarier/Message-flöde
Som med BizTalk Server är EDI bearbetas i BizTalk-tjänst uppbyggd kring en lösning för handel Partner (TPM). TPM-lösningen har följande viktiga komponenter:

* Handelspartner, som representerar organisationen i en B2B-transaktion.
* Profiler som representerar avdelningar inom en handelspartner.
* Handel partner (eller avtal), som representerar företag avtal mellan två parter-profiler.

Följande bild visar likheter, samt skillnader mellan en BizTalk Server EDI-lösning och BizTalk Services EDI-lösning:

![][EDImessageflow]

De viktigaste skillnaderna och likheter mellan en EDI-lösningen flöde i BizTalk Server och BizTalk-tjänst är:

* Precis som BizTalk Server använder en EDIReceive pipeline för att ta emot meddelandet EDI och en EDISend pipeline för att skicka meddelandet EDI, använder BizTalk Services en EDI får bridge för att ta emot och skicka EDI bridge för att skicka ut EDI-meddelanden. BizTalk Server pipelines är kopplade till ett avtal med skicka eller ta emot portar. Avtalet själva anger skicka eller ta emot bridge i BizTalk-tjänst.
* BizTalk Server när EDIReceive pipeline bearbetar EDI-meddelandet dumpas meddelandet i en SQL Server-databas. Sedan hämtar meddelandet från SQL Server-databasen EdiSend pipeline, bearbetar den och skickar den till handelspartner.
  
    När EDI får bridge bearbetar EDI-meddelandet i BizTalk-tjänst, dirigerar meddelandet till en extern process. Extern process kan köras på Microsoft Azure eller lokalt. Extern process ska vidarebefordra meddelandet till EDI skicka bridge; Skicka bryggan emot natur inte meddelandet. Efter bearbetningen av meddelandet dirigerar bryggan EDI skicka meddelandet till handelspartner.

BizTalk-tjänst ger en enkel att använda configuration-upplevelse för att snabbt skapa och distribuera en B2B-avtal mellan handelspartner utan att konfigurera alla Microsoft Azure Compute instanser (webb- eller Arbetarroll roller), alla Microsoft Azure SQL-databaser eller något Microsoft Azure storage-konton. Mer komplicerade scenarier kräver binda i arbetsflöden eller annan bearbetning för tjänsten ”runt kanterna” på en handel partneravtalet, det vill säga före eller efter handel Partner avtal EDI bridge bearbetning. Detaljerade inträffar händelser följande under en EDI meddelandebehandling i BizTalk-tjänst.

1. En EDI-meddelandet tas emot från handelspartner Fabrikam.  För att ta emot EDI-meddelanden från handelspartner stöder BizTalk-tjänst transportprotokoll som FTP, SFTP, AS2 och HTTP/S.
2. Handel partner avtal mottagarsidan bearbetningen Disassemblerar EDI-meddelande till XML-format.  Du kan vidarebefordra nedmonterade EDI-meddelandet (i XML-format) till Service Bus-slutpunkter som en Service Bus Relay-slutpunkt, Service Bus-ämne, Service Bus-kö eller en brygga BizTalk-tjänst.
3. Nedmonterade XML-meddelanden kan sedan tas emot från slutpunkten för ytterligare anpassade bearbetning.  Dessa slutpunkter kunde till exempel bearbetas av en lokal komponent eller en Microsoft Azure Compute bearbetar meddelandet i en Windows-arbetsflödet (WF) eller Windows Communication Foundation (WCF) service-instans.
4. ”Skicka sida bearbetningen” handelspartner avtal sedan monterar XML-meddelande till EDI-format och skickar den till handelspartner Contoso.  För att skicka EDI-meddelanden för handelspartners stöder BizTalk Services samma protokoll som används för att ta emot EDI-meddelanden.

Ytterligare det här dokumentet ger konceptuell information på migrera några av de olika BizTalk Server EDI-artefakterna till BizTalk-tjänst.

## <a name="sendreceive-ports-to-trading-partners"></a>Skicka och ta emot portar till handelspartner
I BizTalk Server skapar du få platser och ta emot portar för att ta emot EDI/XML-meddelanden från handelspartner och du ställer in skicka portar för att skicka EDI/XML-meddelanden till handelspartner. Du binder sedan upp dessa portar till ett handelspartneravtal via BizTalk Server-administrationskonsolen. I de platser där du får meddelanden från handelspartner BizTalk-tjänster och där du skicka meddelanden till handelspartner är konfigurerade som en del av handelspartneravtal (som en del av Transport inställningar) i BizTalk-Services-portalen.  Så har du verkligen inte begreppet ”skicka portar” och ”ta emot platser” sig självt, i BizTalk-tjänst. Mer information finns i [skapa avtal](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (bryggor)
I BizTalk Server EDI är pipelines meddelandet bearbetning enheter som även kan innehålla anpassade logik för behandling av specifika funktioner som krävs av programmet. Motsvarande skulle EDI-brygga för BizTalk-tjänst. Men i BizTalk-tjänst för tillfället EDI-bryggor ”stängs”.  Det vill säga kan du lägga till egna anpassade aktiviteter till EDI-brygga. Alla anpassade bearbetning måste göras utanför EDI-bridge i ditt program före eller efter meddelandet anger brygga som konfigurerats som en del av handel partneravtalet. EAI bryggor har du möjlighet att göra anpassade bearbetning. Om du vill anpassad bearbetning, kan du använda EAI bryggor före eller efter att meddelandet bearbetas av EDI-bridge. Mer information finns i [så innehåller anpassad kod i bryggor](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Du kan infoga ett Publicera/prenumerera-flöde med anpassad kod och/eller med hjälp av Service Bus-meddelanden köer och ämnen innan handelspartneravtal tar emot meddelandet eller efter avtalet bearbetar meddelandet och skickar den till en Service Bus-slutpunkten.

Se **scenarier/Message flödet** i det här avsnittet för meddelande flödet mönstret.

## <a name="agreements"></a>Avtal
Om du är bekant med BizTalk Server 2010 Trading Partner avtalen används för EDI-bearbetning letar BizTalk-tjänst handelspartneravtal insatt. De flesta inställningar för avtal är samma och använda samma terminologi. I vissa fall avtalet inställningarna är mycket enklare jämfört med samma inställningar i BizTalk-servern. Microsoft Azure BizTalk Services stöder X12-, EDIFACT- och AS2-transport.

Microsoft Azure BizTalk Services tillhandahåller också en **TPM datamigrering** verktyg för att migrera handel partners och avtal från BizTalk Server handel Partner modulen BizTalk-Services-portalen. Datamigrering för TPM-verktyget är tillgängligt som en del av ett verktyg för paket, som kan hämtas från den [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Paketet innehåller också en viktig information som innehåller instruktioner om hur du använder verktyget och grundläggande felsökningsinformation för verktyget.

## <a name="schemas"></a>Scheman
BizTalk-tjänst ger EDI-scheman som kan användas i BizTalk-Services-lösningar.  Dessutom kan BizTalk Server EDI-scheman också användas med BizTalk-tjänst eftersom rotnoden för EDI-schemat är samma mellan BizTalk Server samt BizTalk-tjänst. Därför kommer du att kunna direkt BizTalk Server EDI-scheman och använda dem i EDI-lösningar som du utvecklar med BizTalk-tjänst. Du kan också hämta scheman från den [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Maps (transformeringar)
Maps i BizTalk Server kallas transformeringar i BizTalk-tjänst. Migrera mappningar från BizTalk Server till BizTalk-tjänst kan vara något mer komplexa åtgärder för att uppnå (beroende på kartan komplexitet). Verktyget mappning används för BizTalk-tjänst skiljer sig från BizTalk-mappning. Även om mapparen främst ser likadant ut, är underliggande mappningsformat olika. Functoids (kallas **kartan Operations** i BizTalk-tjänst) tillgängliga för användarna är också olika.  Du kan i praktiken direkt använda en BizTalk-karta i BizTalk-tjänst. Inte alla functoids som är tillgängliga i BizTalk Server är också tillgängliga som kartan åtgärder i BizTalk-tjänst.

### <a name="new-transform-operations"></a>Nya omvandlingsåtgärder
När listan över kartan omvandlingsåtgärder tillgängliga kan verka detsamma BizTalk Server mapper, har BizTalk Services transformeringar nya sätt att utföra samma uppgifter. Exempelvis BizTalk Services transformeringar har **Liståtgärder** tillgängliga. Detta är inte tillgänglig i BizTalk-mappning.  Den **Liståtgärder** kan du skapa och fungerar på en ”lista”, där en lista är en uppsättning objekt (även kallat ”rader”) och varje objekt kan ha flera medlemmar (även kallat ”kolumner”).  Du kan sortera listan, Välj objekt baserat på ett villkor, osv.

Ett annat exempel de nya funktionerna i BizTalk Services omvandlar är den **Loop Operations**.  Det är svårt att skapa kapslade loopar i BizTalk Server-mappning.  Därmed läggs åtgärderna Loop kartan för BizTalk Services transformeringar.

Har ett annat exempel är den **If-Then-Else** operation i kartan.  Gör en if-then-else åtgärd var möjligt i BizTalk mapper, men det krävs flera functoids att utföra en till synes enkel uppgift.

### <a name="migrating-biztalk-server-maps"></a>Migrera BizTalk Server mappar
Microsoft Azure BizTalk Services ger ett verktyg för att migrera BizTalk Server mappar till BizTalk-tjänst-transformeringar. Den **BTMMigrationTool** är tillgänglig som en del av den **verktyg** paket som ingår i den [BizTalk Services SDK-hämtningen](http://go.microsoft.com/fwlink/p/?LinkId=235057). Mer information om verktyget finns [konvertera en BizTalk-karta till en BizTalk Services transformera](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Du kan också titta på ett prov av Sandro Pereira, BizTalk MVP om hur du [migrerar BizTalk Server mappningar till BizTalk-tjänst transformeringar](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orkestreringarna
Om du behöver migrera BizTalk Server orchestration bearbetning till Microsoft Azure skulle orkestreringarna behöva skrivas eftersom Microsoft Azure inte stöder körs BizTalk Server orkestreringarna.  Du kan skriva om orchestration-funktioner i en tjänst i Windows Workflow Foundation 4.0 (WF4).  Detta är en fullständig omarbetning eftersom det inte finns för närvarande inga migrering från BizTalk Server orkestreringarna till WF4. Här följer några resurser för Windows-arbetsflödet:

* [*Hur du integrerar en WCF-tjänst för arbetsflödet med Service Bus-köer och ämnen* ](https://msdn.microsoft.com/library/azure/hh709041.aspx) av Paolo Salvatori. 
* [*Skapa appar med Windows Workflow Foundation och Azure* session](http://go.microsoft.com/fwlink/p/?LinkId=237314) från Build 2011 konferensrum.
* [*Windows Workflow Foundation Developer Center* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) på MSDN.
* [*Dokumentation för Windows Workflow Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) på MSDN.

## <a name="other-considerations"></a>Andra överväganden
Följande är några säkerhetsaspekter som du måste göra när du använder BizTalk-tjänst.

### <a name="fallback-agreements"></a>Fallback avtal
BizTalk Server EDI-bearbetningen har begreppet ”reserv avtalen”.  BizTalk-tjänst har **inte** hittills har ett begrepp som reserv avtal.  BizTalk-dokumentationen avsnitten [i rollen för avtal vid bearbetning av EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) och [reserv avtal egenskaper eller konfigurera globala](https://msdn.microsoft.com/library/bb245981.aspx) information om hur du använder reserv avtal i BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Routning till flera mål
BizTalk-tjänst bryggor i dess aktuella tillstånd stöder inte vidarebefordra meddelanden till flera mål med hjälp av ett Publicera-prenumerera modellen. Du kan i stället vidarebefordra meddelanden från en brygga BizTalk-tjänst till ett Service Bus-ämne, som sedan kan ha flera prenumerationer för att ta emot meddelandet vid mer än en slutpunkt.

## <a name="conclusion"></a>Slutsats
Microsoft Azure BizTalk-tjänst har uppdaterats vid regelbundna milstolpar att lägga till flera funktioner och möjligheter. Varje uppdatering hoppas vi att stödja nya funktioner för att underlätta skapar slutpunkt till slutpunkt-lösningar med hjälp av BizTalk-tjänster och andra tekniker för Azure.

## <a name="see-also"></a>Se även
[Utveckla företagsprogram med Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
