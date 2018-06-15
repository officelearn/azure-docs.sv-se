---
title: Optimering av stora filer download med Azure CDN
description: Den här artikeln förklarar hur stor fil hämtningar kan optimeras.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 2bdb6bdea7b6180e34458883d026161403e4cb58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766214"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimering av stora filer download med Azure CDN

Filstorlekarna av innehåll som levereras via internet fortsätter att växa på grund av utökad funktionalitet, förbättrad grafik och medieinnehåll. Den här tillväxt drivs av många faktorer: bredband intrång, större billiga lagringsenheter, omfattande fler HD-video- och internet-anslutna enheter (IoT). Snabba och effektiva leveransmekanismen för stora filer är viktigt att säkerställa en smidig och roligare användarfunktioner.

Överföringen av stora filer har flera utmaningar. Genomsnittlig tid för att hämta en stor fil kan först vara betydande eftersom program inte kan hämta alla data i tur och ordning. Program kan i vissa fall kan ladda ned den sista delen av en fil innan den första delen. Om bara en liten del av en fil har begärts eller användaren håller en hämtning kan hämtningen misslyckas. Hämtningen kan vara fördröjd förrän efter innehållsleveransnätverk (CDN) hämtar hela filen från den ursprungliga servern. 

Andra anger fördröjningen mellan en användares dator och filen hastighet med vilken de kan visa innehållet. Dessutom påverkar överbelastning och kapacitet nätverksproblem också genomflöde. Större avstånd mellan servrar och användare skapa ytterligare möjligheter för paketförlust ska ske, vilket minskar kvalitet. Minskning av kvaliteten på grund av begränsad genomflöde och ökad paketförlust kan öka väntetiden för en filhämtning ska slutföras. 

Det tredje många stora filer levereras inte i sin helhet. Användare kan avbryta hämtningen halva eller titta på bara de första bara några minuterna efter en lång MP4-video. Programvara och media leverans företag vill leverera endast del av en fil som krävs. Effektiv distribution av de begärda delarna minskar risken för utgående trafik från den ursprungliga servern. Effektiv distribution minskar också minne och i/o-belastning på den ursprungliga servern. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimera för överföringen av stora filer med Azure CDN från Microsoft

**Azure CDN Standard från Microsoft** slutpunkter leverera stora filer utan ett tak för filstorlek. Ytterligare funktioner är aktiverade som standard så att överföringen av stora filer snabbare.

### <a name="object-chunking"></a>Objekt-högoptimerat 

**Azure CDN Standard från Microsoft** använder en teknik som kallas objektet högoptimerat. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern tar emot en begäran om filen full eller byte-intervall, begär gränsservern CDN filen med ursprung i mängder 8 MB. 

När segmentet når gränsen CDN, har den cachelagras och hanteras direkt av användaren. CDN åttakilobytessegment sedan nästa segment parallellt. Den här prefetch säkerställer att innehållet förblir en datamängd i den användaren, vilket minskar svarstider. Den här processen fortsätter tills hela filen laddas ned (om så krävs) och alla byteintervall är tillgängliga (om så krävs), eller klienten avslutar anslutningen. 

