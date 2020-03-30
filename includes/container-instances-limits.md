---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334638"
---
| Resurs | Gräns |
| --- | :--- |
| Standardsku-behållargrupper per region per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Dedikerade sku-behållargrupper per region per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Portar per IP-adress | 5 |
| Loggstorlek för behållarförekomst – löpinstans | 4 MB |
| Loggstorlek för behållarinstans - stoppad instans | 16 KB eller 1 000 rader |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |


<sup>1.</sup> Om du vill begära en limit-ökning skapar du en [Azure Support-begäran][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
