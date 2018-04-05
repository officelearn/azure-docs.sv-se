---
title: ta med fil
description: ta med fil
services: virtual-machines
author: iainfoulds
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: include file
ms.openlocfilehash: a6b75b3804e7736fe5203caecb8c6badcfbf9ab4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regioner och tillgänglighet för virtuella datorer i Azure
Azure körs på ett antal datacenter över hela världen. Dessa datacenter är grupperade i geografiska regioner så att du kan välja var du vill bygga dina program. Det är viktigt att förstå hur och var dina virtuella datorer körs i Azure och vilka alternativ du har för att maximera prestanda, tillgänglighet och redundans. Den här artikeln ger en översikt över funktionerna för tillgänglighet och redundans i Azure.

## <a name="what-are-azure-regions"></a>Vad är Azure-regioner?
Du kan skapa Azure-resurser i definierade geografiska områden som 'Västra USA', 'Nordeuropa' eller 'Sydostasien'. Se [listan över regioner och deras platser](https://azure.microsoft.com/regions/). I varje region finns flera datacenter för att skapa förutsättningar för redundans och tillgänglighet. Den här metoden ger flexibilitet när du utformar program för att skapa virtuella datorer som är närmast dina användare och för att uppfylla eventuella, efterlevnad eller skatt syften.

## <a name="special-azure-regions"></a>Särskilda Azure-regioner
Azure har vissa särskilda områden som du kanske vill använda när du skapar ut dina program för kompatibilitet eller juridiska ändamål. De särskilda regionerna innefattar:

* **Virginia (USA-förvaltad region)** och **Iowa (USA-förvaltad region)**
  * En fysisk och logisk nätverksisolerad instans av Azure för amerikanska myndigheter och partner som drivs av säkerhetskontrollerad amerikansk personal. Innefattar ytterligare efterlevnadscertifieringar som [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) och [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Läs mer om [Azure Government](https://azure.microsoft.com/features/gov/).
* **Östra Kina** och **Norra Kina**
  * Dessa regioner är tillgängliga via ett unikt partnerskap mellan Microsoft och 21Vianet, vilket innebär att Microsoft inte direkt underhåller dessa datacenter. Läs mer om [Microsoft Azure i Kina](http://www.windowsazure.cn/).
* **Centrala Tyskland** och **Nordöstra Tyskland**
  * Dessa områden är tillgängliga via en datamodell förvaltning där kunddata förblir i Tyskland under kontroll av T-system, en Deutsche Telekom företag som agerar som tyska data förvaltning.

## <a name="region-pairs"></a>Regionpar
Varje Azure-region är kopplad till en annan region inom samma geografiska område (till exempel USA, Europa och Asien). På så sätt kan resurser som VM-lagring replikeras över geografiska områden som inte troligtvis påverkas samtidigt av naturkatastrofer, oroligheter i landet, strömavbrott eller avbrott i fysiska nätverk. Ytterligare fördelar med regionpar:

* Vid ett större Azure-avbrott prioriteras en region i varje par för att minska tiden för programåterställning. 
* Planerade Azure-uppdateringar distribueras en i taget till kopplade regioner för att minimera nedtid och risk för programavbrott.
* Data finns kvar i samma geografiska region som den andra regionen i paret (med undantag för Södra Brasilien) av skatte- och jurisdiktionsmässiga skäl.

Exempel på regionpar:

| Primär | Sekundär |
|:--- |:--- |
| Västra USA |USA, östra |
| Europa, norra |Europa, västra |
| Sydostasien |Östasien |

Se [den fullständiga listan över regionpar](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Funktionstillgänglighet
Vissa tjänster eller VM-funktioner är endast tillgängliga i vissa regioner, till exempel särskilda VM-storlekar eller lagringstyper. Det finns också vissa globala Azure-tjänster som inte kräver att du väljer en viss region, till exempel [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) och [Azure DNS](../articles/dns/dns-overview.md). När du utformar din programmiljö kan du kontrollera [tillgängligheten av Azure-tjänster för varje region](https://azure.microsoft.com/regions/#services). Du kan också [programmässigt fråga stöds VM-storlekar och begränsningar i varje region](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

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
| Data replikeras över flera anläggningar. |Nej |Ja |Ja |Ja |
| Data kan läsas från den sekundära platsen och från den primära platsen. |Nej |Nej |Nej |Ja |
| Antal kopior av data som finns på olika noder. |3 |3 |6 |6 |

Du kan läsa mer om [Azure Storage-replikeringsalternativen här](../articles/storage/common/storage-redundancy.md). Mer information om hanterade diskar finns i [Översikt över Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Lagringskostnader
Priserna varierar beroende på vilken lagringstyp och tillgänglighet du väljer.

**Azure Managed Disks**
* Hanterade Premiumdiskar backas upp av Solid-State-hårddiskar (SSD) och hanteras standarddiskar backas upp av vanliga snurrande diskar. Både Premium och Standard Managed Disks debiteras baserat på etablerad kapacitet för disken.

**Ohanterade diskar**
* Premium-lagring backas upp av Solid-State-hårddiskar (SSD) och debiteras baserat på diskens kapacitet.
* Standard Storage använder roterande diskar och debiteras baserat på den kapacitet som används och önskad lagringstillgänglighet.
  * För RA-GRS tillkommer en extra dataöverföringsavgift för geo-replikering för bandbredden som används för att replikera dessa data till en annan Azure-region.

Se [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/) för prisinformation för olika lagringstyper och tillgänglighetsalternativ.

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En tillgänglighetsuppsättning är en logisk gruppering av virtuella datorer inom ett datacenter som gör att Azure för att förstå hur ditt program är utformat för att tillhandahålla för redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighetsuppsättning för ett program med hög tillgänglighet och uppfyller de [99,95% SLA för Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Det finns inga kostnaden för Tillgänglighetsuppsättningen själva, du betalar bara för varje VM-instans som du skapar. När en enskild virtuell dator använder [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md) gäller serviceavtalet för Azure för oplanerat underhåll. 

En tillgänglighetsuppsättning består av två ytterligare grupperingar som skydd mot maskinvarufel och tillåta uppdateringar att på ett säkert sätt tillämpas - fault-domäner (FDs) och uppdatera domäner (UDs). Läs mer om hur du hanterar tillgänglighet för [virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md) och [virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Feldomäner
En feldomän är en logisk grupp av underliggande maskinvara som delar en gemensam strömkälla och nätverksswitch, ungefär som ett rack i ett lokalt datacenter. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt dina virtuella datorer mellan dessa feldomäner. På så sätt begränsas påverkan av potentiella fel på fysisk maskinvara, nätverksavbrott och strömavbrott.

### <a name="update-domains"></a>Uppdateringsdomäner
En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt de virtuella datorerna mellan dessa uppdateringsdomäner. På så sätt säkerställs att minst en instans av ditt program alltid körs vid ett periodiskt underhåll av Azure-plattformen. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om.

### <a name="managed-disk-fault-domains"></a>Hanterade Disk feldomäner
För virtuella datorer som använder [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. Du kan läsa mer om dessa hanterade disken feldomäner för [virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) eller [virtuella Windows-datorer](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

## <a name="availability-zones"></a>Tillgänglighet zoner

[Tillgänglighet zoner](../articles/availability-zones/az-overview.md), anger ett alternativ till tillgänglighet, expandera kontrollnivån som du måste hantera tillgängligheten för program och data på din virtuella dator. En tillgänglighetszon är en fysiskt separat zon i en Azure-region. Det finns tre tillgänglighet zoner per Azure-region som stöds. Varje zon tillgänglighet har en distinkt power käll-, nätverks- och kylning och är logiskt åtskild från andra tillgänglighet zoner i Azure-regionen. Du kan bygga dina lösningar för att använda replikerade virtuella datorer i zoner för att skydda dina appar och data från förlust av ett datacenter. Om en zon äventyras, sedan replikerade appar och data är omedelbart tillgängliga i en annan zon. 

![Tillgänglighet zoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Lär dig mer om hur du distribuerar en [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM i en zon för tillgänglighet.

## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](../articles/best-practices-availability-checklist.md).

