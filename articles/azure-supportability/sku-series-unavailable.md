---
title: "SKU-serien otillgänglig | Microsoft Docs"
description: "Vissa SKU-serien är inte tillgängliga för den valda prenumerationen för den här regionen."
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>Region eller SKU är inte tillgänglig
Den här artikeln beskriver hur du löser problemet med en Azure-prenumeration inte har åtkomst till en region eller en VM-SKU.

## <a name="symptoms"></a>Symtom

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>När du distribuerar en virtuell dator, visas något av följande felmeddelanden:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>När du köper reserverade instanser för virtuella datorer, visas något av följande felmeddelanden:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>När du skapar en supportförfrågan för att öka beräknings-kärnkvot är inte en region eller en SKU-familjen kan väljas.

## <a name="solution"></a>Lösning
Först rekommenderar vi att du överväger en annan region eller SKU som uppfyller dina affärsbehov. Om det inte går att hitta en lämplig region eller SKU skapa ”prenumerationshantering” [supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) följa stegen nedan:


- Välj typ av problem som ”prenumerationshantering” på sidan grunderna, Välj prenumerationen och klicka på ”Nästa”.

![Bladet Grundläggande inställningar](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Välj typ av Problem som ”andra allmänna frågor” på sidan Problem.
- I avsnittet information:
  - Ange om du vill ha för att distribuera virtuella datorer eller köper reserverade virtuella instanser
  - Ange regionen, SKU och antalet instanser för virtuella datorer som du planerar att distribuera eller köpa


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Ange din kontaktinformation och klicka på ”Skapa”.

![Kontaktinformation](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Feedback
Vi är alltid öppna för feedback och förslag! Skicka oss dina [förslag](https://feedback.azure.com/forums/266794-support-feedback). Dessutom kan du kan interagera med oss via [Twitter](https://twitter.com/azuresupport) eller [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Läs mer
[Azure-supporten vanliga frågor och svar](https://azure.microsoft.com/support/faq)

