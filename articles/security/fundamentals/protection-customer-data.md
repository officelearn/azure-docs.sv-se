---
title: Skydd av kunddata i Azure
description: Den här artikeln behandlar hur Azure skyddar kunddata.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726686"
---
# <a name="azure-customer-data-protection"></a>Azure-kunddataskydd   
Åtkomst till kunddata av Microsofts verksamhet och supportpersonal nekas som standard. När åtkomst till kunddata beviljas krävs ledarskapsgodkännande och sedan hanteras och loggas åtkomsten noggrant. Kraven för åtkomstkontroll har fastställts av följande Azure Security Policy:

- Ingen åtkomst till kunddata, som standard.
- Inga användar- eller administratörskonton på virtuella kunddatorer ( VMs).
- Bevilja det minsta privilegium som krävs för att slutföra uppgiften. gransknings- och loggåtkomstbegäranden.

Azure-supportpersonal tilldelas unika Active Directory-företagskonton av Microsoft. Azure förlitar sig på Microsoft corporate Active Directory, som hanteras av Microsoft Information Technology (MSIT), för att styra åtkomsten till viktiga informationssystem. Multifaktorautentisering krävs och åtkomst beviljas endast från säkra konsoler.

Alla åtkomstförsök övervakas och kan visas via en grundläggande uppsättning rapporter.

## <a name="data-protection"></a>Dataskydd
Azure ger kunderna stark datasäkerhet, både som standard och som kundalternativ.

**Datasegregering**: Azure är en tjänst med flera innehavare, vilket innebär att flera kunddistributioner och virtuella datorer lagras på samma fysiska maskinvara. Azure använder logisk isolering för att särskilja varje kunds data från andras data. Segregation ger omfattningen och de ekonomiska fördelarna med tjänster med flera innehavare samtidigt som de noggrant hindrar kunder från att komma åt varandras data.

**Dataskydd i vila**: Kunderna ansvarar för att data som lagras i Azure krypteras i enlighet med deras standarder. Azure erbjuder ett brett utbud av krypteringsfunktioner, vilket ger kunderna flexibiliteten att välja den lösning som bäst uppfyller deras behov. Azure Key Vault hjälper kunderna att enkelt behålla kontrollen över nycklar som används av molnprogram och tjänster för att kryptera data. Azure Disk Encryption gör det möjligt för kunder att kryptera virtuella datorer. Azure Storage Service Encryption gör det möjligt att kryptera alla data som placeras i en kunds lagringskonto.

**Dataskydd under överföring:** Kunder kan aktivera kryptering för trafik mellan egna virtuella datorer och slutanvändare. Azure skyddar data under överföring till eller från externa komponenter och data som överförs internt, till exempel mellan två virtuella nätverk. Azure använder TLS (Transport Layer Security) 1.2 eller senare protokoll (Transport Layer Security) som är av branschstandard med 2 048 -bitars RSA/SHA256-krypteringsnycklar, enligt rekommendation av CESG/NCSC, för att kryptera kommunikation mellan:

- Kunden och molnet.
- Internt mellan Azure-system och datacenter.

**Kryptering**: Kryptering av data i lagring och under överföring kan distribueras av kunder som en bästa praxis för att säkerställa sekretess och integritet för data. Det är enkelt för kunder att konfigurera sina Azure-molntjänster för att använda SSL för att skydda kommunikationen från internet och till och med mellan deras virtuella azure-värddatorn.

**Dataredundans**: Microsoft hjälper till att säkerställa att data skyddas om det finns en cyberattack eller fysiska skador på ett datacenter. Kunder kan välja:

- Lagring i land/region för efterlevnad eller svarstid.
- Lagring utanför landet/utanför regionen för säkerhets- eller haveriberedskap.

Data kan replikeras inom ett valt geografiskt område för redundans men kan inte överföras utanför det. Kunder har flera alternativ för att replikera data, inklusive antalet kopior och antalet och platsen för replikeringsdata.

När du skapar ditt lagringskonto väljer du något av följande replikeringsalternativ:

- **Lokalt redundant lagring (LRS)**: Lokalt redundant lagring underhåller tre kopior av dina data. LRS replikeras tre gånger på en anläggning i en region. LRS skyddar dina data från normala maskinvarufel, men inte från ett fel på en enda anläggning.
- **Zonupptraktad lagring (ZRS):** Zonupptraktad lagring underhåller tre kopior av dina data. ZRS replikeras tre gånger över två till tre anläggningar för att ge högre hållbarhet än LRS. Replikering sker inom en enda region eller mellan två regioner. ZRS hjälper till att säkerställa att dina data är hållbara inom en enda region.
- **GEO-redundant lagring (GRS)**: Geo-redundant lagring är aktiverad för ditt lagringskonto som standard när du skapar det. Med GRS underhålls sex kopior av dina data. Med GRS replikeras dina data tre gånger inom den primära regionen. Dina data replikeras också tre gånger i en sekundär region hundratals miles från den primära regionen, vilket ger den högsta nivån av hållbarhet. I händelse av ett fel i den primära regionen växlar Azure Storage över till den sekundära regionen. GRS hjälper till att säkerställa att dina data är hållbara i två separata regioner.

**Dataförstöring**: När kunder tar bort data eller lämnar Azure följer Microsoft strikta standarder för att skriva över lagringsresurser innan de återanvänds, samt fysisk förstörelse av inaktiverad maskinvara. Microsoft utför en fullständig radering av data på kundens begäran och vid uppsägning av avtal.

## <a name="customer-data-ownership"></a>Ägande av kunddata
Microsoft granskar inte, godkänner eller övervakar inte program som kunder distribuerar till Azure. Dessutom vet Microsoft inte vilken typ av data kunder väljer att lagra i Azure. Microsoft gör inte anspråk på dataägo över kundinformation som har angetts i Azure.

## <a name="records-management"></a>Hantering av arkivhandlingar
Azure har fastställt interna registerkvarhållningskrav för backend-data. Kunderna ansvarar för att identifiera sina egna krav för lagring av register. För poster som lagras i Azure ansvarar kunderna för att extrahera sina data och behålla sitt innehåll utanför Azure för en kundspecificerad kvarhållningsperiod.

Med Azure kan kunder exportera data och granskningsrapporter från produkten. Exporten sparas lokalt för att behålla informationen för en kunddefinierad kvarhållningsperiod.

## <a name="electronic-discovery-e-discovery"></a>Elektronisk upptäckt (e-upptäckt)
Azure-kunder ansvarar för att uppfylla e-identifieringskrav i sin användning av Azure-tjänster. Om Azure-kunder måste bevara sina kunddata kan de exportera och spara data lokalt. Dessutom kan kunder begära export av sina data från Azure Customer Support-avdelningen. Förutom att tillåta kunder att exportera sina data utför Azure omfattande loggning och övervakning internt.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
