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
ms.openlocfilehash: f059f23031c2cdd74daaa856213d7e06f87dc27c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273927"
---
1. Om du vill skapa ett cacheminne loggar du först in på [Azure Portal](https://portal.azure.com). Välj sedan **Skapa en resurs** > **Databaser** > **Azure Cache for Redis**.

    ![Ny Azure cache för Redis-menyn](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. I **Ny Azure Cache for Redis** konfigurerar du inställningarna för den nya cachen.

    | Inställning      | Föreslaget värde  | Beskrivning |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-namn** | Globalt unikt namn | Cachenamnet. Det måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och tecknet `-`. Cachenamnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.  | 
    | **Prenumeration** | Din prenumeration | Den prenumeration där den här nya Azure Cache for Redis-instansen skapas. | 
    | **Resursgrupp** |  *TestResources* | Namnet på den nya resursgrupp där du vill skapa ditt cacheminne. Genom att lägga alla resurser för en app i en grupp, kan du hantera dem tillsammans. Genom att till exempel ta bort resursgruppen tar du bort alla resurser som är associerade med appen. | 
    | **Location** | East US | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som använder ditt cacheminne. |
    | **[Prisnivå](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB Cache) |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md). |
    | **Fäst vid instrumentpanelen** |  Valda | Fäst det nya cacheminnet på din instrumentpanel så att det är enkelt att hitta det. |

    ![Skapa Azure-cache för Redis](media/redis-cache-create/redis-cache-cache-create.png) 

3. När de nya cacheinställningarna har konfigurerats väljer du **Skapa**. 

    Det kan ta några minuter för cacheminnet att skapas. Du kan kontrollera statusen genom att övervaka förloppet på instrumentpanelen. När cacheminnet har skapats visas statusen **Körs** och du kan börja använda det.

    ![Azure cache för Redis har skapats](media/redis-cache-create/redis-cache-cache-created.png)

