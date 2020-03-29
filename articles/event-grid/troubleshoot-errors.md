---
title: Azure Event Grid - Felsökningsguide
description: Den här artikeln innehåller en lista över felkoder, felmeddelanden, beskrivningar och rekommenderade åtgärder.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645080"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Felsöka Fel i Azure Event Grid-fel
Den här felsökningsguiden innehåller en lista över felkoder för Azure Event Grid, felmeddelanden, deras beskrivningar och rekommenderade åtgärder som du bör vidta när du får dessa fel. 

## <a name="error-code-400"></a>Felkod: 400
| Felkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode.BadRequest<br/>400 | Ämnesnamnet måste vara mellan 3 och 50 tecken långt. | Den anpassade ämnesnamnslängden ska vara mellan 3 och 50 tecken lång. Endast alfanumeriska bokstäver, siffror och "-" tecken är tillåtna i ämnesnamnet. Namnet ska inte heller börja med följande reserverade ord: <ul><li>Microsoft</li><li>EventGrid (På ett sätt som är fallet)</li><li>System</li></ul> | Välj ett annat ämnesnamn som följer ämnesnamnskraven. |
| HttpStatusCode.BadRequest<br/>400 | Domännamnet måste vara mellan 3 och 50 tecken långt. | Domännamnets längd ska vara mellan 3 och 50 tecken lång. Endast alfanumeriska bokstäver, siffror och "-" tecken är tillåtna i ämnesnamnet. Namnet ska inte heller börja med följande reserverade ord:<ul><li>Microsoft</li><li>EventGrid (På ett sätt som är fallet)</li><li>System</li> | Välj ett annat domännamn som följer domännamnskraven. |
| HttpStatusCode.BadRequest<br/>400 | Ogiltig utgångstid. | Utgångstiden för händelseprenumerationen avgör när händelseprenumerationen går i pension. Det här värdet ska vara ett giltigt DateTime-värde i framtiden.| Kontrollera att förfallotiden för händelseprenumerationen är giltig i ett giltigt DateTime-format och att den är inställd på att vara i framtiden. |

## <a name="error-code-409"></a>Felkod: 409
| Felkod | Felmeddelande | Beskrivning | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | -------------- | 
| httpstatuscode.konflikt <br/>409 | Det finns redan ämne med det angivna namnet. Välj ett annat ämnesnamn.   | Det anpassade ämnesnamnet ska vara unikt i en enda Azure-region för att säkerställa en korrekt publiceringsåtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för ämnet. |
| httpstatuscode.konflikt <br/> 409 | Domän med det angivna finns redan. Välj ett annat domännamn. | Domännamnet ska vara unikt i en enda Azure-region för att säkerställa en korrekt publiceringsåtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för domänen. |
| httpstatuscode.konflikt<br/>409 | Kvotgränsen har nåtts. Mer information om dessa gränser finns i [Azure Event Grid-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Varje Azure-prenumeration har en gräns för antalet Azure Event Grid-resurser som den kan använda. En del av eller hela denna kvot hade överskridits och inga fler resurser kunde skapas. |    Kontrollera din nuvarande resursanvändning och ta bort alla som inte behövs. Om du fortfarande behöver öka din kvot skickar du ett e-postmeddelande till [aeg@microsoft.com](mailto:aeg@microsoft.com) med det exakta antalet resurser som behövs. |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du lägga upp ditt problem i [forumet Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna en [supportbiljett.](https://azure.microsoft.com/support/options/) 
