---
title: Optimering av stora filer med Azure CDN
description: I den här artikeln beskrivs hur stora filhämtningar kan optimeras.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 28b3c4faf62bcd9f9495810927ece03e2dadc1fc
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260538"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimering av stora filer med Azure CDN

Filstorlekarna på innehåll som levereras via internet fortsätter att öka på grund av förbättrade funktioner, förbättrad grafik och rikt medieinnehåll. Denna tillväxt drivs av många faktorer: bredbandspenetration, större billiga lagringsenheter, en utbredd ökning av HD-video och internetanslutna enheter (IoT). En snabb och effektiv leveransmekanism för stora filer är avgörande för att säkerställa en smidig och trevlig konsumentupplevelse.

Leverans av stora filer har flera utmaningar. För det första kan den genomsnittliga tiden för att hämta en stor fil vara betydande eftersom program kanske inte hämtar alla data sekventiellt. I vissa fall kan program hämta den sista delen av en fil före den första delen. När endast en liten mängd av en fil begärs eller en användare pausar en nedladdning kan hämtningen misslyckas. Hämtningen kan också fördröjas tills cdn (Content Delivery Network) hämtar hela filen från ursprungsservern. 

För det andra avgör svarstiden mellan en användares dator och filen hur snabbt de kan visa innehåll. Dessutom påverkar trafikstockningar och kapacitetsproblem också genomströmningen. Större avstånd mellan servrar och användare skapar ytterligare möjligheter för paketförlust att inträffa, vilket minskar kvaliteten. Den minskade kvaliteten som orsakas av begränsat dataflöde och ökad paketförlust kan öka väntetiden för en filhämtning. 

För det tredje levereras många stora filer inte i sin helhet. Användare kan avbryta en nedladdning halvvägs eller titta bara de första minuterna av en lång MP4-video. Därför vill programvaru- och medieleveransföretag bara leverera den del av en fil som begärs. Effektiv distribution av de begärda delarna minskar utgående trafik från ursprungsservern. Effektiv distribution minskar också minnet och I/O-trycket på ursprungsservern. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimera för leverans av stora filer med Azure CDN från Microsoft

**Azure CDN Standard från** Microsoft-slutpunkter levererar stora filer utan tak för filstorlek. Ytterligare funktioner är aktiverade som standard för att göra leveransen av stora filer snabbare.

### <a name="object-chunking"></a>Objektsegmentering 

**Azure CDN Standard från Microsoft** använder en teknik som kallas objektsegmentering. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern har tagit emot en filbegäran för hela eller byteintervall begär CDN-kantservern filen från ursprunget i segment på 8 MB. 

När segmentet anländer till CDN-kanten cachelagras den och serveras omedelbart till användaren. CDN sedan prefetches nästa bit parallellt. Den här prefetchen ser till att innehållet stannar en bit före användaren, vilket minskar svarstiden. Den här processen fortsätter tills hela filen hämtas (om så önskas), alla byteintervall är tillgängliga (om så önskas) eller så avslutar klienten anslutningen. 

Mer information om begäran om byteintervall finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden för fil- eller byteintervallen visas från CDN-cachen. Om inte alla segment cachelagras på CDN används prefetch för att begära segment från ursprunget. Den här optimeringen är beroende av ursprungsserverns förmåga att stödja begäranden om byteintervall. Om ursprungsservern inte stöder begäranden om byteintervall är den här optimeringen inte effektiv. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer
Stora filoptimeringsfunktioner för **Azure CDN Standard från Microsoft** aktiveras som standard när du använder den allmänna optimeringstypen för webbleverans. Det finns inga gränser för maximal filstorlek.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimera för leverans av stora filer med Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** slutpunkter leverera stora filer utan tak för filstorlek. Ytterligare funktioner är aktiverade som standard för att göra leveransen av stora filer snabbare.

### <a name="complete-cache-fill"></a>Fullständig cachefyllning

Standardfunktionen för fullständig cachefyllning gör att CDN kan hämta en fil till cacheminnet när en första begäran överges eller förloras. 

Fullständig cachefyllning är mest användbar för stora tillgångar. Vanligtvis hämtar användarna inte dem från början till. De använder progressiv nedladdning. Standardbeteendet tvingar kantservern att initiera en bakgrundshämtning av tillgången från ursprungsservern. Därefter finns tillgången i kantserverns lokala cacheminne. När hela objektet finns i cacheminnet uppfyller kantservern begäranden om byteintervall till CDN för det cachelagrade objektet.

Standardbeteendet kan inaktiveras via regelmotorn i **Azure CDN Premium från Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Peer-cachefyllning för snabb arkivering

Standardfunktionen för peer-cachefyllning använder en sofistikerad proprietär algoritm. Den använder ytterligare edge caching-servrar baserat på bandbredd och aggregerade begäranden mått för att uppfylla klientbegäranden för stora, mycket populära objekt. Den här funktionen förhindrar en situation där ett stort antal extra begäranden skickas till en användares ursprungsserver. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

Stora filoptimeringsfunktioner för **Azure CDN Standard från Verizon** och Azure **CDN Premium från Verizon** aktiveras som standard när du använder den allmänna optimeringstypen för webbleverans. Det finns inga gränser för maximal filstorlek. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimera för leverans av stora filer med Azure CDN Standard från Akamai

