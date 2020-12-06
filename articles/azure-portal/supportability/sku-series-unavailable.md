---
title: Region eller SKU-serien är inte tillgänglig
description: Vissa SKU-serier är inte tillgängliga för den valda prenumerationen för den här regionen, vilket kan kräva support förfrågan för prenumerations hantering.
author: stevendotwang
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: 2bbe1dff6bbf8fed481300db95482c46b0e8a7b7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745289"
---
# <a name="region-or-sku-unavailable"></a>Otillgänglig region eller SKU

Den här artikeln beskriver hur du löser problemet med en Azure-prenumeration som inte har åtkomst till en region eller en VM SKU.

## <a name="symptoms"></a>Symtom

När du distribuerar en virtuell dator får du ett av följande fel meddelanden:

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

När du köper reserverade instanser av virtuella datorer får du ett av följande fel meddelanden:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

När du skapar en supportbegäran för att öka beräknings kärn kvoten är en region eller en SKU-familj inte tillgänglig för val.

## <a name="solution"></a>Lösning

Vi rekommenderar först att du betraktar en alternativ region eller SKU som uppfyller dina affärs behov.

Om du inte kan hitta en lämplig region eller SKU skapar du en [support förfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) för **prenumerations hantering** enligt följande steg:

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**. Välj sedan **ny supportbegäran**.

1. I **grunderna** väljer du **prenumerations hantering** för **typ av problem**.

1. Välj en **prenumeration** och ange en kort beskrivning i **sammanfattningen**.

   ![Fliken grunder i den nya support förfrågan](./media/SKU-series-unavailable/support-request-basics.png)

1. För **problem typ** väljer du **Välj typ av problem**.

1. I **Välj problem typ** väljer du ett alternativ, till exempel kan inte **komma åt min prenumeration eller resurs**  >  **mitt problem inte visas ovan**. Välj **Spara**.

   ![Ange ett problem för begäran](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Välj **Nästa: lösningar** för att utforska möjliga lösningar. Om det behövs väljer du **Nästa: information** att fortsätta.

1. Ange ytterligare information som du kan ange, tillsammans med din kontakt information.

1. Välj **Granska + skapa**. När du har verifierat din information väljer du **skapa** för att skapa begäran.

## <a name="send-us-your-suggestions"></a>Skicka förslag till oss

Vi är alltid öppna för feedback och förslag! Skicka oss dina [förslag](https://feedback.azure.com/forums/266794-support-feedback). Dessutom kan du engagera oss på [Twitter](https://twitter.com/azuresupport) eller på [sidan Microsoft Q&en fråga](/answers/products/azure).

## <a name="learn-more"></a>Läs mer

[Vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq)