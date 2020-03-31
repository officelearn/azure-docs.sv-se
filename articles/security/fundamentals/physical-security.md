---
title: Fysisk säkerhet för Azure-datacenter – Microsoft Azure | Microsoft-dokument
description: I artikeln beskrivs vad Microsoft gör för att skydda Azure-datacenter, inklusive fysisk infrastruktur, säkerhet och efterlevnadserbjudanden.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726733"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-anläggningar, lokaler och fysisk säkerhet
I den här artikeln beskrivs vad Microsoft gör för att skydda Azure-infrastrukturen.

## <a name="datacenter-infrastructure"></a>Datacenterinfrastruktur
Azure består av en [globalt distribuerad datacenterinfrastruktur](https://azure.microsoft.com/global-infrastructure/)som stöder tusentals onlinetjänster och som omfattar mer än 100 mycket säkra anläggningar över hela världen.

Infrastrukturen är utformad för att föra program närmare användare runt om i världen, bevara datahemvist och erbjuda omfattande efterlevnads- och återhämtningsalternativ för kunder. Azure har 52 regioner över hela världen och är tillgängligt i 140 länder/regioner.

En region är en uppsättning datacenter som är sammankopplade via ett massivt och motståndskraftigt nätverk. Nätverket innehåller innehållsdistribution, belastningsutjämning, redundans och kryptering som standard. Med fler globala regioner än någon annan molnleverantör ger Azure dig flexibiliteten att distribuera program där du behöver dem.

Azure-regioner är ordnade i geografiska områden. En Azure-geografi säkerställer att datahemvist, suveränitet, efterlevnad och återhämtningskrav uppfylls inom geografiska gränser.

Geografiska områden hjälper kunder med specifika behov kring dataplacering och regelefterlevnad att hålla sina data och program nära. Geografiska områden är feltoleranta för att motstå fullständigt regionfel, genom att de är anslutning till den dedikerade nätverksinfrastrukturen med hög kapacitet.

Tillgänglighetszoner är fysiskt separata platser inom en Azure-region. Varje tillgänglighetszon består av ett eller fler datacenter som är utrustade med oberoende ström, kylning och nätverkstjänster. Med tillgänglighetszoner kan du köra verksamhetskritiska program med hög tillgänglighet och replikering med låg latens.

Följande bild visar hur Azures globala infrastruktur par region och tillgänglighet zoner inom samma data hemvist gräns för hög tillgänglighet, haveriberedskap och säkerhetskopiering.

![Diagram som visar gränsen för datahemvist](./media/physical-security/data-residency-boundary.png)

Geografiskt distribuerade datacenter gör det möjligt för Microsoft att vara nära kunder, minska nätverksfördröjningen och möjliggöra geos redundant säkerhetskopiering och redundans.

## <a name="physical-security"></a>Fysisk säkerhet
Microsoft designar, bygger och driver datacenter på ett sätt som strikt styr fysisk åtkomst till de områden där dina data lagras. Microsoft förstår vikten av att skydda dina data och strävar efter att skydda datacenter som innehåller dina data. Vi har en hel avdelning på Microsoft som ägnar sig åt att utforma, bygga och driva de fysiska anläggningar som stöder Azure. Det här teamet är investerat i att upprätthålla den senaste fysiska säkerheten.

Microsoft använder en metod i flera lager för fysisk säkerhet för att minska risken för att obehöriga användare får fysisk åtkomst till data och datacenterresurserna. Datacenter som hanteras av Microsoft har omfattande lager av skydd: åtkomstgodkännande vid anläggningens omkrets, vid byggnadens omkrets, inne i byggnaden och på datacentergolvet. Lager av fysisk säkerhet är:

- **Begäran om åtkomst och godkännande.** Du måste begära åtkomst innan du anländer till datacentret. Du måste ange en giltig affärsmotivering för ditt besök, till exempel efterlevnads- eller granskningsändamål. Alla begäranden godkänns på behovsbasis av Microsoft-anställda. En behovsgrund hjälper till att hålla antalet personer som behövs för att slutföra en uppgift i datacenter till ett minimum. När Microsoft har beviljat behörighet har en person endast åtkomst till det diskreta området i det datacenter som krävs, baserat på den godkända affärsmotiveringen. Behörigheter är begränsade till en viss tidsperiod och upphör sedan att gälla.

- **Anläggningens omkrets.** När du anländer till ett datacenter måste du gå igenom en väldefinierad åtkomstpunkt. Typiskt, höga staket av stål och betong omfattar varje tum av omkretsen. Det finns kameror runt datacenter, med ett säkerhetsteam som övervakar deras videor hela tiden.

- **Byggnadens ingång.** Datacentrets entré är bemannad med professionella säkerhetstjänstemän som har genomgått rigorös utbildning och bakgrundskontroller. Dessa säkerhetstjänstemän patrullerar också rutinmässigt datacentret och övervakar videor av kameror inuti datacentret hela tiden.

- **Inne i byggnaden.** När du har angett byggnaden måste du skicka tvåfaktorsautentisering med biometri för att fortsätta röra dig genom datacentret. Om din identitet valideras kan du bara ange den del av datacentret som du har godkänt åtkomst till. Du kan stanna där endast under den tid som godkänts.

- **Datacenter golv.** Du får bara komma upp på golvet som du är godkänd för att ange. Du måste passera en hel kropp metall detektering screening. För att minska risken för att obehöriga data kommer in i eller lämnar datacentret utan vår vetskap kan endast godkända enheter ta sig in i datacentrets golv. Dessutom övervakar videokameror framsidan och baksidan av varje serverrack. När du lämnar datacentergolvet måste du återigen passera genom identifiering av hela kroppen. För att lämna datacentret måste du gå igenom en extra säkerhetsgenomsökning.

Microsoft kräver att besökare ger upp märken vid avresa från alla Microsoft-anläggningar.

## <a name="physical-security-reviews"></a>Fysiska säkerhetsgranskningar
Med jämna mellanrum genomför vi fysiska säkerhetsgranskningar av anläggningarna för att säkerställa att datacentermen korrekt uppfyller Azures säkerhetskrav. Datacentervärdtjänstleverantörens personal tillhandahåller inte Azure-tjänsthantering. Personal kan inte logga in på Azure-system och har inte fysisk åtkomst till Azure-samlokaliseringsrummet och burarna.

## <a name="data-bearing-devices"></a>Datalageranordningar
Microsoft använder metodtips och en lösning som uppfyller [NIST 800-88.](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01) För hårddiskar som inte kan torkas använder vi en förstörelseprocess som förstör den och gör det omöjligt att återställa information. Denna förstörelse process kan vara att sönderdelas, strimla, pulverisera, eller förbränna. Vi bestämmer vilka avyttringsmetoder som ska ske beroende på tillgångstyp. Vi har register över förstörelsen.  

## <a name="equipment-disposal"></a>Avfallshantering av utrustning
När ett system är uttjänt följer Microsofts operativa personal rigorösa datahanterings- och maskinvaruhanteringsprocedurer för att säkerställa att maskinvara som innehåller dina data inte görs tillgänglig för icke betrodda parter. Vi använder en säker raderingsmetod för hårddiskar som stöder den. För hårddiskar som inte kan torkas använder vi en förstörelseprocess som förstör enheten och gör det omöjligt att återställa information. Denna förstörelse process kan vara att sönderdelas, strimla, pulverisera, eller förbränna. Vi bestämmer vilka avyttringsmetoder som ska ske beroende på tillgångstyp. Vi har register över förstörelsen. Alla Azure-tjänster använder godkända medielagrings- och hanteringstjänster för lagring.

## <a name="compliance"></a>Efterlevnad
Vi utformar och hanterar Azure-infrastrukturen för att uppfylla en bred uppsättning internationella och branschspecifika efterlevnadsstandarder, till exempel ISO 27001, HIPAA, FedRAMP, SOC 1 och SOC 2. Vi uppfyller även lands- eller regionspecifika standarder, inklusive Australien IRAP, UK G-Cloud och Singapore MTCS. Rigorösa tredjepartsrevisioner, såsom de som görs av British Standards Institute, verifierar efterlevnaden av de strikta säkerhetskontrollerna av dessa standarder.

En fullständig lista över efterlevnadsstandarder som Azure följer finns i [efterlevnadserbjudandena](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)


