---
title: Tjänst kvoter och-gränser – Azure Batch | Microsoft Docs
description: Lär dig mer om standard Azure Batch kvoter, gränser och begränsningar samt hur du begär kvot ökningar
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 1e7d64aabba19f04fb98bd208389dc0b2077bfe4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094701"
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är kopplade till batch-tjänsten. Många av de här gränserna är standard kvoter som används av Azure på prenumerations-eller konto nivå. I den här artikeln beskrivs dessa standardvärden och hur du kan begära kvot ökningar.

Behåll dessa kvoter i åtanke när du utformar och skalar upp dina batch-arbetsbelastningar. Om din pool till exempel inte når mål antalet datornoder som du har angett kan du ha nått kärn kvot gränsen för ditt batch-konto.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra produktions arbets belastningar i batch kan du behöva öka en eller flera av kvoterna ovanför standardvärdet. Om du vill öka en kvot kan du öppna en [support förfrågan](#increase-a-quota) online utan kostnad.

## <a name="resource-quotas"></a>Resurskvoter

En kvot är en kreditgräns, inte en garanti för kapacitet. Kontakta Azure-supporten om du har storskaliga kapacitets behov.

Observera också att kvoter inte är garanterade värden. Kvoter kan variera beroende på ändringar från batch-tjänsten eller en användar förfrågan för att ändra ett kvot värde.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kärn kvoter i användar prenumerations läge

Om du har skapat ett batch-konto med pool-allokeringsinställningar inställt på **användar prenumeration**tillämpas kvoter på olika sätt. I det här läget skapas virtuella batch-datorer och andra resurser direkt i prenumerationen när en pool skapas. Kvoterna för Azure Batch kärnor gäller inte för ett konto som skapats i det här läget. I stället tillämpas kvoterna i din prenumeration för regionala beräknings kärnor och andra resurser. Läs mer om de här kvoterna i [Azure-prenumerationer och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Storleks begränsningar för pooler

Storleks gränser för pooler anges av batch-tjänsten. Till skillnad från [resurs kvoter](#resource-quotas)kan de här värdena inte ändras. Endast pooler med kommunikation mellan noder och anpassade avbildningar har begränsningar som skiljer sig från standard kvoten.

| **Resurs** | **Övre gräns** |
| --- | --- |
| **Compute-noder i en [pool för kommunikation mellan noder](batch-mpi.md)**  ||
| Allocation mode för batch service pool | 100 |
| Pool för batch-Fakturapool | 80 |
| **Compute-noder som [skapats med anpassad VM-avbildning](batch-custom-images.md)**  <sup>1</sup> ||
| Reserverade noder | 2000 |
| Lågprioriterade virtuella noder | 1000 |

<sup>1</sup> för pooler som inte är kommunikation mellan noder aktive rad.

## <a name="other-limits"></a>Andra gränser

Ytterligare begränsningar som anges av batch-tjänsten. Till skillnad från [resurs kvoter](#resource-quotas)kan de här värdena inte ändras.

| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga aktiviteter](batch-parallel-node-tasks.md) per Compute-nod | 4 x antal Node-kärnor |
| [Program](batch-application-packages.md) per batch-konto | 20 |
| Programpaket per program | 40 |
| Programpaket per pool | 10 |
| Maximal varaktighet för aktivitet | 180 dagar<sup>1</sup> |
| [Monteringar](virtual-file-mount.md) per Compute-nod | 10 |

<sup>1</sup> den maximala livs längden för en aktivitet, från när den läggs till i jobbet till när den är klar, är 180 dagar. Slutförda uppgifter sparas i sju dagar; data för uppgifter som inte har slutförts inom den maximala livs längden är inte tillgängliga.

## <a name="view-batch-quotas"></a>Visa batch-kvoter

Visa kvoterna för batch-kontot i [Azure Portal][portal].

1. Välj **batch-konton** i portalen och välj sedan det batch-konto som du är intresse rad av.
1. Välj **kvoter** på menyn för batch-kontot.
1. Visa de kvoter som för närvarande tillämpas på batch-kontot

    ![Batch-konto-kvoter][account_quotas]

## <a name="increase-a-quota"></a>Öka en kvot

Följ dessa steg om du vill begära en kvot ökning för batch-kontot eller din prenumeration med hjälp av [Azure Portal][portal]. Vilken typ av kvot ökning som är beror på poolens fördelnings läge för batch-kontot. Om du vill begära en kvot ökning måste du ta med den VM-serien som du vill öka kvoten för. När kvot ökningen tillämpas tillämpas den på alla virtuella datorer.

### <a name="increase-cores-quota-in-batch"></a>Öka kärn kvoten i batch 

1. Välj panelen **Hjälp + Support** på portalens instrument panel eller frågetecknet ( **?** ) i det övre högra hörnet i portalen.
1. Välj **nya grundläggande support förfrågningar** > .
1. I **grunderna**:
   
    a. **Ärende typ** > **tjänst och prenumerations gränser (kvoter)**
   
    b. Välj din prenumeration.
   
    c. **Kvot typ** > **batch**
      
    Välj **Nästa**.
    
1. I **information**:
      
    a. I **Ange information**anger du plats, kvot typ och batch-konto.
    
    ![Ökning av batch-kvot][quota_increase]

    Kvot typer är:

    * **Per batch-konto**  
        Värden som är specifikt för ett enda batch-konto, inklusive dedikerade och låg prioritets kärnor samt antal jobb och pooler.
        
    * **Per region**  
        Värden som gäller för alla batch-konton i en region och innehåller antalet batch-konton per region per prenumeration.

    Kvoten med låg prioritet är ett enda värde i alla VM-serier. Om du behöver begränsade SKU: er måste du välja **låg prioritets kärnor** och inkludera VM-familjer som ska begäras.

    b. Välj en **allvarlighets grad** för ditt [företags påverkan][support_sev].

    Välj **Nästa**.

1. I **kontakt information**:
   
    a. Välj en **önskad kontakt metod**.
   
    b. Verifiera och ange nödvändig kontakt information.
   
    Välj **skapa** för att skicka in support förfrågan.

När du har skickat in ditt support ärende kontaktar Azure-supporten. Kvot begär Anden kan slutföras inom några minuter eller upp till två arbets dagar.

## <a name="related-quotas-for-vm-pools"></a>Relaterade kvoter för VM-pooler

Batch-pooler i den virtuella dator konfigurationen som distribueras i ett virtuellt Azure-nätverk allokerar automatiskt ytterligare Azure nätverks resurser. Följande resurser behövs för varje 50-pool i ett virtuellt nätverk:

* En [nätverks säkerhets grupp](../virtual-network/security-overview.md#network-security-groups)
* En [offentlig IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* En [belastningsutjämnare](../load-balancer/load-balancer-overview.md)

De här resurserna allokeras i prenumerationen som innehåller det virtuella nätverk som angavs när du skapade batch-poolen. Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-subscription-service-limits.md). Om du planerar distributioner av stora pooler i ett virtuellt nätverk kontrollerar du prenumerationens kvoter för dessa resurser. Om det behövs kan du begära en ökning av Azure Portal genom att välja **Hjälp + Support**.


## <a name="related-topics"></a>Relaterade ämnen
* [Skapa ett Azure Batch konto med hjälp av Azure Portal](batch-account-create-portal.md)
* [Översikt över Azure Batch funktioner](batch-api-basics.md)
* [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
