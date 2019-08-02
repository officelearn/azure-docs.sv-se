---
title: Kostnads analys och budget Azure Batch
description: Lär dig hur du får en kostnads analys och hur du ställer in en budget för batch-arbetsbelastningen.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: lahugh
ms.openlocfilehash: e6a99021a5e05f04672a4db2b4c208b8f4bad8c8
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361796"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kostnads analys och budgetar för Azure Batch

Det kostar inget att Azure Batch sig själv, bara de underliggande beräknings resurserna och program varu licenserna som används för att köra batch-arbetsbelastningar. På en hög nivå uppstår kostnader från Virtual Machines (VM) i en pool, data överföring från den virtuella datorn eller indata eller utdata som lagras i molnet. Låt oss ta en titt på några viktiga komponenter i batch för att förstå var kostnader kommer från, hur man ställer in en budget för en pool eller ett konto och vissa tekniker för att göra batch-arbetsbelastningar mer kostnads effektivt.

## <a name="batch-resources"></a>Batch-resurser

Virtuella datorer är den viktigaste resursen som används för batchbearbetning. Kostnaden för att använda virtuella datorer för batch beräknas utifrån typ, kvantitet och användnings tid. Fakturerings alternativ för virtuella datorer omfattar [betala per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning eller [reservation](../billing/billing-save-compute-costs-reservations.md) (betala i förväg). Båda betalnings alternativen har olika fördelar beroende på din beräknings belastning och båda betalnings modellerna kommer att påverka din faktura på ett annat sätt.

När program distribueras till batch-noder (VM: ar [](batch-application-packages.md)) med hjälp av programpaket debiteras du för de Azure Storage-resurser som dina program paket använder. Du debiteras också för lagring av indata-eller utdatafiler, till exempel resursfiler och andra loggdata. I allmänhet är kostnaden för lagrings data som är kopplade till batch mycket lägre än kostnaden för beräknings resurser. Varje virtuell dator i en pool som skapats med **VirtualMachineConfiguration** har en tillhör ande OS-disk som använder Azure-hanterade diskar. Azure-hanterade diskar har ytterligare kostnader och andra disk prestanda nivåer har också olika kostnader.

Batch-pooler använder nätverks resurser. I synnerhet används **VirtualMachineConfiguration** -pooler för standard belastnings utjämning, vilket kräver statiska IP-adresser. De belastnings utjämningar som används av batch är synliga för **användar prenumerations** konton, men är inte synliga för **batch service-** konton. Standard belastnings utjämning debiteras för alla data som skickas till och från virtuella batch-pooler. Välj batch-API: er som hämtar data från pool-noder (till exempel hämta aktivitet/Node-fil), uppgiftens programpaket, resurs/utdatafiler och behållar avbildningar debiteras.

### <a name="additional-services"></a>Ytterligare tjänster

Tjänster som inte omfattar virtuella datorer och lagrings enheter kan vara till för kostnaden för ditt batch-konto.

Andra tjänster som används ofta med batch kan innehålla:

- Application Insights
- Data Factory
- Azure Monitor
- Virtuellt nätverk
- Virtuella datorer med grafik program

Beroende på vilka tjänster du använder med din batch-lösning kan du debiteras ytterligare avgifter. Se [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att fastställa kostnaden för varje ytterligare tjänst.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kostnads analys och budget för en pool

Du kan skapa budgetar och utgifts aviseringar för dina batch-pooler eller batch-konton via Azure Portal. Budgetar och aviseringar är användbara för att meddela intressenter om eventuella risker med överförbrukning. Det är möjligt att det finns en fördröjning i utgifts aviseringar och att det är något som är något som kan överstiga en budget. I det här exemplet ska vi visa en kostnads analys av en enskild batch-pool.

1. I Azure Portal väljer du **Cost Management + fakturering** i det vänstra navigerings fältet.
1. Välj din prenumeration i avsnittet **Mina prenumerationer**
1. Gå till **kostnads analys** under **Cost Management** avsnittet i det vänstra navigerings fältet, som visar en vy så här:
1. Välj **Lägg till filter**. I den första List rutan väljer du **resurs** ![och väljer resurs filtret](./media/batch-budget/resource-filter.png)
1. I den andra List rutan väljer du batch-poolen. När du väljer poolen kommer kostnads analysen att se ut ungefär som i följande analys.
    ![Kostnads analys av en pool](./media/batch-budget/pool-cost-analysis.png)

Den resulterande kostnads analysen visar kostnaden för poolen och de resurser som bidrar till den här kostnaden. I det här exemplet är de virtuella datorer som används i poolen den mest dyra resursen.

Om du vill skapa en budget för poolen väljer du **Budget: ingen**och väljer sedan **skapa ny budget >** . Använd nu fönstret för att konfigurera en budget som är specifik för din pool.

Mer information om hur du konfigurerar en budget finns i [skapa och hantera Azure](../cost-management/tutorial-acm-create-budgets.md)-budgetar.

> [!NOTE]
> Azure Batch bygger på Azure Cloud Services-och Azure Virtual Machines-teknik. När du väljer **Cloud Services konfiguration**debiteras du baserat på Cloud Services prissättnings struktur. När du väljer **konfiguration för virtuell dator**debiteras du baserat på Virtual Machines pris strukturen. Exemplet på den här sidan använder konfigurationen för den **virtuella datorn**.

## <a name="minimize-cost"></a>Minimera kostnader

Det kan vara dyrt att använda flera virtuella datorer och Azure-tjänster för längre tids perioder. Lyckligt vis finns det tillgängliga tjänster som hjälper dig att minska dina utgifter, samt strategier för att maximera arbets Belastningens effektivitet.

### <a name="low-priority-virtual-machines"></a>Virtuella datorer med låg prioritet

Virtuella datorer med låg prioritet minskar kostnaden för batch-arbetsbelastningar genom att dra nytta av överskotts data behandling i Azure. När du anger virtuella datorer med låg prioritet i dina pooler använder batch detta överskott för att köra arbets belastningen. Det finns betydande besparingar genom att använda virtuella datorer med låg prioritet i stället för dedikerade virtuella datorer.

Läs mer om hur du konfigurerar virtuella datorer med låg prioritet för din arbets belastning vid [användning av virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Typ av operativ system disk för virtuell dator

Det finns flera [typer av virtuella dator diskar](../virtual-machines/windows/disks-types.md). De flesta VM-serier har storlekar som stöder både Premium-och standard-lagring. När ens VM-storlek har valts för en pool konfigurerar batch att Premium SSD OS-diskar. När den virtuella datorns icke-s-storlek väljs används den billigare, standard hård disk typen. Till exempel används Premium SSD OS-diskar för `Standard_D2s_v3` och standard diskar för HDD-operativsystem används för. `Standard_D2_v3`

Premium SSD OS-diskar är dyrare, men har högre prestanda och virtuella datorer med Premium diskar kan starta något snabbare än virtuella datorer med standard diskar för HDD-operativsystem. Med batch är operativ system disken ofta inte så mycket som program-och aktivitets filerna finns på de virtuella datorernas temporära SSD-diskar. Därför behöver du i många fall inte betala den ökade kostnaden för Premium SSD som tillhandahålls när storleken på en virtuell dator har angetts.

### <a name="reserved-virtual-machine-instances"></a>Reserverade instanser av virtuella datorer

Om du avser att använda batch under en lång tids period kan du spara pengar på virtuella datorer genom att använda [Azure reservations](../billing/billing-save-compute-costs-reservations.md) för dina arbets belastningar. En reservations taxa är betydligt lägre än en taxa enligt principen betala per användning. Virtuella dator instanser som används utan reservationer debiteras enligt priset betala per användning. Om du köper en reservation tillämpas reservations rabatten och du debiteras inte längre enligt priserna för betala per användning.

### <a name="automatic-scaling"></a>Automatisk skalning

Med [automatisk skalning](batch-automatic-scaling.md) skalas antalet virtuella datorer dynamiskt i batch-poolen baserat på kraven för det aktuella jobbet. Genom att skala poolen baserat på ett jobbs livs längd säkerställer automatisk skalning att virtuella datorer skalas upp och används endast när det finns ett jobb att utföra. När jobbet har slutförts, eller om det inte finns några jobb, skalas de virtuella datorerna automatiskt ned för att spara beräknings resurser. Med skalning kan du sänka den totala kostnaden för din batch-lösning genom att bara använda de resurser du behöver.

Mer information om automatisk skalning finns i [automatisk skalning av Compute-noder i en Azure Batch pool](batch-automatic-scaling.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-API: er och verktyg](batch-apis-tools.md) som är tillgängliga för att skapa och övervaka batch-lösningar.  

- Läs mer om [virtuella datorer med låg prioritet med batch](batch-low-pri-vms.md).
