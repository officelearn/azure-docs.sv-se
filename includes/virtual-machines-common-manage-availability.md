---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1f4bd0d81d16997a9d627f918d77cb8e770a5b53
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Förstå omstarter av virtuella datorer – underhåll och driftavbrott
Det finns tre scenarier som kan leda till virtuell dator i Azure som påverkas: oplanerad maskinvara underhåll och oplanerade driftstopp planerat underhåll.

* **Oplanerat maskinvaruunderhåll** inträffar när Azure-plattformen förutser att maskinvaran eller plattformskomponenter som är associerade med en fysisk dator är på väg att få problem. När plattformen förutser ett problem skickar den en händelse om oplanerat maskinvaruunderhåll för att minska påverkan på virtuella datorer som finns på maskinvaran i fråga. Azure använder direktmigreringsteknik för att migrera de virtuella datorerna från maskinvaran som fått problem till en felfri fysisk dator. Direktmigrering är en åtgärd för att skydda virtuella datorer, som endast pausar den virtuella datorn en kort stund. Minne, öppna filer och nätverksanslutningar bevaras, men prestanda kan försämras före och/eller efter händelsen. I de fall då det inte går att använda direktmigrering uppstår ett oväntat driftavbrott på den virtuella datorn (se nedan).


* **Oväntade driftavbrott** är ovanliga och inträffar om det uppstår problem på maskinvaran eller den fysiska infrastrukturen som den virtuella datorn finns på. Det kan vara lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När sådant fel identifieras migrerar Azure-plattformen automatiskt (heals) den virtuella datorn till en felfri fysisk dator i samma datacenter. Återställningsprocessen medför driftavbrott (omstart) på virtuella datorer och i vissa fall förlust av den temporära enheten. Anslutna operativsystems- och datadiskar bevaras alltid. 

  Virtuella datorer kan även uppstå avbrottstid i händelse av ett strömavbrott eller katastrof som påverkar ett helt datacenter eller även en hel region. För dessa scenarier kan Azure tillhandahåller skyddsalternativ inklusive [tillgänglighet zoner](../articles/availability-zones/az-overview.md) och [länkas regioner](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Planerat underhåll** är periodiska uppdateringar som Microsoft utför i syfte att förbättra tillförlitligheten, prestanda och säkerheten för den plattformsinfrastruktur som dina virtuella datorer körs i. De flesta av de här uppdateringarna utförs utan att påverka dina virtuella datorer eller molntjänster (mer information finns i [VM Preserving Maintenance](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance) (Underhåll utan påverkan på virtuella datorer)). Azure-plattformen försöker alltid att utföra underhåll utan att påverka virtuella datorer, men i sällsynta fall kräver dessa uppdateringar en omstart av den virtuella datorn för att de nödvändiga uppdateringarna av den underliggande infrastrukturen ska kunna installeras. I detta fall kan du utföra planerat underhåll i Azure med underhålls- och omdistributionsåtgärden genom att initiera underhållet för de virtuella datorerna vid en lämplig tidpunkt. Mer information finns i [Planned Maintenance for Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/) (Planerat underhåll för virtuella datorer).


För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* [Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]
* [Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]
* [Använd schemalagda händelser för att proaktivt svar på VM påverka händelser] (https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]
* [Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar]
* [Använda tillgänglighet zoner för att skydda mot datacenter nivån fel]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen inom ett datacenter garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse, är tillgänglig och uppfyller 99,95% SLA för Azure. Mer information finns i [Serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Undvik att endast ha en enda virtuell dator i en tillgänglighetsuppsättning. Virtuella datorer med en sådan konfiguration är inte kvalificerade för serviceavtalets drifttidsgaranti vid planerat Azure-underhåll, med undantag för om den virtuella datorn använder [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md). Azures serviceavtal gäller för enskilda virtuella datorer som använder Premium Storage.

Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en **uppdateringsdomän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigureras i din tillgänglighetsuppsättning indelade i tre feldomäner för Resource Manager-distributioner (två feldomäner för klassisk distribution). Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

<!--Image reference-->
   ![Skiss på en konfiguration med uppdateringsdomäner och feldomäner](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning
Om du för närvarande använder virtuella datorer med ohanterade diskar rekommenderar vi starkt att du [konverterar virtuella datorer i tillgänglighetsuppsättningar för att använda hanterade diskar](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) ger bättre tillförlitlighet för tillgänglighetsuppsättningar genom att säkerställa att diskarna på virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritiska systemdelar. Det gör du genom att automatiskt placerar diskarna i feldomäner för olika lagring (storage kluster) och justera dem med VM-feldomän. Endast VM-instans med diskar på lagring feldomänen misslyckas om en lagring feldomän misslyckas på grund av maskinvara eller programvara.
![Hanterade diskar FDs](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Antalet feldomäner för hanterade tillgänglighetsuppsättningar varierar beroende på region – antingen två eller tre per region. I följande tabell ser du antalet per region

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Om du planerar att använda virtuella datorer med [ohanterade diskar](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks) följer du rekommendationerna nedan för Storage-konton där virtuella hårddiskar (VHD) på virtuella datorer lagras som [sidblobbar](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Förvara alla diskar (operativsystem och data) som är associerade med en virtuell dator i samma lagringskonto**
2. **Kontrollera [gränserna](../articles/storage/common/storage-scalability-targets.md) för antalet ohanterade diskar i ett Storage-konto** innan du lägger till fler virtuella hårddiskar till ett lagringskonto
3. **Använd ett separat lagringskonto för varje virtuell dator i en tillgänglighetsuppsättning.** Dela inte Storage-konton med flera virtuella datorer i samma tillgänglighetsuppsättning. Det är acceptabelt för virtuella datorer över olika Tillgänglighetsuppsättningar för att dela lagringskonton om ovan rekommenderade metoder följs ![ohanterad diskar FDs](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurera varje programnivå i separata tillgänglighetsuppsättningar
Om de virtuella datorerna är nästan identiska och tjänar samma syfte för ditt program rekommenderar vi att du konfigurerar en tillgänglighetsuppsättning för varje nivå av ditt program.  Om du placerar två olika nivåer i samma tillgänglighetsuppsättning kan alla virtuella datorer på samma programnivå startas om samtidigt. Genom att konfigurera minst två virtuella datorer i en tillgänglighetsuppsättning för varje nivå garanterar du att minst en virtuell dator är tillgänglig på varje nivå.

Du kan till exempel placera alla virtuella datorer i klientdelen av ditt program som kör IIS, Apache eller Nginx i en enskild tillgänglighetsuppsättning. Se till att endast virtuella klientdatorer placeras i samma tillgänglighetsuppsättning. Se också till att endast virtuella datanivådatorer placeras i en egen tillgänglighetsuppsättning, till exempel replikerade virtuella datorer som kör SQL Server eller MySQL.

<!--Image reference-->
   ![Programnivåer](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar
Kombinera [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) med en tillgänglighetsuppsättning för att få bästa programåterhämtning. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastningsutjämning för virtuella datorer finns i [Belastningsutjämna virtuella datorer](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Om belastningsutjämnaren inte konfigureras för att jämna ut trafiken mellan flera virtuella datorer kommer varje planerat underhåll att påverka endast den virtuella dator som hanterar trafik och orsaka ett avbrott på programnivån. Om du placerar flera virtuella datorer av samma nivå under samma belastningsutjämnare och tillgänglighetsuppsättning kommer trafiken att hanteras kontinuerligt av minst en instans.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Använda tillgänglighet zoner för att skydda mot datacenter nivån fel

[Tillgänglighet zoner](../articles/availability-zones/az-overview.md) (förhandsgranskning), ett alternativ till tillgänglighet anger, expandera kontrollnivån som du måste hantera tillgängligheten för program och data på din virtuella dator. En tillgänglighetszon är en fysiskt separat zon i en Azure-region. Det finns tre tillgänglighet zoner per Azure-region som stöds. Varje zon tillgänglighet har en distinkt power käll-, nätverks- och kylning och är logiskt åtskild från andra tillgänglighet zoner i Azure-regionen. Du kan bygga dina lösningar för att använda replikerade virtuella datorer i zoner för att skydda dina appar och data från förlust av ett datacenter. Om en zon äventyras, sedan replikerade appar och data är omedelbart tillgängliga i en annan zon. 

![Tillgänglighet zoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Lär dig mer om hur du distribuerar en [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) VM i en zon för tillgänglighet.


<!-- Link references -->
[Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]: #configure-each-application-tier-into-separate-availability-sets
[Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]: #use-managed-disks-for-vms-in-an-availability-set
[Använda tillgänglighet zoner för att skydda mot datacenter nivån fel]: #use-availability-zones-to-protect-from-datacenter-level-failures
