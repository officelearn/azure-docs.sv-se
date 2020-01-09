---
title: Azure Event Grid – fel söknings guide
description: Den här artikeln innehåller en lista över felkoder, fel meddelanden, beskrivningar och rekommenderade åtgärder.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 1ab9aeac0bde21e229fdb57b7ad02d5d48471551
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645080"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Felsöka Azure Event Grid fel
Den här fel söknings guiden innehåller en lista över Azure Event Grid felkoder, fel meddelanden, beskrivningar och rekommenderade åtgärder som du bör vidta när du får dessa fel. 

## <a name="error-code-400"></a>Felkod: 400
| Felkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | Ämnes namnet måste vara mellan 3 och 50 tecken långt. | Namnet på den anpassade ämnes namnet ska vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i ämnes namnet. Namnet får inte heller börja med följande reserverade ord: <ul><li>Microsoft</li><li>EventGrid</li><li>System</li></ul> | Välj ett annat ämnes namn som följer namn kraven för ämnet. |
| HttpStatusCode. BadRequest<br/>400 | Domän namnet måste vara mellan 3 och 50 tecken långt. | Domän namnets längd måste vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i ämnes namnet. Namnet får inte heller börja med följande reserverade ord:<ul><li>Microsoft</li><li>EventGrid</li><li>System</li> | Välj ett annat domän namn som följer domän namns kraven. |
| HttpStatusCode. BadRequest<br/>400 | Ogiltig förfallo tid. | Förfallo tiden för händelse prenumerationen avgör när händelse prenumerationen ska tas ur bruk. Värdet måste vara ett giltigt DateTime-värde i framtiden.| Se till att förfallo tiden för händelse prenumerationen är i ett giltigt DateTime-format och att den har angetts vara i framtiden. |

## <a name="error-code-409"></a>Felkod: 409
| Felkod | Felmeddelande | Beskrivning | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Det finns redan ett ämne med det angivna namnet. Välj ett annat ämnes namn.   | Namnet på den anpassade artikeln bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för ämnet. |
| HttpStatusCode. konflikt <br/> 409 | Den angivna domänen finns redan. Välj ett annat domän namn. | Domän namnet bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för domänen. |
| HttpStatusCode. konflikt<br/>409 | Kvot gränsen har uppnåtts. Mer information om dessa gränser finns [Azure Event Grid gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Varje Azure-prenumeration har en gräns för antalet Azure Event Grid-resurser som den kan använda. En eller flera av kvoten har överskridits och inga fler resurser kunde skapas. |    Kontrol lera den aktuella resurs användningen och ta bort alla som inte behövs. Om du fortfarande behöver öka kvoten skickar du ett e-postmeddelande till [aeg@microsoft.com](mailto:aeg@microsoft.com) med det exakta antalet resurser som behövs. |


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