**Azure CDN Standard från Akamai-profilslutpunkter** erbjuder en funktion som levererar stora filer effektivt till användare över hela världen i stor skala. Funktionen minskar fördröjningar eftersom det minskar belastningen på ursprungsservrarna.

Funktionen för optimering av stora filer aktiverar nätverksoptimeringar och konfigurationer för att leverera stora filer snabbare och mer responsivt. Allmän webbleverans med **Azure CDN Standard från Akamai-slutpunkter** cachelagrar filer endast under 1,8 GB och kan tunnelfiler (inte cache) filer upp till 150 GB. Stor filoptimering cachelagrar filer upp till 150 GB.

Stor filoptimering är effektiv när vissa villkor är uppfyllda. Villkor inkluderar hur ursprungsservern fungerar och storleken och typerna av de filer som begärs. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera leveransen av stora filer

Du kan konfigurera din **Azure CDN Standard från Akamai-slutpunkten** för att optimera leveransen för stora filer via Azure-portalen. Du kan också använda REST-API:erna eller någon av klient-SDK:erna för att göra detta. Följande steg visar processen via Azure-portalen för en **Azure CDN Standard från Akamai-profil:**

1. Om du vill lägga till en ny slutpunkt väljer du **Slutpunkt**på en Akamai **CDN-profilsida** .

    ![Ny slutpunkt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Välj **Stor filhämtning**i listrutan **Optimerad för** .

    ![Stor filoptimering vald](./media/cdn-large-file-optimization/cdn-large-file-select.png)


När du har skapat CDN-slutpunkten tillämpas de stora filoptimeringarna för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen.

### <a name="object-chunking"></a>Objektsegmentering 

Stor filoptimering med **Azure CDN Standard från Akamai** använder en teknik som kallas objektsegmentering. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern har tagit emot en filbegäran för hela eller byteintervall kontrollerar den om filtypen stöds för den här optimeringen. Den kontrollerar också om filtypen uppfyller kraven på filstorlek. Om filstorleken är större än 10 MB begär CDN-kantservern filen från ursprunget i segment på 2 MB. 

När segmentet anländer till CDN-kanten cachelagras den och serveras omedelbart till användaren. CDN sedan prefetches nästa bit parallellt. Den här prefetchen ser till att innehållet stannar en bit före användaren, vilket minskar svarstiden. Den här processen fortsätter tills hela filen hämtas (om så önskas), alla byteintervall är tillgängliga (om så önskas) eller så avslutar klienten anslutningen. 

Mer information om begäran om byteintervall finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden för fil- eller byteintervallen visas från CDN-cachen. Om inte alla segment cachelagras på CDN används prefetch för att begära segment från ursprunget. Den här optimeringen är beroende av ursprungsserverns förmåga att stödja begäranden om byteintervall. Om ursprungsservern inte stöder begäranden om byteintervall är den här optimeringen inte effektiv.

### <a name="caching"></a>Caching
Stor filoptimering använder olika standardcache-utgångstider från allmän webbleverans. Det skiljer mellan positiv cachelagring och negativ cachelagring baserat på HTTP-svarskoder. Om ursprungsservern anger en förfallotid via en cachekontroll eller förfaller huvudet i svaret, hedrar CDN det värdet. När ursprunget inte anger och filen matchar typ- och storleksvillkoren för den här optimeringstypen använder CDN standardvärdena för stor filoptimering. Annars använder CDN standardvärden för allmän webbleverans.


|    | Allmänt webb | Optimering av stora filer 
--- | --- | --- 
Caching: Positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |1 dag  
Caching: Negativ <br> HTTP 204, 305, 404, <br> och 405 | Inget | 1 sekund 

### <a name="deal-with-origin-failure"></a>Hantera ursprungsfel

Ursprungsavläsningstidslängden ökar från två sekunder för allmän webbleverans till två minuter för den stora filoptimeringstypen. Den här ökningen står för de större filstorlekarna för att undvika en för tidig timeout-anslutning.

När en anslutning timeout försöker CDN igen ett antal gånger innan den skickar ett "504 - Gateway Timeout"-fel till klienten. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

I följande tabell visas de villkor som ska uppfyllas för optimering av stora filer:

Villkor | Värden 
--- | --- 
Filtyper som stöds | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tjära, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minsta filstorlek | 10 MB 
Maximal filstorlek | 150 GB 
Ursprungsserveregenskaper | Måste stödja begäranden om byteintervall 

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Tänk på följande ytterligare aspekter för den här optimeringstypen:

- Segmenteringsprocessen genererar ytterligare begäranden till ursprungsservern. Den totala mängden data som levereras från ursprunget är dock mycket mindre. Chunking resulterar i bättre cachelagring egenskaper på CDN.

- Minne och I/O-tryck reduceras vid beskärningen eftersom mindre delar av filen levereras.

- För segment som cachelagras på CDN finns det inga ytterligare begäranden till ursprunget förrän innehållet upphör att gälla eller det är bortträkt från cachen.

- Användare kan göra intervallbegäranden till CDN, som behandlas som vilken vanlig fil som helst. Optimering gäller endast om det är en giltig filtyp och byteintervallet är mellan 10 MB och 150 GB. Om den genomsnittliga filstorleken som begärs är mindre än 10 MB använder du allmän webbleverans i stället.

