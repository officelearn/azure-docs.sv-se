---
title: Skydd av kund information i Azure
description: Den här artikeln handlar om hur Azure skyddar kund information.
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726686"
---
# <a name="azure-customer-data-protection"></a>Data skydd för Azure-kunder   
Till gång till kund information av Microsoft-åtgärder och support personal nekas som standard. När åtkomst till kunddata beviljas krävs ledarskaps godkännande och åtkomsten hanteras noga och loggas. Kraven för åtkomst kontroll upprättas av följande Azure-säkerhets princip:

- Ingen åtkomst till kund information, som standard.
- Inga användar-eller administratörs konton på kundens virtuella datorer (VM).
- Ge den minsta behörighet som krävs för att slutföra uppgiften. granska och logga åtkomst begär Anden.

Support personal för Azure tilldelas unika företags Active Directory konton av Microsoft. Azure är beroende av Microsofts företags Active Directory, som hanteras av Microsoft Information Technology (MSIT), för att kontrol lera åtkomsten till viktiga informations system. Multi-Factor Authentication krävs och åtkomst beviljas endast från säkra konsoler.

Alla åtkomst försök övervakas och kan visas via en enkel uppsättning rapporter.

## <a name="data-protection"></a>Dataskydd
Azure ger kunderna stark data säkerhet, både som standard och som kund alternativ.

**Data uppdelning**: Azure är en tjänst för flera innehavare, vilket innebär att flera kund distributioner och virtuella datorer lagras på samma fysiska maskin vara. Azure använder logisk isolering för att åtskilja varje kunds data från andra data. Åtskillnad ger skalbarhet och ekonomiska fördelar med tjänster för flera innehavare samtidigt som kunder kan komma åt en annans data.

**Data skydd i vila**: Kunderna är ansvariga för att säkerställa att data som lagras i Azure krypteras i enlighet med deras standarder. Azure erbjuder en mängd olika krypterings funktioner, vilket ger kunderna möjlighet att välja den lösning som bäst uppfyller deras behov. Azure Key Vault hjälper kunderna att enkelt upprätthålla kontrollen över nycklar som används av moln program och-tjänster för att kryptera data. Azure Disk Encryption gör det möjligt för kunderna att kryptera virtuella datorer. Med Azure Storage tjänst kryptering kan du kryptera alla data som placeras i en kunds lagrings konto.

**Data skydd under överföring**: Kunder kan aktivera kryptering för trafik mellan sina egna virtuella datorer och slutanvändare. Azure skyddar data i överföring till eller från externa komponenter och data som överförs internt, till exempel mellan två virtuella nätverk. Azure använder Transport Layer Security-protokollet (TLS) 1,2 eller senare med 2 048-bitars RSA/SHA256 krypterings nycklar, som rekommenderas av CESG/NCSC, för att kryptera kommunikation mellan:

- Kunden och molnet.
- Internt mellan Azure-system och data Center.

**Kryptering**: Kryptering av data i lagring och i överföring kan distribueras av kunder som bästa praxis för att säkerställa konfidentialitet och integritet för data. Det är enkelt för kunderna att konfigurera sina Azure-Molntjänster att använda SSL för att skydda kommunikation från Internet och även mellan sina virtuella datorer i Azure.

**Dataredundans**: Microsoft hjälper till att säkerställa att data skyddas om det finns en cyberattack eller fysisk skada på ett Data Center. Kunder kan välja mellan:

- Lagring i land/region för efterlevnad eller svars tids aspekter.
- Out-of-region-lagring för säkerhets-eller katastrof återställning.

Data kan replikeras inom ett valt geografiskt område för redundans, men kan inte skickas utanför det. Kunder har flera alternativ för att replikera data, inklusive antalet kopior och antalet och platsen för data Center för replikering.

När du skapar ditt lagrings konto väljer du något av följande replikeringsalternativ:

- **Lokalt Redundant lagring (LRS)** : Med lokalt redundant lagring underhålls tre kopior av dina data. LRS replikeras tre gånger på en anläggning i en region. LRS skyddar dina data mot normala maskin varu problem, men inte från ett problem med en enda funktion.
- **Zon-redundant lagring (ZRS)** : Med zonredundant lagring underhålls tre kopior av dina data. ZRS replikeras tre gånger mellan två och tre anläggningar för att ge högre hållbarhet än LRS. Replikering sker inom en enda region eller mellan två regioner. ZRS hjälper till att säkerställa att dina data är varaktiga inom en enda region.
- **Geo-redundant lagring (GRS)** : Geo-redundant lagring är aktiverat för ditt lagringskonto som standard när du skapar det. Med GRS underhålls sex kopior av dina data. Med GRS replikeras dina data tre gånger inom den primära regionen. Dina data replikeras också tre gånger i en sekundär region hundratals mil bort från den primära regionen, vilket ger den högsta nivån av hållbarhet. Om det skulle uppstå ett fel i den primära regionen Azure Storage växlar över till den sekundära regionen. GRS hjälper till att säkerställa att dina data är beständiga i två olika regioner.

**Data förstörelse**: När kunderna tar bort data eller lämnar Azure, följer Microsofts strikta standarder för att skriva över lagrings resurser innan de återanvänds, samt fysisk förstörelse av inaktive rad maskin vara. Microsoft genomför en fullständig borttagning av data för kund förfrågan och avslutning av kontrakt.

## <a name="customer-data-ownership"></a>Ägarskap för kund data
Microsoft kontrollerar, godkänner eller övervakar inte program som kunderna distribuerar till Azure. Dessutom vet Microsoft inte vilken typ av data kunder som väljer att lagra i Azure. Microsoft antecknar inte data ägarskap över kund informationen som anges i Azure.

## <a name="records-management"></a>Hantering av poster
Azure har upprättat interna poster – bevarande krav för Server dels data. Kunderna är ansvariga för att identifiera sina egna krav för post lagring. För poster som lagras i Azure ansvarar kunderna för att extrahera sina data och behålla sitt innehåll utanför Azure för en kund angiven kvarhållningsperiod.

Med Azure kan kunderna exportera data och granska rapporter från produkten. Exporterna sparas lokalt för att behålla informationen för en kunddefinierad kvarhållningsperiod.

## <a name="electronic-discovery-e-discovery"></a>Elektronisk identifiering (e-identifiering)
Azure-kunder ansvarar för att uppfylla kraven på e-identifiering i användningen av Azure-tjänster. Om Azure-kunder måste bevara sina kund uppgifter kan de exportera och spara data lokalt. Dessutom kan kunderna begära export av sina data från kund support avdelningen för Azure. Förutom att tillåta kunder att exportera sina data, utför Azure omfattande loggning och övervakning internt.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
