---
title: Vanliga frågor och svar i Azure Data Catalog
description: Vanliga frågor och svar om Azure Data Catalog, inklusive funktioner för identifiering av datakällor, anteckningar och hantering.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409027"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Vanliga frågor och svar i Azure Data Catalog
Den här artikeln innehåller svar på vanliga frågor som rör Azure Data Catalog-tjänsten.

## <a name="what-is-azure-data-catalog"></a>Vad är Azure Data Catalog?
Data Catalog är en fullständigt hanterad tjänst, som finns i Microsoft Azure, som fungerar som ett system för registrering och identifiering för företagets datakällor. Med Data Catalog kan alla användare, från analytiker till datavetare och utvecklare, registrera, upptäcka, förstå och använda datakällor.

## <a name="what-customer-challenges-does-it-solve"></a>Vilka kundutmaningar löser det?
Data Catalog tar itu med utmaningarna med datakällidentifiering och "mörka data" så att användarna kan identifiera och förstå företagets datakällor.

## <a name="what-are-its-target-audiences"></a>Vilka är dess målgrupper?
Data Catalog är utformad för tekniska och icke-tekniska användare, inklusive:

* Datautvecklare och BI- och analysexperter: Personer som ansvarar för att producera data- och analysinnehåll som andra kan använda.
* Dataförvaltare: Personer som har kunskap om data, vad det innebär och hur de är avsedda att användas.
* Datakonsumenter: Personer som enkelt behöver kunna upptäcka, förstå och ansluta till de data de behöver för att göra sitt jobb genom att använda det verktyg de själva väljer.
* Central IT: Personer som behöver göra hundratals datakällor upptäckbara av företagsanvändare och som behöver upprätthålla tillsyn över hur data används och av vem.

## <a name="what-is-its-availability-by-region"></a>Vad är dess tillgänglighet per region?
Data Catalog-tjänster är för närvarande tillgängliga i följande datacenter:

* USA, västra
* USA, östra
* Europa, västra
* Europa, norra
* Australien, östra
* Sydostasien

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Vilka är dess gränser för antalet datatillgångar?
Free Edition of Data Catalog är begränsad till 5 000 registrerade datatillgångar.

Standardutgåvan för datakatalogen stöder upp till 100 000 registrerade datatillgångar.

