---
title: Optimering för nedladdning av stora filer med Azure CDN
description: Den här artikeln förklarar hur stora filer nedladdningar kan optimeras.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9793348b47763e6de10992b9a8a4606fc532cc4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636765"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Optimering för nedladdning av stora filer med Azure CDN

Filstorlekar av innehåll som tillhandahålls via internet fortsätter att växa på grund av utökad funktionalitet, förbättrad grafik och medieinnehåll. Den här tillväxten drivs av många faktorer: bredband penetrationstester, större prisvärda lagringsenheter, utökas ökningen av högupplöst video- och internet-anslutna enheter (IoT). En snabb och effektiv leveransmekanism för stora filer är viktigt att säkerställa en smidig och roligare användarfunktioner.

Leveransen av stora filer har flera utmaningar. Genomsnittlig tid för att ladda ned en stor fil kan först vara betydande eftersom program inte kanske att hämta alla data sekventiellt. I vissa fall kan program som kan ladda ned den sista delen av en fil innan den första delen. När bara en liten del av en fil har begärts eller användaren håller en nedladdning, kan inte ned. Nedladdningen kan vara fördröjda förrän efter nätverk för innehållsleverans (CDN) hämtar hela filen från ursprungsservern. 

Dessutom anger fördröjningen mellan en användares dator och filen hastighet med vilken de kan visa innehållet. Dessutom kan överbelastning och kapacitet nätverksproblem också påverka dataflöde. Större avstånd mellan servrar och användare skapa ytterligare möjligheter för paketförlust ske, vilket minskar kvalitet. Minskningen kvalitet på grund av begränsad genomströmning och ökad paketförlust kan öka väntetiden för en filhämtning ska slutföras. 

Det tredje många stora filer inte har levererats i sin helhet. Användare kan avbryta en halva hämtning eller titta på bara de första bara några minuterna efter en lång MP4-video. Programvara och leverans medieföretag vill därför leverera endast del av en fil som har begärts. Effektiv distribution av de begärda delarna minskar den utgående trafiken från den ursprungliga servern. Effektiv distribution minskar också minne och i/o-belastning på den ursprungliga servern. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimera leveransen av stora filer med Azure CDN från Microsoft

**Azure CDN Standard från Microsoft** slutpunkter leverera stora filer utan ett tak för filstorlek. Ytterligare funktioner är aktiverade som standard för att leveransen av stora filer snabbare.

### <a name="object-chunking"></a>Objekt-storlekar 

**Azure CDN Standard från Microsoft** använder en teknik som kallas objekt storlekar. När en stor fil har begärts, hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern tar emot en begäran om filen full- eller byte-intervall, begär CDN edge-servern filen från ursprunget i segment på 8 MB. 

När segmentet anländer till CDN-gränsen, har den cachelagras och hanteras direkt av användaren. CDN åttakilobytessegment sedan nästa segment parallellt. Den här prefetch säkerställer att innehållet förblir en segment före användaren, vilket minskar svarstider. Den här processen fortsätter tills hela filen hämtas (om så krävs), alla byte-intervall finns (om så krävs), eller klienten avslutar anslutningen. 

Mer information om byte-intervall begäran finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden om filen eller byte-intervall hanteras från CDN-cachen. Om inte alla segment cachelagras i CDN, prefetch för att begära segment från ursprunget. Denna optimering är beroende av den ursprungliga servern möjlighet att hantera begäranden om byte-intervall; Om ursprungsservern inte stöder begäranden med byte-intervall kan inte denna optimering är effektivt. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer
Optimering av stora filer som har funktioner för **Azure CDN Standard från Microsoft** är aktiverade som standard när du använder optimering för allmän Leveranstyp. Det finns ingen gräns för maximal filstorlek.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimera leveransen av stora filer med Azure CDN från Verizon

**Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** slutpunkter leverera stora filer utan ett tak för filstorlek. Ytterligare funktioner är aktiverade som standard för att leveransen av stora filer snabbare.

### <a name="complete-cache-fill"></a>Fullständig cache fyllning

Standard fullständig cache fyllningsfunktionen kan CDN för att hämta en fil till cachen när en ursprunglig begäran övergivna eller tappas bort. 

Fullständig cache fyllning är mest användbara för stora tillgångar. Normalt kan hämta användare inte dem från början till slut. De använder progressiv nedladdning. Standardbeteendet tvingar edge-servern för att initiera en bakgrundshämtning tillgångens från den ursprungliga servern. Därefter är tillgången i lokalt cacheminne för edge-servern. När fullständig objektet är i cacheminnet, uppfyller edge-servern byte-intervall begäranden till CDN för det cachelagrade objektet.

Standardbeteendet kan inaktiveras via regelmotor i **Azure CDN Premium från Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Peer-cache Fyll hot Momsregistrering

Standard peer cache fyllning hot Momsregistrering funktionen använder en avancerad upphovsrättsskyddade algoritm. Den använder ytterligare edge cachelagring servrar baserat på bandbredd och aggregering begär mått för att slutföra klientförfrågningar för stora, mycket populära objekt. Den här funktionen förhindrar att en situation där stora mängder extra begäranden skickas till en användares ursprungsservern. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

Optimering av stora filer som har funktioner för **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** är aktiverade som standard när du använder optimering för allmän Leveranstyp. Det finns ingen gräns för maximal filstorlek. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimera leveransen av stora filer med Azure CDN Standard från Akamai

**Azure CDN Standard från Akamai** profil slutpunkter erbjuder en funktion som ger effektivt stora filer åt användare över hela världen i stor skala. Funktionen minskar svarstiderna eftersom det minskar belastningen på ursprungsservrar.

