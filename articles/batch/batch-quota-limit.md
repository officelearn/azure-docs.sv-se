---
title: "Tjänsten kvoter och gränser för Azure Batch | Microsoft Docs"
description: "Lär dig mer om standard Azure Batch-kvoter, gränser och begränsningar och hur du begär kvoten ökar"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3f69ed8d3a985afe07e648e7512a88b25278ced
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Som med andra Azure-tjänster, det finns begränsningar på vissa resurser som är associerade med Batch-tjänsten. Många av dessa gränser är standard kvoterna som används av Azure-prenumeration eller kontonivå. Den här artikeln beskrivs dessa standardinställningar och hur du kan begära kvoten ökar.

Tänk på dessa kvoter när du utformar och skalar upp dina Batch-arbetsbelastningar. Till exempel om din pool inte når målet antalet compute-noder som du har angett, kanske du har nått core kvotgränsen för Batch-kontot eller en regionala Virtuella kärnor kvot för din prenumeration.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra produktionsarbetsbelastningar i Batch kan du behöva öka en eller flera av kvoter ovan standardvärdet. Om du vill generera en kvot kan du öppna en online [kundsupport](#increase-a-quota) utan kostnad.

> [!NOTE]
> En kvot är en kreditgräns, inte en kapacitet garanti. Kontakta Azure-supporten om du har stora kapacitetsbehov.
> 
> 

## <a name="resource-quotas"></a>Resurskvoter
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>Kvoter i användarläge för prenumeration

För Batch-kontot med poolen allokering inställd på **användarens prenumeration**, Batch-virtuella datorer och andra resurser, till exempel storage-konton, skapas direkt i din prenumeration när poolen har skapats. Azure Batch kärnor kvoten gäller inte för ett konto som har skapats i det här läget. I stället kvoter i din prenumeration för regional compute kärnor och andra resurser används. Mer information om dessa kvoter i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).

När du planerar resursanvändningen för ett konto som har skapats i användarläge för prenumerationen kan du Observera följande Batch-resurser (förutom beräkning kärnor) krävs för varje 40 virtuella Linux-datorer eller 20 virtuella Windows-datorer:

| Resurs | Kvot | Leverantör |
| --- | ---| --- |
| Ett lagringskonto | Lagringskonton | Microsoft.Storage |
| En offentlig IP-adress | Offentliga IP-adresser | Microsoft.Network | 
| Ett virtuellt nätverk | Virtuella nätverk | Microsoft.Network | 
| En nätverkssäkerhetsgrupp | Nätverkssäkerhetsgrupper | Microsoft.Network | 
| En virtuella datorns skaluppsättning | Skalningsuppsättningar för Virtual Machines | Microsoft.Compute | 
| En belastningsutjämnare | Belastningsutjämnare | Microsoft.Network | 

Kärnor kvoten på regional nivå eller per VM familj ska anges enligt VM-storlek som krävs för din Batch-pool eller pooler:

| Kvot | Leverantör |
| --- | ---- |
| Totalt antal regionala kärnor | Microsoft.Compute |
| … Family kärnor | Microsoft.Compute |



## <a name="other-limits"></a>Övriga begränsningar
| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga uppgifter](batch-parallel-node-tasks.md) per compute-nod |4 x antal nod kärnor |
| [Program](batch-application-packages.md) per Batch-kontot |20 |
| Programpaket per program |40 |
| Storlek för paketet (alla) |Uppskattat 195GB<sup>1</sup> |
| Maximal startstorlek för aktiviteten | 32768 tecken<sup>2</sup> |

<sup>1</sup> azure Storage-gränsen för högsta blob-blockstorlek<br />
<sup>2</sup> innehåller resursfiler och miljövariabler

## <a name="view-batch-quotas"></a>Visa Batch-kvoter
Visa kvoterna Batch-konto i den [Azure-portalen][portal].

1. Välj **Batch-konton** i portalen, välj sedan Batch-kontot som du är intresserad av.
2. Välj **egenskaper** på menyn bladet för Batch-kontot.
3. Egenskapsbladet visar den **kvoter** för närvarande används för Batch-kontot
   
    ![Kvoter för batch-konto][account_quotas]

Visa kvoter för relaterade prenumeration i Azure Portal för ett Batch-konto som skapats i användarläge för prenumerationen.

1. Välj **prenumerationer**, och välj den prenumeration som du använder för Batch-kontot.

2. På den **prenumeration** bladet väljer **användning + kvoter**.



## <a name="increase-a-quota"></a>Öka en kvot
Följ dessa steg för att begära en kvot öka för Batch-kontot eller din prenumeration med hjälp av den [Azure-portalen][portal]. Typ av databaskvot beror på poolen allokering läget för Batch-kontot.

### <a name="increase-a-batch-cores-quota"></a>Öka kvoten för kärnor en Batch 

Om Batch-kontot har skapats i **Batch-tjänsten** läge, Följ dessa steg för att begära en Batch kärnor kvot öka:

1. Välj den **hjälp + support** panelen på instrumentpanelen i portalen eller frågetecken (**?**) i övre högra hörnet av portalen.
2. Välj **ny supportbegäran** > **grunderna**.
3. På den **grunderna** bladet:
   
    a. **Utfärda typ** > **kvot**
   
    b. Välj din prenumeration.
   
    c. **Typ av kvot** > **Batch**
   
    d. **Supportplan** > **kvot support - ingår**
   
    Klicka på **Nästa**.
4. På den **problemet** bladet:
   
    a. Välj en **allvarlighetsgrad** enligt din [inverkan på verksamheten][support_sev].
   
    b. I **information**, ange varje kvot som du vill ändra, Batch-kontonamnet och den nya gränsen.
   
    Klicka på **Nästa**.
5. På den **kontaktinformation** bladet:
   
    a. Välj en **önskad kontaktmetod**.
   
    b. Verifiera och ange nödvändiga kontaktinformation.
   
    Klicka på **skapa** för att skicka in supportbegäran.

När du har skickat supportförfrågan Azure-supporten kommer att kontakta dig. Observera att slutföra begäran kan ta upp till 2 arbetsdagar.

### <a name="increase-a-subscription-cores-quota"></a>Öka kvoten för kärnor en prenumeration

Om Batch-kontot har skapats i **användarens prenumeration** läge och du behöver ytterligare regionala eller VM family kärnor, begäran en kvot ökar i din prenumeration. Anvisningar finns [Resource Manager kärnkvot öka begäranden](../azure-supportability/resource-manager-core-quotas-request.md).



## <a name="related-topics"></a>Relaterade ämnen
* [Skapa ett Azure Batch-konto med Azure-portalen](batch-account-create-portal.md)
* [Översikt av Azure Batch-funktion](batch-api-basics.md)
* [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG
