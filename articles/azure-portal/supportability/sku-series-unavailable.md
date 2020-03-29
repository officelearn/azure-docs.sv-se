---
title: Regionen eller SKU-serien är inte tillgänglig - Azure
description: Vissa SKU-serier är inte tillgängliga för den valda prenumerationen för den här regionen, vilket kan kräva supportbegäran för prenumerationshantering.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843656"
---
# <a name="region-or-sku-unavailable"></a>Otillgänglig region eller SKU

I den här artikeln beskrivs hur du löser problemet med en Azure-prenumeration som inte har åtkomst till en region eller en VM SKU.

## <a name="symptoms"></a>Symtom

När du distribuerar en virtuell dator visas något av följande felmeddelanden:

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

När du köper reserverade instanser för virtuella datorer visas något av följande felmeddelanden:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

När du skapar en supportbegäran för att öka beräkningskärnkvoten är en region eller en SKU-familj inte tillgänglig för val.

## <a name="solution"></a>Lösning

Vi rekommenderar först att du överväger en alternativ region eller SKU som uppfyller dina affärsbehov.

Om du inte kan hitta en lämplig region eller SKU skapar du en [supportbegäran för](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **prenumerationshantering** enligt följande steg:

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**. Välj sedan **Ny supportbegäran**.

1. Välj **Prenumerationshantering**för **Typen Basics**. **Issue type**

1. Välj en **prenumeration** och ange en kort beskrivning i **Sammanfattning**.

   ![Fliken Grunderna i ny supportbegäran](./media/SKU-series-unavailable/support-request-basics.png)

1. För **problemtyp**väljer du **Välj problemtyp**.

1. För **Välj problemtyp**väljer du ett alternativ, **till exempel, Det går inte att komma åt min prenumeration eller resurs** > **Mitt problem visas inte ovan**. Välj **Spara**.

   ![Ange ett problem för begäran](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Välj **Nästa: Lösningar** för att utforska möjliga lösningar. Om det behövs väljer du **Nästa: Information om** att fortsätta.

1. Ange eventuell ytterligare information som du kan lämna tillsammans med dina kontaktuppgifter.

1. Välj **Granska + skapa**. När du har verifierat din information väljer du **Skapa** för att skapa begäran.

## <a name="send-us-your-suggestions"></a>Skicka oss dina förslag

Vi är alltid öppna för feedback och förslag! Skicka oss dina [förslag.](https://feedback.azure.com/forums/266794-support-feedback) Dessutom kan du samarbeta med oss på [Twitter](https://twitter.com/azuresupport) eller [MSDN forum](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Läs mer

[Vanliga frågor och svar om Azure-support](https://azure.microsoft.com/support/faq)
