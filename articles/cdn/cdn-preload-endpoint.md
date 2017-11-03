---
title: "Förinläsa tillgångar på en Azure CDN-slutpunkt | Microsoft Docs"
description: "Lär dig att i förväg ladda cachelagrat innehåll på en Azure CDN-slutpunkt."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Läsa in tillgångar för en Azure CDN-slutpunkt i förväg
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagras först tillgångar när de begärs. Detta innebär att den första begäranden från varje region kan ta längre tid eftersom edge-servrar inte har innehållet cachelagras och kommer att vidarebefordra begäran till den ursprungliga servern. Den här första träffar latens undviks förinläsning innehåll.

Förutom att tillhandahålla en bättre kundupplevelse kan förinläsning dina cachelagrade tillgångar också minska nätverkstrafik på den ursprungliga servern.

> [!NOTE]
> Förinläsning tillgångar är användbart för stora händelser eller innehåll som samtidigt tillgängliga för ett stort antal användare, till exempel en ny film version eller en programuppdatering.
> 
> 

Den här självstudiekursen vägleder dig genom förinläsning cachelagrat innehåll på alla noder i Azure CDN-kant.

## <a name="walkthrough"></a>Genomgång
1. I den [Azure Portal](https://portal.azure.com), bläddra till CDN-profilen som innehåller den slutpunkt som du vill läsa in före.  Profil-blad öppnas.
2. Klicka på slutpunkten i listan.  Slutpunkten blad öppnas.
3. Klicka på Läs in från bladet CDN-slutpunkten.
   
    ![CDN-slutpunkten bladet](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Läs in blad öppnas.
   
    ![CDN belastningen bladet](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Ange den fullständiga sökvägen för varje resurs som du vill läsa in (t.ex. `/pictures/kitten.png`) i den **sökväg** textruta.
   
   > [!TIP]
   > Flera **sökväg** textrutor visas när du har angett texten så att du kan skapa en lista över flera resurser.  Du kan ta bort tillgångar i listan genom att klicka på knappen med ellipstecknet (...).
   > 
   > Sökvägar måste vara en relativ URL som passar följande [reguljärt uttryck](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > >Läsa in sökväg för en enskild fil `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >Läsa in en enstaka fil med frågesträng`@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Varje tillgång måste ha sin egen sökväg.  Det finns inga jokertecken funktioner för före inläsning tillgångar.
   > 
   > 
   
    ![Läs in](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Klicka på den **belastningen** knappen.
   
    ![Läs in](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> Det finns en begränsning på 10 belastningsförfrågningar per minut per CDN-profilen. 50 sökvägar tillåts per begäran. Varje sökväg har en sökvägens längd på högst 1024 tecken.
> 
> 

## <a name="see-also"></a>Se även
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Azure CDN REST API-referens - rensa eller i förväg läsa in en slutpunkt](https://msdn.microsoft.com/library/mt634451.aspx)

