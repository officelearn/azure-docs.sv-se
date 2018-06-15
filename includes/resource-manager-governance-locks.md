---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29402498"
---
Resurslås användarna i din organisation av misstag tas bort eller ändra viktiga resurser. Till skillnad från rollbaserad åtkomstkontroll gäller resurslås en begränsning för alla användare och roller. 

Du kan ange låset för **CanNotDelete** eller **ReadOnly**. I portalen visas nivåerna lås som **ta bort** och **skrivskyddad** respektive.

* **CanNotDelete** innebär behöriga användare kan läsa och ändra en resurs fortfarande, men de kan inte ta bort resursen. 
* **ReadOnly** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Tillämpa den här Lås liknar begränsa alla behöriga användare till de behörigheter som utfärdats av den **Reader** roll. 

> [!TIP]
> Var försiktig när du använder en **ReadOnly** Lås. Vissa åtgärder som ser ut som att läsa operations faktiskt kräver ytterligare åtgärder. Till exempel en **ReadOnly** lås på ett lagringskonto som förhindrar att alla användare lista nycklarna. Listan med nycklar operationen hanteras via en POST-begäran eftersom returnerade nycklar är tillgängliga för skrivåtgärder. En **ReadOnly** lås på en App Service-resurs som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivåtkomst.

När du använder ett lås på en överordnad omfattning, ärver alla resurser i omfattningen samma låset. Även resurser som du senare lägger till ärver låset från överordnat. Det mest restriktiva låset i ärvda företräde.

Hanteraren för filserverresurser Lås gäller endast för åtgärder som sker i management-plan, som består av åtgärder som skickas till `https://management.azure.com`. Lås begränsa inte hur resurser bearbeta egna funktioner. Resursändringar begränsas men resursen operations är inte begränsade. Till exempel hindrar en ReadOnly-lås på en SQL-databas dig från att ta bort eller ändra databasen. Den hindra inte dig från att skapa, uppdatera eller ta bort data i databasen. Data transaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Vem kan skapa eller ta bort lås i din organisation
För att skapa eller ta bort management lås, måste du ha åtkomst till `Microsoft.Authorization/locks/*` åtgärder. I de inbyggda rollerna, endast **ägare** och **administratör för användaråtkomst** beviljas dessa åtgärder.
