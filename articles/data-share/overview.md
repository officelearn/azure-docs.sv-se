---
title: Vad är Azure Data Share?
description: Lär dig mer om att dela data enkelt och säkert till flera kunder och partner med Hjälp av Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 363feda1409d2bb54e60d1b2168cba38f2a8a41c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621858"
---
# <a name="what-is-azure-data-share"></a>Vad är Azure Data Share?

I dagens värld ses data som en viktig strategisk tillgång som många organisationer behöver för att enkelt och säkert dela med sina kunder och partners. Det finns många sätt som kunderna gör detta idag, bland annat genom FTP, e-post, API: er för att nämna några. Organisationer kan lätt tappa bort vem de har delat sina data med. Att dela data via FTP eller genom att stå upp sin egen API-infrastruktur är ofta dyrt att etablera och administrera. Det finns hanteringskostnader i samband med att använda dessa metoder för att dela i stor skala. 

Många organisationer måste vara ansvariga för de data som de har delat. Förutom ansvarsskyldighet vill många organisationer kunna kontrollera, hantera och övervaka all datadelning på ett enkelt sätt. I dagens värld, där data förväntas fortsätta att växa i en exponentiell takt, organisationer behöver ett enkelt sätt att dela stordata. Kunderna kräver de senaste uppgifterna för att säkerställa att de kan få insikter i tid.

Med Azure Data Share kan organisationer enkelt och säkert dela data med flera kunder och partner. Med bara några få klick kan du etablera ett nytt dataandelskonto, lägga till datauppsättningar och bjuda in dina kunder och partner till din dataresurs. Dataleverantörer har alltid kontroll över de data som de har delat. Azure Data Share gör det enkelt att hantera och övervaka vilka data som delades, när och av vem. 

En dataleverantör kan behålla kontrollen över hur deras data hanteras genom att ange användningsvillkor för deras dataresurs. Datakonsumenten måste acceptera dessa villkor innan de kan ta emot data. Dataleverantörer kan ange hur ofta datakonsumenter får uppdateringar. Åtkomsten till nya uppdateringar kan när som helst återkallas av dataleverantören. 

Azure Data Share hjälper till att förbättra insikter genom att göra det enkelt att kombinera data från tredje part för att berika analyser och AI-scenarier. Använd enkelt kraften i Azure analytics-verktyg för att förbereda, bearbeta och analysera data som delas med Azure Data Share. 

Både dataprovidern och datakonsumenten måste ha en Azure-prenumeration för att dela och ta emot data. Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenarier för Azure-dataresurs

Azure Data Share kan användas i ett antal olika branscher. En återförsäljare kanske till exempel vill dela de senaste försäljningspunkterna med sina leverantörer. Med hjälp av Azure Data Share kan en återförsäljare ställa in en dataresurs som innehåller försäljningspunkt för alla sina leverantörer och dela försäljningen per timme eller dagligen. 

Azure Data Share kan också användas för att upprätta en datamarknadsplats för en viss bransch. Till exempel en regering eller en forskningsinstitution som regelbundet delar anonymiserade data om befolkningstillväxt med tredje part. 

Ett annat användningsfall för Azure Data Share är att upprätta ett datakonsortium. Ett antal olika forskningsinstitutioner kan till exempel dela data med ett enda betrott organ. Data analyseras, aggregeras eller bearbetas med hjälp av Azure-analysverktyg och delas sedan med berörda parter. 

## <a name="how-it-works"></a>Hur det fungerar

Azure Data Share erbjuder för närvarande ögonblicksbildbaserad delning och delning på plats. 

Vid ögonblicksbildbaserad delning flyttas data från dataleverantörens Azure-prenumeration och hamnar i datakonsumentens Azure-prenumeration. Som dataleverantör etablerar du en dataresurs och bjuder in mottagare till dataresursen. Datakonsumenterna får en inbjudan till din datadelning via e-post. När en datakonsument accepterar inbjudan kan de utlösa en fullständig ögonblicksbild av de data som delas med dem. Dessa data tas emot i datakonsumenternas lagringskonto. Datakonsumenter kan få regelbundna, inkrementella uppdateringar av de data som delas med dem så att de alltid har den senaste versionen av data. 

