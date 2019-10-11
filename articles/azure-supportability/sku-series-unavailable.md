---
title: SKU-serien är inte tillgänglig | Microsoft Docs
description: Vissa SKU-serier är inte tillgängliga för den valda prenumerationen för den här regionen.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249044"
---
# <a name="region-or-sku-unavailable"></a>Otillgänglig region eller SKU
Den här artikeln beskriver hur du löser problemet med en Azure-prenumeration som inte har åtkomst till en region eller en VM SKU.

## <a name="symptoms"></a>Symtom

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>När du distribuerar en virtuell dator får du ett av följande fel meddelanden:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>När du köper reserverade instanser av virtuella datorer får du ett av följande fel meddelanden:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>När du skapar en supportbegäran för att öka beräknings kärn kvoten är en region eller en SKU-serie inte tillgänglig för urvalet.

## <a name="solution"></a>Lösning
Vi rekommenderar först att du betraktar en alternativ region eller SKU som uppfyller dina affärs behov. Om du inte kan hitta en lämplig region eller SKU skapar du en [support förfrågan](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) för prenumerations hantering enligt stegen nedan:


- På sidan grundläggande väljer du ärende typ som "prenumerations hantering", väljer prenumerationen och klickar på Nästa.

![Bladet Grundläggande inställningar](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   På sidan problem väljer du problem typen som "andra allmänna frågor".
- I avsnittet information:
  - Ange om du vill distribuera virtuella datorer eller köpa reserverade instanser av virtuella datorer
  - Ange region, SKU och antalet virtuella dator instanser som du planerar att distribuera eller köpa


![Problem](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Ange din kontakt information och klicka på "skapa".

![Kontaktinformation](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Feedback
Vi är alltid öppna för feedback och förslag! Skicka oss dina [förslag](https://feedback.azure.com/forums/266794-support-feedback). Dessutom kan du delta i oss via [Twitter](https://twitter.com/azuresupport) eller MSDN- [Forum](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Läs mer
[Vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq)

