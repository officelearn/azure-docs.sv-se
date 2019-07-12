---
title: Vad är förhandsversionen av Azure Data resurs
description: En översikt över Azure förhandsgranskning för resursen
author: joannapea
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 7d4e51ec9564bfb123cf73d9fe89d040f42fe650
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807551"
---
# <a name="what-is-azure-data-share-preview"></a>Vad är förhandsversionen av Azure Data Share?

I dagens värld visas informationen som en viktig strategisk tillgång som många organisationer behöver för att helt enkelt och säkert sätt dela med kunder och partner. Det finns många sätt att kunder gör den här idag, inklusive via FTP, e-post, API: er att nämna några. Organisationer kan enkelt tappa som de har delat sina data med. Dela data via FTP eller skapa egna API-infrastruktur är ofta dyra att etablera och administrera. Det finns hanteringskostnader som är associerade med hjälp av dessa metoder att dela i stor skala. 

Många organisationer behöver vara ansvariga för de data som de har delat. Förutom ansvarstagande vill många organisationer kunna styra, hantera och övervaka alla sina data delning på ett enkelt sätt. I dag världen, där data förväntas fortsätter att växa i exponentiell takt kan behöver organisationer du ett enkelt sätt att dela big data. Kunder kräver den senaste informationen om du vill se till att de kan att härleda insikter i god tid.

Dela förhandsversionen av Azure Data gör det möjligt för organisationer att helt enkelt och säkert sätt dela data med flera kunder och partner. Med bara några klick du etablerar en ny resurs-konto för data, Lägg till datauppsättningar och bjuda in dina kunder och partner till din data filresurs. Dataleverantörer har alltid kontroll över de data som de har delat. Azure-Dataresursen gör det enkelt att hantera och övervaka vilka data som har delats, när och av vem. 

En dataleverantör kan behålla kontrollen över hur data hanteras genom att ange villkor för användning av deras dataresursen. Datakonsumenten måste acceptera dessa villkor innan du kan ta emot data. Dataleverantörer kan ange hur ofta som deras datakonsumenterna ta emot uppdateringar. Åtkomst till nya uppdateringar kan återkallas när som helst av dataleverantören. 

Azure-Dataresursen bidrar till att förbättra insikter genom att göra det enkelt att kombinera data från andra leverantörer att utöka analys och AI-scenarier. Enkelt använda power ot Azure analytics-verktyg för att förbereda, bearbeta och analysera data som delas med hjälp av Azure Data filresurs. 

## <a name="scenarios-for-azure-data-share"></a>Scenarier för Azure Dataresurs

Azure Dataresursen kan användas i ett antal olika branscher. En återförsäljare kanske exempelvis vill dela senaste återställningspunkt försäljningsdata med deras leverantörer. Med hjälp av Azure Data delar, en återförsäljare ställa in en Dataresurs som innehåller försäljningsdata punkt för alla deras leverantörer och dela försäljning på grundval av per timme eller dag. 

Azure-Dataresursen kan också användas för att upprätta en data-marknadsplatsen efter en viss bransch. Till exempel myndigheter eller en research institution som delar regelbundet maskerade data om befolkningstillväxten med tredje part. 

En annan användningsfall för Azure Data filresurs är att upprätta en data-consortium. Till exempel kan ett antal olika forskningsinstitutioner dela data med en enda betrodda brödtext. Data analyseras, aggregeras eller med hjälp av Azures analysverktyg och sedan delas med berörda parter. 

## <a name="how-it-works"></a>Hur det fungerar

Azure-Dataresursen använder en ögonblicksbildbaserade delningsapplikationen metod, där data flyttas från Dataleverantörens Azure-prenumeration och hamnar i data-consumer Azure-prenumeration. Som en dataleverantör etablera en Dataresurs och bjuda in mottagare till dataresursen. Datakonsumenterna får en inbjudan till din data filresurs via e-post. När en datakonsumenten accepterar inbjudan, de kan utlösa en fullständig ögonblicksbild av data som delas du delat dem. Dessa data tas emot till konsumenter storage-konto för data. Datakonsumenter kan ta emot regelbundna, inkrementella uppdateringar av data som delas med dem så att de alltid har den senaste versionen av data. 

