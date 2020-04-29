---
title: Åsidosätt HTTP-beteende med Azure CDN-Verizon Premium Rules-motor
description: Med regel motorn kan du anpassa hur HTTP-begäranden hanteras av Azure CDN från Verizon Premium, till exempel blockera leverans av vissa typer av innehåll, definiera en princip för cachelagring och ändra HTTP-huvuden.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253483"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Åsidosätt HTTP-beteende med Azure CDN från Verizon Premium Rules Engine

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Med motorn för Azure CDNs regler kan du anpassa hur HTTP-begäranden hanteras. Till exempel blockera leverans av vissa innehålls typer, definiera en princip för cachelagring eller ändra ett HTTP-huvud. Den här självstudien visar hur du skapar en regel som ändrar cachelagringen av CDN-tillgångar. Mer information om syntaxen för regel motorn finns i [Azure CDN regel motor referens](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Åtkomst

Om du vill komma åt regel motorn måste du först välja **Hantera** överst på sidan **CDN-profil** för att komma åt sidan Azure CDN hantering. Beroende på om din slut punkt är optimerad för DSA (Dynamic site acceleration) kommer du till regel motorn med den uppsättning regler som är lämpliga för din typ av slut punkt:

- Slut punkter som är optimerade för allmän webb leverans eller annan icke-DSA-optimering:
    
    Välj fliken **http-stor** och välj sedan **regel motor**.

    ![Regel motor för HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Slut punkter som är optimerade för DSA:
    
    Välj fliken **och** och välj sedan **regel motor**.
    
    OCH är en term som används av Verizon för att ange DSA-innehåll. Alla regler som du skapar här ignoreras av eventuella slut punkter i din profil som inte är optimerade för DSA.

    ![Regel motor för DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Självstudier

1. På sidan **CDN-profil** väljer du **Hantera**.
   
    ![Knappen Hantera CDN-profil](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Hanterings portalen för CDN öppnas.

2. Välj fliken **http-stor** och välj sedan **regel motor**.
   
    Alternativen för en ny regel visas.
   
    ![Nya regel alternativ för CDN](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > Ordningen för hur flera regler visas påverkar hur de hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   >

3. Ange ett namn i text rutan **namn/beskrivning** .

4. Identifiera vilken typ av förfrågningar som regeln gäller för. Använd standard villkoret för matchning, **alltid**.
   
   ![Villkor för CDN-regel matchning](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Det finns flera matchnings villkor i list rutan. Om du vill ha mer information om det valda matchnings villkoret väljer du den blå informations ikonen till vänster.
   >
   >  En detaljerad lista över villkors uttryck finns i [regel motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > En detaljerad lista över matchnings villkor finns i [regel motor matchnings villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Om du vill lägga till en ny funktion **+** väljer du knappen bredvid **funktioner**.  Välj **tvinga intern max ålder**i list rutan till vänster.  I text rutan som visas anger du **300**. Ändra inte återstående standardvärden.
   
   ![Funktionen CDN-regel](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Det finns flera funktioner i list rutan. Om du vill ha mer information om den markerade funktionen väljer du den blå informations ikonen till vänster.
   >
   > För **tvingande intern max ålder**åsidosätts till `Cache-Control` gångens `Expires` och Huvudenas för att styra när CDN Edge-noden uppdaterar till gången från ursprunget. I det här exemplet cachelagrar CDN Edge-noden till gången i 300 sekunder eller 5 minuter innan den uppdaterar till gången från sitt ursprung.
   >
   > En detaljerad lista över funktioner finns i [funktioner i regel motorn](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Välj **Lägg till** för att spara den nya regeln.  Den nya regeln väntar nu på godkännande. När den har godkänts ändras statusen från **väntar XML** till **aktiv XML**.
   
   > [!IMPORTANT]
   > Regel ändringar kan ta upp till 10 minuter innan de sprids via Azure CDN.
   >
   >

## <a name="see-also"></a>Se även

- [Översikt över Azure CDN](cdn-overview.md)
- [Regel motor referens](cdn-verizon-premium-rules-engine-reference.md)
- [Regel motor matchnings villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regel motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funktioner i regel motorn](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure-fredagar: Azure CDN de kraftfulla nya Premium-funktionerna](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)