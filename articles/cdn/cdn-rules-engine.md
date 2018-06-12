---
title: Åsidosätta HTTP beteende med hjälp av Azure CDN regelmotor | Microsoft Docs
description: Motorn regler kan du anpassa hur Azure CDN hanteras HTTP-begäranden som blockerar leveransen av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: df8114aaf5b4672ea51482978abde6f0ce724528
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261057"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Åsidosätta HTTP beteende med hjälp av Azure CDN regelmotor
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt
Azure CDN regelmotor kan du anpassa hur HTTP-begäranden ska hanteras. Till exempel blockerar leveransen av vissa typer av innehåll, definiera en princip för cachelagring eller ändra ett HTTP-huvud. Den här kursen visar hur du skapar en regel som ändrar CDN tillgångar cachelagring beteende. Mer information om syntaxen som motorn regler finns [Azure CDN regler motorn referens](cdn-rules-engine-reference.md).

## <a name="access"></a>Access
För att komma åt regelmotor, måste du först välja **hantera** från början av den **CDN-profilen** sidan för att komma åt sidan för hantering av Azure CDN. Beroende på om slutpunkten är optimerad för dynamiska acceleration (DSA), du sedan komma åt regelmotor med uppsättningen regler som är lämpliga för din typ av slutpunkt:

- Slutpunkter som är optimerade för allmänna webben eller andra icke-DSA-optimering: 
    
    Välj den **HTTP stora** fliken och markera sedan **regelmotor**.

    ![Regelmotor för HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Slutpunkter som är optimerade för DSA: 
    
    Välj den **ADN** fliken och markera sedan **regelmotor**. 
    
    ADN är en term som används av Verizon för att ange DSA-innehåll. Alla regler som du skapar här ignoreras av alla slutpunkter i din profil som inte är optimerade för DSA. 

    ![Regelmotor för DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Självstudier
1. Från den **CDN-profilen** väljer **hantera**.
   
    ![CDN-profilen hantera knappen](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.

2. Välj den **HTTP stora** fliken och markera sedan **regelmotor**.
   
    Alternativ för en ny regel visas.
   
    ![CDN nya alternativ för regel](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Den ordning i vilken flera regler påverkar hur de ska hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   > 

3. Ange ett namn i den **namn / beskrivning** textruta.

4. Identifiera vilken typ av förfrågningar som regeln gäller för. Använda standard matchar villkoret, **alltid**. 
   
   ![CDN matchar regelvillkor](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Flera matchar villkor är tillgängliga i den nedrullningsbara listan. För information om det markerade matchar villkoret, markerar du informationsmeddelande ikonen till vänster.
   > 
   >  En detaljerad lista över villkorsuttryck finns [regler motorn villkorsuttryck](cdn-rules-engine-reference-match-conditions.md).
   >  
   > En detaljerad lista över villkor för matchning finns [regler motorn matchar villkor](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Om du vill lägga till en ny funktion, Välj den **+** knappen bredvid **funktioner**.  Välj i listrutan till vänster, **kraft interna Max-ålder**.  Ange i textrutan som visas, **300**. Ändra inte de återstående standardvärdena.
   
   ![Funktionen för CDN-regel](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Flera funktioner är tillgängliga i den nedrullningsbara listan. Välj information ikonen till vänster för information om den valda funktionen. 
   >
   > För **kraft interna Max-ålder**, tillgångens `Cache-Control` och `Expires` huvuden åsidosätts för att styra när kantnod CDN uppdaterar tillgången från ursprunget. I det här exemplet cachelagrar kantnod CDN tillgången i 300 sekunder eller 5 minuter innan den uppdaterar tillgången från sin ursprungsplats.
   > 
   > En detaljerad lista över funktioner finns i [regler motorn funktioner](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Välj **Lägg till** att spara den nya regeln.  Den nya regeln är nu väntar på godkännande. När den har blivit godkänd status ändras från **väntande XML** till **Active XML**.
   
   > [!IMPORTANT]
   > Regler ändringarna kan ta upp till 10 minuter att sprida via Azure CDN.
   > 
   > 

## <a name="see-also"></a>Se också
* [Översikt över Azure CDN](cdn-overview.md)
* [Regelmotor – referens](cdn-rules-engine-reference.md)
* [Regelmotor – matchningsvillkor](cdn-rules-engine-reference-match-conditions.md)
* [Regelmotor – villkorliga uttryck](cdn-rules-engine-reference-conditional-expressions.md)
* [Regelmotor – funktioner](cdn-rules-engine-reference-features.md)
* [Azure fredagar: Azure CDN kraftfulla nya premiumfunktioner](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)