Funktionen för optimering-typ i stora filer Aktiverar optimeringar av nätverk och konfigurationer för att leverera stora filer snabbare och mer dynamiskt. Allmän webbleverans med **Azure CDN Standard från Akamai** slutpunkter cachelagrar filer endast nedan 1,8 GB och kan tunneln (inte cache) filer upp till 150 GB. Cacheminnen för optimering av stora filer filer upp till 150 GB.

Optimering av stora filer är effektiv när vissa villkor är uppfyllda. Villkoren innefattar hur den ursprungliga servern fungerar och storlekar och typer av filer som har begärts. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfigurera en Akamai CDN-slutpunkt för att optimera leveransen av stora filer

Du kan konfigurera din **Azure CDN Standard från Akamai** slutpunkt för att optimera leverans av stora filer via Azure portal. Du kan också använda REST-API: er eller någon av klient-SDK: er för att göra detta. Följande steg visar hur via Azure portal för en **Azure CDN Standard från Akamai** profil:

1. Lägga till en ny slutpunkt på en Akamai **CDN-profil** väljer **Endpoint**.

    ![Ny slutpunkt](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. I den **optimerade för** listrutan, väljer **nedladdning av stora filer**.

    ![Optimering av stora filer har valts](./media/cdn-large-file-optimization/cdn-large-file-select.png)


När du har skapat CDN-slutpunkten gäller stora filer optimeringar för alla filer som matchar vissa kriterier. I följande avsnitt beskrivs den här processen.

### <a name="object-chunking"></a>Objekt-storlekar 

Optimering av stora filer med **Azure CDN Standard från Akamai** använder en teknik som kallas objekt storlekar. När en stor fil har begärts, hämtar CDN mindre delar av filen från ursprunget. När CDN POP-servern tar emot en begäran om filen full- eller byte-intervall, kontrollerar den om filtypen stöds för denna optimering. Den kontrollerar också om filtypen uppfyller storlekskraven för filen. Om filen är större än 10 MB, begär CDN edge-servern filen från ursprunget i segment om 2 MB. 

När segmentet anländer till CDN-gränsen, har den cachelagras och hanteras direkt av användaren. CDN åttakilobytessegment sedan nästa segment parallellt. Den här prefetch säkerställer att innehållet förblir en segment före användaren, vilket minskar svarstider. Den här processen fortsätter tills hela filen hämtas (om så krävs), alla byte-intervall finns (om så krävs), eller klienten avslutar anslutningen. 

Mer information om byte-intervall begäran finns i [RFC 7233](https://tools.ietf.org/html/rfc7233).

CDN cachelagrar alla segment när de tas emot. Hela filen behöver inte cachelagras på CDN-cachen. Efterföljande begäranden om filen eller byte-intervall hanteras från CDN-cachen. Om inte alla segment cachelagras i CDN, prefetch för att begära segment från ursprunget. Denna optimering är beroende av den ursprungliga servern möjlighet att hantera begäranden om byte-intervall; Om ursprungsservern inte stöder begäranden med byte-intervall kan inte denna optimering är effektivt.

### <a name="caching"></a>Cachelagring
Optimering av stora filer använder olika cachelagring giltighetstid standardtiderna från allmän webbleverans. Den skiljer mellan cachelagring av positiva och negativa cachelagring baserat på HTTP-svarskoder. Om den ursprungliga servern anger en förfallotid via en cache-control eller expires-huvud i svaret, godkänner CDN detta värde. Om ursprunget inte angav filen matchar villkoren typ och storlek för den här typen av slutpunktsoptimering och använder CDN standardvärdena för optimering av stora filer. I annat fall används CDN standardvärden för allmän webbleverans.


|    | Allmän | Optimering av stora filer 
--- | --- | --- 
Caching: Positivt <br> HTTP 200, 203, 300, <br> 301, 302 och 410 | 7 dagar |1 dag  
Caching: Negativt <br> HTTP 204, 305, 404, <br> och 405 | Ingen | 1 sekund 

### <a name="deal-with-origin-failure"></a>Hantera ursprung fel

Läs-timeout-längden ursprung ökar från två sekunder för allmän webbleverans till två minuter för typ för optimering av stora filer. Denna ökning konton för större filstorlekar att undvika en tidig timeout-anslutning.

När en anslutning tidsgränsen återförsök CDN flera gånger innan den skickar ett ”504 - Gatewaytimeout”-fel till klienten. 

### <a name="conditions-for-large-file-optimization"></a>Villkor för optimering av stora filer

I följande tabell visas en uppsättning kriterier uppfyllas för optimering av stora filer:

Tillstånd | Värden 
--- | --- 
Filtyper som stöds | 3g 2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG, mpg, mts, pkg, qt, rm, SWF-, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minsta filstorlek | 10 MB 
Maximal filstorlek | 150 GB 
Ursprung server egenskaper | Måste ha stöd för begäranden med byte-intervall 

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Överväg följande ytterligare aspekter för den här optimeringstypen:

- För att maximera dedupliceringsbesparingen processen genererar ytterligare begäranden till den ursprungliga servern. Den totala mängden data som levereras från ursprunget är dock mycket mindre. För att maximera dedupliceringsbesparingen resulterar i bättre cachelagring egenskaper på CDN.

- Minne och i/o-belastning kan minskas till ursprungsplatsen eftersom mindre delar av filen levereras.

- För segment som cachelagras på CDN, finns inga ytterligare begäranden till ursprunget tills innehållet upphör att gälla eller avlägsnas från cachen.

- Användare kan göra range-begäran till CDN-nätverket som behandlas som normala filer. Optimering gäller endast om det är en giltig filtyp och byte-intervallet är mellan 10 MB och 150 GB. Om den genomsnittliga storleken begärt är mindre än 10 MB, Använd allmän webbleverans i stället.

