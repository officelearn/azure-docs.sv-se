---
title: Azure anläggningar, plats och fysisk säkerhet | Microsoft Docs
description: Artikeln beskriver den Azure-datacenter, inklusive fysisk infrastruktur, säkerhet och efterlevnadserbjudanden.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586800"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure anläggningar, plats och fysisk säkerhet
Azure består av en [infrastruktur med globalt distribuerade datacenter](https://azure.microsoft.com/global-infrastructure/), stöd för tusentals onlinetjänster och utsträckning anläggningar för fler än 100 med hög säkerhet världen över.

Infrastrukturen som är utformad för att föra program närmare användare världen över, bevarar dataplacering och omfattande efterlevnad och elasticitet lagringsalternativ för kunder. Azure har 52 regioner över hela världen och är tillgängligt i 140 länder/regioner.

En region är en uppsättning datacenter som är sammankopplade via ett omfattande och flexibel nätverk. Nätverket innehåller innehållsdistribution, belastningsutjämning, redundans och kryptering som standard. Fler globala regioner än någon annan molnleverantör ger Azure dig flexibiliteten att distribuera program där du behöver.

De geografiska Azure-regionerna är ordnade i områden. Ett geografiskt Azure-område garanterar att krav på dataplacering, landsbaserad placering, efterlevnad och elasticitet stöds inom geografiska gränser.

Geografiska områden hjälper kunder med specifika behov av dataplacering och efterlevnad att hålla sina data och program nära. Geografiska områden är feltoleranta till fullständiga Regionsfel, via anslutningen till den dedikerade nätverksinfrastrukturen med hög kapacitet.

Tillgänglighetszoner är fysiskt separata platser inom en Azure-region. Varje tillgänglighetszon består av en eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverkstjänster. Tillgänglighetszoner låter dig köra verksamhetskritiska program med hög tillgänglighet och låg latens replikering.

Följande bild visar hur Azure global infrastruktur kombinationer av regioner och tillgänglighet zoner inom samma dataplaceringsgräns för hög tillgänglighet, haveriberedskap och säkerhetskopiering.

![Diagram som visar dataplaceringsgräns][1]

Geografiskt fördelade datacenter hjälper Microsoft att vara nära kunder och Sänk svarstiderna i nätverket och tillåter geo-redundant säkerhetskopia och redundans.

## <a name="physical-security"></a>Fysisk säkerhet
Microsoft utformar, bygger och fungerar Datacenter på ett sätt som styr strikt fysisk åtkomst till de områden där dina data lagras. Microsoft förstår vikten av att skydda dina data och hjälper dig skydda det datacenter som innehåller dina data att. Vi har en hel delning på Microsoft som är tillägnad utforma, bygga och driva lokaler som stöd för Azure. Det här laget har investerat i underhålla den senaste fysisk säkerhet.

Microsoft tar ett lager till fysisk säkerhet, och minska risken för obehöriga användare får tillgång till data och datacenterresurser. Datacenter som hanteras av Microsoft har omfattande skyddslager: åtkomst till godkännande på den anläggning perimetern, på den byggnad perimetern, i byggnaden och på datacenter våning. Lager av fysisk säkerhet är:

- **Åtkomstbegäran och godkännande.** Du måste begära åtkomst innan anländer till datacentret. Du måste ange en giltig motivering för webbplatsen, till exempel efterlevnad eller granskningsändamål. Alla förfrågningar har godkänts på basis av behovet att nå av Microsoft-anställda. Behovet av att nå bas hjälper till att hålla nere antalet personer som krävs för att slutföra en uppgift i datacenter till minst. När Microsoft ger behörighet, en enskild endast har åtkomst till området diskreta för det datacenter som krävs, baserat på godkända affärsrelaterad motivering. Behörigheter är begränsade till en viss tidsperiod och sedan upphör att gälla.

- **Anläggnings perimeternätverket.** När du kommer till ett datacenter, måste så du gå igenom en väldefinierad åtkomstpunkt. Hög avgränsningstecken som gjorts av stål och konkret omfattar vanligtvis tum i perimeternätverket. Det finns kameror kring datacenter, med en säkerhetsteam för övervakning av deras videor hela tiden.

- **Skapa ingång.** Datacenter-ingång får du hjälp med professionella security införlivande som har genomgått rigorös kontroller för utbildning och bakgrund. Dessa också regelbundet patrullera datacentret, och övervaka videofilmer med kameror inifrån datacentret hela tiden.

- **I byggnaden.** När du har angett byggnaden måste du ange tvåfaktorsautentisering med biometrik fortsätta flytta i datacentret. Om din identitet har verifierats kan välja du att endast del av det datacenter som du har godkänt åtkomst till. Du kan hålla det endast för tiden som godkänts.

- **Datacenter våning.** Du har endast tillåtelse till golv att du har godkänt för att ange. Du behöver för att skicka en fullständig brödtext operativsystem identifiering kontroll. För att minska risken för obehörig lämnar datacentret utan vår kunskaper eller kan bara godkända enheter hamna i datacenter våning. Dessutom video kameror övervakaren fram och tillbaka på varje server rack. När du avslutar datacenter våning måste dig igen passera fullständig brödtext operativsystem identifiering av kontrollen. Om du vill lämna datacenter, måste du passera en skanning för ytterligare säkerhet.

Microsoft kräver besökare att avstå från Aktivitetsikoner efter alla Microsoft-kontor.

## <a name="physical-security-reviews"></a>Fysisk säkerhet går igenom
Med jämna mellanrum, genomför vi fysisk säkerhet granskningar av anläggningar, så datacentren korrekt adress Azure säkerhetskrav. Datacenter som värd-providern personalen tillhandahåller inte Azure service management. Personal kan inte logga in på Azure-system och behöver inte fysisk åtkomst till Azure samplacering utrymme och burar.

## <a name="data-bearing-devices"></a>Data med enheter
Microsoft använder den bästa praxis procedurer och en Radera lösning som är [NIST 800-88 kompatibla](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Vi använder en destruktion process som förstör det och återger det omöjligt för återställning av information för hårddiskar som inte kan rensas. Den här processen destruktion kan vara att splittras, hjälp, pulverisera dem eller förbränning. Vi fastställer insamling av avyttring beroende på resurstyp. Vi behåller poster i destruktion.  

## <a name="equipment-disposal"></a>Borttagning av utrustning
När ett system slutet livscykel Följ Microsoft operational personal rigorösa datahantering och maskinvara avyttring procedurer för att garantera att maskinvara som innehåller dina data inte är tillgängliga till inte är betrodd part. Vi använder en metod för säker radering för enheter som har stöd för den. Vi använder en destruktion process som förstör enheten och återger det omöjligt för återställning av information för hårddiskar som inte kan rensas. Den här processen destruktion kan vara att splittras, hjälp, pulverisera dem eller förbränning. Vi fastställer insamling av avyttring beroende på resurstyp. Vi behåller poster i destruktion. Alla Azure-tjänster använder godkända media lagring och avyttring management services.

## <a name="compliance"></a>Efterlevnad
Vi skapa och hantera Azure-infrastrukturen för att uppfylla en rad olika internationella och branschspecifika efterlevnadsstandarder, till exempel ISO 27001, HIPAA, FedRAMP, SOC 1 och SOC 2. Vi uppfyller även landsspecifika standarder inklusive IRAP i Australien, G-Cloud i Storbritannien och MTCS i Singapore. Noggranna granskningar från tredje part, till exempel de som görs av British Standards Institute, kontrollerar du infört de stränga säkerhetskontrollerna dessa standarder säkerhetsbehov.

En fullständig lista över efterlevnadsstandarder som Azure följer finns i den [efterlevnadserbjudanden](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
