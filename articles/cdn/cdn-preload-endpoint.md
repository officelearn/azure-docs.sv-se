---
title: Förinläsning av resurser på en Azure CDN-slutpunkt | Microsoft-dokument
description: Lär dig hur du förinläsning av cachelagrat innehåll på en Azure CDN-slutpunkt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d91507ad2cb271b23b588ef7da88e6e6712915b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593587"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Läsa in tillgångar för en Azure CDN-slutpunkt i förväg
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Som standard cachelagras tillgångar endast när de begärs. Eftersom kantservrarna ännu inte har cachelagrat innehållet och behöver vidarebefordra begäran till ursprungsservern kan den första begäran från varje region ta längre tid än efterföljande begäranden. För att undvika den här svarstiden för första träffen, förinläsning av dina tillgångar. Förutom att ge en bättre kundupplevelse kan förinläsning av dina cachelagrade tillgångar minska nätverkstrafiken på ursprungsservern.

> [!NOTE]
> Förinläsning av resurser är användbart för stora händelser eller innehåll som blir tillgängligt samtidigt för många användare, till exempel en ny filmutgåva eller en programuppdatering.
> 
> 

Den här självstudien går igenom förinläsning av cachelagrat innehåll på alla Azure CDN-kantnoder.

## <a name="to-pre-load-assets"></a>Så här förinläsning av tillgångar
1. I [Azure-portalen](https://portal.azure.com)bläddrar du till CDN-profilen som innehåller den slutpunkt som du vill förinläsa. Profilfönstret öppnas.
    
2. Klicka på slutpunkten i listan. Slutpunktsfönstret öppnas.
3. Välj **Läs in**i cdn-slutpunktsfönstret .
   
    ![Fönstret CDN-slutpunkt](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    **Fönstret Läs in** öppnas.
   
    ![Inläsningsfönstret för CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. För **sökväg innehåll**anger du hela sökvägen för varje `/pictures/kitten.png`tillgång som du vill läsa in (till exempel ).
   
   > [!TIP]
   > När du har börjat skriva text visas fler textrutor **för innehållssökväg** så att du kan skapa en lista med flera resurser. Om du vill ta bort tillgångar från listan markerar du knappen ellips (...) och väljer sedan **Ta bort**.
   > 
   > Varje innehållssökväg måste vara en relativ URL som passar följande [reguljära uttryck:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Läs in en enda filsökväg:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Läsa in en enskild fil med frågesträng:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Eftersom varje tillgång måste ha en egen sökväg finns det ingen jokerteckenfunktion för förinläsning av tillgångar.
   > 
   > 
   
    ![Knappen Läs in](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. När du har angett innehållssökvägar väljer du **Läs in**.
   

> [!NOTE]
> Det finns en gräns på 10 belastningsbegäranden per minut per CDN-profil och 50 samtidiga sökvägar kan bearbetas samtidigt. Varje bana har en väglängdsgräns på 1024 tecken.
> 
> 

## <a name="see-also"></a>Se även
* [Rensa en Azure CDN-slutpunkt](cdn-purge-endpoint.md)
* [Azure CDN REST API-referens: Förinläsning av innehåll på en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API-referens: Rensa innehåll från en slutpunkt](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

