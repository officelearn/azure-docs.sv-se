---
title: Skydd av kundinformation i Azure
description: Den här artikeln tar hur Azure skyddar kundens data.
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
ms.openlocfilehash: 9a3b00e39f78f65b05b7d730447440d481979539
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102695"
---
# <a name="protection-of-customer-data-in-azure"></a>Skydd av kundinformation i Azure   
Åtkomst till kunddata av Microsoft operations och supportpersonal nekas som standard. När åtkomst till kunddata beviljas ledande godkännande krävs och sedan åtkomst är noggrant hanteras och loggas. Krav på åtkomstkontroll som upprättas av följande Microsoft Azure säkerhetsprincip:

- Ingen åtkomst till kunddata som standard
- Inga användare eller administratör konton på kundens virtuella datorer
- Bevilja lägsta behörighet som krävs för att slutföra registreringen; Granska och logga förfrågningar

Microsoft Azure supporttekniker tilldelas unika Corporate AD-konton av Microsoft. Microsoft Azure förlitar sig på Microsoft företagets Active Directory, hanteras av MSIT att kontrollera åtkomst till viktig information systems. Multifaktorautentisering krävs och bara bevilja åtkomst från säkra konsoler.

Alla åtkomstförsök övervakas och kan visas via en grundläggande uppsättning rapporter.

## <a name="data-protection"></a>Dataskydd
Azure tillhandahåller kunder med stark datasäkerhet – både som standard och som alternativ för customer.

**Dataavgränsning** -Azure är en tjänst för flera innehavare, vilket innebär att flera kunder distributioner och virtuella datorer lagras på samma fysisk maskinvara. Azure använder logisk isolering för att särskilja varje kund data från den andra. Ansvarsfördelning tillhandahåller skalning och ekonomiska fördelarna med flera tjänster när noggrant förhindra kunder från att komma åt varandras data.

**Skydd av data i vila** -kunder ansvarar för att säkerställa att data som lagras i Azure krypteras i enlighet med deras standarder. Azure erbjuder en mängd olika krypteringsfunktioner för, vilket ger kunderna möjlighet att välja den lösning som bäst uppfyller deras behov. Azure Key Vault kan kunder enkelt kan behålla kontrollen över nycklar som används av molnprogram och tjänster för att kryptera data. Azure Disk Encryption kan kunder kryptera virtuella datorer. Azure Storage Service-kryptering gör det möjligt att kryptera alla data som placeras i en kund storage-konto.

**Dataskydd under överföring** -kunder kan aktivera kryptering för trafik mellan sina egna virtuella datorer och användare. Azure skyddar data under överföring till eller från utanför komponenter och data i transit internt, t.ex två virtuella nätverk. Azure använder den standardmässiga säkerhet TLS (Transport Layer) 1.2 eller senare protokollet med 2048-bitars RSA/SHA256 krypteringsnycklar som rekommenderas av CESG/NCSC för att kryptera kommunikationen mellan:

- kunden och molnet
- internt mellan Azure och Datacenter

**Kryptering** -kryptering av data i lagring och under överföring kan distribueras av kunder som bästa praxis för att säkerställa sekretess och integriteten hos data. Det är enkelt för kunder att konfigurera sina Azure cloud services att använda SSL för att skydda kommunikationen från Internet och även mellan sina Azure värd för virtuella datorer.

**Dataredundans** -Microsoft garanterar att data är skyddade om det finns en cyberattack eller fysisk skada på ett datacenter. Kunder kan välja:

- i land lagring för kompatibilitet eller latens överväganden
- out land lagring för säkerhet eller disaster recovery

Data kan replikeras inom en vald region för redundans men inte kommer att överföras utanför den. Kunder har flera alternativ för att replikera data, inklusive antalet kopior, nummer och platsen för replikering datacenter.

När du skapar ditt lagringskonto måste du välja ett av följande replikeringsalternativ:

- Lokalt redundant lagring (LRS). Med lokalt redundant lagring underhålls tre kopior av dina data. LRS replikeras tre gånger på en anläggning i en region. LRS skyddar dina data mot normala maskinvarufel, men inte mot fel på en enskild anläggning.
- Zonredundant lagring (ZRS). Med zonredundant lagring underhålls tre kopior av dina data. ZRS replikeras tre gånger mellan två eller tre anläggningar som ger högre hållbarhet än LRS. Replikering inträffar inom en enskild region eller mellan två regioner. ZRS garanterar att dina data skyddas inom en enskild region.
- GEO-redundant lagring (GRS). Geo-redundant lagring är aktiverat för ditt lagringskonto som standard när du skapar det. Med GRS underhålls sex kopior av dina data. Med GRS replikeras dina data tre gånger i den primära regionen. Dina data också replikeras tre gånger i en sekundär region hundratals mil bort från den primära regionen, vilket ger den högsta nivån av hållbarhet. Om det uppstår ett fel i den primära regionen kommer Azure Storage att redundansväxla till den sekundära regionen. GRS garanterar att dina data skyddas i två olika områden.

**Destruktion** – när kunder tar bort data eller lämna Azure, Microsoft följer strikta standarder för att skriva över lagringsresurser innan återanvändning samt fysiska förstörs inaktiverade maskinvara. Microsoft kör en fullständig radering av data på kundbegäran och kontrakt-avslutning.

## <a name="customer-data-ownership"></a>Kunden dataägarskap
Microsoft inte granska, godkänna eller övervaka program som kunder distribuera till Azure. Dessutom vet Microsoft inte vilka typer av data kunder väljer att lagra i Azure. Microsoft gör inte anspråk dataägarskap över den kundinformation som anges i Azure.

## <a name="records-management"></a>Posthantering av
Azure har upprättat interna poster krav på datalagring för backend-data. Kunder ansvarar för att identifiera sina egna krav på poster datalagring. Kunden är ansvarig för extrahering av sina data och behålla innehållet utanför Azure för en kvarhållningsperiod som anges av kunden för poster som lagras i Azure.

Azure tillhandahåller kunden möjlighet att exportera data och granska rapporter från produkten. Exporten sparas lokalt om du vill behålla information för ett kunddefinierat kvarhållning tidsperiod.

## <a name="electronic-discovery-e-discovery"></a>Identifiering av elektroniska (e-identifiering)
Azure-kunder ansvarar för att uppfylla kraven i e-identifiering i deras användning i Azure-tjänster. Om en kund med Azure måste bevara deras kundinformation, kan de exportera och sparar data lokalt. Kunder kan dessutom begära export av data från Azure kundsupport avdelning. Förutom att kunderna kan exportera data, utför Azure utförlig loggning och övervakning internt.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
