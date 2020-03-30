---
title: Tjänstkvoter och -gränser – Azure Batch | Microsoft-dokument
description: Lär dig mer om azure batch-standardkvoter, begränsningar och begränsningar och hur du begär kvothöjningar
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: c8a78acfa1f3e7332cee337ae1a82ee365fe356a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248234"
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Precis som med andra Azure-tjänster finns det gränser för vissa resurser som är associerade med batch-tjänsten. Många av dessa gränser är standardkvoter som tillämpas av Azure på prenumerations- eller kontonivå. I den här artikeln beskrivs standardinställningarna och hur du kan begära kvothöjningar.

Tänk på dessa kvoter när du utformar och skalar upp dina batcharbetsbelastningar. Om poolen till exempel inte når det målantal beräkningsnoder som du har angett kan du ha nått kärnkvotgränsen för ditt Batch-konto.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra produktionsarbetsbelastningar i Batch kan du behöva öka en eller flera av kvoterna ovanför standardvärdet. Om du vill höja en kvot kan du öppna en [kundsupportbegäran](#increase-a-quota) online utan kostnad.

## <a name="resource-quotas"></a>Resurskvoter

En kvot är en kreditgräns, inte en kapacitetsgaranti. Om du har stora kapacitetsbehov kontaktar du Azure-supporten.

Observera också att kvoter inte är garanterade värden. Kvoter kan variera beroende på ändringar från batch-tjänsten eller en användarbegäran om att ändra ett kvotvärde.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kärnkvoter i användarprenumerationsläge

Om du har skapat ett batchkonto med poolallokeringsläge inställt på **användarprenumeration**tillämpas kvoter på olika sätt. I det här läget skapas virtuella batch-datorer och andra resurser direkt i prenumerationen när en pool skapas. Azure Batch-kärnorna gäller inte för ett konto som skapats i det här läget. I stället tillämpas kvoterna i prenumerationen för regionala beräkningskärnor och andra resurser. Läs mer om dessa kvoter i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Storleksbegränsningar för pool

Poolstorleksgränser anges av batch-tjänsten. Till skillnad från [resurskvoter](#resource-quotas)kan dessa värden inte ändras. Endast pooler med internodkommunikation och anpassade avbildningar har andra begränsningar än standardkvoten.

| **Resurs** | **Övre gräns** |
| --- | --- |
| **Beräkningsnoder i [kommunikationsaktiverad pool mellan noder](batch-mpi.md)**  ||
| Allokeringsläge för batchservicepool | 100 |
| Allokeringsläge för batchpool | 80 |
| **Beräkna noder i [poolen som skapats med en hanterad avbildningsresurs](batch-custom-images.md)**<sup>1</sup> ||
| Dedikerade noder | 2000 |
| Lågprioriterade virtuella noder | 1000 |

<sup>1</sup> För pooler som inte är kommunikation mellan nod aktiverade.

## <a name="other-limits"></a>Andra gränser

Ytterligare gränser som anges av batch-tjänsten. Till skillnad från [resurskvoter](#resource-quotas)kan dessa värden inte ändras.

| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga aktiviteter](batch-parallel-node-tasks.md) per beräkningsnod | 4 x antal nodkärnor |
| [Program](batch-application-packages.md) per batch-konto | 20 |
| Programpaket per program | 40 |
| Programpaket per pool | 10 |
| Maximal livslängd för aktiviteten | 180 dagar<sup>1</sup> |
| [Monterar per beräkningsnod](virtual-file-mount.md) | 10 |

<sup>1</sup> Den maximala livslängden för en aktivitet, från när den läggs till i jobbet till när den är klar, är 180 dagar. Slutförda uppgifter kvarstår i sju dagar; uppgifter för uppgifter som inte har slutförts inom den maximala livslängden är inte tillgängliga.

## <a name="view-batch-quotas"></a>Visa batchkvoter

Visa dina batchkontokvoter i [Azure-portalen][portal].

1. Välj **Batch-konton** i portalen och välj sedan det Batch-konto som du är intresserad av.
1. Välj **Kvoter på** batchkontots meny.
1. Visa de kvoter som för närvarande tillämpas på batchkontot

    ![Kvoter för batchkonto][account_quotas]

## <a name="increase-a-quota"></a>Öka en kvot

Följ dessa steg för att begära en kvotökning för ditt Batch-konto eller din prenumeration med Hjälp av [Azure-portalen][portal]. Vilken typ av kvotökning beror på poolallokeringsläget för ditt batchkonto. Om du vill begära en kvotökning måste du inkludera den VM-serie som du vill öka kvoten för. När kvotökningen tillämpas tillämpas den på alla serier av virtuella datorer.

### <a name="increase-cores-quota-in-batch"></a>Öka kärnornas kvot i Batch 

1. Välj **stödpanelen Hjälp +** på portalens instrumentpanel eller frågetecknet (**?**) i det övre högra hörnet av portalen.
1. Välj **Nya supportbegäran** > **Basics**.
1. I **grunderna:**
   
    a. **Problemtypstjänst** > **och prenumerationsgränser (kvoter)**
   
    b. Välj din prenumeration.
   
    c. **Batch för kvottyp** > **Batch**
      
    Välj **Nästa**.
    
1. I **Information**:
      
    a. Ange plats, kvottyp och Batch-konto i **Ange information.**
    
    ![Ökning av batchkvot][quota_increase]

    Kvottyper inkluderar:

    * **Konto per batch**  
        Värden som är specifika för ett enda batchkonto, inklusive dedikerade kärnor med låg prioritet och antal jobb och pooler.
        
    * **Per region**  
        Värden som gäller för alla batchkonton i en region och inkluderar antalet Batch-konton per region per prenumeration.

    Lågprioriterad kvot är ett enda värde i alla VM-serier. Om du behöver begränsade SKU:er måste du välja **lågprioriterade kärnor** och inkludera den virtuella datorns familjer att begära.

    b. Välj en **allvarlighetsgrad** enligt din inverkan på [verksamheten][support_sev].

    Välj **Nästa**.

1. I **kontaktinformation:**
   
    a. Välj en **önskad kontaktmetod**.
   
    b. Verifiera och ange de kontaktuppgifter som krävs.
   
    Välj **Skapa** om du vill skicka supportbegäran.

När du har skickat in din supportbegäran kontaktar Azure-supporten dig. Kvotbegäranden kan slutföras inom några minuter eller upp till två arbetsdagar.

## <a name="related-quotas-for-vm-pools"></a>Relaterade kvoter för VM-pooler

Batchpooler i konfigurationen för virtuella datorer som distribueras i ett virtuellt Azure-nätverk allokerar automatiskt ytterligare Azure-nätverksresurser. Följande resurser behövs för varje 50 poolnoder i ett virtuellt nätverk:

* En [nätverkssäkerhetsgrupp](../virtual-network/security-overview.md#network-security-groups)
* En [offentlig IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* En [belastningsutjämnare](../load-balancer/load-balancer-overview.md)

Dessa resurser allokeras i prenumerationen som innehåller det virtuella nätverket som tillhandahålls när batchpoolen skapas. Dessa resurser begränsas av prenumerationens [resurskvoter](../azure-resource-manager/management/azure-subscription-service-limits.md). Om du planerar stora pooldistributioner i ett virtuellt nätverk kontrollerar du prenumerationens kvoter för dessa resurser. Om det behövs, begär en ökning av Azure-portalen genom att välja **Hjälp + support**.


## <a name="related-topics"></a>Relaterade ämnen
* [Skapa ett Azure Batch-konto med hjälp av Azure-portalen](batch-account-create-portal.md)
* [Översikt över Azure Batch-funktioner](batch-api-basics.md)
* [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
