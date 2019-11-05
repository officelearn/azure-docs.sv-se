---
title: Vad är Azure Data Share?
description: Lär dig mer om att dela data på ett säkert sätt till flera kunder och partner med hjälp av Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 43972bc8e64e0bf88aae00126735d3e0c8751254
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499381"
---
# <a name="what-is-azure-data-share"></a>Vad är Azure Data Share?

I dagens värld visas data som en viktig strategisk till gång som många organisationer behöver för att enkelt och säkert dela med sina kunder och partners. Det finns många sätt för kunderna att göra detta idag, inklusive via FTP, e-post och API: er för att ge några. Organisationer kan enkelt förlora vem de har delat med sig av sina data med. Att dela data via FTP eller genom att skapa sin egen API-infrastruktur är ofta kostsamt att etablera och administrera. Det finns hanterings kostnader för att använda dessa metoder för delning i stor skala. 

Många organisationer måste vara konto bara för de data som de har delat. Förutom ansvar skulle många organisationer vilja kunna styra, hantera och övervaka all sin data delning på ett enkelt sätt. I dagens värld, där data förväntas växa i en exponentiell takt, behöver organisationer ett enkelt sätt att dela Big data. Kunder efterfrågar de senaste data som krävs för att säkerställa att de kan komma i rätt tid.

Azure Data Share gör det möjligt för organisationer att enkelt och säkert dela data med flera kunder och partner. Med bara några klick kan du etablera ett nytt data delnings konto, lägga till data uppsättningar och bjuda in kunder och partner till din data resurs. Data leverantörer har alltid kontroll över de data som de har delat. Azure Data Share gör det enkelt att hantera och övervaka vilka data som delades, när och av vem. 

En DataProvider kan hålla kontrollen över hur deras data hanteras genom att ange användnings villkoren för data resursen. Datakonsumenten måste acceptera dessa villkor innan de kan ta emot data. Dataleverantörer kan ange hur ofta datakonsumenter får uppdateringar. Åtkomsten till nya uppdateringar kan när som helst återkallas av dataleverantören. 

Azure Data Share hjälper till att förbättra insikter genom att göra det enkelt att kombinera data från tredje part för att utöka analys-och AI-scenarier. Använd enkelt Azure Analytics-verktyg i Power of för att förbereda, bearbeta och analysera data som delas med Azure Data Share. 

Både data leverantören och data konsumenten måste ha en Azure-prenumeration för att kunna dela och ta emot data. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenarier för Azure Data Share

Azure Data Share kan användas i flera olika branscher. En åter försäljare kan till exempel vilja dela den senaste försäljnings platsen med deras leverantörer. Med hjälp av Azure Data Share kan en åter försäljare skapa en data resurs som innehåller en data resurs som innehåller en data källa för alla deras leverantörer och dela försäljningen per timme eller dag. 

Azure Data Share kan också användas för att upprätta en data Marketplace för en speciell bransch. Till exempel en myndighet eller en forsknings institution som regelbundet delar anonymiserats data om populationens tillväxt med tredje part. 

Ett annat användnings fall för Azure Data Share är att upprätta ett data konsortium. Ett antal olika forsknings institutioner kan till exempel dela data med ett enda betrott innehåll. Data analyseras, aggregeras eller bearbetas med hjälp av Azure Analytics-verktyg och delas sedan med berörda parter. 

## <a name="how-it-works"></a>Så här fungerar det

Azure data sharing erbjuder för närvarande ögonblicks bilds delning och delning på plats (i begränsad för hands version). 

I ögonblicks bilds-baserad delning flyttas data från data leverantörens Azure-prenumeration och hamnar i data konsumentens Azure-prenumeration. Som data leverantör etablerar du en data resurs och bjuder in mottagare till data resursen. Data konsumenter får en inbjudan till din data resurs via e-post. När en data konsument accepterar inbjudan kan de utlösa en fullständig ögonblicks bild av de data som delas med dem. Dessa data tas emot i lagrings kontot för data konsumenter. Data konsumenter kan ta emot regelbundna, stegvisa uppdateringar av data som delas med dem så att de alltid har den senaste versionen av datan. 