Mer information om byte-intervall begäran finns [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden om filen eller byte områden är hämtas från cachen CDN. Om inte alla segment cachelagras på CDN, prefetch används för att begära segment från ursprunget. Denna optimering förlitar sig på den ursprungliga servern möjlighet att stöder byte-intervall begäranden; Om den ursprungliga servern inte stöder byte-intervall begäranden, inte denna optimering effektivt. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer
Optimering av stora filer funktioner för **Azure CDN Standard från Microsoft** är aktiverad som standard när du använder Internet optimering Leveranstyp. Det finns ingen begränsning för maximal filstorlek.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimera för överföringen av stora filer med Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** slutpunkter leverera stora filer utan ett tak för filstorlek. Ytterligare funktioner är aktiverade som standard så att överföringen av stora filer snabbare.

### <a name="complete-cache-fill"></a>Fullständig cache fill

Standard fullständig cache fill funktionen gör CDN kan hämta en fil till cachen när en inledande begäran avbrutna eller tappas bort. 

Fullständig cache fill är mest användbart för stora tillgångar. Normalt hämta användare inte dem från början till slut. De använder progressiv hämtning. Standardbeteendet tvingar edge-server för att initiera en bakgrundshämtning tillgångens från den ursprungliga servern. Därefter kan är tillgången i det lokala cacheminnet i edge-server. När fullständig objektet är i cacheminnet, uppfyller gränsservern byte-intervall förfrågningar till CDN för cachelagrade objekt.

Standardbeteendet kan inaktiveras via regelmotor i **Azure CDN Premium från Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Peer-cache fyller hot arkivering

Standard peer-cache fill hot ansökan funktionen använder en avancerad upphovsrättsskyddade algoritm. Den använder ytterligare edge cachelagring servrar som baseras på bandbredd och mängd begäranden mått för att slutföra klientförfrågningar för stora, hög populära objekt. Den här funktionen förhindrar att en situation där stort antal extra begäranden skickas till en användares ursprungsservern. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

Optimering av stora filer funktioner för **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** är aktiverad som standard när du använder Internet optimering Leveranstyp. Det finns ingen begränsning för maximal filstorlek. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimera för överföringen av stora filer med Azure CDN Standard från Akamai

**Azure CDN Standard från Akamai** profil slutpunkter erbjuder en funktion som levererar effektivt stora filer till användare världen i större skala. Funktionen minskar svarstiderna eftersom minskar belastningen på servrarna ursprung.

Funktionen för optimering-typ i stora filer Aktiverar optimeringar av nätverk och konfigurationer för att leverera stora filer snabbare och mer responsively. Allmän web leverans med **Azure CDN Standard från Akamai** slutpunkter cachelagrar filer endast under 1,8 GB och kan tunneln (inte cache) filer upp till 150 GB. Stora filer optimering cacheminnen filer upp till 150 GB.

Optimering av stora filer är effektiv när vissa villkor är uppfyllda. Villkoren omfattar hur ursprungsservern fungerar och storlekar och typer av filer som har begärts. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurera en Akamai CDN-slutpunkten för att optimera överföringen av stora filer

Du kan konfigurera din **Azure CDN Standard från Akamai** slutpunkten för att optimera leverans för stora filer via Azure portal. Du kan också använda REST-API: er eller någon av klient-SDK: er för att göra detta. Följande steg visar processen via Azure portal för en **Azure CDN Standard från Akamai** profil:

1. Lägga till en ny slutpunkt på en Akamai **CDN-profilen** väljer **Endpoint**.

    ![Ny slutpunkt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. I den **optimerade för** listrutan, Välj **stora Filhämtning**.

    ![Optimering av stora filer markerat](./media/cdn-large-file-optimization/cdn-large-file-select.png)


När du har skapat CDN-slutpunkten gäller stora filer optimeringar för alla filer som matchar vissa villkor. I följande avsnitt beskrivs den här processen.

### <a name="object-chunking"></a>Objekt-högoptimerat 

Optimering av stora filer med **Azure CDN Standard från Akamai** använder en teknik som kallas objektet högoptimerat. När en stor fil begärs hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern tar emot en begäran om filen full eller byte-intervall, kontrollerar den om filtypen stöds denna optimering. Den kontrollerar också om filtypen uppfyller filen storlek. Om filens storlek är större än 10 MB, begär gränsservern CDN filen med ursprung i block 2 MB. 

När segmentet når gränsen CDN, har den cachelagras och hanteras direkt av användaren. CDN åttakilobytessegment sedan nästa segment parallellt. Den här prefetch säkerställer att innehållet förblir en datamängd i den användaren, vilket minskar svarstider. Den här processen fortsätter tills hela filen laddas ned (om så krävs) och alla byteintervall är tillgängliga (om så krävs), eller klienten avslutar anslutningen. 

Mer information om byte-intervall begäran finns [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden om filen eller byte områden är hämtas från cachen CDN. Om inte alla segment cachelagras på CDN, prefetch används för att begära segment från ursprunget. Denna optimering förlitar sig på den ursprungliga servern möjlighet att stöder byte-intervall begäranden; Om den ursprungliga servern inte stöder byte-intervall begäranden, inte denna optimering effektivt.

### <a name="caching"></a>Cachelagring
Optimering av stora filer använder olika cachelagring förfallodatum standardtiden från Internet leverans. Den skiljer mellan cachelagring av positiva och negativa cachelagring baserat på http-svarskoder. Om den ursprungliga servern anger en förfallotid via en cache-control eller expires-rubrik i svaret, godkänner CDN värdet. När ursprung inte ange och filen matchar villkor som typ och storlek för den här typen av optimering, använder CDN standardvärden för optimering av stora filer. I annat fall används CDN standardvärden för allmän web leverans.


|    | Allmän web | Optimering av stora filer 
--- | --- | --- 
Cachelagring: positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |1 dag  
Cachelagring: negativt <br> HTTP 204, 305, 404, <br> och 405 | Ingen | 1 sekund 

### <a name="deal-with-origin-failure"></a>Hantera ursprung fel

Läs-timeout-längden ursprung ökar från två sekunderna för allmän web leverans till två minuter för optimering för stora filtypen. Denna ökning konton för större filstorlek att undvika en för tidig timeout-anslutning.

När en anslutning timeout återförsök CDN flera gånger innan den skickar en ”504 - Gateway-Timeout”-fel till klienten. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

I följande tabell visas en uppsättning villkor är uppfyllda för optimering av stora filer:

Villkor | Värden 
--- | --- 
Filtyper som stöds | 3g, 2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> GZ, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minsta filstorlek | 10 MB 
Maximal filstorlek | 150 GB 
Ursprung server egenskaper | Måste ha stöd för begäranden med byte-intervall 

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Överväg följande ytterligare aspekter för den här typen av optimering:

- Segment processen genererar ytterligare begäranden till den ursprungliga servern. Men är den övergripande datavolymen som levereras med ursprung mycket mindre. Segment resulterar i bättre cachelagring egenskaper på CDN.

- Minne och i/o-belastning minskas vid ursprung eftersom mindre delar av filen levereras.

- För segment som cachelagras på CDN finns inga ytterligare begäranden till ursprunget tills innehållet upphör att gälla eller avlägsnas från cache.

- Användare kan göra range-begäran för CDN som betraktas som normala filer. Optimering gäller endast om det är en ogiltig filtyp och byte-intervallet är mellan 10 MB och 150 GB. Om den genomsnittliga filstorleken som begärdes är mindre än 10 MB, Använd Internet leverans i stället.

