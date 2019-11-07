---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719109"
---
1. Om du vill skapa en cache loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs**. 
   
   ![Välj Skapa en resurs](media/redis-cache-create/create-a-resource.png)
   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.
   
   ![Välj Azure-cache för Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* kommer att *\<DNS-namn >. Redis. cache. Windows. net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **prisnivå** | List rutan och välj en [pris nivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
   
1. Välj **Skapa**. 
   
   ![Skapa Azure-cache för Redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång**är cacheminnet redo att användas.
   
   ![Azure cache för Redis har skapats](media/redis-cache-create/redis-cache-cache-created.png)