Data leverantörer kan erbjuda sina data konsumenters stegvisa uppdateringar av de data som delas med dem genom ett schema för ögonblicks bilder. Ögonblicks bilds scheman erbjuds per timme eller per dag. När en data konsument accepterar och konfigurerar sin data resurs kan de prenumerera på ett ögonblicks bild schema. Detta är fördelaktigt i scenarier där delade data uppdateras regelbundet och data konsumenten behöver de mest aktuella data. 

![data resurs flöde](media/data-share-flow.png)

När en data konsument accepterar en data resurs kan de ta emot data i ett valfritt data lager. Om dataprovidern t. ex. delar data med Azure Blob Storage kan data konsumenten ta emot dessa data i Azure Data Lake Store. Om data leverantören delar data från en Azure SQL Data Warehouse kan data konsumenten välja om de vill ta emot data i en Azure Data Lake Store, en Azure SQL Database eller en Azure SQL Data Warehouse. Vid delning från SQL-baserade källor kan data konsumenten även välja om de ska ta emot data i Parquet eller CSV. 

Delning på plats är för närvarande en begränsad för hands version för Azure Datautforskaren. Data leverantörer kan dela data där de finns, utan data förflyttning via en symbolisk länk. Registrera dig för den begränsade för hands versionen av delning på plats för Azure Datautforskaren [här](https://aka.ms/azuredatasharepreviewsignup). 

## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Data Share gör det möjligt för data leverantörer att:

* Dela data från listan över [data lager som stöds](supported-data-stores.md) med kunder och partner utanför organisationen

* Håll koll på vem du har delat dina data med

* Hur ofta dina data konsumenter tar emot uppdateringar av dina data

* Gör det möjligt för dina kunder att hämta den senaste versionen av dina data vid behov, eller tillåta att de automatiskt får stegvisa ändringar i dina data enligt ett intervall som definierats av dig

Azure Data Share gör det möjligt för data konsumenter att: 

* Visa en beskrivning av den typ av data som delas

* Visa användnings villkoren för data

* Acceptera eller avvisa en Azure Data Share-inbjudan

* Utlös en fullständig eller stegvis ögonblicks bild av en data resurs som en organisation har delat med dig

* Prenumerera på en data resurs för att ta emot den senaste kopian av data via en stegvis ögonblicks bilds kopia

* Acceptera data som delas med dig i ett [data lager som stöds](supported-data-stores.md).

Alla viktiga funktioner som anges ovan stöds via Azure eller via REST-API: er. Mer information om hur du använder Azure Data Share via REST-API: er finns i vår referens dokumentation. 

## <a name="security"></a>Säkerhet

Azure Data Share utnyttjar den underliggande säkerhet som Azure erbjuder för att skydda data i vila och under överföring. Data krypteras i vila, där de stöds av det underliggande data lagret. Data krypteras också vid överföring. Metadata om en data resurs krypteras också i vila och under överföring. 

Åtkomst kontroller kan ställas in på resurs nivån på Azure-dataresursen för att säkerställa att de kan nås av de som är auktoriserade. 

Azure Data Share utnyttjar hanterade identiteter för Azure-resurser (kallades tidigare MSIs) för automatisk identitets hantering i Azure Active Directory. Hanterade identiteter för Azure-resurser utnyttjas för åtkomst till de data lager som används för data delning. Det finns inget utbyte av autentiseringsuppgifter mellan en data leverantör och en data konsument. Mer information finns på [sidan hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Regioner som stöds

En lista över Azure-regioner som gör Azure-dataresursen tillgänglig finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-share/) och Sök efter Azure Data Share. 

Azure Data Share lagrar inga data. Data lagras i det underliggande data lager som delas. Om en data producent till exempel lagrar sina data i ett Azure Data Lake Store konto som finns i västra USA, är det där data lagras. Om de delar data med ett Azure Storage konto som finns i Europa, västra, överförs data direkt till det Azure Storage konto som finns i Västeuropa. 

Azure Data Share-tjänsten behöver inte vara tillgänglig i din region för att utnyttja tjänsten. Om du till exempel har data som lagras i ett Azure Storage konto som finns i en region där Azure Data Share inte är tillgängligt ännu, kan du fortfarande använda tjänsten för att dela dina data. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
