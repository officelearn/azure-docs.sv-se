---
title: "Vanliga frågor och svar om Azure Data Catalog | Microsoft Docs"
description: "Vanliga frågor och svar om Azure Data Catalog, inklusive funktioner för datakällsidentifiering, anteckning och hantering."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 11e4bc7e71b4a94c3a0eda4275745b1beb44974d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Data Catalog
Den här artikeln innehåller svar på vanliga frågor som rör tjänsten Azure Data Catalog.

## <a name="what-is-azure-data-catalog"></a>Vad är Azure Data Catalog?
Data Catalog är en helt hanterad tjänst som finns i Microsoft Azure som fungerar som ett system för registrering och identifiering för företagets datakällor. Med Data Catalog kan alla användare från analytiker till datavetare och utvecklare, registrera, identifiera, förstå och använda datakällor.

## <a name="what-customer-challenges-does-it-solve"></a>Vilken kund utmaningar fungerar lösa?
Data Catalog löser problem för identifiering av datakälla och ”mörkt data” så att användare kan identifiera och förstå företagets datakällor.

## <a name="what-are-its-target-audiences"></a>Vad är dess målgrupperna?
Data Catalog är utformad för teknisk och icke-tekniska användare, inklusive:

* Data utvecklare och BI och analyser tekniker: personer som ansvarar för att framställa data och analyser innehållet för andra att använda.
* Data förvaltare: personer som har kunskapen om data vad det innebär och hur den är avsedd att användas.
* Datakonsumenterna: personer som ska kunna identifiera, förstå och Anslut till data de behöver för att utföra sitt jobb med hjälp av valfri.
* Central IT: personer som behöver för att hundratals datakällor kunna identifieras av användare i verksamheten och som behöver tillsyn över hur data används och av vem.

## <a name="what-is-its-availability-by-region"></a>Vad är dess tillgänglighet per region?
Katalogtjänster data är tillgängliga i följande datacenter:

* Västra USA
* Östra USA
* Västra Europa
* Norra Europa
* Östra Australien
* Sydostasien

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Vad är gränsen för antalet datatillgångar?
Den kostnadsfria Edition av Data Catalog är begränsad till 5 000 registrerade datatillgångar.

