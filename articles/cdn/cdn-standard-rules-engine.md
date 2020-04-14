---
title: Använd en regelmotor för att framtvinga HTTPS i Azure-standard-CDN | Microsoft-dokument
description: Använd regelmotorn för Azure CDN (Microsoft Standard Azure Content Delivery Network) för att anpassa hur Azure CDN hanterar HTTP-begäranden, inklusive att blockera leveransen av vissa typer av innehåll, definiera en cachelagringsprincip och ändra HTTP-huvuden. I den här artikeln kan du lära dig hur du skapar en regel för att omdirigera användare till HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259941"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Konfigurera standardregler för Azure CDN

I den här artikeln beskrivs hur du konfigurerar och använder standardreglersmotorn för Azure Content Delivery Network (Azure CDN).

## <a name="standard-rules-engine"></a>Standardregler motor

Du kan använda standardreglersmotorn för Azure CDN för att anpassa hur HTTP-begäranden hanteras. Du kan till exempel använda regelmotorn för att framtvinga innehållsleverans för att använda specifika protokoll, för att definiera en cachelagringsprincip eller för att ändra ett HTTP-huvud. Den här artikeln visar hur du skapar en regel som automatiskt omdirigerar användare till HTTPS. 

> [!NOTE]
> Regelmotorn som beskrivs i den här artikeln är endast tillgänglig för Standard Azure CDN från Microsoft. 

## <a name="redirect-users-to-https"></a>Omdirigera användare till HTTPS

1. Gå till Azure Content Delivery Network i dina Microsoft-profiler.

1. På **CDN-profilsidan** väljer du den slutpunkt som du vill skapa regler för.
  
1. Välj fliken **Regelmotor.**
   
    Fönstret **Regelmotor** öppnas och visar listan över tillgängliga globala regler. 
   
    [![Sidan Nya regler i Azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > I vilken ordning flera regler visas påverkar hur regler hanteras. De åtgärder som anges i en regel kan skrivas över av en efterföljande regel.
   >

1. Välj **Lägg till regel** och ange ett regelnamn. Regelnamn måste börja med en bokstav och får bara innehålla siffror och bokstäver.

1. Skapa ett matchningsvillkor för att identifiera vilken typ av begäranden regeln gäller:
    1. Välj **Lägg till villkor**och välj sedan villkoret Begär **protokollmatchning.**
    1. Som **Operator** väljer du **Lika med**.
    1. För **Värde**väljer du **HTTP**.
   
   [![Villkor för matchning av Azure CDN-regel](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Du kan välja mellan flera matchningsvillkor i listrutan **Lägg till villkor.** En detaljerad lista över matchningsvillkor finns [i Matcha villkor i standardreglersmotorn](cdn-standard-rules-engine-match-conditions.md).
   
1. Välj den åtgärd som ska tillämpas på de begäranden som uppfyller matchningsvillkoret:
   1. Välj **Lägg till åtgärd**och välj sedan **URL-omdirigering**.
   1. För **Typ**väljer du **Hittade (302)**.
   1. För **Protokoll** väljer du **HTTPS**.
   1. Lämna alla andra fält tomma om du vill använda inkommande värden.
   
   [![Åtgärden Azure CDN-regel](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Du kan välja mellan flera åtgärder i listrutan **Lägg till åtgärd.** En detaljerad lista över åtgärder finns [i Åtgärder i standardreglersmotorn](cdn-standard-rules-engine-actions.md).

6. Välj **Spara** om du vill spara den nya regeln. Regeln är nu tillgänglig att använda.
   
   > [!IMPORTANT]
   > Regeländringar kan ta upp till 15 minuter att sprida via Azure CDN.
   >
   

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure CDN](cdn-overview.md)
- [Referens för standardregelmotor](cdn-standard-rules-engine-reference.md)
- [Matcha villkor i standardregler motorn](cdn-standard-rules-engine-match-conditions.md)
- [Åtgärder i standardregler motorn](cdn-standard-rules-engine-actions.md)
