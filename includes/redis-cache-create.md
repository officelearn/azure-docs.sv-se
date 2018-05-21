---
title: ta med fil
description: ta med fil
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e035b49d9e386287baf67bba756f7b58a764acc5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
Om du vill skapa ett cacheminne loggar du först in på [Azure Portal](https://portal.azure.com) och klickar på **Skapa en resurs** > **Databaser** > **Redis Cache**.

![Nytt cacheminne](media/redis-cache-create/redis-cache-new-cache-menu.png)

I **Nytt Redis-cache**, konfigurerar du inställningarna för ditt nya cacheminne.

| Inställning      | Föreslaget värde  | Beskrivning |
| ------------ |  ------- | -------------------------------------------------- |
| **DNS-namn** | Globalt unikt namn | Cachenamnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Cachenamnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.  | 
| **Prenumeration** | Din prenumeration | Prenumerationen där det här nya Azure Redis-cacheminnet har skapats. | 
| **Resursgrupp** |  *TestResources* | Namnet på den nya resursgrupp där du vill skapa ditt cacheminne. Genom att lägga alla resurser för en app i en grupp, kan du hantera dem tillsammans. Genom att till exempel ta bort resursgruppen skulle du ta bort alla resurser som är associerade med appen. | 
| **Plats** | Östra USA | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som använder ditt cacheminne. |
| **[Prisnivå](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB Cache) |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
| **Fäst vid instrumentpanelen** |  Vald | Klicka på fäst nytt cacheminne på din instrumentpanel för att göra det enkelt att hitta det. |

![Skapa ett cacheminne](media/redis-cache-create/redis-cache-cache-create.png) 

När de nya cacheinställningarna har konfigurerats klickar du på **Skapa**. 

Det kan ta några minuter för cacheminnet att skapas. Du kan kontrollera statusen genom att övervaka förloppet på instrumentpanelen. När cachen har skapats, har ditt nya cacheminne statusen **Körs** och är redo för användning.

![Cachen har skapats](media/redis-cache-create/redis-cache-cache-created.png)

