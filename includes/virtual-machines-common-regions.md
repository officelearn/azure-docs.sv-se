---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5e3f25727204343de107bacb9fc99d6cfb77d76f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020937"
---
Det är viktigt att förstå hur och var dina virtuella datorer körs i Azure och vilka alternativ du har för att maximera prestanda, tillgänglighet och redundans. Den här artikeln ger en översikt över funktionerna för tillgänglighet och redundans i Azure.


## <a name="what-are-azure-regions"></a>Vad är Azure-regioner?
Azure körs på ett antal datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. 

Du skapar Azure-resurser i definierade geografiska områden som "västra USA", "Nord Europa" eller "Sydostasien". Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). I varje region finns flera datacenter för att skapa förutsättningar för redundans och tillgänglighet. Den här metoden ger dig flexibilitet när du utformar program för att skapa virtuella datorer som är närmast dina användare och som uppfyller juridiska, efterlevnads-eller skatte syfte.

## <a name="special-azure-regions"></a>Särskilda Azure-regioner
Azure har vissa särskilda regioner som du kanske vill använda när du skapar program för efterlevnad eller i juridiskt syfte. De särskilda regionerna innefattar:

* **US Gov, Virginia** och **US Gov, Iowa**
  * En fysisk och logisk nätverksisolerad instans av Azure för amerikanska myndigheter och partner som drivs av säkerhetskontrollerad amerikansk personal. Innefattar ytterligare efterlevnadscertifieringar som [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) och [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Läs mer om [Azure Government](https://azure.microsoft.com/features/gov/).
* **Östra Kina** och **Norra Kina**
  * Dessa regioner är tillgängliga via ett unikt partnerskap mellan Microsoft och 21Vianet, vilket innebär att Microsoft inte direkt underhåller dessa datacenter. Läs mer om [Azure Kina 21Vianet](https://www.windowsazure.cn/).
* **Centrala Tyskland** och **Nordöstra Tyskland**
  * Dessa regioner är tillgängliga via en data förvaltar modell där kund information förblir i Tyskland under kontroll av T-Systems, ett tyska telekom-företag som agerar som den tyska data förvaltar.

## <a name="region-pairs"></a>Regionpar
Varje Azure-region är kopplad till en annan region inom samma geografiska område (till exempel USA, Europa och Asien). På så sätt kan resurser som VM-lagring replikeras över geografiska områden som inte troligtvis påverkas samtidigt av naturkatastrofer, oroligheter i landet, strömavbrott eller avbrott i fysiska nätverk. Ytterligare fördelar med regionpar:

* Vid ett större Azure-avbrott prioriteras en region i varje par för att minska tiden för programåterställning. 
* Planerade Azure-uppdateringar distribueras en i taget till kopplade regioner för att minimera nedtid och risk för programavbrott.
* Data finns kvar i samma geografiska region som den andra regionen i paret (med undantag för Södra Brasilien) av skatte- och jurisdiktionsmässiga skäl.

Exempel på regionpar:

| Primär | Sekundär |
|:--- |:--- |
| USA, västra |USA, östra |
| Europa, norra |Europa, västra |
| Asien, sydöstra |Asien, östra |

Se [den fullständiga listan över regionpar](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Funktionens tillgänglighet
Vissa tjänster eller VM-funktioner är endast tillgängliga i vissa regioner, till exempel särskilda VM-storlekar eller lagringstyper. Det finns också vissa globala Azure-tjänster som inte kräver att du väljer en viss region, till exempel [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) och [Azure DNS](../articles/dns/dns-overview.md). När du utformar din programmiljö kan du kontrollera [tillgängligheten av Azure-tjänster för varje region](https://azure.microsoft.com/regions/#services). Du kan också [program mässigt fråga efter de VM-storlekar som stöds och begränsningar i varje region](../articles/azure-resource-manager/templates/error-sku-not-available.md).

## <a name="storage-availability"></a>Lagringstillgänglighet
Det är viktigt att förstå Azure-regioner och geografiska områden när du ska välja bland de tillgängliga alternativen för lagringsreplikering. Det finns olika replikeringsalternativ beroende på vilken lagringstyp du har.

**Azure Managed Disks**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.

**Diskar baserade på lagringskonto**
* Lokalt redundant lagring (LRS)
  * Replikerar data tre gånger inom den region där du skapade ditt lagringskonto.
* Zonredundant lagring (ZRS)
  * Replikerar data tre gånger mellan två eller tre anläggningar, antingen inom en enda region eller mellan två regioner.
* Geo-redundant lagring (GRS)
  * Replikerar data till en sekundär region som ligger hundratals kilometer från den primära regionen.
* Geo-redundant lagring med läsbehörighet (RA-GRS)
  * Replikerar data till en sekundär region som med GRS, och ger även skrivskyddad åtkomst till data på den sekundära platsen.

Följande tabell ger en snabb översikt över skillnaderna mellan lagringsreplikeringstyperna:

| Replikeringsstrategi | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Data replikeras över flera anläggningar. |Inga |Ja |Ja |Ja |
| Data kan läsas från den sekundära platsen och från den primära platsen. |Inga |Inga |Inga |Ja |
| Antal kopior av data som finns på olika noder. |3 |3 |6 |6 |

Du kan läsa mer om [Azure Storage-replikeringsalternativen här](../articles/storage/common/storage-redundancy.md). Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Lagringskostnader
Priserna varierar beroende på vilken lagringstyp och tillgänglighet du väljer.

**Azure Managed Disks**
* Premium Managed Disks backas upp av solid state-hårddiskar (SSD) och standard Managed Disks backas upp av vanliga snurrande diskar. Både Premium och Standard Managed Disks debiteras baserat på etablerad kapacitet för disken.

**Ohanterade diskar**
* Premium Storage backas upp av solid state-hårddiskar (SSD) och debiteras baserat på diskens kapacitet.
* Standard Storage använder roterande diskar och debiteras baserat på den kapacitet som används och önskad lagringstillgänglighet.
  * För RA-GRS tillkommer en extra dataöverföringsavgift för geo-replikering för bandbredden som används för att replikera dessa data till en annan Azure-region.

Se [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/) för prisinformation för olika lagringstyper och tillgänglighetsalternativ.

