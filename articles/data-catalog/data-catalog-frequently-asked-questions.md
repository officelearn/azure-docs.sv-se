---
title: Vanliga och frågor svar om Azure Data Catalog
description: Vanliga frågor och svar om Azure Data Catalog, inklusive funktioner för identifiering av datakällor, anteckning och hantering.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 9aaba38b7dc0cd2bf659c9d91c9d7660bd17a6dd
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47409192"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Data Catalog
Den här artikeln innehåller svar på vanliga frågor som rör Azure Data Catalog-tjänsten.

## <a name="what-is-azure-data-catalog"></a>Vad är Azure Data Catalog?
Data Catalog är en fullständigt hanterad tjänst som finns i Microsoft Azure, som fungerar som ett system för registrering och upptäckt för företagsdatakällor. Med Data Catalog kan alla användare, från analytiker till dataexperter och utvecklare, registrera, identifiera, förstå och använda datakällor.

## <a name="what-customer-challenges-does-it-solve"></a>Problem med vilken kund gör det lösa?
Data Catalog adresser utmaningarna med identifiering av datakällor och ”undangömda data” så att användare kan identifiera och förstå företagets datakällor.

## <a name="what-are-its-target-audiences"></a>Vad är dess målgrupper?
Data Catalog är utformat för teknisk och icke-teknisk användare, inklusive:

* Data-utvecklare och experter för BI och analysverktyg: personer som ansvarar för att producera och analys innehåll så att andra kan använda.
* Data förvaltare: personer som har kunskapen om data, vad det innebär och hur den är avsedd att användas.
* Datakonsumenterna: personer som behöver kunna enkelt upptäcka, förstå och Anslut till data som de behöver för att utföra sitt arbete med hjälp av verktyget för valfri.
* Central IT: personer som behöver för att hundratals datakällor kunna identifieras av användare i verksamheten, och som behöver underhålla översikt över hur data används och av vem.

## <a name="what-is-its-availability-by-region"></a>Vad är dess tillgänglighet efter region?
Data Catalog-tjänster är tillgängliga i följande datacenter:

* Västra USA
* Östra USA
* Västra Europa
* Norra Europa
* Östra Australien
* Sydostasien

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Vad är dess gränser för antalet datauppsättningar?
Den kostnadsfria versionen av Data Catalog är begränsad till 5 000 registrerade datatillgångar.

Standard Edition av Data Catalog har stöd för upp till 100 000 registrerade datatillgångar.