I Standard Edition av Data Catalog har stöd för upp till 100 000 registrerade datatillgångar.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Vad är dess stöds käll- och tillgångsinformation datatyper?
En lista över datakällor som för närvarande stöds, se [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hur jag för att begära stöd för en annan datakälla?
För att skicka funktionsbegäranden om och andra feedback, gå till den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="how-do-i-get-started-with-data-catalog"></a>Hur kommer jag igång med Data Catalog?
Det bästa sättet att komma igång är genom att gå till [komma igång med Data Catalog](data-catalog-get-started.md). Den här artikeln är en översikt över funktionerna i tjänsten.

## <a name="how-do-i-register-my-data"></a>Hur registrerar jag mina data?
Att registrera dina data i Data Catalog:
1. I Azure Data Catalog-portalen i den **publicera** område, starta verktyget Azure Data Catalog registrering. 
2. Logga in med samma autentiseringsuppgifter att du använder för att få åtkomst till Data Catalog-portalen i Data Catalog-publicera programmet.
3. Markera datakällan och särskilda tillgångar som du vill registrera.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Vilka egenskaper den extrahera för datatillgångar som har registrerats?
Specifika egenskaper som skiljer sig från datakällan till datakällan, men i allmänhet publiceringstjänsten i Data Catalog extraherar följande information:

* Tillgångsnamnet
* Tillgångstypen
* Beskrivning av tillgångsinformation
* Attributet/kolumnnamn
* Attributet/kolumn datatyper
* Beskrivning av attributet/kolumn

> [!IMPORTANT]
> Registrera datatillgångar med Data Catalog inte flytta eller kopiera data till molnet. Registrera resurser från en datakälla kopierar de tillgångar metadata till Azure, men data som finns kvar i den befintliga datakällplats. Undantaget till detta är om du vill överföra Förhandsgranska poster eller en profil för data när du registrerar tillgångar. När du inkluderar en förhandsgranskning, upp till 20 poster kopieras från varje tillgång och lagras som en ögonblicksbild i Data Catalog. När du inkluderar en data-profil beräknas samlar in information och ingår i de metadata som lagras i katalogen. Samlar in information kan innehålla storleken på tabeller, procentandelen null-värden per kolumn eller lägsta, högsta och genomsnittliga värden för kolumner. 
>
>

> [!NOTE]
> För datakällor, till exempel SQL Server Analysis Services som har en förstklassig **beskrivning** egenskapen datakatalogen publicera program extraherar det egenskapsvärdet. För relationsdatabaser i SQL Server, som saknar en förstklassig **beskrivning** egenskapen datakatalogen publicera programmet hämtar värdet från den **ms_description** utökad egenskap för objekt och kolumner. Mer information finns i [med utökade egenskaper på databasobjekt](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hur lång tid tar det för nyligen registrerade tillgångar ska visas i katalogen?
När du har registrerat tillgångar med Data Catalog kan finnas det en period på 5 till 10 sekunder innan de visas i Data Catalog-portalen.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hur gör kommentera och utöka metadata för min registrerade datatillgångar?
Det enklaste sättet att tillhandahålla metadata för registrerade tillgångar är att markerar du objektet i Data Catalog-portalen och ange sedan värden i egenskapsrutan eller schema-fönstret för det valda objektet.

Du kan också ange vissa metadata, till exempel experter och taggar, under registreringen. De värden som du anger i Data Catalog publiceringstjänsten gäller för alla tillgångar som registreras som för närvarande. Om du vill visa objekt som nyligen registrerade för ytterligare anmärkningar i portalen, Välj den **visa Portal** knappen sista sidan i datakatalogen publicera program.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hur tar jag bort min registrerade dataobjekt?
Du kan ta bort ett objekt från Data Catalog genom att markera objektet i portalen och klicka på den **ta bort** knappen. Tar bort objektet tas dess metadata från Data Catalog men påverkar inte den underliggande datakällan.

## <a name="what-is-an-expert"></a>Vad är en expert?
En expert är en person som har ett välgrundat perspektiv om ett dataobjekt. Ett objekt kan ha flera experter. En expert behöver inte vara ”ägare” för ett objekt, men är bara någon som vet hur data kan och bör användas.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hur gör jag dela information med Data Catalog-teamet om det uppstår problem?
Att rapportera problem, dela information och ställa frågor, gå till den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Katalogen fungerar tillsammans med en annan datakälla som jag?
Vi arbetar aktivt för att lägga till fler datakällor till datakatalogen. Om du vill se en särskild datakälla som stöds, föreslår det (eller röst din support om det redan har förslag) genom att gå till den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Hur är Azure Data Catalog relaterad till datakatalogen i Power BI för Office 365?
Du kan se Azure Data Catalog som en utvecklingen av datakatalogen i Power BI. Från och med vår 2017 används Azure Data Catalog för att aktivera delning och identifiering av frågorna i Excel 2016 och Power Query för Excel. Funktioner i data Catalog i Excel är tillgängliga för användare med Power BI Pro licenser.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Vilka behörigheter som behöver att registrera tillgångar med Data Catalog?
Om du vill köra verktyget för Data Catalog-registrering, behöver du behörigheter på datakällan som gör att du kan läsa metadata från källan. Du måste ha behörighet att läsa data från de objekt som registreras för att också inkludera en förhandsgranskning.

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog görs tillgänglig för lokala-distributionen?
Data Catalog är en molnbaserad tjänst som fungerar med både till molnet och lokala datakällor för att leverera en hybridlösning datakälla identifiering. Det finns för närvarande inga planer för en version av Data Catalog-tjänst som körs lokalt.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Extraherar jag mer eller bättre metadata från de datakällor som jag registrera?
Vi arbetar aktivt för att utöka funktionerna i Data Catalog. Om du vill ha ytterligare metadata som extraherats från datakällan under registreringen, föreslår det (eller rösta på det, om det redan har förslag) i den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). I framtiden kommer tillåter vi tredje parter kan lägga till nya typer av datakällor genom en utökningsbarhet API.

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hur begränsar jag synligheten för registrerade datatillgångar så att bara vissa personer kan identifiera dem?
Välj datatillgångarna i Data Catalog och klicka sedan på den **bli ägare** knappen. Ägarna av datatillgångar i Data Catalog kan ändra inställningarna för kolumnsynlighet för att antingen tillåta alla användare att identifiera företagsägda tillgångar eller begränsa synligheten för specifika användare.

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hur kan jag uppdatera registreringen för en datatillgång så att ändringar i datakällan visas i katalogen?
För att uppdatera metadata för datatillgångar som redan har registrerats i katalogen, helt enkelt registrera datakälla som innehåller tillgångarna. Alla ändringar i datakällan, t.ex. kolumner läggs till eller tas bort från tabeller eller vyer, uppdateras i katalogen, men alla kommentarer som användare bevaras.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Min fråga besvaras inte här. Var kan jag få svar?
Gå till den [Azure Data Catalog forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Det frågor ska hitta här.
