---
title: "Åsidosätta HTTP beteende med hjälp av Azure CDN regelmotor | Microsoft Docs"
description: "Motorn regler kan du anpassa hur Azure CDN hanteras HTTP-begäranden som blockerar leveransen av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: abfe283476206b181018d187675b47112dc5ad2f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Åsidosätta HTTP beteende med hjälp av Azure CDN regelmotor
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Motorn regler kan du anpassa hur HTTP-begäranden hanteras som blockerar leveransen av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden.  Den här kursen visar att skapa en regel som ändrar cachelagringsbeteendet CDN tillgångar.  Det finns också videoinnehåll i den ”[finns också](#see-also)” avsnittet.

   > [!TIP] 
   > En referens till syntax i detalj finns [regler modulreferens](cdn-rules-engine-reference.md).
   > 


## <a name="tutorial"></a>Självstudier
1. CDN-profilbladet klickar du på den **hantera** knappen.
   
    ![CDN-profilbladet hantera knappen](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.
2. Klicka på den **HTTP stora** TABB, följt av **regelmotor**.
   
    Alternativ för en ny regel visas.
   
    ![CDN nya alternativ för regel](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Den ordning i vilken flera regler påverkar hur de ska hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   > 
   > 
3. Ange ett namn i den **namn / beskrivning** textruta.
4. Identifiera vilken typ av förfrågningar som regeln gäller för.  Som standard den **alltid** matchar villkoret är markerad.  Du kommer att använda **alltid** för den här självstudiekursen, så klicka på OK.
   
   ![CDN matchar villkoret](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!TIP]
   > Det finns många typer av matchar villkoren som är tillgängliga i listrutan.  Klicka på ikonen blå information till vänster om matchar villkoret förklarar markerade villkoret i detalj.
   > 
   >  En fullständig lista över villkorsuttryck i detalj finns [regler motorn villkorsuttryck](cdn-rules-engine-reference-match-conditions.md).
   >  
   > En fullständig lista över matchar villkoren i detalj finns [regler motorn matchar villkor](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
5. Klicka på den  **+**  knappen bredvid **funktioner** att lägga till en ny funktion.  Välj i listrutan till vänster, **kraft interna Max-ålder**.  Ange i textrutan som visas, **300**.  Lämna de återstående standardvärdena.
   
   ![CDN-funktion](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Som matchar villkoren visas klickar till vänster om den nya funktionen informationsmeddelande ikonen information om den här funktionen.  I fall av **kraft interna Max-ålder**, vi åsidosätter tillgångens **Cache-Control** och **upphör att gälla** rubriker för att styra när kantnod CDN uppdateras tillgången från ursprung.  Vårt exempel 300 sekunder innebär kantnod CDN cachelagrar tillgången i 5 minuter innan du uppdaterar tillgången från sin ursprungsplats.
   > 
   > En fullständig lista över funktioner i detalj finns [regler motorn funktionen information](cdn-rules-engine-reference-features.md).
   > 
   > 
6. Klicka på den **Lägg till** för att spara den nya regeln.  Den nya regeln är nu väntar på godkännande. När den har blivit godkänd status ändras från **väntande XML** till **Active XML**.
   
   > [!IMPORTANT]
   > Regler ändringarna kan ta upp till 90 minuter att sprida via CDN.
   > 
   > 

## <a name="see-also"></a>Se även
* [Azure CDN-översikt](cdn-overview.md)
* [Regler modulreferens](cdn-rules-engine-reference.md)
* [Regler motorn matchar villkor](cdn-rules-engine-reference-match-conditions.md)
* [Regler motorn villkorsuttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regler motorn funktioner](cdn-rules-engine-reference-features.md)
* [Åsidosätta standardbeteendet i HTTP-motorn regler](cdn-rules-engine.md)
* [Azure fredagar: Azure CDN kraftfulla nya Premiumfunktioner](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)