---
title: Tjänsten kvoter och gränser för Azure Batch | Microsoft Docs
description: Lär dig mer om standard Azure Batch-kvoter, gränser och begränsningar och hur du begär kvoten ökar
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 19cdf81a355bae4d59cb49c1c588b995b040309b
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="batch-service-quotas-and-limits"></a>Kvoter och begränsningar för Batch-tjänsten

Som med andra Azure-tjänster, det finns begränsningar på vissa resurser som är associerade med Batch-tjänsten. Många av dessa gränser är standard kvoterna som används av Azure-prenumeration eller kontonivå. Den här artikeln beskrivs dessa standardinställningar och hur du kan begära kvoten ökar.

Tänk kvoterna som du design- och skalas upp Batch-arbetsbelastningar. Exempelvis om din pool inte nå målet antalet compute-noder som du angav, kanske du har nått kvotgränsen kärnor för Batch-kontot.

Du kan köra flera Batch-arbetsbelastningar i samma Batch-konto eller distribuera dina arbetsbelastningar mellan Batch-konton som är i samma prenumeration, men i olika Azure-regioner.

Om du planerar att köra produktionsarbetsbelastningar i Batch kan du behöva öka en eller flera av kvoter ovan standardvärdet. Om du vill generera en kvot kan du öppna en online [kundsupport](#increase-a-quota) utan kostnad.

> [!NOTE]
> En kvot är en kreditgräns, inte en kapacitet garanti. Kontakta Azure-supporten om du har stora kapacitetsbehov.
> 
> 

## <a name="resource-quotas"></a>Resurskvoter
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Kvoter för kärnor i prenumerationen användarläge

Om du har skapat ett Batch-konto med poolen allokering inställd på **användarens prenumeration**, kvoter tillämpas på olika sätt. I det här läget skapas Batch virtuella datorer och andra resurser direkt i din prenumeration när poolen har skapats. Azure Batch kärnor kvoter gäller inte för ett konto som har skapats i det här läget. I stället kvoter i din prenumeration för regional compute kärnor och andra resurser används. Mer information om dessa kvoter i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Övriga begränsningar
| **Resurs** | **Övre gräns** |
| --- | --- |
| [Samtidiga uppgifter](batch-parallel-node-tasks.md) per compute-nod |4 x antal nod kärnor |
| [Program](batch-application-packages.md) per Batch-kontot |20 |
| Programpaket per program |40 |
| Storlek för paketet (alla) |Uppskattat 195GB<sup>1</sup> |
| Maximal startstorlek för aktiviteten | 32768 tecken<sup>2</sup> |
| Maximal uppgiften livslängd | 7 dagar<sup>3</sup> |
| Compute-noder i kommunikationen mellan noder aktiverat pool | 100 |

<sup>1</sup> azure Storage-gränsen för högsta blob-blockstorlek<br />
<sup>2</sup> innehåller resursfiler och miljövariabler<br />
<sup>3</sup> högsta livstid för en aktivitet från när den har lagts till jobbet när den har slutförts är 7 dagar. Slutförda uppgifter finns kvar på obestämd tid. Data för uppgifter som inte slutförts inom den maximala livstiden är inte tillgängliga.


## <a name="view-batch-quotas"></a>Visa Batch-kvoter
Visa kvoterna Batch-konto i den [Azure-portalen][portal].

1. Välj **Batch-konton** i portalen, välj sedan Batch-kontot som du är intresserad av.
2. Välj **kvoter** på menyn för Batch-kontot.
3. Visa kvoter för närvarande används för Batch-kontot
   
    ![Kvoter för batch-konto][account_quotas]



## <a name="increase-a-quota"></a>Öka en kvot
Följ dessa steg för att begära en kvot öka för Batch-kontot eller din prenumeration med hjälp av den [Azure-portalen][portal]. Typ av databaskvot beror på poolen allokering läget för Batch-kontot.

### <a name="increase-a-batch-cores-quota"></a>Öka kvoten för kärnor en Batch 

1. Välj den **hjälp + support** panelen på instrumentpanelen i portalen eller frågetecken (**?**) i övre högra hörnet av portalen.
2. Välj **ny supportbegäran** > **grunderna**.
3. I **grunderna**:
   
    a. **Utfärda typ** > **kvot**
   
    b. Välj din prenumeration.
   
    c. **Typ av kvot** > **Batch**
   
    d. **Supportplan** > **kvot support - ingår**
   
    Klicka på **Nästa**.
4. I **problemet**:
   
    a. Välj en **allvarlighetsgrad** enligt din [inverkan på verksamheten][support_sev].
   
    b. I **information**, ange varje kvot som du vill ändra, Batch-kontonamnet och den nya gränsen.
   
    Klicka på **Nästa**.
5. I **kontaktinformation**:
   
    a. Välj en **önskad kontaktmetod**.
   
    b. Verifiera och ange nödvändiga kontaktinformation.
   
    Klicka på **skapa** för att skicka in supportbegäran.

När du har skickat supportförfrågan Azure-supporten kommer att kontakta dig. Observera att slutföra begäran kan ta upp till 2 arbetsdagar.


## <a name="related-topics"></a>Relaterade ämnen
* [Skapa ett Azure Batch-konto med Azure-portalen](batch-account-create-portal.md)
* [Översikt av Azure Batch-funktion](batch-api-basics.md)
* [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
