---
title: inkludera fil
description: inkludera fil
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828849"
---
1. Om du vill skapa en cache loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Välj Azure-cache för Redis.":::

   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är * \<DNS name> . Redis.cache.Windows.net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Prisnivå** | List rutan och välj en [pris nivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |

1. Välj fliken **nätverk** eller klicka på knappen **nätverk** längst ned på sidan.

1. På fliken **nätverk** väljer du anslutnings metod.

1. Välj **Nästa: fliken Avancerat** eller klicka på **Nästa: Avancerat** längst ned på sidan.

1. På fliken **Avancerat** för en Basic-eller standard-cachepost väljer du aktivera växling om du vill aktivera en icke-TLS-port.

1. På fliken **Avancerat** för Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet.

1. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

1. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

1. Välj **Granska + skapa**. Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

1. När meddelandet grön verifiering har skickats visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts**sidan för Azure-cache för Redis   . När **statusen**   är **igång**är cacheminnet redo att användas. 