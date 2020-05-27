---
title: Vad är Azure Security Center?| Microsoft Docs
description: Den här sidan beskriver Security Center viktiga fördelar med att identifiera ditt säkerhets tillstånd och förbättra den med täckning av moln-och lokal resurser.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2019
ms.author: memildin
ms.openlocfilehash: f437205ae76feb36596b05ee0e320aaed0a37184
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846673"
---
# <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?

Azure Security Center är ett enhetligt system för hantering av infrastruktursäkerhet som stärker dina datacentras säkerhetstillstånd och tillhandahåller avancerat skydd för dina hybridarbetsbelastningar såväl i molnet, oavsett om de finns i Azure eller inte, som lokalt.

Att hålla dina resurser skyddade är en gemensam uppgift för din molnleverantör, Azure och dig som kund. Du måste se till att dina arbetsbelastningar skyddas när du migrerar till molnet, samtidigt som det, när du flyttar till IaaS (infrastruktur som en tjänst), innebär ett större kundansvar jämfört med hur det var i PaaS (plattform som en tjänst) och SaaS (programvara som en tjänst). Azure Security Center ger dig de verktyg du behöver för att stärka ditt nätverk, skydda dina tjänster och se till att du har koll på din säkerhetshållning.

Azure Security Center adresserar de tre mest brådskande säkerhetsutmaningarna:

-   **Snabbt ändra arbets belastningar** – det är både en styrka och en utmaning i molnet. Å ena sidan uppmuntras slutanvändarna att göra mer. Å andra sidan, hur försäkrar du dig om att de ständigt föränderliga tjänster som människorna använder och skapar uppfyller dina säkerhetsstandarder och följer rekommenderade säkerhetsmetoder?

-   **Allt mer sofistikerade attacker** – Oavsett var du kör dina arbetsbelastningar blir attackerna ständigt alltmer avancerade. Du måste skydda dina arbetsbelastningar i det offentliga molnet, vilka i praktiken är en Internet-inriktad arbetsbelastning som kan gör dig ännu mer sårbar om du inte följer rekommenderade säkerhetsmetoder.

-   **Kompetens inom säkerhetsområdet är en bristvara**– Antalet säkerhetsaviseringar och aviseringssystem överträffar vida antalet administratörer med den nödvändiga bakgrund och erfarenhet som krävs för att se till att dina miljöer är skyddade. Att hålla sig informerad om de senaste attackerna är en konstant utmaning, vilket gör det omöjligt att alltid vara på rätt plats eftersom kampen mot säkerhetshoten sker vid en ständigt föränderlig stridslinje.

Som en hjälp i skyddet mot dessa utmaningar erbjuder Security Center dig verktyg för att:

-   **Förstärk säkerhets position**: Security Center utvärderar din miljö och gör det möjligt att förstå statusen för dina resurser och om de är säkra.

-   **Skydda mot hot**: Security Center utvärderar dina arbets belastningar och aktiverar rekommendationer för skydd mot hot och säkerhets aviseringar.

-   **Bli säker snabbare**: i Security Center görs allt i moln hastigheten. Eftersom Security Center är inbyggt sker distributionen snabbt och enkelt, och du får automatisk etablering och skydd för Azure-tjänster.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="architecture"></a>Arkitektur

Eftersom Security Center ingår internt i Azure, så övervakas och skyddas PaaS-tjänster i Azure, som Service Fabric, SQL-databaser och lagringskonton, utan att det krävs någon distribution.

Security Center skyddar dessutom icke-Azure-servrar och virtuella datorer i molnet eller lokalt, för både Windows-och Linux-servrar genom att installera Log Analytics-agenten på dem. Virtuella Azure-datorer etableras automatiskt i Security Center.

De händelser som samlas in från agenterna och från Azure korreleras i Security Analytics-motorn för att ge dig anpassade rekommendationer (härdning av aktiviteter), som du bör följa för att se till att dina arbets belastningar är säkra och säkerhets aviseringar. Du bör kontrollera dessa aviseringar så snart som möjligt så att du kan försäkra dig om att dina arbetsbelastningar inte utsätts för potentiellt skadliga attacker.

