---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: dd5c8878ce71b49b3a25f5d14a00bfe4f49ee769
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333755"
---
| Resurs | Standardgräns |
| --- | :--- |
| Containergrupper per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Portar per IP-adress | 5 |
| Container-instans loggstorleken - körningsinstans | 4 MB |
| Container-instans loggstorleken - stoppad instans | 16 KB eller 1000 rader |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |


<sup>1</sup> Skapa en [Azure-supportbegäran][azure-support] för att begära ökning av gränsen.<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