Objekt som registrerats i Data Catalog kan till exempel tabeller, vyer, filer och rapporter, räknas det som en datatillgång.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Vad är dess käll- och tillgången datatyper som stöds?
En lista över datakällor som stöds för närvarande finns i [DSR för Data Catalog](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hur jag för att begära stöd för en annan datakälla?
Om du vill skicka funktionsförfrågningar och annan feedback, går du till den [datakatalog på Azure Feedback-forum](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Hur kommer jag igång med Data Catalog?
Det bästa sättet att komma igång är genom att gå till [komma igång med Data Catalog](data-catalog-get-started.md). Den här artikeln är en översikt över funktionerna i tjänsten.

## <a name="how-do-i-register-my-data"></a>Hur registrerar jag mina data?
Registrera dina data i Data Catalog:
1. I Azure Data Catalog-portalen i den **publicera** området starta registreringsverktyget för Azure Data Catalog. 
2. I den Data Catalog registreringsverktyget, loggar du in med samma autentiseringsuppgifter att du använder för att få åtkomst till Data Catalog-portalen.
3. Välj datakällan och de specifika tillgångar som du vill registrera.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Vilka egenskaper det extraherar för datatillgångar som registreras?
Specifika egenskaper som skiljer sig från datakällan till datakällan, men i allmänhet publiceringstjänsten i Data Catalog extraherar du följande information:

* Tillgångsnamn
* Tillgångstyp
* Tillgången beskrivning
* Attributet/kolumnnamn
* Attributet/kolumn datatyper
* Attributet/Kolumnbeskrivning

> [!IMPORTANT]
> Registrera datatillgångar med Data Catalog inte flytta eller kopiera dina data till molnet. Registrera resurser från en datakälla kopierar metadata för de resurser till Azure, men data som finns kvar i den befintliga platsen där datakällan finns. Undantaget till detta är om du väljer att ladda upp förhandsversion poster eller en dataprofil när du registrerar tillgångarna. När du inkluderar en förhandsgranskning, upp till 20 poster kopieras från varje tillgång och lagras som en ögonblicksbild i Data Catalog. När du inkluderar en dataprofil beräknas samlar in information och ingår i de metadata som lagras i katalogen. Samlar in information kan innehålla storleken på tabeller, procent av null-värden per kolumn eller lägsta, högsta och genomsnittliga värden för kolumner. 
>
>

> [!NOTE]
> Datakällor som SQL Server Analysis Services som har en första klassens **beskrivning** egenskapen Data Catalog registreringsverktyget extraherar det egenskapsvärdet. För relationsdatabaser i SQL Server, som saknar en förstklassig **beskrivning** egenskapen Data Catalog registreringsverktyget extraherar värdet från den **ms_description** utökad egenskap för objekt och kolumner. Mer information finns i [med utökade egenskaper på databasobjekt](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hur lång tid tar det för nyligen registrerade tillgångar ska visas i katalogen?
När du har registrerat tillgångar med Data Catalog kan finnas det en period på 5-10 sekunder innan de visas i Data Catalog-portalen.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hur jag för att kommentera och berika metadata för min registrerade datatillgångar?
Det enklaste sättet att tillhandahålla metadata för registrerade tillgångar är att välja tillgången i Data Catalog-portalen och ange sedan värden i egenskapsrutan eller schema-fönstret för det valda objektet.

Du kan också ange vissa metadata, till exempel experter och taggar, under registreringsprocessen. De värden som du anger i Data Catalog-publiceringstjänsten gäller för alla tillgångar som registreras vid den tidpunkten. Om du vill visa de nyligen registrerade objekt i portalen för ytterligare anmärkningar, Välj den **visa Portal** knappen på den sista skärmen i Data Catalog registreringsverktyget.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hur tar jag bort min registrerade dataobjekt?
Du kan ta bort ett objekt från Data Catalog genom att markera objektet i portalen och klicka sedan på den **ta bort** knappen. Ta bort objektet tas dess metadata från Data Catalog, men påverkar inte den underliggande datakällan.

## <a name="what-is-an-expert"></a>Vad är en expert?
En expert är en person som har en välgrundade perspektiv om ett dataobjekt. Ett objekt kan ha flera experter. En expert behöver inte vara ”ägare” för ett objekt, men är helt enkelt någon som vet hur data kan och bör användas.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hur gör jag dela information med Data Catalog-teamet om jag får problem?
Att rapportera problem, dela information och ställa frågor, gå till den [Azure Data Catalog-forumet](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Fungerar katalogen med en annan datakälla som jag är intresserad av?
Vi arbetar aktivt med att lägga till fler datakällor i Data Catalog. Om du vill se en särskild datakälla som stöds, föreslå den (eller röst support om det redan har föreslagits) genom att gå till den [datakatalog på Azure Feedback-forum](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Hur är Azure Data Catalog kopplad till datakatalogen i Power BI för Office 365?
Du kan tänka på Azure Data Catalog som en utveckling av datakatalogen i Power BI. Från och med våren 2017 används Azure Data Catalog för att aktivera delning och identifiering av frågor i Excel 2016 och Power Query för Excel. Funktioner i data Catalog i Excel är tillgängliga för användare med Power BI Pro-licenser.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Vilka behörigheter behöver jag att registrera tillgångar med Data Catalog?
Om du vill köra registreringsverktyget för Data Catalog, behöver du behörigheterna för den datakälla som gör att du kan läsa metadata från källan. Du måste ha behörigheter som gör det möjligt att läsa data från objekten som registreras för att även inkludera en förhandsgranskning.

Data Catalog kan också katalogadministratörer att begränsa vilka användare och grupper som kan lägga till metadata i katalogen. Mer information finns i [hur du skyddar åtkomsten till data catalog och datatillgångar](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog görs tillgänglig för lokal distribution samt?
Data Catalog är en molntjänst som fungerar med både i molnet och lokala datakällor för att leverera en hybridlösning för identifiering av datakällan. Det finns för närvarande inga planer för en version av Data Catalog-tjänsten som körs lokalt.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Extraherar jag fler eller mer omfattande metadata från datakällor som jag mig?
Vi arbetar aktivt för att utöka funktionerna hos Data Catalog. Om du vill ha ytterligare metadata som extraherats från datakällan under registreringen, föreslår det (eller rösta på det, om det redan har föreslagits) i den [datakatalog på Azure Feedback-forum](https://feedback.azure.com/forums/906052-data-catalog). 

Om du vill inkludera kolumnen/metadataschemat eller förhandsversioner data profiler för datakällor där dessa metadata inte ska extraheras av registreringsverktyget, kan du använda Data Catalog-API: et för att lägga till dessa metadata. Mer information finns i [Azure Data Catalog REST API: et](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hur begränsar jag visningen av registrerade datatillgångar så att endast vissa personer kan upptäcka dem?
Välj dataresurser i Data Catalog och klicka sedan på den **bli ägare** knappen. Ägare av datatillgångar i Data Catalog kan ändra inställningarna för kolumnsynlighet för att antingen tillåta alla användare att identifiera företagsägda tillgångar eller begränsa synligheten till specifika användare. Mer information finns i [hantera datatillgångar i Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hur uppdaterar jag registreringen för en datatillgång så att ändringar i datakällan visas i katalogen?
För att uppdatera metadata för datatillgångar som redan har registrerats i katalogen, bara Omregistrera den datakälla som innehåller tillgångarna. Alla ändringar i datakällan, t.ex. kolumner läggs till eller tas bort från tabeller eller vyer, uppdateras i katalogen, men alla kommentarer som tillhandahålls av användare bevaras.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Min fråga besvaras inte här. Var kan jag få svar?
Gå till den [Azure Data Catalog-forumet](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Frågor och svar det kommer fram här.
