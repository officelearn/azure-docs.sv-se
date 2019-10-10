---
title: Vad är för hands version av Azure Data-resurs
description: Lär dig mer om hur du enkelt och säkert delar data med flera kunder och partner med hjälp av Azure Data repreview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: overview
ms.date: 07/10/2019
ms.openlocfilehash: 6ba6e9df7b3fd2992891f4b085581c51fe900744
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169076"
---
# <a name="what-is-azure-data-share-preview"></a>Vad är förhandsversionen av Azure Data Share?

I dagens värld visas data som en viktig strategisk till gång som många organisationer behöver för att enkelt och säkert dela med sina kunder och partners. Det finns många sätt för kunderna att göra detta idag, inklusive via FTP, e-post och API: er för att ge några. Organisationer kan enkelt förlora vem de har delat med sig av sina data med. Att dela data via FTP eller genom att skapa sin egen API-infrastruktur är ofta kostsamt att etablera och administrera. Det finns hanterings kostnader för att använda dessa metoder för delning i stor skala. 

Många organisationer måste vara konto bara för de data som de har delat. Förutom ansvar skulle många organisationer vilja kunna styra, hantera och övervaka all sin data delning på ett enkelt sätt. I dagens värld, där data förväntas växa i en exponentiell takt, behöver organisationer ett enkelt sätt att dela Big data. Kunder efterfrågar de senaste data som krävs för att säkerställa att de kan komma i rätt tid.

Med för hands versionen av Azure Data Share kan organisationer enkelt och säkert dela data med flera kunder och partner. Med bara några klick kan du etablera ett nytt data delnings konto, lägga till data uppsättningar och bjuda in kunder och partner till din data resurs. Data leverantörer har alltid kontroll över de data som de har delat. Azure Data Share gör det enkelt att hantera och övervaka vilka data som delades, när och av vem. 

En DataProvider kan hålla kontrollen över hur deras data hanteras genom att ange användnings villkoren för data resursen. Data konsumenten måste acceptera dessa villkor innan de kan ta emot data. Data leverantörer kan ange med vilken frekvens data konsumenter får uppdateringar. Åtkomst till nya uppdateringar kan återkallas när som helst av data leverantören. 

Azure Data Share hjälper till att förbättra insikter genom att göra det enkelt att kombinera data från tredje part för att utöka analys-och AI-scenarier. Använd enkelt Azure Analytics-verktyg i Power of för att förbereda, bearbeta och analysera data som delas med Azure Data Share. 

Både data leverantören och data konsumenten måste ha en Azure-prenumeration för att kunna dela och ta emot data. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenarier för Azure Data Share

Azure Data Share kan användas i flera olika branscher. En åter försäljare kan till exempel vilja dela den senaste försäljnings platsen med deras leverantörer. Med hjälp av Azure Data Share kan en åter försäljare skapa en data resurs som innehåller en data resurs som innehåller en data källa för alla deras leverantörer och dela försäljningen per timme eller dag. 

Azure Data Share kan också användas för att upprätta en data Marketplace för en speciell bransch. Till exempel en myndighet eller en forsknings institution som regelbundet delar anonymiserats data om populationens tillväxt med tredje part. 

Ett annat användnings fall för Azure Data Share är att upprätta ett data konsortium. Ett antal olika forsknings institutioner kan till exempel dela data med ett enda betrott innehåll. Data analyseras, aggregeras eller bearbetas med hjälp av Azure Analytics-verktyg och delas sedan med berörda parter. 

## <a name="how-it-works"></a>Så här fungerar det

Azure Data Share använder en ögonblicks bild baserad delnings metod där data flyttas från data leverantörens Azure-prenumeration och hamnar i data konsumentens Azure-prenumeration. Som data leverantör etablerar du en data resurs och bjuder in mottagare till data resursen. Data konsumenter får en inbjudan till din data resurs via e-post. När en data konsument accepterar inbjudan kan de utlösa en fullständig ögonblicks bild av de data som delas som delas. Dessa data tas emot i lagrings kontot för data konsumenter. Data konsumenter kan ta emot regelbundna, stegvisa uppdateringar av data som delas med dem så att de alltid har den senaste versionen av datan. 

