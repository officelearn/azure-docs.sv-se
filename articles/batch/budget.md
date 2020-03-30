---
title: Kostnadsanalys och budget - Azure Batch
description: Lär dig hur du får en kostnadsanalys och anger en budget för underliggande beräkningsresurser och programvarulicenser som används för att köra dina batcharbetsbelastningar.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022723"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kostnadsanalys och budgetar för Azure Batch

Det finns ingen avgift för Azure Batch själv, bara de underliggande beräkningsresurser och programvarulicenser som används för att köra Batch-arbetsbelastningar. På en hög nivå uppstår kostnader från virtuella datorer (VMs) i en pool, dataöverföring från den virtuella datorn eller indata eller utdata som lagras i molnet. Låt oss ta en titt på några viktiga komponenter i Batch för att förstå var kostnaderna kommer ifrån, hur du ställer in en budget för en pool eller ett konto och några tekniker för att göra dina batcharbetsbelastningar mer kostnadseffektiva.

## <a name="batch-resources"></a>Batchresurser

Virtuella datorer är den viktigaste resursen som används för batchbearbetning. Kostnaden för att använda virtuella datorer för batch beräknas baserat på typ, kvantitet och användningstid. Alternativ för [vm-fakturering](https://azure.microsoft.com/offers/ms-azr-0003p/) inkluderar betalning per du kör eller [bokning](../cost-management-billing/reservations/save-compute-costs-reservations.md) (betala i förväg). Båda betalningsalternativen har olika fördelar beroende på din beräkningsarbetsbelastning, och båda betalningsmodellerna påverkar din faktura på olika sätt.

När program distribueras till batchnoder (VMs) med hjälp av [programpaket](batch-application-packages.md)debiteras du för de Azure Storage-resurser som programpaketen använder. Du debiteras också för lagring av indata- eller utdatafiler, till exempel resursfiler och andra loggdata. I allmänhet är kostnaden för lagringsdata som är associerade med Batch mycket lägre än kostnaden för beräkningsresurser. Varje virtuell dator i en pool som skapats med **VirtualMachineConfiguration** har en associerad OS-disk som använder Azure-hanterade diskar. Azure-hanterade diskar har en extra kostnad och andra diskprestandanivåer har också olika kostnader.

Batchpooler använder nätverksresurser. I synnerhet för **VirtualMachineConfiguration** pooler standard belastningsutjämnare används, som kräver statiska IP-adresser. De belastningsutjämnare som används av Batch visas för användarkonton för **användarprenumeration,** men visas inte för **batchtjänstkonton.** Standardbelastningsutjämnare medför avgifter för alla data som skickas till och från virtuella batchpool-datorer. Välj Batch-API:er som hämtar data från poolnoder (till exempel Hämta uppgifts-/nodfil), aktivitetsprogrampaket, resurs-/utdatafiler och behållaravbildningar medför avgifter.

### <a name="additional-services"></a>Ytterligare tjänster

Tjänster som inte inkluderar virtuella datorer och lagring kan ta hänsyn till kostnaden för ditt Batch-konto.

Andra tjänster som vanligen används med Batch kan vara:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Virtuella datorer med grafikprogram

Beroende på vilka tjänster du använder med din Batch-lösning kan du ådra dig extra avgifter. Se [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att fastställa kostnaden för varje tilläggstjänst.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kostnadsanalys och budget för en pool

Via Azure-portalen kan du skapa budgetar och utgiftsaviseringar för dina batch-pooler eller batchkonto. Budgetar och varningar är användbara för att meddela berörda parter om eventuella risker för överutnyttjande. Det är möjligt att det blir en fördröjning i utgiftsvarningar och att något överstiga en budget. I det här exemplet visar vi kostnadsanalys av en enskild batchpool.

1. I Azure-portalen väljer du **Kostnadshantering + Fakturering** i det vänstra navigeringsfältet.
1. Välj din prenumeration i avsnittet **Mina prenumerationer**
1. Gå till **Kostnadsanalys** under avsnittet **Kostnadshantering** i det vänstra navigeringsfältet, som visar en vy som denna:
1. Välj **Lägg till filter**. I den första listrutan väljer du **Resursval** ![resursfiltret](./media/batch-budget/resource-filter.png)
1. I den andra listrutan väljer du batchpoolen. När poolen är markerad ser kostnadsanalysen ut ungefär som följande analys.
    ![Kostnadsanalys av en pool](./media/batch-budget/pool-cost-analysis.png)

Den resulterande kostnadsanalysen visar kostnaden för poolen samt de resurser som bidrar till den här kostnaden. I det här exemplet är de virtuella datorer som används i poolen den mest kostsamma resursen.

Om du vill skapa en budget för poolen väljer du **Budget: ingen**och väljer sedan **Skapa ny budget >**. Använd nu fönstret för att konfigurera en budget specifikt för din pool.

Mer information om hur du konfigurerar en budget finns i [Skapa och hantera Azure-budgetar](../cost-management-billing/costs/tutorial-acm-create-budgets.md).

> [!NOTE]
> Azure Batch bygger på Azure Cloud Services och Azure Virtual Machines-teknik. När du väljer **Cloud Services-konfiguration**debiteras du baserat på molntjänsternas prisstruktur. När du väljer **Konfiguration för virtuella**datorer debiteras du baserat på prisstrukturen för virtuella datorer. I exemplet på den här sidan används **konfigurationen för den virtuella datorn**.

## <a name="minimize-cost"></a>Minimera kostnaden

Det kan vara dyrt att använda flera virtuella datorer och Azure-tjänster under längre tidsperioder. Lyckligtvis finns det tjänster tillgängliga för att minska dina utgifter, samt strategier för att maximera effektiviteten i din arbetsbelastning.

### <a name="low-priority-virtual-machines"></a>Virtuella datorer med låg prioritet

Virtuella datorer med låg prioritet minskar kostnaden för batcharbetsbelastningar genom att dra nytta av överskottsdatorkapacitet i Azure. När du anger virtuella datorer med låg prioritet i poolerna använder Batch det här överskottet för att köra din arbetsbelastning. Det finns en betydande kostnadsbesparing genom att använda lågprioriterade virtuella datorer i stället för dedikerade virtuella datorer.

Läs mer om hur du konfigurerar virtuella datorer med låg prioritet för din arbetsbelastning vid [Använd virtuella datorer med låg prioritet med Batch](batch-low-pri-vms.md).

### <a name="virtual-machine-os-disk-type"></a>Disktyp för virtuell dator

Det finns flera [VM OS-disktyper](../virtual-machines/windows/disks-types.md). De flesta VM-serier har storlekar som stöder både premium- och standardlagring. När en VM-storlek har valts för en pool konfigurerar Batch premium-SSD OS-diskar. När den "icke-s" VM-storleken väljs används den billigare, vanliga hårddisktypen. Till exempel används premium SSD OS-diskar för `Standard_D2s_v3` och vanliga `Standard_D2_v3`HDD OS-diskar används för .

Premium SSD OS-diskar är dyrare, men har högre prestanda och virtuella datorer med premiumdiskar kan starta något snabbare än virtuella datorer med vanliga HDD OS-diskar. Med Batch används OS-disken ofta inte mycket eftersom programmen och uppgiftsfilerna finns på de virtuella datorernas tillfälliga SSD-disk. Därför finns det i många fall ingen anledning att betala den ökade kostnaden för premium SSD som etableras när en 's' VM-storlek anges.

### <a name="reserved-virtual-machine-instances"></a>Reserverade instanser av virtuella datorer

Om du tänker använda Batch under en längre tid kan du spara på kostnaden för virtuella datorer genom att använda [Azure-reservationer](../cost-management-billing/reservations/save-compute-costs-reservations.md) för dina arbetsbelastningar. En bokningsgrad är betydligt lägre än en betaltaxa för er.A reservation rate is considerably lower than a pay-as-you-go rate. Instanser av virtuella datorer som används utan reservation debiteras enligt användningsbaserad betalning. Om du köper en bokning gäller reservationsrabatten och du debiteras inte längre till priserna för betalning per dun.

### <a name="automatic-scaling"></a>Automatisk skalning

[Automatisk skalning](batch-automatic-scaling.md) skalar dynamiskt antalet virtuella datorer i batchpoolen baserat på krav för det aktuella jobbet. Genom att skala poolen baserat på livslängden för ett jobb säkerställer automatisk skalning att virtuella datorer skalas upp och används endast när det finns ett jobb att utföra. När jobbet är slutfört, eller om det inte finns några jobb, skalas de virtuella datorerna automatiskt ned för att spara beräkningsresurser. Med skalning kan du sänka den totala kostnaden för batch-lösningen genom att bara använda de resurser du behöver.

Mer information om automatisk skalning finns i [Skala automatiskt beräkningsnoder i en Azure Batch-pool](batch-automatic-scaling.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [batch-API:erna och verktygen](batch-apis-tools.md) för att skapa och övervaka batchlösningar.  

- Lär dig mer om [virtuella datorer med låg prioritet med Batch](batch-low-pri-vms.md).
