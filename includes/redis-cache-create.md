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
ms.openlocfilehash: 6a96bd7c3d7d02f181a7d7513edb3bb39881274f
ms.sourcegitcommit: 7de1432648c4ff3bcd09530c079418477d9f4d00
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35719446"
---
1. Om du vill skapa ett cacheminne loggar du först in på [Azure Portal](https://portal.azure.com). Välj sedan **Skapa en resurs** > **Databaser** > **Redis Cache**.

    ![Nytt cacheminne](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. I **Nytt Redis-cache**, konfigurerar du inställningarna för ditt nya cacheminne.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-namn** | Globalt unikt namn | Cachenamnet. Det måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och tecknet `-`. Cachenamnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen där den här nya Azure Redis Cache-instansen skapas. | 
    | **Resursgrupp** |  *TestResources* | Namnet på den nya resursgrupp där du vill skapa ditt cacheminne. Genom att lägga alla resurser för en app i en grupp, kan du hantera dem tillsammans. Genom att till exempel ta bort resursgruppen tar du bort alla resurser som är associerade med appen. | 
    | **Plats** | Östra USA | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som använder ditt cacheminne. |
    | **[Prisnivå](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB Cache) |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Redis Cache](../articles/redis-cache/cache-overview.md). |
    | **Fäst vid instrumentpanelen** |  Vald | Fäst det nya cacheminnet på din instrumentpanel så att det är enkelt att hitta det. |

    ![Skapa ett cacheminne](media/redis-cache-create/redis-cache-cache-create.png) 

3. När de nya cacheinställningarna har konfigurerats väljer du **Skapa**. 

    Det kan ta några minuter för cacheminnet att skapas. Du kan kontrollera statusen genom att övervaka förloppet på instrumentpanelen. När cacheminnet har skapats visas statusen **Körs** och du kan börja använda det.

    ![Cachen har skapats](media/redis-cache-create/redis-cache-cache-created.png)

