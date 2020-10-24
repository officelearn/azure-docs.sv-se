---
author: baanders
description: inkludera fil för att verifiera roll tilldelningen i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b4dfd154ff3fb7af48ef43b0a1dca168c5a93481
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489054"
---
Ett sätt att kontrol lera att roll tilldelningen har kon figurer ATS är att Visa roll tilldelningarna för Azures digitala dubbla instansen i [Azure Portal](https://portal.azure.com). Gå till Azure Digitals-instansen i Azure Portal (du kan titta på den på sidan med [Azure Digitals dubbla instanser](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) eller Sök efter namnet i portalens Sök fält).

Sedan kan du Visa alla tilldelade roller under *åtkomst kontroll (IAM) > roll tilldelningar*. Användaren ska visas i listan med en roll av *Azure Digitals sammanflätade data ägare*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vy över roll tilldelningarna för en digital Azure-instans i Azure Portal":::