---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: aff3f47624fe21e1d0f020e8e5732e60b4b53657
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084063"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Förstå omstarter av virtuella datorer – underhåll och driftavbrott
Det finns tre scenarier som kan leda till virtuell dator i Azure som påverkas: oplanerat maskinvaruunderhåll, oväntat driftavbrott och planerat underhåll.

* **Oplanerat maskinvaruunderhåll** inträffar när Azure-plattformen förutser att maskinvaran eller plattformskomponenter som är associerade med en fysisk dator är på väg att få problem. När plattformen förutser ett problem skickar den en händelse om oplanerat maskinvaruunderhåll för att minska påverkan på virtuella datorer som finns på maskinvaran i fråga. Azure använder direktmigreringsteknik för att migrera de virtuella datorerna från maskinvaran som fått problem till en felfri fysisk dator. Direktmigrering är en åtgärd för att skydda virtuella datorer, som endast pausar den virtuella datorn en kort stund. Minne, öppna filer och nätverksanslutningar bevaras, men prestanda kan försämras före och/eller efter händelsen. I de fall då det inte går att använda direktmigrering uppstår ett oväntat driftavbrott på den virtuella datorn (se nedan).


* **Oväntade driftavbrott** när maskinvaran eller den fysiska infrastrukturen för den virtuella datorn misslyckades oväntat. Detta kan inkludera lokala nätverksfel, lokala diskfel eller andra fel på racknivå. När du har identifierats, migrerar Azure-plattformen automatiskt (återställer) den virtuella datorn till en felfri fysisk dator i samma datacenter. Återställningsprocessen medför driftavbrott (omstart) på virtuella datorer och i vissa fall förlust av den temporära enheten. Anslutna operativsystems- och datadiskar bevaras alltid. 

  Virtuella datorer kan också drabbas vid händelse av ett avbrott eller katastrof som påverkar ett helt datacenter eller till och med en hel region. Dessa scenarier kan Azure tillhandahåller skyddsalternativ inklusive [tillgänglighetszoner](../articles/availability-zones/az-overview.md) och [länkade regioner](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Planerat underhåll** är periodiska uppdateringar som Microsoft utför i syfte att förbättra tillförlitligheten, prestanda och säkerheten för den plattformsinfrastruktur som dina virtuella datorer körs i. De flesta av de här uppdateringarna utförs utan att påverka dina virtuella datorer eller molntjänster (mer information finns i [VM Preserving Maintenance](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance) (Underhåll utan påverkan på virtuella datorer)). Azure-plattformen försöker alltid att utföra underhåll utan att påverka virtuella datorer, men i sällsynta fall kräver dessa uppdateringar en omstart av den virtuella datorn för att de nödvändiga uppdateringarna av den underliggande infrastrukturen ska kunna installeras. I detta fall kan du utföra planerat underhåll i Azure med underhålls- och omdistributionsåtgärden genom att initiera underhållet för de virtuella datorerna vid en lämplig tidpunkt. Mer information finns i [Planned Maintenance for Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/) (Planerat underhåll för virtuella datorer).


För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* [Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]
* [Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]
* [Använda schemalagda händelser för att proaktivt bemöta VM som påverkar händelser](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]
* [Kombinera en lastbalanserare med tillgänglighetsuppsättningar]
* [Använd tillgänglighetszoner för att skydda mot datacenterproblem nivå]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
För att ge ditt program redundans rekommenderar vi att du grupperar två eller flera virtuella datorer i en tillgänglighetsuppsättning. Den här konfigurationen inom ett datacenter garanterar att minst en virtuell dator under antingen en planerad eller oplanerad underhållshändelse är tillgänglig och uppfyller 99,95% serviceavtalet för Azure. Mer information finns i [Serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Undvik att endast ha en enda virtuell dator i en tillgänglighetsuppsättning. Virtuella datorer med en sådan konfiguration är inte kvalificerade för serviceavtalets drifttidsgaranti vid planerat Azure-underhåll, med undantag för om den virtuella datorn använder [Azure Premium Storage](../articles/virtual-machines/windows/premium-storage.md). Azures serviceavtal gäller för enskilda virtuella datorer som använder Premium Storage.

Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en **uppdateringsdomän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigureras i din tillgänglighetsuppsättning indelade i tre feldomäner för Resource Manager-distributioner (två feldomäner för klassisk distribution). Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

<!--Image reference--> ![Skiss på update uppdateringsdomäner och feldomäner domänkonfiguration](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning
Om du för närvarande använder virtuella datorer med ohanterade diskar rekommenderar vi starkt att du [konverterar virtuella datorer i tillgänglighetsuppsättningar för att använda hanterade diskar](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) ger bättre tillförlitlighet för tillgänglighetsuppsättningar genom att säkerställa att diskarna på virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritiska systemdelar. Det gör du genom att automatiskt genom att diskarna placeras i olika lagringsenheter feldomäner (lagringskluster) och justera dem med VM-feldomän. Om en feldomän för lagring misslyckas på grund av maskin- eller programvarufel, inte bara den Virtuella datorinstansen med diskar på feldomänen lagring.
![Hanterade diskar FD](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Antalet feldomäner för hanterade tillgänglighetsuppsättningar varierar beroende på region – antingen två eller tre per region. I följande tabell ser du antalet per region

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Om du planerar att använda virtuella datorer med [ohanterade diskar](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks) följer du rekommendationerna nedan för Storage-konton där virtuella hårddiskar (VHD) på virtuella datorer lagras som [sidblobbar](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Förvara alla diskar (operativsystem och data) som är associerade med en virtuell dator i samma lagringskonto**
2. **Kontrollera [gränserna](../articles/storage/common/storage-scalability-targets.md) för antalet ohanterade diskar i ett Storage-konto** innan du lägger till fler virtuella hårddiskar till ett lagringskonto
3. **Använd ett separat lagringskonto för varje virtuell dator i en tillgänglighetsuppsättning.** Dela inte Storage-konton med flera virtuella datorer i samma tillgänglighetsuppsättning. Det är virtuella datorer i olika Tillgänglighetsuppsättningar kan dela lagringskonton om rekommendationerna ovan följs ![ohanterade diskar FD](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Använda schemalagda händelser för att proaktivt besvara VM som påverkar händelser

När du prenumererar på [schemalagda händelser](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events), den virtuella datorn får ett meddelande om kommande underhållshändelser som kan påverka den virtuella datorn. När schemalagda händelser är aktiverad kan får den virtuella datorn en minimal mängd tid innan aktiviteten underhåll utförs. Till exempel Värdoperativsystem uppdateringar som kan påverka den virtuella datorn är i kö som händelser som anger påverkan, samt en tid då underhållet utförs om ingen åtgärd utförs. Schema-händelser finns också i kö när Azure upptäcker överhängande maskinvarufel som kan påverka din virtuella dator där du kan bestämma när den återställning ska utföras. Kunder kan använda händelsen för att utföra uppgifter före underhåll, till exempel sparande av tillstånd, redundansväxla till sekundärt och så vidare. När du har slutfört logik för att hantera ett smidigt sätt händelsen Underhåll kan du godkänna utestående händelsen för att tillåta plattform för att fortsätta med Underhåll.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurera varje programnivå i separata tillgänglighetsuppsättningar
Om de virtuella datorerna är nästan identiska och tjänar samma syfte för ditt program rekommenderar vi att du konfigurerar en tillgänglighetsuppsättning för varje nivå av ditt program.  Om du placerar två olika nivåer i samma tillgänglighetsuppsättning kan alla virtuella datorer på samma programnivå startas om samtidigt. Genom att konfigurera minst två virtuella datorer i en tillgänglighetsuppsättning för varje nivå garanterar du att minst en virtuell dator är tillgänglig på varje nivå.

Du kan till exempel placera alla virtuella datorer i klientdelen av ditt program som kör IIS, Apache eller Nginx i en enskild tillgänglighetsuppsättning. Se till att endast virtuella klientdatorer placeras i samma tillgänglighetsuppsättning. Se också till att endast virtuella datanivådatorer placeras i en egen tillgänglighetsuppsättning, till exempel replikerade virtuella datorer som kör SQL Server eller MySQL.

<!--Image reference--> ![Programnivåer](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinera en lastbalanserare med tillgänglighetsuppsättningar
Kombinera [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) med en tillgänglighetsuppsättning för att få bästa programåterhämtning. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastningsutjämning för virtuella datorer finns i [Belastningsutjämna virtuella datorer](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Om lastbalanseraren inte konfigureras för att jämna ut trafiken mellan flera virtuella datorer kommer varje planerat underhåll att påverka endast den virtuella dator som hanterar trafik och orsaka ett avbrott på programnivån. Om du placerar flera virtuella datorer av samma nivå under samma lastbalanserare och tillgänglighetsuppsättning kommer trafiken att hanteras kontinuerligt av minst en instans.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Använd tillgänglighetszoner för att skydda mot datacenterproblem nivå

[Tillgänglighetszoner](../articles/availability-zones/az-overview.md), anger ett alternativ till tillgänglighet, expandera nivå av kontroll du behöver hantera tillgängligheten för de program och data på dina virtuella datorer. En tillgänglighetszon är en fysiskt separat zon i en Azure-region. Det finns tre Tillgänglighetszoner per Azure-region som stöds. Varje Tillgänglighetszon har en distinkt strömkälla, nätverk och kylning och är logiskt åtskild från andra Tillgänglighetszoner i Azure-region. Du kan utforma dina lösningar om du vill använda replikerade virtuella datorer i zoner, för att skydda dina appar och data från förlust av ett datacenter. Om en zon komprometteras sedan är replikerade appar och data omedelbart tillgängliga i en annan zon. 

![Tillgänglighetszoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Läs mer om hur du distribuerar en [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) virtuell dator i en Tillgänglighetszon.


<!-- Link references -->
[Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]: #configure-each-application-tier-into-separate-availability-sets
[Kombinera en lastbalanserare med tillgänglighetsuppsättningar]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]: #use-managed-disks-for-vms-in-an-availability-set
[Använd tillgänglighetszoner för att skydda mot datacenterproblem nivå]: #use-availability-zones-to-protect-from-datacenter-level-failures
