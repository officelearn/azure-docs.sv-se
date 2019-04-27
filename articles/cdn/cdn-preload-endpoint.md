---
title: Läsa in tillgångar för en Azure CDN-slutpunkt i förväg | Microsoft Docs
description: Lär dig hur du Förhandsladda cachelagrat innehåll på en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: 0d34985c8d83e8adad43aeec36ead939d8b22132
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627427"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Läsa in tillgångar för en Azure CDN-slutpunkt i förväg
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagras tillgångar endast när de har begärts. Eftersom edge-servrar inte har ännu har cachelagrats innehållet och måste vidarebefordra begäran till den ursprungliga servern, kan det ta längre tid än efterföljande förfrågningar den första begäran från varje region. För att undvika den här första träff fördröjningen Förhandsladda dina tillgångar. Förutom att tillhandahålla en bättre kundupplevelse minska förinläsning dina cachelagrade tillgångar nätverkstrafiken på den ursprungliga servern.

> [!NOTE]
> Förinläsning tillgångar är användbart för stora evenemang eller innehåll som blir samtidigt tillgänglig för många användare, till exempel en ny film-version eller en programuppdatering.
> 
> 

Den här självstudien vägleder dig genom förinläsning cachelagrat innehåll på alla Azure CDN-gränsnoder.

## <a name="to-pre-load-assets"></a>Att läsa in tillgångar i förväg
1. I den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profil som innehåller den slutpunkt som du vill läsa in i förväg. Profil-fönstret öppnas.
    
2. Klicka på slutpunkten i listan. Slutpunkt-fönstret öppnas.
3. Fönstret för CDN-slutpunkt Välj **belastningen**.
   
    ![Fönstret för CDN-slutpunkt](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Den **belastningen** öppnas fönstret.
   
    ![Fönstret för CDN-belastning](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. För **innehållssökvägen**, ange den fullständiga sökvägen för varje resurs som du vill läsa in (till exempel `/pictures/kitten.png`).
   
   > [!TIP]
   > När du har startat textinmatning, mer **innehållssökvägen** textrutor visas så att du kan skapa en lista över flera tillgångar. Om du vill ta bort tillgångar i listan, Välj knappen med tre punkter (...) och välj sedan **ta bort**.
   > 
   > Varje sökväg till innehåll måste vara en relativ URL som passar följande [reguljära uttryck](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Läs in en enskild sökväg: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Läs in en enstaka fil med frågesträng: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Eftersom varje tillgång måste ha sin egen sökväg, finns det inga jokertecken funktioner för före inläsning av tillgångar.
   > 
   > 
   
    ![Läs in](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. När du är klar att ange sökvägar till innehåll, Välj **belastningen**.
   

> [!NOTE]
> Det finns en gräns på 10 belastningen begäranden per minut per CDN-profil och 50 samtidiga sökvägar kan bearbetas i taget. Varje sökväg har en begränsning för sökväg längd på 1024 tecken.
> 
> 

## <a name="see-also"></a>Se också
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Azure CDN REST API-referens: Förhandsladda innehåll på en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API-referens: Rensa innehåll från en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

