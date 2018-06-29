---
title: Azure verksamhet, lokal och fysisk säkerhet | Microsoft Docs
description: Den här artikeln innehåller den Azure-datacenter, inklusive fysisk infrastruktur, säkerhet och efterlevnad erbjudanden.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102468"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure verksamhet, lokal och fysisk säkerhet
Microsofts molntjänster består av en [globalt distribuerade datacenter infrastruktur](https://azure.microsoft.com/global-infrastructure/) stöder tusentals onlinetjänster och utsträckning mer än 100 mycket säkra verksamhet över hela världen.

Infrastrukturen som är utformat för att sätta program närmare till användare i världen, bevara data land och ger omfattande efterlevnad och återhämtning alternativ för kunder. Azure har 52 regioner över hela världen och är tillgänglig i 140 länder.

En region är en uppsättning datacenter som är sammankopplade via en omfattande och flexibel nätverk. Nätverket innehåller innehållsdistribution, belastningsutjämning, redundans och kryptering som standard. Azure har fler globala regioner än någon annan molnleverantör och ger kunderna möjligheten att distribuera program där det behövs.

De geografiska Azure-regionerna är ordnade i områden. Ett geografiskt Azure-område garanterar att krav på dataplacering, landsbaserad placering, efterlevnad och elasticitet stöds inom geografiska gränser.

Geografiska områden hjälper kunder med specifika behov av dataplacering och efterlevnad att hålla sina data och program nära. Geografiska områden är feltolerant klara fullständig region fel via anslutningen till dedikerade hög kapacitet nätverksinfrastrukturen.

Tillgänglighetszoner är fysiskt separata platser inom en Azure-region. Varje tillgänglighetszon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. Med Availability Zones kan kunderna köra verksamhetskritiska program med hög tillgänglighet och replikering med låg latens.

Följande bild visar hur Azure-globala infrastrukturen par region och tillgänglighet zoner inom samma data land gräns för hög tillgänglighet, katastrofåterställning och säkerhetskopiering.

![Data land gräns][1]

En stor geografiskt distribuerade storleken på Datacenter kan Microsoft vara nära kunder för att minska nätverkslatensen och möjliggöra geo-redundant säkerhetskopia och växling vid fel.

## <a name="physical-security"></a>Fysisk säkerhet
Microsoft utformar, bygger och fungerar Datacenter på ett sätt som styr absolut fysisk åtkomst till de områden där kundinformation lagras. Microsoft förstår vikten av att skydda kundens data och arbetar med att hjälpa skydda datacenter som innehåller dina data. Vi har en hela delning på Microsoft att utforma, utveckling och drift av lokaler som stöder Azure. Det här teamet investerat för att behålla den senaste fysisk säkerhet.

Microsoft tar en överlappande tillvägagångssättet till fysisk säkerhet för att minska risken för att obehöriga användare får tillgång till data och datacenterresurser. Datacenter som hanteras av Microsoft har omfattande skyddslager: komma åt godkännande på den anläggning perimeter på i byggnaden perimeternätverk, i byggnaden och på datacenter våning. Lager av fysisk säkerhet är:

- Begäran om åtkomst och godkännande – du måste begära åtkomst innan anländer till datacentret. Du måste ange en giltig affärsjustering för ditt besök, till exempel kompatibilitet eller granskningsändamål. Alla begäranden som är godkända på grundval av behovet av att åtkomst av Microsofts anställda. Bas behovet av att åtkomst gör att personer som behövs för att slutföra en aktivitet i datacenter till minst antalet. När du har beviljats behörighet att har en enskild endast åtkomst till området diskreta datacentret, baserat på godkända affärsjustering. Behörigheter som är begränsade till en viss tidsperiod och upphör efter den tillåtna tidsperioden.

- Lokals perimeter - när du kommer till ett datacenter, är du behöver gå igenom en väldefinierad åtkomstpunkt. Hög avgränsningstecken av stål och konkret omfattar vanligtvis tum i perimeternätverket. Det finns kameror runt datacenter med en säkerhetsteam övervaka deras videor 24/7 och 365 dagar på året.

- Skapa ingångs - datacenter ingången får du hjälp med professional säkerhet polis som har genomgått rigorösa utbildnings- och kontroller. Dessa säkerhet polis patrullera även regelbundet datacentret medan de också övervaka videor kameror i ett datacenter 24/7 och 365 dagar per år.

- När du har angett i byggnaden i Skapa - måste du överföra tvåfaktorsautentisering med biometrik fortsätta flytta i datacentret. Om din identitet har verifierats, kan du ange del av det datacenter som du har godkänt åtkomst till. Du kan hålla det endast för den tidsperiod som godkänt.

- Datacenter våning – du tillåts endast i plan som du har godkänt för att ange. Du måste skicka en fullständig brödtext metal identifiering kontroll. För att minska risken för obehörig lämnar datacenter utan våra vetskap eller kan bara godkända enheter göra deras sätt datacenter våning. Dessutom video kameror övervakaren fram och tillbaka på varje server rack. Fullständig text metal identifiering kontrollen upprepas när du avslutar datacenter våning. Om du vill lämna datacenter, måste du passera en genomsökning för ytterligare säkerhet.

Besökare krävs att avstå från märken efter Microsoft anläggningar.

## <a name="physical-security-reviews"></a>Fysisk säkerhet granskar
Fysisk säkerhet granskningar i anläggningen utförs med jämna mellanrum för att säkerställa Datacenter korrekt adress säkerhetskrav för Microsoft Azure. Datacenter värd providern personal tillhandahåller inte Microsoft Azure-tjänsthantering. Personal har inte inloggningsåtkomst till Azure-system eller fysisk åtkomst till Azure samplacering plats och burar.

## <a name="data-bearing-devices"></a>Data med enheter
Microsoft använder den bästa praxis procedurer och en Radera lösning som är [NIST 800-88 kompatibla](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). En destruktion process som förstör den och återger återställning av information omöjligt används för hårddiskar som inte kan rensas. Destruktion process kan vara splittras, hjälp, pulverisera dem eller förbränna. Lämpligt sätt avyttra bestäms av tillgångstyp. Posterna för destruktion bevaras.  

## <a name="equipment-disposal"></a>Utrustning avyttring
Microsoft Azure implementerar denna princip dina kunders räkning. När ett system slutet av-livscykeln Följ operativa Microsoft-Personal rigorösa datahantering och maskinvara avyttring procedurer för att garantera att maskinvara som innehåller kundinformation inte görs tillgängligt för obetrodda parter. En säker radering metod följs (via hårddisken inbyggd programvara) för enheter som stöder den. En destruktion process som förstör den och återger återställning av information omöjligt används för hårddiskar som inte kan rensas. Destruktion process kan vara splittras, hjälp, pulverisera dem eller förbränna. Lämpligt sätt avyttra bestäms av tillgångstyp. Posterna för destruktion bevaras. Alla Microsoft Azure-tjänster använder godkända media lagring och avyttring hanteringstjänster.

## <a name="compliance"></a>Efterlevnad
Azure-infrastrukturen är utformad och hanteras för att uppfylla en bred uppsättning internationella och branschspecifika standarder, till exempel ISO 27001, HIPAA, FedRAMP, SOC 1 och SOC 2. Landsspecifika standarder också är uppfyllda, inklusive Australien IRAP, Storbritannien G-moln och Singapore MTCS. Omfattande granskningar utförda av oberoende part, till exempel British Standards Institute, kontrollerar att Azure har infört de stränga säkerhetskontrollerna i dessa standarder.

Finns det [kompatibilitet erbjudanden](https://www.microsoft.com/trustcenter/compliance/complianceofferings) för en fullständig lista över efterlevnadsstandarder följt av Azure.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
