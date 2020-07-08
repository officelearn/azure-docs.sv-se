---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: 33a82a55b03cde79d2d80826041ee6d43565476a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334638"
---
| Resurs | Gräns |
| --- | :--- |
| Standard-SKU behållar grupper per region per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Dedikerade SKU container Groups per region per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 0<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Portar per IP-adress | 5 |
| Logg storlek för container instans-instans som körs | 4 MB |
| Logg storlek för container instans-stoppad instans | 16 KB-eller 1 000-linjer |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |


<sup>1</sup> Skapa ett [Azure-supportbegäran][azure-support]för att begära en gräns ökning.<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
