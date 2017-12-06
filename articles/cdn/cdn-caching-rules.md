---
title: "Kontrollera Azure Content Delivery Network cachelagring av frågesträngar med cachelagring regler | Microsoft Docs"
description: "Du kan använda CDN cachelagring regler för att ange eller ändra standardbeteendet cache giltighetstid både globalt och med villkor, till exempel en URL-sökväg och filnamn tillägg."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 8f89ef5a1763d5fc4ad09a9aeae89ccf683138c7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>Kontrollen Azure Content Delivery Network cachelagring av frågesträngar med cachelagring regler

> [!NOTE] 
> Regler för cachelagring är bara tillgängliga för **Azure CDN från Verizon Standard** och **Azure CDN från Akamai Standard**. För **Azure CDN från Verizon Premium**, du kan använda den [Azure CDN regelmotor](cdn-rules-engine.md) i den **hantera** portalen för liknande funktionalitet.
 
Azure Content Delivery Network finns två sätt att styra hur dina filer cachelagras: 

- Cachelagring regler: den här artikeln beskriver hur du kan använda innehållsleveransnätverk (CDN) cachelagring regler för att ange eller ändra standardbeteendet cache giltighetstid både globalt och med anpassade villkor, till exempel ett URL-sökväg och filnamn tillägg. Azure CDN finns två typer av cachelagring regler:
   - Global cachelagring regler: du kan ange en regel för globala cachelagring för varje slutpunkt i din profil, vilket påverkar alla förfrågningar till slutpunkten. Den globala cacheregeln åsidosätter eventuella cache-direktiv HTTP-huvuden om ange.
   - Anpassade regler för cachelagring: du kan ange en eller flera anpassade cachelagringsregler för varje slutpunkt i din profil. Ange anpassade cachelagring regler matchar specifika sökvägar filnamnstillägg, bearbetas i ordning och åsidosätta den globala cacheregeln om. 

- Cachelagring av frågesträngar i frågan: du kan ändra hur Azure CDN behandlar cachelagring för begäranden med frågesträngar. Mer information finns i [kontroll Azure CDN cachelagring av frågesträngar med frågesträngar](cdn-query-string.md). Om filen inte är Cacheable ställs har frågesträngen cachelagring inställningen ingen effekt, baserat på cachelagring regler och standardbeteenden för CDN.

Information om standard cachelagring direktivet sidhuvuden och funktionssätt för cachelagring finns [hur cachelagring fungerar](cdn-how-caching-works.md).

## <a name="tutorial"></a>Självstudier

Hur du ställer in CDN cachelagring regler:

1. Öppna Azure portal, Välj en CDN-profil och sedan väljer du en slutpunkt.
2. I det vänstra fönstret under inställningar klickar du på **Cache**.
3. Skapa en global cachelagring regel enligt följande:
   1. Under **Global cachelagring regler**, ange **cachelagring av frågesträngar** till **Ignorera frågesträngar**.
   2. Ange **cachelagring av frågesträngar** till **om saknas**.
   3. För **cachelagra giltighetstiden**, skriver du 10 i den **dagar** fältet.

       Den globala cacheregeln påverkar alla förfrågningar till slutpunkten. Den här regeln godkänner ursprung cache-direktiv sidhuvud, om de finns (`Cache-Control` eller `Expires`), annars, om de inte har angetts, anger cachen på 10 dagar. 

4. Skapa en anpassad regel för cachelagring enligt följande:
    1. Under **anpassad cachelagring regler**, ange **matchar villkoret** till **sökväg** och **matchar värdet** till `/images/*.jpg`.
    2. Ange **cachelagring av frågesträngar** till **åsidosätta** och ange 30 i den **dagar** fältet.
       
       Den här anpassade cacheregeln anger cache varaktighet på 30 dagar på någon `.jpg` bildfiler i den `/images` mappen för din slutpunkt. Den åsidosätter eventuella `Cache-Control` eller `Expires` HTTP-huvuden som skickas av den ursprungliga servern.

  ![Dialogrutan för cachelagring regler](./media/cdn-caching-rules/cdn-caching-rules-dialog.png)

> [!NOTE] 
> Filer som cachelagras innan en ändring för regeln behåller sitt ursprung cache-inställningen för varaktighet. Om du vill återställa sina cache varaktighet, måste du [Rensa filen](cdn-purge-endpoint.md). För **Azure CDN från Verizon** slutpunkter kan det ta upp till 90 minuter för cachelagring av regler ska börja gälla.

## <a name="reference"></a>Referens

### <a name="caching-behavior-settings"></a>Inställningar för cachelagring beteende
För global och anpassade cachelagring regler, kan du ange följande **cachelagring av frågesträngar** inställningar:

- **Kringgå cache**: cachelagra inte och ignorera angivna ursprung cache-direktiv huvuden.
- **Åsidosätt**: ignorera angivna ursprung cache-direktiv huvuden; Använd angivna cachelagringens varaktighet i stället.
- **Ange om de saknas**: Kontrollera om ursprung cache-direktiv sidhuvud, om sådana finns; annars använder angivna cachelagringens varaktighet.

### <a name="cache-expiration-duration"></a>Giltighetstid för cache
Du kan ange cache giltighetstiden för global och anpassade cachelagring regler, i dagar, timmar, minuter och sekunder:

- För den **åsidosätta** och **om saknas** **cachelagring av frågesträngar** inställningar, giltig cache varaktigheter intervallet mellan 0 sekunder och 366 dagar. För ett värde av 0 sekunder CDN cachelagrar innehållet, men måste verifiera varje begäran med den ursprungliga servern.
- För den **kringgå cache** inställningen cachelagringens varaktighet automatiskt till 0 sekunder och kan inte ändras.

### <a name="custom-caching-rules-match-conditions"></a>Anpassade cachelagringsregler matchar villkor

Två matchar villkor är tillgängliga för anpassade cache regler:
 
- **Sökvägen**: det här tillståndet matchar sökvägen för URL-Adressen, exklusive domännamnet och stöder jokertecknet (\*). Till exempel `/myfile.html`, `/my/folder/*`, och `/my/images/*.jpg`. Den maximala längden är 260 tecken.

- **Tillägget**: det här tillståndet matchar filnamnstillägget för den begärda filen. Du kan ange en lista över kommaavgränsade filnamnstillägg som ska matcha. Till exempel `.jpg`, `.mp3`, eller `.png`. Det maximala antalet tillägg är 50 och det maximala antalet tecken per tillägget är 16. 

### <a name="global-and-custom-rule-processing-order"></a>Bearbetningsordningen för globala och anpassad regel
Global och anpassade cachelagring regler bearbetas i följande ordning:

- Globala cachelagring regler företräde framför CDN cachelagring standardbeteendet (HTTP-huvud för cache-direktiv inställningar). 

- Anpassade regler för cachelagring företräde framför globala cachelagring regler, där de tillämpas. Anpassade cachelagring regler bearbetas i ordning uppifrån och ned. Det vill säga om en begäran matchar bägge dessa villkor, åsidosätter regler längst ned i listan regler överst i listan. Därför ska du placera särskilda regler längst ned i listan.

**Exempel**:
- Globala cacheregeln: 
   - Cachelagring av frågesträngar: **åsidosätta**
   - Cachelagra giltighetstiden: 1 dag

- Anpassad cachelagring regel #1:
   - Matcha villkor: **sökväg**
   - Matcha värden:`/home/*`
   - Cachelagring av frågesträngar: **åsidosätta**
   - Cachelagra giltighetstiden: 2 dagar

- Anpassad cachelagring regeln #2:
   - Matcha villkor: **tillägg**
   - Matcha värden:`.html`
   - Cachelagring av frågesträngar: **om saknas**
   - Cachelagra giltighetstiden: 3 dagar

När dessa regler är inställda, en begäran om `<endpoint>.azureedge.net/home/index.html` utlösare anpassade cachelagring regel #2, som har angetts till: **om saknas** och 3 dagar. Därför, om den `index.html` filen har `Cache-Control` eller `Expires` HTTP-huvuden som de är hanteras; annars, om dessa huvuden inte har angetts filen lagras i 3 dagar.

