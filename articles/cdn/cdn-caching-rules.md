---
title: Kontrollera Cachelagringsbeteendet med cachelagringsregler | Microsoft Docs
description: Du kan använda CDN-nätverkets regler för att ange eller ändra standardbeteendet cachen upphör att gälla både globalt och med villkor, till exempel en URL-sökvägen och filnamnet tillägg.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: 3a94b8252feb7c5c345d678579c477fce02d6e03
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259745"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Kontrollera Cachelagringsbeteendet med cachelagringsregler

> [!NOTE] 
> Cachelagringsregler är endast tillgängliga för **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** profiler. För **Azure CDN Premium från Verizon** profiler, måste du använda den [Azure CDN regelmotor](cdn-rules-engine.md) i den **hantera** portal för liknande funktionalitet.
 
Azure Content Delivery Network (CDN) erbjuder två sätt att kontrollera hur filerna cachelagras: 

- Cachelagringsregler: Den här artikeln beskrivs hur du kan använda nätverk för innehållsleverans (CDN) cachelagringsregler för att ange eller ändra standardbeteendet cachen upphör att gälla både globalt och med anpassade villkor, till exempel en URL-sökvägen och filnamnet tillägget. Azure CDN har två typer av cachelagringsregler:

   - Globala cachelagringsregler: Du kan ange en global cachelagringsregeln för varje slutpunkt i din profil, vilket påverkar alla förfrågningar till slutpunkten. Den globala cachelagringsregeln åsidosätter eventuella huvuden för HTTP-cachedirektiv, om angivna.

   - Anpassade cachelagringsregler: Du kan ange en eller flera anpassade cachelagringsreglerna för varje slutpunkt i din profil. Anpassade cachelagringsregler matchar specifika sökvägar och filnamnstillägg, bearbetas i ordning och åsidosätter den globala cacheregeln om sådan finns. 

- Fråga efter cachelagring av frågesträngar: Du kan justera hur Azure CDN behandlar cachelagring för begäranden med frågesträngar. Mer information finns i [Kontrollera Cachelagringsbeteendet med frågesträngar](cdn-query-string.md). Om filen inte är komma har frågesträngen cachelagring inställningen ingen effekt, baserat på cachelagring av regler och standardbeteenden för CDN.

Läs om hur standard funktionssätt för cachelagring och cachelagring direktivsidhuvuden [så här fungerar cachelagring](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Åtkomst till Azure CDN-cachelagringsregler

1. Öppna Azure portal, Välj en CDN-profil och sedan väljer du en slutpunkt.

2. I det vänstra fönstret under inställningar, väljer du **Cachelagringsregler**.

   ![Knappen CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Sidan **Cachelagringsregler** visas.

   ![Sidan CDN-cachelagringsregler](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Inställningar för cachelagring beteende
Du kan ange följande för globala och anpassade cachelagringsreglerna **Cachelagringsbeteende** inställningar:

- **Kringgå cache**: Cachelagra inte och ignorera angivna ursprunget cachelagringsdirektiv.

- **Åsidosätt**: Ignorera angivna ursprunget cachevaraktighet; Använd den tillhandahållna Cachevaraktigheten i stället. Detta åsidosätter inte cache-control: no-cache.

- **Ange om saknas**: Respektera angivna ursprunget cachelagringsdirektiv, om sådana finns. Annars kan du använda angivna cachelagringens varaktighet.

![Globala cachelagringsregler](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Anpassade cachelagringsregler](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Giltighetstid för cache
Du kan ange cachens giltighetstid i dagar, timmar, minuter och sekunder för globala och anpassade cachelagringsreglerna:

- För den **åsidosätta** och **ange om saknas** **Cachelagringsbeteende** inställningar, giltigt cache varaktigheter intervallet 0 sekunder till 366 dagar. För ett värde av 0 sekunder CDN cachelagrar innehållet, men måste verifiera varje begäran med den ursprungliga servern.

- För den **kringgå cache** inställningen cachelagringens varaktighet ställs automatiskt in 0 sekunder och kan inte ändras.

## <a name="custom-caching-rules-match-conditions"></a>Anpassade cachelagringsregler matchningsvillkor

För anpassade cacheregler är två matchningsvillkor tillgängliga:
 
- **Sökväg**: Det här tillståndet matchar sökvägen till URL: en, exklusive domännamnet, och har stöd för jokertecknet (\*). Till exempel _/myfile.html_, _/min/mapp / *_, och _/my/images/*.jpg_. Maxlängden är 260 tecken.

- **Tillägget**: Det här tillståndet matchar filnamnstillägget för den begärda filen. Du kan ange en lista över kommaavgränsade filnamnstillägg som matchar. Till exempel _.jpg_, _.mp3_, eller _.png_. Det maximala antalet tillägg är 50 och det maximala antalet tecken per tillägget är 16. 

## <a name="global-and-custom-rule-processing-order"></a>Bearbetningsordningen för globala och anpassade regeln
Globala och anpassade cachelagringsreglerna bearbetas i följande ordning:

- Globala cachelagringsregler företräde framför standard CDN funktionssättet för cachelagring (inställningar för HTTP-huvud för cache-direktiv). 

- Anpassade cachelagringsregler företräde framför globala cachelagringsregler, där de tillämpas. Anpassade cachelagringsregler bearbetas i ordning från början till slutet. Det vill säga om en begäran matchar båda villkoren, högre regler längst ned i listan över prioritet än reglerna överst i listan. Därför ska du placera mer specifika regler lägre i listan.

**Exempel**:
- Global cachelagringsregeln: 
   - Funktionssättet för cachelagring: **åsidosättning**
   - Giltighetstid för cache: 1 dag

- Anpassad cachelagring regel #1:
   - Matchningsvillkor: **Sökväg**
   - Matcha värden:   _/home / *_
   - Funktionssättet för cachelagring: **åsidosättning**
   - Giltighetstid för cache: 2 dagar

- Anpassad cachelagring regel #2.
   - Matchningsvillkor: **Tillägget**
   - Matcha värden: _.html_
   - Funktionssättet för cachelagring: **Ange om saknas**
   - Giltighetstid för cache: 3 dagar

När dessa regler är inställda, en begäran om  _&lt;slutpunktens värdnamn&gt;_.azureedge.net/home/index.html utlösare anpassad cachelagring regel #2, som har angetts till: **Ange om saknas** och tre dagar. Därför, om den *index.html* filen har `Cache-Control` eller `Expires` HTTP-huvuden, de är Aviseringsfrekvens; annars, om dessa huvuden inte har angetts filen cachelagras för tre dagar.

> [!NOTE] 
> Filer som cachelagras innan en regeländring ha sitt ursprung cache-inställningen för varaktigheten. Om du vill återställa varaktigheterna cache, måste du [Rensa filen](cdn-purge-endpoint.md). 
>
> Azure CDN-konfigurationsändringar kan ta lite tid att sprida via nätverket: 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon** profiler, slutförs spridningen vanligtvis inom 10 minuter.  
>

## <a name="see-also"></a>Se också

- [Så här fungerar cachelagring](cdn-how-caching-works.md)
- [Självstudier: Konfigurera Azure CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)