När du aktiverar Security Center visas den inbyggda Security Center-säkerhetsprincipen i Azure Policy som ett inbyggt initiativ under kategorin Security Center. Det inbyggda initiativet tilldelas automatiskt till alla Security Center-registrerade prenumerationer (på kostnadsfri nivå eller standardnivå). Det inbyggda initiativet innehåller endast granskningsprinciper. Läs mer om Security Center-principer i Azure Policy i artikeln om att [arbeta med säkerhetsprinciper](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Förbättra säkerheten

Med Azure Security Center kan du förbättra din säkerhet. Du får hjälp med att identifiera och utföra de härdningsuppgifter som anges som rekommenderade säkerhetsmetoder och implementera dem i dina datorer, datatjänster och appar. Detta inkluderar hantering och tillämpning av säkerhetsprinciper och säkerställande av att dina virtuella Azure-datorer, icke-Azure-servrar och Azure PaaS-tjänster är kompatibla. Security Center ger dig de verktyg du behöver ha få en översikt över dina arbetsbelastningar med fokuserad synlighet för din nätverkssäkerhetsegendom. 

### <a name="manage-organization-security-policy-and-compliance"></a>Hantera principer för organisationssäkerhet och efterlevnad

Det tillhör grunderna i säkerhetsarbetet att känna till och skydda sina arbetsbelastningar, och utgångspunkten är att ha skräddarsydda säkerhetsprinciper på plats. Eftersom alla principer i Security Center bygger på Azure Policy kontroller får du full räckvidd och flexibilitet i en **princip lösning i världs klass**. Du kan konfigurera dina principer i Security Center så att de körs för hanteringsgrupper, för prenumerationer och även för en hel klientorganisation.

![Instrumentpanelen i Security Center](media/security-center-intro/sc-dashboard.png)

Security Center hjälper dig att **identifiera SKUGG IT-prenumerationer**. Genom att titta på prenumerationer märkta **omfattas inte** på instrumentpanelen kan du se direkt när det har skapats nya prenumerationer och se till att de omfattas av dina principer och skyddas av Azure Security Center.

![Principinstrumentpanelen i Security Center](media/security-center-intro/sc-policy-dashboard.png)

Med de avancerade övervakningsfunktionerna i Security Center kan du också **spåra och hantera efterlevnad och styrning över tid**. Den **övergripande kompatibiliteten** ger dig ett mått på hur mycket dina prenumerationer är kompatibla med principer som är kopplade till din arbets belastning. 

![Principer över tid i Security Center](media/security-center-intro/sc-policy-time.png)

### <a name="continuous-assessments"></a>Kontinuerliga utvärderingar

Security Center identifierar nya resurser som distribueras till dina arbetsbelastningar och utvärderar om de har konfigurerats enligt rekommenderade säkerhetsmetoder. Om så inte är fallet flaggas de och du får en prioriterad lista med rekommendationer om vad du måste åtgärda för att skydda dina datorer.

Ett av de mest kraftfulla verktygen Security Center ger en kontinuerlig övervakning av nätverkets säkerhets status som **nätverks kartan**. Med hjälp av kartan kan du se dina arbetsbelastningars topologi, och därmed även se om varje enskild nod har konfigurerats korrekt. Du kan se hur dina noder är anslutna, vilket hjälper dig att blockera oönskade anslutningar som potentiellt kan göra det enklare för en angripare att ta sig in i ditt nätverk.

![Security Center-nätverkskarta](media/security-center-intro/sc-net-map.png)

Security Center minimerar dina säkerhets varningar ett steg enklare genom att lägga till en **säker Poäng**. De säkra poängen är nu kopplade till varje rekommendation som du får för att hjälpa dig att förstå hur viktiga varje rekommendation är för den övergripande säkerhets position. Detta är viktigt för att du ska kunna **prioritera ditt säkerhets arbete**.

![Security Center säkra Poäng](media/security-center-intro/sc-secure-score.png)

### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimera och förbättra säkerheten genom att konfigurera rekommenderas kontroller

Den avgörande faktorn för värdet av Azure Security Center ligger i dess rekommendationer. Rekommendationerna skräddarsys för varje enskilt säkerhetsproblem som finns i dina arbetsbelastningar och Security Center gör det administrativa säkerhetsarbetet för dig, inte bara genom att söka efter dina säkerhetsrisker, utan även genom att ge dig specifika anvisningar för hur du tar bort dem.

![Security Center-rekommendationer](media/security-center-intro/sc-recommendations.png)

På så sätt gör Security Center det möjligt för dig att inte bara ställa in säkerhetsprinciper, utan även att tillämpa säkra konfigurationsstandarder för alla dina resurser.

Rekommendationerna hjälper dig att minska risken för angrepp för alla dina resurser. Detta omfattar Azure-datorer, icke-Azure-servrar och Azure PaaS-tjänster, som SQL- och Storage-konton och mycket mer, där varje resurstyp bedöms på olika sätt och har sin egen standarder.

![Security Center-rekommendationsexempel](media/security-center-intro/sc-recommendation-example.png)

## <a name="protect-against-threats"></a>Skydda dig mot risker

Med Security Centers hotskydd kan du upptäcka och förebygga hot mot infrastrukturen som en tjänst (IaaS), icke-Azure-servrar och plattformar som en tjänst (PaaS) i Azure.

Security Centers hotskydd innehåller fusionsattackkedjeanalys, som automatiskt korrelerar aviseringar i din miljö, baserat på cyberattackkedjeanalysen, så att du bättre ska förstå en attackkampanjs i dess helhet, var den startade och vilken typ av effekt den haft på dina resurser.



![Security Center-attackrekommendationer](media/security-center-intro/sc-attack-recommendation.png)

### <a name="integration-with-microsoft-defender-advanced-threat-protection"></a>Integrering med Microsoft Defender Avancerat skydd

Security Center inkluderar automatisk, inbyggd integrering med Microsoft Defender Avancerat skydd. Det innebär att Windows-och Linux-datorerna är helt integrerade med Security Center rekommendationer och utvärderingar utan någon konfiguration.

I kan Security Center du dessutom automatisera programkontrollsprinciper för servermiljöer. Med de anpassningsbara programkontrollerna i Security Center kan du placera program på listan över tillåtna från slutpunkt till slutpunkt för alla dina Windows-servrar. Du behöver varken skapa reglerna eller kontrollera överträdelser. Allt görs automatiskt.

### <a name="protect-paas"></a>Skydda PaaS

Security Center hjälpa dig att upptäcka hot mot dina Azure PaaS-tjänster. Du kan identifiera hot mot dina Azure-tjänster, inklusive Azure App Service, Azure SQL, Azure Storage Account, och andra datatjänster. Du kan också dra nytta av den interna integreringen med Microsoft Cloud App Securitys analys av användar- och entitetsbeteende (UEBA), och därigenom genomföra avvikelseidentifiering på dina Azure-aktivitetsloggar.

### <a name="block-brute-force-attacks"></a>Blockera råstyrkeattackerare

Security Center hjälper dig att begränsa exponeringen för råstyrkeattacker. Genom att minska åtkomsten till VM-portar med hjälp av just-in-time-VM-åtkomst kan du skydda ditt nätverk genom att förhindra onödig åtkomst. Du kan ange principer för säker åtkomst på valda portar enbart för behöriga användare, tillåtna käll-IP-adressintervall eller IP-adresser under en begränsad tidsperiod.

![Security Center-råstyrka](media/security-center-intro/sc-brute-force.png)

### <a name="protect-data-services"></a>Skydda datatjänster

Security Center innehåller funktioner som hjälper dig att utföra automatisk klassificering av dina data i Azure SQL. Du kan också få utvärderingar avseende potentiella säkerhetsrisker för Azure SQL- och lagringstjänster och rekommendationer för hur du löser dem.

### <a name="protect-iot-and-hybrid-cloud-workloads"></a>Skydda IoT-och hybrid moln arbets belastningar

Azure Security Center for IoT (Sakernas Internet) fören klar hybrid arbets belastnings skyddet genom att leverera enhetlig synlighet och kontroll, anpassningsbart hot skydd och intelligent skydd och svar på arbets belastningar som körs på Edge, lokalt, i Azure och i andra moln. Mer information finns i [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

## <a name="get-secure-faster"></a>Bli skyddad snabbare

Intern Azure-integration (inklusive Azure Policy och Azure Monitor loggar) kombinerat med sömlös integrering med andra Microsoft-säkerhetslösningar, till exempel Microsoft Cloud App Security och Windows Defender Avancerat skydd se till att din säkerhetslösning är omfattande och enkelt att publicera och distribuera.

Dessutom kan du utöka den fullständiga lösningen utöver Azure till arbetsbelastningar som körs i andra moln och i lokala datacentra.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Identifiera och publicera Azure-resurser automatiskt

Security Center ger sömlös inbyggd integrering med Azure och Azure-resurser. Det innebär att du kan samla ihop en fullständig säkerhetshistoria som rör Azure Policy och inbyggda principer i Security Center för alla dina Azure-resurser och se till att alltihop tillämpas automatiskt på nyidentifierade resurser när du skapar dem i Azure.

Omfattande logginsamling som innebär att loggar från både Windows och Linux balanseras i säkerhetsanalysmotorn och används för att skapa rekommendationer och aviseringar.

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Security Center måste du ha en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).

- Security Center kostnads fri pris nivå är aktive rad för alla dina aktuella Azure-prenumerationer när du går till Azure Security Center instrument panelen i Azure Portal för första gången, eller om den aktive ras via programmering via API. Om du vill dra nytta av funktionerna för avancerad säkerhets hantering och hot identifiering måste du uppgradera till standard pris nivån. Standard nivån kan provas kostnads fritt i 30 dagar. På [prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/) finns det mer information.

- Om du är redo att aktivera Security Center standard nu går du igenom anvisningarna genom att [publicera din Azure-prenumeration till Security Center standard](security-center-get-started.md) .