Data leverantörer kan erbjuda sina data konsumenters stegvisa uppdateringar av de data som delas med dem genom ett schema för ögonblicks bilder. Ögonblicks bilds scheman erbjuds per timme eller per dag. När en data konsument accepterar och konfigurerar sin data resurs kan de prenumerera på ett ögonblicks bild schema. Detta är fördelaktigt i scenarier där delade data uppdateras regelbundet och data konsumenten behöver de mest aktuella data. 

![data resurs flöde](media/data-share-flow.png)

När en data konsument accepterar en data resurs kan de ta emot data i ett lagrings konto som de väljer. Om dataprovidern t. ex. delar data med Azure Blob Storage kan data konsumenten ta emot dessa data i Azure Data Lake Store. 

## <a name="key-capabilities"></a>De viktigaste funktionerna

Azure Data Share gör det möjligt för data leverantörer att:

* Dela data från Azure Storage och Azure Data Lake Store med kunder och partner utanför organisationen

* Håll koll på vem du har delat dina data med

* Hur ofta dina data konsumenter tar emot uppdateringar av dina data

* Gör det möjligt för dina kunder att hämta den senaste versionen av dina data vid behov, eller tillåta att de automatiskt får stegvisa ändringar i dina data enligt ett intervall som definierats av dig

Azure Data Share gör det möjligt för data konsumenter att: 

* Visa en beskrivning av den typ av data som delas

* Visa användnings villkoren för data

* Acceptera eller avvisa en Azure Data Share-inbjudan

* Utlös en fullständig eller stegvis ögonblicks bild av en data resurs som en organisation har delat med dig

* Prenumerera på en data resurs för att ta emot den senaste kopian av data via en stegvis ögonblicks bilds kopia

* Acceptera data som delas med dig i ett Azure Blob Storage-eller Azure Data Lake Gen2-konto

Alla viktiga funktioner som anges ovan stöds via Azure eller via REST-API: er. Mer information om hur du använder Azure Data Share via REST-API: er finns i vår referens dokumentation. 

## <a name="security"></a>Säkerhet

Azure Data Share utnyttjar den underliggande säkerhet som Azure erbjuder för att skydda data i vila och under överföring. Data krypteras i vila, där det stöds av den underliggande lagrings funktionen. Data krypteras också vid överföring. Metadata om en data resurs krypteras också i vila och under överföring. 

Åtkomst kontroller kan ställas in på resurs nivån på Azure-dataresursen för att säkerställa att de kan nås av de som är auktoriserade. 

Azure Data Share utnyttjar hanterade identiteter för Azure-resurser (kallades tidigare MSIs) för automatisk identitets hantering i Azure Active Directory. Hanterade identiteter för Azure-resurser utnyttjas för åtkomst till de lagrings konton som används för data delning. Det finns inget utbyte av autentiseringsuppgifter mellan en data leverantör och en data konsument. Mer information finns på [sidan hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities). 


## <a name="supported-regions"></a>Regioner som stöds

En lista över Azure-regioner som gör Azure-dataresursen tillgänglig finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) och Sök efter Azure Data Share. 

Azure Data Share lagrar inga data. Data lagras i de underliggande lagrings konton som delas. Om en data producent till exempel lagrar sina data i ett Azure Data Lake Store konto som finns i västra USA, är det där data lagras. Om de delar data med ett Azure Storage konto som finns i Europa, västra, överförs data direkt till det Azure Storage konto som finns i Västeuropa. 

Azure Data Share-tjänsten behöver inte vara tillgänglig i din region för att utnyttja tjänsten. Om du till exempel har data som lagras i ett Azure Storage konto som finns i en region där Azure Data Share inte är tillgängligt ännu, kan du fortfarande använda tjänsten för att dela dina data. 

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .
