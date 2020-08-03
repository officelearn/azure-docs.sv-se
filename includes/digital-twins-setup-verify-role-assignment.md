---
author: baanders
description: inkludera fil för att verifiera roll tilldelningen i installations programmet för Azure Digitals
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405603"
---
Ett sätt att kontrol lera att roll tilldelningen har kon figurer ATS är att Visa roll tilldelningarna för Azures digitala dubbla instansen i [Azure Portal](https://portal.azure.com). Gå till portalens sida med [Azure Digitals dubbla instanser](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (du kan använda den här länken eller Sök efter den i portalens sökfält) och välja namnet på den instans som du vill kontrol lera. 

Sedan kan du Visa alla tilldelade roller under *åtkomst kontroll (IAM) > roll tilldelningar*. Användaren ska visas i listan med en roll av *Azure Digitals flätade ägare (för hands version)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vy över roll tilldelningarna för en digital Azure-instans i Azure Portal":::