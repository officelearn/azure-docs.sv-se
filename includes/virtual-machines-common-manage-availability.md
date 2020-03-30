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
ms.openlocfilehash: a4140ffc0d4e97afabb1c3080951eeb75c792a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76961477"
---
## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Förstå omstarter av virtuella datorer – underhåll och driftavbrott
Det finns tre scenarier som kan leda till att virtuell dator i Azure påverkas: oplanerat maskinvaruunderhåll, oväntad driftstopp och planerat underhåll.

* **Oplanerat maskinvaruunderhåll** inträffar när Azure-plattformen förutser att maskinvaran eller plattformskomponenter som är associerade med en fysisk dator är på väg att få problem. När plattformen förutser ett problem skickar den en händelse om oplanerat maskinvaruunderhåll för att minska påverkan på virtuella datorer som finns på maskinvaran i fråga. Azure använder [Direktmigreringsteknik](https://docs.microsoft.com/azure/virtual-machines/linux/maintenance-and-updates) för att migrera virtuella datorer från den felaktiga maskinvaran till en felfri fysisk dator. Direktmigrering är en åtgärd för att skydda virtuella datorer, som endast pausar den virtuella datorn en kort stund. Minne, öppna filer och nätverksanslutningar bevaras, men prestanda kan försämras före och/eller efter händelsen. I de fall då det inte går att använda direktmigrering uppstår ett oväntat driftavbrott på den virtuella datorn (se nedan).


* **Ett oväntat driftstopp** är när maskinvaran eller den fysiska infrastrukturen för den virtuella datorn misslyckas oväntat. Detta kan inkludera lokala nätverksfel, lokala diskfel eller andra racknivåfel. När azure-plattformen identifieras migreras (läker) din virtuella dator till en felfri fysisk dator i samma datacenter. Återställningsprocessen medför driftavbrott (omstart) på virtuella datorer och i vissa fall förlust av den temporära enheten. Anslutna operativsystems- och datadiskar bevaras alltid.

  Virtuella datorer kan också uppleva driftstopp i den osannolika händelsen av ett avbrott eller en katastrof som påverkar ett helt datacenter, eller till och med en hel region. För dessa scenarier tillhandahåller Azure skyddsalternativ, inklusive [tillgänglighetszoner](../articles/availability-zones/az-overview.md) och [parade regioner](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Planerade underhållshändelser** är periodiska uppdateringar som microsoft gör till den underliggande Azure-plattformen för att förbättra den övergripande tillförlitligheten, prestandan och säkerheten för plattformsinfrastrukturen som dina virtuella datorer körs på. De flesta av de här uppdateringarna utförs utan att påverka dina virtuella datorer eller molntjänster (mer information finns i [VM Preserving Maintenance](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance) (Underhåll utan påverkan på virtuella datorer)). Azure-plattformen försöker alltid att utföra underhåll utan att påverka virtuella datorer, men i sällsynta fall kräver dessa uppdateringar en omstart av den virtuella datorn för att de nödvändiga uppdateringarna av den underliggande infrastrukturen ska kunna installeras. I detta fall kan du utföra planerat underhåll i Azure med underhålls- och omdistributionsåtgärden genom att initiera underhållet för de virtuella datorerna vid en lämplig tidpunkt. Mer information finns i [Planned Maintenance for Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/) (Planerat underhåll för virtuella datorer).


För att undvika påverkan av den här typen av avbrott rekommenderar vi att du gör följande för att säkerställa hög tillgänglighet för dina virtuella datorer:

* [Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]
* [Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]
* [Använd schemalagda händelser för att proaktivt svara på vm-påverkan på händelser](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]
* [Kombinera en belastningsutjämnare med tillgänglighetsuppsättningar]
* [Använd tillgänglighetszoner för att skydda mot datacenternivåfel]

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Använd tillgänglighetszoner för att skydda mot datacenternivåfel

[Tillgänglighetszoner](../articles/availability-zones/az-overview.md) utökar den kontrollnivå du har för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel. Zonuppsagda tjänster replikerar dina program och data över tillgänglighetszoner för att skydda mot enstaka felpunkter.

En tillgänglighetszon i en Azure-region är en kombination av en **feldomän** och en **uppdateringsdomän**. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

Med tillgänglighetszonerna kan Azure erbjuda branschens bästa serviceavtal med en drifttid på 99,99 % för virtuella datorer. Genom att skapa dina lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina program och data från förlusten av ett datacenter. Om en zon komprometterats är replikerade appar och data omedelbart tillgängliga i en annan zon.

![Tillgänglighetszoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Läs mer om hur du distribuerar en [Virtuell Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) i en tillgänglighetszon.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans
Tillgänglighetsuppsättningar är en annan datacenterkonfiguration för att tillhandahålla vm-redundans och tillgänglighet. Den här konfigurationen i ett datacenter säkerställer att minst en virtuell dator är tillgänglig under en planerad eller oplanerad underhållshändelse och uppfyller 99,95 % Azure SLA. Mer information finns i [Serviceavtal för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> En virtuell dator med en instans i en tillgänglighetsuppsättning av sig själv bör använda Premium SSD eller Ultra Disk för alla operativsystemdiskar och datadiskar för att kvalificera sig för SLA för anslutning till virtuella datorer på minst 99,9 %.

Varje virtuell dator i tillgänglighetsuppsättningen tilldelas en **uppdateringsdomän** och en **feldomän** av den underliggande Azure-plattformen. För en viss tillgänglighetsuppsättning tilldelas som standard fem uppdateringsdomäner, som inte kan konfigureras av användare, (Resource Manager-distributioner kan utökas till 20 uppdateringsdomäner) för att ange grupper av virtuella datorer och underliggande fysisk maskinvara som kan startas om samtidigt. Om fler än fem virtuella datorer har konfigurerats i en enskild tillgänglighetsuppsättning placeras den sjätte virtuella datorn i samma uppdateringsdomän som den första virtuella datorn. Den sjunde placeras i samma uppdateringsdomän som den andra virtuella datorn och så vidare. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om. En omstartad uppdateringsdomän får 30 minuter på sig för återställning innan underhållet initieras i en annan uppdateringsdomän.

Feldomäner definierar den grupp av virtuella datorer som delar samma strömkälla och nätverksswitch. Som standard är de virtuella datorer som konfigureras i din tillgänglighetsuppsättning indelade i tre feldomäner för Resource Manager-distributioner (två feldomäner för klassisk distribution). Att placera de virtuella datorerna i en tillgänglighetsuppsättning skyddar inte ditt program mot operativsystemfel eller programspecifika fel, men det begränsar påverkan av potentiella fel på fysisk maskinvara, problem med nätverket och strömavbrott.

<!--Image reference-->
   ![Skiss på en konfiguration med uppdateringsdomäner och feldomäner](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning
Om du för närvarande använder virtuella datorer med ohanterade diskar rekommenderar vi starkt att du [konverterar virtuella datorer i tillgänglighetsuppsättningar för att använda hanterade diskar](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Hanterade diskar](../articles/virtual-machines/windows/managed-disks-overview.md) ger bättre tillförlitlighet för tillgänglighetsuppsättningar genom att säkerställa att diskarna på virtuella datorer i en tillgänglighetsuppsättning är tillräckligt isolerade från varandra för att undvika felkritiska systemdelar. Detta sker genom att diskarna automatiskt placeras i olika lagringsfeldomäner (lagringskluster) och anpassa dem till feldomänen för virtuella datorer. Om en lagringsfeldomän misslyckas på grund av maskinvaru- eller programvarufel misslyckas endast vm-instansen med diskar på lagringsfeldomänen.
![Hanterade diskar-FD:er](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Antalet feldomäner för hanterade tillgänglighetsuppsättningar varierar beroende på region – antingen två eller tre per region. Du kan se feldomänen för varje region genom att köra följande skript.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Under vissa omständigheter kan två virtuella datorer i samma AvailabilitySet dela samma FaultDomain. Detta kan bekräftas genom att gå in i din tillgänglighetsuppsättning och kontrollera kolumnen **Feldomän.**
> Detta kan bero på följande sekvens när du distribuerar de virtuella datorerna:
> - Distribuera den första virtuella datorn
> - Stoppa/frigöra den första virtuella datorn
> - Distribuera den andra virtuella datorn Under dessa omständigheter kan OS-disken för den andra virtuella datorn skapas på samma feldomän som den första virtuella datorn, och därför kommer den andra virtuella datorn också att landa på samma FaultDomain. 
> För att undvika det här problemet rekommenderas att inte stoppa/deallocate de virtuella datorerna mellan distributioner.

Om du planerar att använda virtuella datorer med ohanterade diskar följer du nedanstående metodtips för lagringskonton där virtuella hårddiskar (VIRTUELLA hårddiskar) på virtuella datorer lagras som [sidblobar](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Förvara alla diskar (operativsystem och data) som är associerade med en virtuell dator i samma lagringskonto**
2. **Granska [gränserna för](../articles/storage/blobs/scalability-targets-premium-page-blobs.md) antalet ohanterade diskar i ett Azure Storage-konto** innan du lägger till fler virtuella hårddiskar i ett lagringskonto
3. **Använd ett separat lagringskonto för varje virtuell dator i en tillgänglighetsuppsättning.** Dela inte Storage-konton med flera virtuella datorer i samma tillgänglighetsuppsättning. Det är acceptabelt att virtuella datorer i olika tillgänglighetsuppsättningar ![delar lagringskonton om ovanstående metodtips följs Ohanterat diskar-FD:er](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Använda schemalagda händelser för att proaktivt svara på vm-påverkanshändelser

När du prenumererar på [schemalagda händelser](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)meddelas den virtuella datorn om kommande underhållshändelser som kan påverka den virtuella datorn. När schemalagda händelser är aktiverade får den virtuella datorn en minimal tid innan underhållsaktiviteten utförs. Värdoperativsystemuppdateringar som kan påverka den virtuella datorn köas till exempel som händelser som anger effekten, samt en tidpunkt då underhållet ska utföras om ingen åtgärd vidtas. Schemahändelser köas också när Azure upptäcker överhängande maskinvarufel som kan påverka din virtuella dator, vilket gör att du kan bestämma när läkningen ska utföras. Kunder kan använda händelsen för att utföra uppgifter före underhållet, till exempel spara tillstånd, misslyckas över till den sekundära och så vidare. När du har slutfört logiken för att hantera underhållshändelsen på ett smidigt sätt kan du godkänna den utestående schemalagda händelsen så att plattformen kan fortsätta med underhåll.

## <a name="configure-each-application-tier-into-separate-availability-zones-or-availability-sets"></a>Konfigurera varje programnivå i separata tillgänglighetszoner eller tillgänglighetsuppsättningar
Om dina virtuella datorer är nästan identiska och har samma syfte för ditt program rekommenderar vi att du konfigurerar en tillgänglighetszon eller tillgänglighetsuppsättning för varje nivå i ditt program.  Om du placerar två olika nivåer i samma tillgänglighetszon eller uppsättning kan alla virtuella datorer på samma programnivå startas om samtidigt. Genom att konfigurera minst två virtuella datorer i en tillgänglighetszon eller ange för varje nivå garanterar du att minst en virtuell dator på varje nivå är tillgänglig.

Du kan till exempel placera alla virtuella datorer i klientdelen av programmet som kör IIS, Apache och Nginx i en enda tillgänglighetszon eller uppsättning. Kontrollera att endast klientdat virtuella datorer placeras i samma tillgänglighetszon eller uppsättning. På samma sätt, se till att endast virtuella datanivådatorer placeras i sin egen tillgänglighetszon eller uppsättning, till exempel dina replikerade virtuella SQL Server-datorer eller dina Virtuella MySQL-datorer.

<!--Image reference-->
   ![Programnivåer](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Kombinera en belastningsutjämnare med tillgänglighetszoner eller uppsättningar
Kombinera [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) med en tillgänglighetszon eller ange för att få mest återhämtning av programmet. Azure Load Balancer distribuerar trafiken mellan flera virtuella datorer. Azure Load Balancer ingår i standardnivån för Virtual Machines. Azure Load Balancer ingår inte i alla nivåer för Virtual Machines. Mer information om belastningsutjämning för virtuella datorer finns i [Belastningsutjämna virtuella datorer](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Om lastbalanseraren inte konfigureras för att jämna ut trafiken mellan flera virtuella datorer kommer varje planerat underhåll att påverka endast den virtuella dator som hanterar trafik och orsaka ett avbrott på programnivån. Om du placerar flera virtuella datorer av samma nivå under samma lastbalanserare och tillgänglighetsuppsättning kommer trafiken att hanteras kontinuerligt av minst en instans.

En självstudiekurs om hur du läser in aldo över tillgänglighetszoner finns i [Virtuella datorer för belastningsutjämnare i alla tillgänglighetszoner med hjälp av Azure CLI](../articles/load-balancer/load-balancer-standard-public-zone-redundant-cli.md).


<!-- Link references -->
[Konfigurera flera virtuella datorer i en tillgänglighetsuppsättning för redundans]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurera varje programnivå i separata tillgänglighetsuppsättningar]: #configure-each-application-tier-into-separate-availability-zones-or-availability-sets
[Kombinera en lastbalanserare med tillgänglighetsuppsättningar]: #combine-a-load-balancer-with-availability-zones-or-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Använda hanterade diskar för virtuella datorer i en tillgänglighetsuppsättning]: #use-managed-disks-for-vms-in-an-availability-set
[Använd tillgänglighetszoner för att skydda mot datacenternivåfel]: #use-availability-zones-to-protect-from-datacenter-level-failures
