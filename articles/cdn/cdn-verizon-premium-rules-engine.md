---
title: Åsidosätt HTTP-beteende med Azure CDN - Verizon Premium regelmotor
description: Med regelmotorn kan du anpassa hur HTTP-begäranden hanteras av Azure CDN från Verizon Premium, till exempel blockera leveransen av vissa typer av innehåll, definiera en cachelagringsprincip och ändra HTTP-huvuden.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 2e5e4265f30631f8e68d8f9e7156ea578ae85e7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253483"
---
# <a name="override-http-behavior-using-the-azure-cdn-from-verizon-premium-rules-engine"></a>Åsidosätt HTTP-beteende med hjälp av Azure CDN från Verizon Premium regelmotor

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Översikt

Med Azure CDN-regelmotorn kan du anpassa hur HTTP-begäranden hanteras. Till exempel blockerar leveransen av vissa innehållstyper, definierar en cachelagringsprincip eller ändrar ett HTTP-huvud. Den här självstudien visar hur du skapar en regel som ändrar cachelagringsbeteendet för CDN-tillgångar. Mer information om regelmotorsyntaxen finns i [Azure CDN-regler motorreferens](cdn-verizon-premium-rules-engine-reference.md).

## <a name="access"></a>Åtkomst

För att komma åt regelmotorn måste du först välja **Hantera** högst upp på **CDN-profilsidan** för att komma åt sidan Azure CDN-hantering. Beroende på om slutpunkten är optimerad för dynamisk platsacceleration (DSA) kommer du sedan åt regelmotorn med den uppsättning regler som är lämpliga för din typ av slutpunkt:

- Slutpunkter som är optimerade för allmän webbleverans eller annan icke-DSA-optimering:
    
    Välj fliken **HTTP Stor** och välj sedan **Regelmotor**.

    ![Regelmotor för HTTP](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- Slutpunkter optimerade för DSA:
    
    Välj fliken **ADN** och välj sedan **Regelmotor**.
    
    ADN är en term som används av Verizon för att ange DSA-innehåll. Alla regler som du skapar här ignoreras av alla slutpunkter i din profil som inte är optimerade för DSA.

    ![Regler motor för DSA](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Självstudier

1. Välj **Hantera**på **CDN-profilsidan** .
   
    ![Knappen Hantera CDN-profil](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    CDN-hanteringsportalen öppnas.

2. Välj fliken **HTTP Stor** och välj sedan **Regelmotor**.
   
    Alternativen för en ny regel visas.
   
    ![Alternativ för nya CDN-regler](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > I vilken ordning flera regler visas påverkar hur de hanteras. En efterföljande regel kan åsidosätta de åtgärder som anges av en tidigare regel.
   >

3. Ange ett namn i textrutan **Namn/Beskrivning.**

4. Identifiera vilken typ av begäranden regeln gäller för. Använd standardmatchningsvillkoret **Alltid**.
   
   ![VILLKOR för CDN-regelmatchning](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Flera matchningsvillkor är tillgängliga i listrutan. Om du vill ha information om det valda matchningsvillkoret väljer du den blå informationsikonen till vänster.
   >
   >  En detaljerad lista över villkorsuttryck finns i [Villkorsuttryck för motorns inställningar](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >  
   > En detaljerad lista över matchningsvillkor finns i [Regler motormatchningsvillkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md).
   >
   >

5. Om du vill lägga **+** till en ny funktion markerar du knappen bredvid **Funktioner**.  I listrutan till vänster väljer du **Tvinga till intern maxålder**.  Skriv **300**i textrutan som visas . Ändra inte de återstående standardvärdena.
   
   ![Funktionen CDN-regel](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Flera funktioner är tillgängliga i listrutan. Om du vill ha information om den markerade funktionen väljer du den blå informationsikonen till vänster.
   >
   > För **Force Internal Max-Age**åsidosätts tillgångens `Cache-Control` och `Expires` rubrikerna för att styra när CDN-kantnoden uppdaterar tillgången från ursprunget. I det här exemplet cachelagrar CDN-kanten noden tillgången i 300 sekunder, eller 5 minuter, innan den uppdaterar tillgången från dess ursprung.
   >
   > En detaljerad lista över funktioner finns i [Regler motorfunktioner](cdn-verizon-premium-rules-engine-reference-features.md).
   >
   >

6. Välj **Lägg till** om du vill spara den nya regeln.  Den nya regeln väntar nu på godkännande. När statusen har godkänts ändras statusen från **Väntande XML** till **Aktiv XML**.
   
   > [!IMPORTANT]
   > Regeländringar kan ta upp till 10 minuter att sprida via Azure CDN.
   >
   >

## <a name="see-also"></a>Se även

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för regler motor](cdn-verizon-premium-rules-engine-reference.md)
- [Regler motor match villkor](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regler motor villkorliga uttryck](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regler motorfunktioner](cdn-verizon-premium-rules-engine-reference-features.md)
- [Azure Fridays: Azure CDN:s kraftfulla nya premiumfunktioner](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (video)