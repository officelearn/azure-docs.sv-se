---
title: Vanliga scenarier för Azure Data Catalog
description: En översikt över vanliga scenarier för Azure Data Catalog, inklusive registrering och identifiering av värdefulla datakällor, vilket möjliggör business intelligence med självbetjäning och samlar in befintlig kunskap om datakällor och processer.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: aeae505b510f563a6640726c384ea358983eb24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736463"
---
# <a name="azure-data-catalog-common-scenarios"></a>Vanliga scenarier för Azure Data Catalog
Den här artikeln innehåller vanliga scenarier där Azure Data Catalog kan hjälpa din organisation att få mer värde från sina befintliga datakällor.

## <a name="scenario-1-registration-of-central-data-sources"></a>Scenario 1: Registrering av centrala datakällor
Organisationer har ofta många värdefulla datakällor. Dessa datakällor omfattar branschspecifika system för transaktionsbearbetning (ONLINE transaction processing), datalager och business intelligence/analytics-databaser. Antalet system, och överlappningen mellan dem, växer vanligtvis med tiden i takt med att företagens behov utvecklas och själva verksamheten utvecklas genom till exempel fusioner och förvärv.

Det kan vara svårt för organisationsmedlemmar att veta var data ska hittas i dessa datakällor. Frågor som följande är alltför vanliga:

* Av de tre HR-system som används inom företaget, som ska jag använda för att skapa denna typ av rapport?
* Var ska jag gå för att få de certifierade försäljningssiffrorna för räkenskapsåret som just avslutats?
* Vem ska jag fråga, eller vilken process ska jag använda för att få tillgång till informationslagret?
* Jag vet inte om siffrorna stämmer. Vem kan jag be om insikt om hur dessa data ska användas innan jag delar den här instrumentpanelen med mitt team?

På dessa och andra frågor kan Azure Data Catalog ge svar. De centrala, värdefulla, IT-hanterade datakällor som används i olika organisationer är ofta den logiska utgångspunkten för att fylla i katalogen. Även om alla användare kan registrera en datakälla, med katalogen kick-startade med de datakällor som är mest sannolikt att ge värde till det största antalet användare hjälper till att driva antagande och användning av systemet. 

Om du kommer igång med Azure Data Catalog kan det vara ditt första steg att lyckas genom att identifiera och registrera viktiga datakällor som används av många olika datateam.

Det här scenariot innebär också en möjlighet att kommentera de värdefulla datakällorna för att göra dem lättare att förstå och komma åt. En viktig aspekt av detta arbete är att inkludera information om hur användare kan begära åtkomst till datakällan. Med Azure Data Catalog kan du ange e-postadressen till den användare eller det team som ansvarar för att kontrollera åtkomst till datakällor, länkar till befintliga verktyg eller dokumentation eller fritext som beskriver processen för åtkomstbegäran. Den här informationen hjälper medlemmar som upptäcker registrerade datakällor men som ännu inte har behörighet att komma åt data för att enkelt begära åtkomst med hjälp av de processer som definieras och kontrolleras av datakällägarna.

## <a name="scenario-2-self-service-business-intelligence"></a>Scenario 2: Business Intelligence med självbetjäning
Även om traditionella business intelligence-lösningar fortsätter att vara en ovärderlig del av många organisationers datalandskap, har den föränderliga affärstakten gjort självbetjäning BI allt viktigare. Genom att använda bi med självbetjäning kan informationsarbetare och analytiker skapa egna rapporter, arbetsböcker och instrumentpaneler utan att förlita sig på ett centralt IT-team eller begränsas av IT-teamets schema och tillgänglighet.

I BI-scenarier med självbetjäning kombinerar användare ofta data från flera källor, varav många kanske inte tidigare har använts för BI och analys. Även om vissa av dessa datakällor kanske redan är kända, kan det vara svårt att upptäcka vad du ska göra för att hitta och utvärdera potentiella datakällor för en viss uppgift.

Traditionellt är den här identifieringsprocessen en manuell: analytiker använder sina peer-nätverksanslutningar för att identifiera andra som arbetar med de data som söks. När en datakälla har hittats och använts upprepas processen igen för varje efterföljande BI-insats med självbetjäning, med flera användare som utför en redundant manuell identifieringsprocess.

Med Azure Data Catalog kan din organisation bryta den här arbetscykeln. Efter att ha upptäckt en datakälla på traditionella sätt kan en analytiker registrera den för att göra den lättare att upptäcka av andra användare i framtiden. Även om analytikern kan tillföra mer värde genom att kommentera registrerade datatillgångar, behöver den här anteckningen inte ske samtidigt som registreringen. Användare kan bidra med tiden, som deras scheman tillåter, gradvis lägga till värde till de datakällor som registrerats i katalogen.

Denna organiska tillväxt av kataloginnehållet är ett naturligt komplement till den främre registreringen av centrala datakällor. Förfylla katalogen med data som många användare behöver kan vara en drivkraft för första användning och identifiering. Att göra det möjligt för användare att registrera och kommentera ytterligare källor kan vara ett sätt att hålla dem och andra organisationsmedlemmar engagerade.

Det är värt att notera att även om det här scenariot fokuserar specifikt på bi med självbetjäning gäller samma mönster och utmaningar även för storskaliga BI-projekt för företag. Genom att använda Data Catalog kan din organisation förbättra alla ansträngningar som innebär en manuell process för identifiering av datakällor.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Scenario 3: Fånga stamkunskap
Hur vet du vilka data du behöver för att göra ditt jobb och var du hittar dessa data?

Om du har varit i ditt jobb ett tag, du förmodligen bara vet. Du har genomgått en gradvis inlärningsprocess och har med tiden lärt dig om de datakällor som är nyckeln till ditt dagliga arbete.

När en ny medarbetare går med i ditt team, hur vet den personen vilka data som krävs för jobbet och var den finns?

Oddsen är, den nya personen kommer till dig med dessa frågor.

Denna pågående överföring av tribal kunskap är en del av data-källa upptäcktsprocessen i organisationer stora och små. Mer seniora och erfarna medarbetare har byggt upp kunskap genom åren, och nyare gruppmedlemmar har lärt sig att fråga dem när de har frågor. Den viktigaste informationen finns ofta bara i huvudet på några nyckelpersoner, och när dessa människor är på semester eller lämnar laget, lider organisationen.

Dataexperter anstränger sig vanligtvis för att dokumentera sina kunskaper och dela dem via e-post eller i Word-dokument på en SharePoint-gruppwebbplats. Även om detta tillvägagångssätt kan vara värdefullt, introducerar det ett nytt upptäcktsproblem: hur vet folk vilken dokumentation som finns och var de kan hitta den?

Med Azure Data Catalog har din organisation en enda, central plats för lagring och delning av den här stamkunskapen och för att göra den lätt att upptäcka. I Data Catalog kan dina dataexperter kommentera datatillgångar direkt och tillhandahålla länkar till befintlig dokumentation. När organisationens medlemmar använder katalogen för att upptäcka en datakälla hittar de inte bara själva källan, utan också den kunskap som tidigare bara fanns i huvudet på organisationens experter.