Dataleverantörer kan erbjuda sina datakonsumenter inkrementella uppdateringar av de data som delas med dem genom ett schema för ögonblicksbilder. Ögonblicksbildscheman erbjuds per timme eller dagligen. När en datakonsument accepterar och konfigurerar sin dataresurs kan de prenumerera på ett schema för ögonblicksbilder. Detta är fördelaktigt i scenarier där delade data uppdateras regelbundet och datakonsumenten behöver de mest uppdaterade uppgifterna. 

![datadelningsflöde](media/data-share-flow.png)

När en datakonsument accepterar en dataresurs kan de ta emot data i ett datalager som de själva väljer. Om dataprovidern till exempel delar data med Azure Blob Storage kan datakonsumenten ta emot dessa data i Azure Data Lake Store. Om dataprovidern delar data från ett Azure SQL Data Warehouse kan datakonsumenten välja om de vill ta emot data i ett Azure Data Lake Store, en Azure SQL-databas eller ett Azure SQL Data Warehouse. När det gäller delning från SQL-baserade källor kan datakonsumenten också välja om de får data i parkett eller csv. 

Med delning på plats kan dataleverantörer dela data där de finns utan att kopiera data. När delningsrelationen har upprättats via inbjudningsflödet skapas en symbolisk länk mellan dataleverantörens källdatalager och datakonsumentens måldatalager. Datakonsumenten kan läsa och fråga data i realtid med hjälp av sitt eget datalager. Ändringar i källdatalagret är tillgängliga för datakonsumenten omedelbart. Delning på plats är för närvarande i förhandsversion för Azure Data Explorer.

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med Azure Data Share kan dataleverantörer:

* Dela data från listan över [datalager som stöds](supported-data-stores.md) med kunder och partner utanför organisationen

* Håll reda på vem du har delat dina data med

* Val av ögonblicksbild eller delning på plats

* Hur ofta dina datakonsumenter får uppdateringar av dina data

* Tillåt dina kunder att hämta den senaste versionen av dina data efter behov, eller låta dem automatiskt ta emot inkrementella ändringar av dina data med ett intervall som definieras av dig

Med Azure Data Share kan datakonsumenter: 

* Visa en beskrivning av vilken typ av data som delas

* Visa användningsvillkor för data

* Acceptera eller avvisa en Azure Data Share-inbjudan

* Acceptera data som delas med dig i ett [datalager som stöds](supported-data-stores.md).

* Utlösa en fullständig eller inkrementell ögonblicksbild av en dataresurs som en organisation har delat med dig

* Prenumerera på en dataresurs för att få den senaste kopian av data via inkrementell ögonblicksbild

Alla viktiga funktioner som anges ovan stöds via Azure-portalen eller via REST API:er. Mer information om hur du använder Azure Data Share via REST API:er finns i vår referensdokumentation. 

## <a name="security"></a>Säkerhet

Azure Data Share utnyttjar den underliggande säkerheten som Azure erbjuder för att skydda data i vila och under överföring. Data krypteras i vila, där de stöds av det underliggande datalagret. Data krypteras också under överföringen. Metadata om en dataresurs krypteras också i vila och under överföring. 

Åtkomstkontroller kan ställas in på Azure Data Share-resursnivå för att säkerställa att de används av dem som är auktoriserade. 

Azure Data Share utnyttjar hanterade identiteter för Azure-resurser (tidigare kallade MSI:er) för automatisk identitetshantering i Azure Active Directory. Hanterade identiteter för Azure-resurser används för åtkomst till de datalager som används för datadelning. Det finns inget utbyte av autentiseringsuppgifter mellan en dataleverantör och en datakonsument. Mer information finns på [sidan Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Regioner som stöds

En lista över Azure-regioner som gör Azure Data Share tillgängligt finns på de [produkter som är tillgängliga efter regionsida](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) och söker efter Azure Data Share. 

Azure Data Share lagrar inte en kopia av själva data. Data lagras i det underliggande datalagret som delas. Om till exempel en dataproducent lagrar sina data i ett Azure Data Lake Store-konto i västra USA, är det där data lagras. Om de delar data med ett Azure Storage-konto i Västeuropa via ögonblicksbild överförs vanligtvis data direkt till Azure Storage-kontot i Västeuropa.

Azure Data Share-tjänsten behöver inte vara tillgänglig i din region för att utnyttja tjänsten. Om du till exempel har data som lagras i ett Azure Storage-konto i en region där Azure Data Share ännu inte är tillgängligt, kan du fortfarande utnyttja tjänsten för att dela dina data. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md)
