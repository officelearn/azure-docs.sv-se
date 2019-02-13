---
title: Skydd av kunddata i Azure
description: Den här artikeln tar upp hur Azure skyddar kundinformation.
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
ms.openlocfilehash: 49615dcb2f077d2e1d8b93a4bb900b435e4c87bf
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104506"
---
# <a name="azure-customer-data-protection"></a>Dataskydd för Azure-kund   
Åtkomst till kunddata för Microsofts drift- och supportpersonal nekas som standard. När åtkomst till kunddata beviljas ledarskap godkännande krävs och sedan åtkomst hanteras och noggrant loggas. Åtkomstkontroll kraven som upprättas av följande Azure-säkerhetsprincipen:

- Ingen åtkomst till kunddata, som standard.
- Inga användare eller administratör konton på kundens virtuella datorer (VM).
- Ge det lägsta privilegium som krävs för att slutföra aktiviteten. Granska och logga förfrågningar.

Azure supportpersonal har uppdrag unika företagets Active Directory-konton av Microsoft. Azure är beroende av Microsoft företagets Active Directory, hanteras av Microsoft Information Technology (MSIT) för att styra åtkomsten till viktiga informationssystem. Multifaktorautentisering krävs och åtkomst beviljas endast från säker konsoler.

Alla åtkomstförsök övervakas och kan visas via en grundläggande uppsättning rapporter.

## <a name="data-protection"></a>Dataskydd
Azure tillhandahåller kunder med stark datasäkerhet, både som standard som kunden alternativ.

**Dataavgränsning**: Azure är en multiklienttjänst, vilket innebär att flera kunden distributioner och virtuella datorer lagras på samma fysiska maskinvara. Azure använder logisk isolering för att särskilja varje kunds data från andra data. Uppdelning ger skalbarhet och ekonomiska fördelarna med tjänster med flera innehavare samtidigt som kunderna strikt förhindras från att komma åt varandras data.

**Skydda data i vila**: Kunderna ansvarar för att säkerställa att data som lagras i Azure är krypterad i enlighet med deras standarder. Azure erbjuder en mängd olika krypteringsfunktioner, vilket ger kunderna möjlighet att välja den lösning som bäst motsvarar deras behov. Azure Key Vault kan kunder enkelt kan behålla kontrollen över nycklar som används av molnprogram och molntjänster för att kryptera data. Azure Disk Encryption kan kunder kryptera virtuella datorer. Azure Storage Service Encryption gör det möjligt att kryptera alla data som placeras i en kunds lagringskonto.

**Skydda data under överföring**: Kunder kan aktivera kryptering för trafik mellan sina egna virtuella datorer och slutanvändarna. Azure skyddar data under överföring till eller från utanför komponenter och data i överföra internt, till exempel mellan två virtuella nätverk. Azure använder protokollet branschstandard Transport Layer Security (TLS) 1.2 eller senare med 2 048-bitars RSA/SHA256 krypteringsnycklar, enligt rekommendationer från CESG/NCSC för att kryptera kommunikationen mellan:

- Kunden och molnet.
- Internt mellan Azure-system och datacenter.

**Kryptering**: Kunder kan distribuera kryptering av data i lagring och under överföring som bästa praxis för att säkerställa sekretess och integritet hos data. Det är enkelt för kunder att konfigurera sin Azure-molntjänster för att använda SSL för att skydda kommunikationen från internet och även mellan sina Azure-baserade virtuella datorer.

**Dataredundans**: Microsoft hjälper till att säkerställa att data är skyddade om det finns en cyberattack eller fysisk skada på ett datacenter. Kunder kan välja:

- I landet lagring för efterlevnad eller latens överväganden.
- Tidsgräns för land lagring för säkerhets- eller disaster recovery-syften.

Data kan replikeras inom en vald geografiska område för redundans men kan inte överföras utanför den. Kunder har flera alternativ för att replikera data, inklusive antalet kopior och antal och plats replikering datacenter.

När du skapar ditt storage-konto, väljer du något av följande replikeringsalternativ:

- **Lokalt redundant lagring (LRS)**: Med lokalt redundant lagring underhålls tre kopior av dina data. LRS replikeras tre gånger på en anläggning i en region. LRS skyddar dina data mot normala maskinvarufel, men inte från ett fel på en enskild anläggning.
- **Zonredundant lagring (ZRS)**: Med zonredundant lagring underhålls tre kopior av dina data. ZRS replikeras tre gånger mellan två till tre anläggningar som ger högre hållbarhet än LRS. Replikeringen sker inom en enda region eller mellan två regioner. ZRS garanterar att dina data skyddas inom en enda region.
- **GEO-redundant lagring (GRS)**: Geo-redundant lagring är aktiverat för ditt lagringskonto som standard när du skapar det. Med GRS underhålls sex kopior av dina data. Med GRS replikeras dina data tre gånger på den primära regionen. Dina data replikeras också tre gånger i en sekundär region hundratals mil bort från den primära regionen, vilket ger den högsta nivån av hållbarhet. I händelse av ett fel i den primära regionen kommer Azure Storage som redundansväxlar till den sekundära regionen. GRS garanterar att dina data är beständiga i två olika områden.

**Datadestruktion**: När kunder ta bort data eller lämna Azure, följer Microsoft strikta standarder för att skriva över lagringsresurser innan deras återanvändning, samt fysiska destruktion av inaktiverade maskinvara. Microsoft utför en fullständig borttagning av data på kundkrav och avtals uppsägning.

## <a name="customer-data-ownership"></a>Kunden dataägarskap
Granska inte Microsoft, godkänna eller övervaka program som kunder distribuerar till Azure. Dessutom vet Microsoft inte vad typ av data kunder väljer att lagra i Azure. Microsoft gör inte anspråk på ägarskapet över den kundinformation som har angetts i Azure.

## <a name="records-management"></a>Hantering av arkivhandlingar
Azure har upprättat krav på interna poster-datalagring för backend-data. Kunderna ansvarar för att identifiera sina egna krav, registrera kvarhållning. Kunderna ansvarar för poster som lagras i Azure för extrahering av sina data och behålla sitt innehåll utanför Azure under en kunden angivna period.

Azure kan kunder exportera data och granska rapporter från produkten. Exporterna sparas lokalt om du vill behålla informationen för en kunddefinierad kvarhållning tidsperiod.

## <a name="electronic-discovery-e-discovery"></a>Elektronisk identifiering (e-discovery)
Azure-kunder ansvarar för att uppfylla krav för e-discovery i deras användning av Azure-tjänster. Om Azure-kunder måste bevara deras kunduppgifter, kan de exportera och spara data lokalt. Kunder kan också begära export av sina data från Azures kundsupport-avdelningen. Förutom så att kunder kan exportera sina data, utför omfattande loggning och övervakning internt i Azure.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
