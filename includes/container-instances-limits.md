---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 02/13/2019
ms.author: danlep
ms.openlocfilehash: f8821060b98ebfc954a6e59abad60350e6779b76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537923"
---
| Resurs | Standardgräns |
| --- | :--- |
| Containergrupper per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Portar per IP-adress | 5 |
| Container-instans loggstorleken - körningsinstans | 4 MB |
| Container-instans loggstorleken - stoppad instans | 16 KB eller 1 000 rader |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |


<sup>1</sup>för att få gränsen utökad, skapa en [Azure-supportbegäran][azure-support].<br />

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
