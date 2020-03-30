---
title: Kontrollera Azure CDN-cachelagringsbeteende med cachelagringsregler | Microsoft-dokument
description: Du kan använda CDN-cacheregler för att ange eller ändra standardlagringsbeteendet för förfallodatum för cache både globalt och med villkor, till exempel en URL-sökväg och filtillägg.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: ddd7dc7e1245c2a77e866a454bf6bfa3c1f16f88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278138"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Kontrollera funktionssättet för Azure CDN-cachelagring med cachelagringsregler

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och Azure **CDN Standard från Akamai-profiler.** För **Azure CDN från Microsoft-profiler** måste du använda [standardreglersmotorn](cdn-standard-rules-engine-reference.md) för **Azure CDN Premium från** Verizon-profiler, du måste använda [Verizon Premium-regelmotorn](cdn-rules-engine.md) i **hantera-portalen** för liknande funktioner.
 
Cdn (Azure Content Delivery Network) erbjuder två sätt att styra hur dina filer cachelagras: 

- Cachelagringsregler: I den här artikeln beskrivs hur du kan använda CDN-cachelagringsregler (Content Delivery Network) för att ange eller ändra standardlagringsbeteendet för cacheförfallodatum både globalt och med anpassade villkor, till exempel en URL-sökväg och filnamnstillägg. Azure CDN har två typer av cachelagringsregler:

   - Globala cachelagringsregler: Du kan ange en regel för globala cachelagringsregler för varje slutpunkt i din profil, vilket påverkar alla förfrågningar till slutpunkten. Den globala cachelagringsregeln åsidosätter eventuella huvuden för HTTP-cachedirektiv, om angivna.

   - Anpassade cachelagringsregler: Du kan ange en eller flera anpassade cachelagringsregler för varje slutpunkt i din profil. Anpassade cachelagringsregler matchar specifika sökvägar och filnamnstillägg, bearbetas i ordning och åsidosätter den globala cacheregeln om sådan finns. 

- Frågesträngcache: Du kan justera hur Azure CDN behandlar cachelagring för begäranden med frågesträngar. Information finns i [Kontrollera Azure CDN-cachelagring med frågesträngar](cdn-query-string.md). Om filen inte kan cachelagras har frågesträngcacheinställningen ingen effekt, baserat på cachelagringsregler och STANDARDBETEENDEn för CDN.

Information om standardcachelagringsbeteende och cachelagringsdirektivrubriker finns i Så här [fungerar cachelagring](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Komma åt Azure CDN-cachelagringsregler

1. Öppna Azure-portalen, välj en CDN-profil och välj sedan en slutpunkt.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Sidan CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Inställningar för cachelagringsbeteende
För globala och anpassade cachelagringsregler kan du ange följande inställningar **för cachelagring:**

- **Bypass-cache:** Cacheminnet cache och ignorera inte cachedirektivhuvuden som tillhandahålls av ursprunget.

- **Åsidosätt:** Ignorera cachevaraktighet för ursprung. använd den angivna cachevaraktigheten i stället. Detta åsidosätter inte cachekontroll: no-cache.

- **Ange om det saknas**: Honor-cache-direktivhuvuden som tillhandahålls av ursprung, om de finns. Annars använder du den angivna cachevaraktigheten.

![Globala cachelagringsregler](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Anpassade cachelagringsregler](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Varaktighet för förfallodatum för cache
För globala och anpassade cachelagringsregler kan du ange cachens förfallotid i dagar, timmar, minuter och sekunder:

- För **åsidosättning** och Ange om det **saknas** **inställningar för cachelagring** varierar giltiga cachevaraktighet mellan 0 sekunder och 366 dagar. För ett värde på 0 sekunder cachelagrar CDN innehållet, men måste förnya varje begäran med ursprungsservern.

- För inställningen **Bypass-cache** ställs cachevaraktigheten automatiskt in på 0 sekunder och kan inte ändras.

## <a name="custom-caching-rules-match-conditions"></a>Anpassade cachelagringsregler matchar villkoren

För anpassade cacheregler är två matchningsvillkor tillgängliga:
 
- **Sökväg**: Det här villkoret matchar url:en:s sökväg,\*exklusive domännamnet, och stöder jokerteckensymbolen ( ). Till exempel _/myfile.html_, _/my/folder/*_ och _/my/images/*.jpg_. Den maximala längden är 260 tecken.

- **Tillägg:** Det här villkoret matchar filtillägget för den begärda filen. Du kan ange en lista över kommaavgränsade filnamnstillägg som ska matchas. Till exempel _.jpg_, _.mp3_eller _.png_. Det maximala antalet tillägg är 50 och det maximala antalet tecken per tillägg är 16. 

## <a name="global-and-custom-rule-processing-order"></a>Global och anpassad regelbearbetningsorder
Globala och anpassade cachelagringsregler bearbetas i följande ordning:

- Globala cachelagringsregler har företräde framför standard-CDN-cachelagringsbeteendet (sidinställningar för HTTP-cachedirektiv). 

- Anpassade cachelagringsregler har företräde framför globala cachelagringsregler, där de gäller. Anpassade cachelagringsregler bearbetas i ordning uppifrån och ned. Det vill än om en begäran matchar båda villkoren har regler längst ned i listan företräde framför regler högst upp i listan. Därför bör du placera mer specifika regler lägre i listan.

**Exempel:**
- Global cachelagringsregel: 
   - Cachelagringsbeteende: **Åsidosätt**
   - Cache utgångstid: 1 dag

- Anpassad cachelagringsregel #1:
   - Matchningsvillkor: **Sökväg**
   - Matchvärde: _/home/*_
   - Cachelagringsbeteende: **Åsidosätt**
   - Cache utgångstid: 2 dagar

- Anpassad cachelagringsregel #2:
   - Matchningsvillkor: **Tillägg**
   - Matcha värde: _.html_
   - Cachelagringsbeteende: **Ange om det saknas**
   - Cache utgångstid: 3 dagar

När dessa regler har angetts utlöser en begäran om _ &lt;slutpunktsvärdnamn&gt;_.azureedge.net/home/index.html anpassade cachelagringsregeln #2, som är inställd **på: Ange om saknas** och 3 dagar. Om filen *index.html* har `Cache-Control` `Expires` eller HTTP-huvudena respekteras de därför. Annars, om dessa rubriker inte har angetts, cachelagras filen i 3 dagar.

> [!NOTE] 
> Filer som cachelagras innan en regeländring behåller sin varaktighetsinställning för ursprungscachen. Om du vill återställa cachevaraktigheterna måste du [rensa filen](cdn-purge-endpoint.md). 
>
> Azure CDN-konfigurationsändringar kan ta lite tid att sprida sig via nätverket: 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon-profiler** slutförs vanligtvis spridningen på 10 minuter.  
>

## <a name="see-also"></a>Se även

- [Så här fungerar cachelagring](cdn-how-caching-works.md)
- [Självstudie: Konfigurera Azures CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)
