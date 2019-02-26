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
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825089"
---
Resurslås hindra användare i din organisation av misstag tar bort eller ändrar viktiga resurser. Till skillnad från rollbaserad åtkomstkontroll tillämpar resurslås en begränsning för alla användare och roller. 

Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen visas Lås nivåer som **ta bort** och **skrivskyddad** respektive.

* **CanNotDelete** innebär att behöriga användare kan fortfarande läsa och ändra en resurs, men de kan inte ta bort resursen. 
* **Skrivskyddad** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Tillämpa den här Lås liknar att begränsa alla behöriga användare till de behörigheter som beviljas genom den **läsare** roll. 

> [!TIP]
> Var försiktig när du använder en **ReadOnly** Lås. Vissa åtgärder som verkar vara läsa åtgärder faktiskt kräver ytterligare åtgärder. Till exempel en **ReadOnly** låset på ett lagringskonto som förhindrar att alla användare lista nycklarna. Listan med nycklar åtgärden hanteras via en POST-begäran eftersom de returnerade nycklarna är tillgängliga för skrivåtgärder. En **ReadOnly** lås på en App Service-resurs som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivbehörighet.

När du använder ett lås på en överordnad omfattning, ärver alla resurser i detta omfång samma låset. Även resurser som du lägger till senare ärver låset från överordnat. Den mest restriktiva Lås i arvet företräde.

Resource Manager-Lås gäller endast för åtgärder som sker i Hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsa inte hur resurser bearbeta sina egna funktioner. Resursändringar är begränsade men resursåtgärder är inte begränsade. Till exempel gör ett ReadOnly-lås på en SQL Database att du inte tar bort eller ändrar databasen. Den hindra inte dig från att skapa, uppdatera eller ta bort data i databasen. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Vem som kan skapa eller ta bort lås i din organisation
För att kunna skapa eller ta bort hanteringslås måste du ha åtkomst till `Microsoft.Authorization/locks/*`-åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.
