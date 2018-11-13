---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 44bdaec78e1fad574f29a5945b07041b588aaff8
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572774"
---
| Resurs | Standardgräns |
| --- | :--- |
| Containergrupper per [prenumeration](../articles/billing-buy-sign-up-azure-subscription.md) | 100<sup>1</sup> |
| Antal containrar per containergrupp | 60 |
| Antal volymer per containergrupp | 20 |
| Portar per IP-adress | 5 |
| Skapade containrar per timme |300<sup>1</sup> |
| Skapade containrar per 5 minuter | 100<sup>1</sup> |
| Borttagna containrar per timme | 300<sup>1</sup> |
| Borttagna containrar per 5 minuter | 100<sup>1</sup> |
| Flera containrar per containergrupp | Linux endast<sup>2</sup> |
| Azure Files-volymer | Linux endast<sup>2</sup> |
| GitRepo-volymer | Linux endast<sup>2</sup> |
| Hemliga volymer | Linux endast<sup>2</sup> |

<sup>1</sup> Skapa en [Azure-supportbegäran][azure-support] för att begära ökning av gränsen.<br />
<sup>2</sup> Windows-support för funktionen är planerad.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