Dataleverantörer kan erbjuda sina data konsumenter inkrementella uppdateringar för data som delas med dem via ett schema för ögonblicksbilder. Scheman ögonblicksbilder erbjuds på varje timme eller varje dag. När en datakonsumenten accepterar och konfigurerar sina Dataresurs, kan de prenumerera på ett schema för ögonblicksbilder. Detta är bra i scenarier där de delade data uppdateras regelbundet och datakonsumenten måste de mest uppdaterade data. 

![dataflöde för resursen](media/data-share-flow.png)

När en datakonsumenten accepterar en Dataresurs, kan de ta emot data i ett lagringskonto som de vill. Till exempel om dataleverantören delar data med hjälp av Azure Blob Storage, datakonsumenten kan ta emot dessa data i Azure Data Lake Store. 

## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure-Dataresursen kan Dataprovidrarna:

* Dela data från Azure Storage och Azure Data Lake Store med kunder och partner utanför organisationen

* Håll koll på vilka du har delat dina data med

* Hur ofta dina dataanvändare tar emot uppdateringar till dina data

* Tillåt dina kunder att hämta den senaste versionen av dina data efter behov, eller att de kan få inkrementella ändringar i dina data automatiskt vid ett intervall som du definierar

Azure-Dataresursen gör det möjligt för Datakonsumenterna att: 

* Visa en beskrivning av typ av data som delas

* Visa villkor för användning av data

* Godkänna eller avvisa inbjudan Azure Data filresurs

* Utlös en fullständig eller inkrementell ögonblicksbild av en resurs för Data som en organisation har delat med dig

* Prenumerera på en Data-resurs för att få den senaste kopian av data via kopiering av inkrementell ögonblicksbild

* Acceptera data som delas med dig i ett Azure Blob Storage eller Azure Data Lake Gen2 konto

Alla viktiga funktioner som anges ovan kan hanteras via Azure eller via REST API: er. Kolla in våra referensdokumentation för mer information om hur du använder Azure Data filresursen via REST API: er. 

## <a name="security"></a>Säkerhet

Azure-Dataresursen utnyttjar den underliggande säkerhet som Azure erbjuder att skydda data i vila och under överföring. Data krypteras i viloläge, om det stöds av underliggande Lagringsmekanismen. Data är också krypterad under överföringen. Metadata om en Dataresurs är också krypterade i vila och under överföring. 

Åtkomstkontroller kan ställas in på Azure-Dataresursen resursnivån att se till att den används av de som har behörighet. 

Azure-Dataresursen utnyttjar hanterade identiteter för Azure-resurser (kallades tidigare MSI: er) för automatisk hantering i Azure Active Directory. Hanterade identiteter för Azure-resurser utnyttjas för åtkomst till de lagringskonton som används för delning av data. Det finns inga utbyte av autentiseringsuppgifter mellan en dataleverantör och en datakonsument. Mer information finns i den [hanterade identiteter för Azure-resurser sida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 

## <a name="supported-regions"></a>Regioner som stöds

En lista över Azure-regioner som gör resursen för Azure Data tillgängliga, finns i den [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/) sidan och Sök efter Azure-Dataresursen. 

Azure-Dataresursen lagrar inte några data själva. Data lagras i de underliggande lagringskonton som ska delas. Till exempel om en data-producent lagrar sina data i ett Azure Data Lake Store-konto finns i USA, västra, är det där data lagras. Om de delar data med ett Azure Storage-konto finns i Europa, västra, överförs data direkt till Azure Storage-konto finns i Europa, västra. 

Azure Data Share-tjänsten har inte ska vara tillgängliga i din region för att använda tjänsten. Om du har data som lagras i ett Azure Storage-konto finns i en region där Azure Data delar inte är tillgänglig ännu kan du fortfarande använda tjänsten om du vill dela dina data. 

## <a name="next-steps"></a>Nästa steg

Information om hur du kan börja dela data, fortsätter du till den [dela dina data](share-your-data.md) självstudien.