Alla objekt som är registrerade i Data Catalog, till exempel tabeller, vyer, filer och rapporter, räknas som en datatillgång.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Vilka är dess datakällor och tillgångstyper som stöds?
En lista över datakällor som stöds finns i [Datakatalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hur begär jag stöd för en annan datakälla?
Om du vill skicka funktionsförfrågningar och annan feedback går du till [datakatalogen på Azure Feedback Forum](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Varför får jag redan en *felkatalog* när jag försöker skapa en ny katalog?

När du köper Office 365 E5 med Power BI Pro-licens skapar Microsoft automatiskt en standardkatalog i prenumerationens region. Den här katalogen använder den kostnadsfria SKU.This catalog uses the free SKU. Användarlicensen för Office 365 / Power BI hanteras på sidan Administration av Office 365. 

Den här typen av datakatalog har dock inget **administratörsalternativ** och visas inte i **Azure-portalen**. Du kan inte ta bort den här typen av datakatalog. På samma sätt får du inte byta namn på datakatalogen och du kan inte flytta den till en annan region. 

Användarkonton som tilldelas en Power BI Pro-licens automatiskt har åtkomst till datakatalogen på grund av licensavtalet när de registrerade sig för Office 365 E5 med Power BI Pro-licensen. Den här typen av användare har fullständig åtkomst till datakatalogtillgångar utan administratörsbehörighet. Den typen av användare är *inte* en del av **kataloganvändarrollen** i Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hur kommer jag igång med Data Catalog?
Det bästa sättet att komma igång är att gå till [Komma igång med datakatalog .](data-catalog-get-started.md) Den här artikeln är en heltäckande översikt över funktionerna i tjänsten.

## <a name="how-do-i-register-my-data"></a>Hur registrerar jag mina uppgifter?
Så här registrerar du dina data i datakatalogen:
1. Starta registreringsverktyget för Azure Data Catalog i området **Publicera** i Azure Data Catalog. 
2. Logga in med samma autentiseringsuppgifter som du använder för att komma åt datakatalogportalen i datakatalogens datakäll.
3. Välj datakällan och de specifika tillgångar som du vill registrera.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Vilka egenskaper extraheras för datatillgångar som är registrerade?
De specifika egenskaperna skiljer sig från datakälla till datakälla, men i allmänhet extraherar publiceringstjänsten för datakatalog följande information:

* Tillgångsnamn
* Tillgångstyp
* Beskrivning av tillgångar
* Attribut-/kolumnnamn
* Datatyper för attribut/kolumn
* Beskrivning av attribut/kolumn

> [!IMPORTANT]
> Att registrera datatillgångar med Data Catalog flyttar eller kopierar inte data till molnet. När du registrerar tillgångar från en datakälla kopieras tillgångarnas metadata till Azure, men data finns kvar på den befintliga platsen för datakällan. Undantaget från den här regeln är om du väljer att ladda upp förhandsgranskningsposter eller en dataprofil när du registrerar tillgångarna. När du inkluderar en förhandsgranskning kopieras upp till 20 poster från varje tillgång och lagras som en ögonblicksbild i Data Catalog. När du inkluderar en dataprofil beräknas och inkluderas aggregerad information i metadata som lagras i katalogen. Aggregerad information kan innehålla storleken på tabeller, procentandelen null-värden per kolumn eller lägsta, högsta och genomsnittliga värden för kolumner. 
>
>

> [!NOTE]
> För datakällor som SQL Server Analysis Services som har en förstklassig **beskrivningsegenskap** extraherar registreringsverktyget för datakatalogen egenskapen. För lokala SQL *Server-relationsdatabaser* som saknar en förstklassig **beskrivningsegenskap** extraherar registreringsverktyget för datakällan datakälla värdet från den **ms_description** utökade egenskapen för objekt och kolumner. Den här egenskapen stöds inte för SQL Azure. Mer information finns i [Använda utökade egenskaper för databasobjekt](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Hur lång tid tar det för nyregistrerade tillgångar att visas i katalogen?
När du har registrerat tillgångar med datakatalog kan det finnas en period på 5 till 10 sekunder innan de visas i datakatalogportalen.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hur kommenterar och berikar jag metadata för mina registrerade datatillgångar?
Det enklaste sättet att tillhandahålla metadata för registrerade tillgångar är att välja tillgången i datakatalogportalen och sedan ange värdena i egenskapsfönstret eller schemafönstret för det markerade objektet.

Du kan också ange vissa metadata, till exempel experter och taggar, under registreringsprocessen. De värden som du anger i publiceringstjänsten för datakatalog gäller för alla tillgångar som registreras vid den tidpunkten. Om du vill visa de nyligen registrerade objekten i portalen för ytterligare anteckningar väljer du knappen **Visa portal** på den sista skärmen i registreringsverktyget för datakatalogdatakälla.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hur tar jag bort mina registrerade dataobjekt?
Du kan ta bort ett objekt från Data Catalog genom att markera objektet i portalen och sedan klicka på knappen **Ta bort.** Om du tar bort objektet tas dess metadata bort från datakatalogen men påverkar inte den underliggande datakällan.

## <a name="what-is-an-expert"></a>Vad är en expert?
En expert är en person som har ett informerat perspektiv om ett dataobjekt. Ett objekt kan ha flera experter. En expert behöver inte vara "ägare" för ett objekt, men är helt enkelt någon som vet hur data kan och bör användas.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hur delar jag information med datakatalogteamet om jag stöter på problem?
Om du vill rapportera problem, dela information och ställa frågor går du till [Azure Data Catalog-forumet](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Fungerar katalogen med en annan datakälla som jag är intresserad av?
Vi arbetar aktivt med att lägga till fler datakällor i Data Catalog. Om du vill se en specifik datakälla som stöds föreslår du det (eller uttrycker ditt stöd om det redan har föreslagits) genom att gå till [datakatalogen på Azure Feedback Forum](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Vilka behörigheter behöver jag för att registrera tillgångar med Data Catalog?
Om du vill köra registreringsverktyget för datakatalogen behöver du behörigheter för datakällan som gör att du kan läsa metadata från källan. Om du även vill inkludera en förhandsgranskning måste du ha behörigheter som gör att du kan läsa data från de objekt som registreras.

Med datakatalogen kan katalogadministratörer begränsa vilka användare och grupper som kan lägga till metadata i katalogen. Mer information finns i [Så här skyddar du åtkomst till datakatalog och datatillgångar](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Kommer Data Catalog att göras tillgänglig för lokal distribution också?
Data Catalog är en molntjänst som kan arbeta med både moln- och lokala datakällor för att leverera en hybridlösning för identifiering av datakällor. Det finns för närvarande inga planer för en version av datakatalogtjänsten som körs lokalt.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kan jag extrahera mer eller rikare metadata från de datakällor jag registrerar?
Vi arbetar aktivt med att utöka funktionerna i Data Catalog. Om du vill att ytterligare metadata ska extraheras från datakällan under registreringen föreslår du det (eller röstar för den, om det redan har [föreslagits) i datakatalogen på Azure Feedback Forum](https://feedback.azure.com/forums/906052-data-catalog). 

Om du vill inkludera kolumn-/schemametadata, förhandsgranskningar eller dataprofiler för datakällor där dessa metadata inte extraheras av registreringsverktyget för datakällan, kan du använda API:et för datakatalogen för att lägga till dessa metadata. Mer information finns i [AZURE Data Catalog REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hur begränsar jag synligheten för registrerade datatillgångar, så att endast vissa personer kan upptäcka dem?
Markera datatillgångarna i datakatalogen och klicka sedan på knappen **Ta ägarskap.** Ägare av datatillgångar i Data Catalog kan ändra synlighetsinställningarna så att alla användare antingen kan identifiera de ägda tillgångarna eller begränsa synligheten för specifika användare. Mer information finns [i Hantera datatillgångar i Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hur uppdaterar jag registreringen för en datatillgång så att ändringar i datakällan återspeglas i katalogen?
Om du vill uppdatera metadata för datatillgångar som redan är registrerade i katalogen registrerar du bara om datakällan som innehåller tillgångarna. Alla ändringar i datakällan, till exempel kolumner som läggs till eller tas bort från tabeller eller vyer, uppdateras i katalogen, men alla anteckningar som tillhandahålls av användare behålls.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Min fråga besvaras inte här. Var kan jag ta svar?
Gå till [Azure Data Catalog-forumet](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Frågor som ställs där kommer att hitta sin väg hit.
