---
title: Förinläsa tillgångar på en Azure CDN-slutpunkt | Microsoft Docs
description: Lär dig att i förväg ladda cachelagrat innehåll på en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mazha
ms.openlocfilehash: bf3161d756759e4b278e48ad7a49615e4a73d17f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Läsa in tillgångar för en Azure CDN-slutpunkt i förväg
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagras tillgångar bara om de är begäran. Eftersom edge-servrar inte har ännu har cachelagrats innehållet och måste vidarebefordra begäran till den ursprungliga servern, kan den första begäranden från varje region ta längre tid än efterföljande förfrågningar. Om du vill undvika det här första-träff svarstid i förväg läsa in dina tillgångar. Förutom att tillhandahålla en bättre kundupplevelse minska förinläsning dina cachelagrade tillgångar nätverkstrafik på den ursprungliga servern.

> [!NOTE]
> Förinläsning tillgångar är användbart för stora händelser eller innehåll som samtidigt tillgängliga för många användare, till exempel en ny film version eller en programuppdatering.
> 
> 

Den här självstudiekursen vägleder dig genom förinläsning cachelagrat innehåll på alla noder i Azure CDN-kant.

## <a name="to-pre-load-assets"></a>Att i förväg ladda tillgångar
1. I den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profilen som innehåller den slutpunkt som du vill läsa in före. Profil-fönstret öppnas.
    
2. Klicka på slutpunkten i listan. Slutpunkt-fönstret öppnas.
3. Fönstret CDN-slutpunkten Välj **belastningen**.
   
    ![CDN-slutpunkten fönstret](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Den **belastningen** fönstret öppnas.
   
    ![CDN belastningen fönstret](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. För **innehållssökvägen**, ange den fullständiga sökvägen för varje resurs som du vill läsa in (till exempel `/pictures/kitten.png`).
   
   > [!TIP]
   > När du börjar skriva in text, mer **innehållssökvägen** textrutor visas så att du kan skapa en lista över flera resurser. Om du vill ta bort tillgångar i listan, Välj knappen med ellipstecknet (...) och sedan **ta bort**.
   > 
   > Varje sökväg för innehåll måste vara en relativ URL som passar följande [reguljära uttryck](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > - Läsa in en enskild sökväg: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Läsa in en enstaka fil med frågesträng: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Eftersom varje tillgång måste ha sin egen sökväg, finns inga jokertecken funktioner för före inläsning tillgångar.
   > 
   > 
   
    ![Läs in](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. När du har skrivit sökvägar till innehåll, välja **belastningen**.
   

> [!NOTE]
> Det finns en begränsning på 10 begäranden om belastning per minut per CDN-profilen och 50 samtidiga sökvägar kan bearbetas i taget. Varje sökväg har en sökvägens längd på högst 1024 tecken.
> 
> 

## <a name="see-also"></a>Se också
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Azure CDN REST API-referens: före Läs in innehåll på en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API-referens: ta bort innehållet från en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

