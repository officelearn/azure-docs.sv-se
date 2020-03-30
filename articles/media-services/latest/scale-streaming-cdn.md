---
title: Strömma innehåll med CDN-integrering
titleSuffix: Azure Media Services
description: Lär dig mer om direktuppspelning av innehåll med CDN-integrering, samt prefetching och Origin-Assist CDN-Prefetch.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128058"
---
# <a name="stream-content-with-cdn-integration"></a>Strömma innehåll med CDN-integrering

Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen.  

CDN cachelagrar innehåll som strömmas från en Media Services [Streaming Endpoint (origin)](streaming-endpoint-concept.md) per codec, per streamingprotokoll, bithastighet, per behållarformat och per kryptering/DRM. För varje kombination av codec-streaming protocol-container format-bitrate-kryptering, kommer det att finnas en separat CDN-cache.

Det populära innehållet kommer att serveras direkt från CDN-cachen så länge videofragmentet cachelagras. Live-innehåll kommer sannolikt att cachelagras eftersom du vanligtvis har många människor som tittar på exakt samma sak. On-demand-innehåll kan vara lite svårare eftersom du kan ha lite innehåll som är populärt och vissa som inte är det. Om du har miljontals videotillgångar där ingen av dem är populära (bara en eller två tittare i veckan) men du har tusentals människor som tittar på alla olika videor, blir CDN mycket mindre effektiv.

Du måste också överväga hur adaptiv streaming fungerar. Varje enskilt videofragment cachelagras som sin egen entitet. Föreställ dig till exempel första gången en viss video visas. Om tittaren hoppar runt och tittar bara några sekunder hit och dit, bara videofragmenten i samband med vad personen tittade på cachelagras i CDN. Med adaptiv streaming har du vanligtvis 5 till 7 olika bithastigheter av video. Om en person tittar på en bithastighet och en annan person tittar på en annan bithastighet, då de är varje cachelagras separat i CDN. Även om två personer tittar på samma bitrate, kan de strömma över olika protokoll. Varje protokoll (HLS, MPEG-DASH, Smooth Streaming) cachelagras separat. Så varje bithastighet och protokoll cachelagras separat och endast de videofragment som har begärts cachelagras.

När du bestämmer om du vill aktivera CDN på slutpunkten för [direktuppspelning](streaming-endpoint-concept.md)av Media Services bör du tänka på antalet förväntade tittare. CDN hjälper bara om du förväntar dig många tittare för ditt innehåll. Om tittarnas maximala samtidighet är lägre än 500, rekommenderas att cdn inaktiveras eftersom CDN skalas bäst med samtidighet.

I det här avsnittet beskrivs hur [CDN-integrering](#enable-azure-cdn-integration)kan aktiveras . Det förklarar också prefetching (aktiv caching) och [Origin-Assist CDN-Prefetch](#origin-assist-cdn-prefetch) konceptet.

## <a name="considerations"></a>Överväganden

* `hostname` Slutpunkten [för direktuppspelning](streaming-endpoint-concept.md) och direktuppspelnings-URL:en förblir desamma oavsett om du aktiverar CDN eller inte.
* Om du behöver möjligheten att testa ditt innehåll med eller utan CDN skapar du en annan slutpunkt för direktuppspelning som inte är CDN-aktiverad.

## <a name="enable-azure-cdn-integration"></a>Aktivera Azure CDN-integrering

> [!IMPORTANT]
> Du kan inte aktivera CDN för utvärderingsversion eller delversion av Azure-konton.
>
> CDN-integrering är aktiverad i alla Azure-datacenter utom federala myndigheter och Kina-regioner.

När en slutpunkt för direktuppspelning har etablerats med CDN aktiverat finns det en definierad väntetid på Media Services innan DNS-uppdateringen görs för att mappa slutpunkten för direktuppspelning till CDN-slutpunkten.

Om du senare vill inaktivera/aktivera CDN måste slutpunkten för direktuppspelning vara i **stoppat** tillstånd. Det kan ta upp till två timmar för Azure CDN-integreringen att aktiveras och för att ändringarna ska vara aktiva i alla CDN-POP:er. Du kan dock starta slutpunkten för direktuppspelning och streama utan avbrott från slutpunkten för direktuppspelning. När integrationen är klar levereras strömmen från CDN. Under etableringsperioden kommer slutpunkten för direktuppspelning att vara i **startläge** och du kan observera försämrade prestanda.

När slutpunkten för standardstreaming skapas konfigureras den som standard med Standard Verizon. Du kan konfigurera Premium Verizon- eller Standard Akamai-providers med REST API:er.

Azure Media Services-integrering med Azure CDN implementeras på **Azure CDN från Verizon** för standardslutpunkter för direktuppspelning. Slutpunkter för premiumstreaming kan konfigureras med alla **Azure CDN-prisnivåer och -leverantörer**.

> [!NOTE]
> Mer information om Azure CDN finns i [CDN-översikten](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Ta reda på om en DNS-ändring har gjorts

Du kan avgöra om DNS-ändring har gjorts på en slutpunkt för direktuppspelning <https://www.digwebinterface.com>(trafiken dirigeras till Azure CDN) med hjälp av . Om du ser azureedge.net domännamn i resultaten, är trafiken nu pekas på CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN-cachelagring är en reaktiv process. Om CDN kan förutsäga vad nästa objekt kommer att begära kan CDN proaktivt begära och cachelagra nästa objekt. Med den här processen kan du uppnå en cache-hit för alla (eller de flesta) av objekten, vilket förbättrar prestanda.

Begreppet prefetching strävar efter att placera objekt på "kanten av Internet" i väntan på att dessa kommer att begäras av spelaren inom kort, vilket minskar tiden för att leverera detta objekt till spelaren.

För att uppnå detta mål måste en slutpunkt för direktuppspelning (ursprung) och CDN arbeta hand i hand på ett par sätt:

- Media Services ursprung måste ha "intelligens" (Origin-Assist) för att informera CDN nästa objekt till prefetch.
- CDN gör prefetch och caching (CDN-prefetch del). CDN måste också ha "intelligens" för att informera ursprunget om det är en prefetch eller en vanlig hämta, hantera 404 svar, och ett sätt att undvika oändliga prefetch loop.

### <a name="benefits"></a>Fördelar

Fördelarna med *Origin-Assist CDN-Prefetch-funktionen* inkluderar:

- Prefetch förbättrar videouppspelningskvaliteten genom att förpositionera förväntade videosegment vid kanten under uppspelningen, minska svarstiden för tittaren och förbättra nedladdningstiderna för videosegmentet. Detta resulterar i snabbare videostarttid och lägre rebuffering förekomster.
- Det här konceptet är tillämpligt på allmänna SCENARIO MED CDN-ursprung och är inte begränsat till media.
- Akamai har lagt till den här funktionen i [Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Den här funktionen är ännu inte tillämplig på Akamai CDN som är integrerad med slutpunkten för direktuppspelning av Media Services. Den är dock tillgänglig för Media Services-kunder som har ett befintligt Akamai-kontrakt och kräver anpassad integrering mellan Akamai CDN och Media Services-ursprunget.

### <a name="how-it-works"></a>Hur det fungerar

CDN-stöd `Origin-Assist CDN-Prefetch` för rubrikerna (för streaming på begäran och video på begäran) är tillgängligt för kunder som har direkt kontrakt med Akamai CDN. Funktionen omfattar följande HTTP-huvudutbyten mellan Akamai CDN och mediatjänsternas ursprung:

|HTTP-huvud|Värden|Avsändare|Mottagare|Syfte|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (standard) eller 0 |CDN|Ursprung|För att indikera att CDN är prefetch aktiverat.|
|`CDN-Origin-Assist-Prefetch-Path`| Exempel: <br/>Fragment(video=1400000000,format=mpd-time-cmaf)|Ursprung|CDN|Så här anger du sökvägen till CDN för prefetch.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (prefetch begäran) eller 0 (vanlig begäran)|CDN|Ursprung|För att ange begäran från CDN är en prefetch.|

Om du vill se en del av sidutbytet i praktiken kan du prova följande steg:

1. Använd Postman eller cURL för att utfärda en begäran till mediatjänsternas ursprung för ett ljud- eller videosegment eller fragment. Se till att `CDN-Origin-Assist-Prefetch-Enabled: 1` lägga till huvudet i begäran.
2. I svaret bör du se `CDN-Origin-Assist-Prefetch-Path` rubriken med en relativ sökväg som värde.

### <a name="supported-streaming-protocols"></a>Strömmande protokoll som stöds

Funktionen `Origin-Assist CDN-Prefetch` stöder följande direktuppspelningsprotokoll för direktuppspelning och direktuppspelning på begäran:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Jämn strömning

### <a name="faqs"></a>Vanliga frågor och svar

* Vad händer om en url för prefetch-sökväg är ogiltig så att CDN-prefetch får en 404?

    CDN cachelagrar bara ett 404-svar i 10 sekunder (eller annat konfigurerat värde).

* Anta att du har en video på begäran. Om CDN-prefetch är aktiverat, innebär den här funktionen att när en klient begär det första videosegmentet, kommer prefetch starta en slinga för att prefetch alla efterföljande videosegment med samma bitrate?

    Nej, CDN-prefetch görs först efter en klientinitierad begäran/svar. CDN-prefetch utlöses aldrig av ett förval, för att undvika en prefetch loop.

* Är Origin-Assist CDN-Prefetch funktionen alltid på? Hur kan den slås på/av?

    Den här funktionen är inaktiverad som standard. Kunderna måste slå på den via Akamai API.

* Vad skulle hända med Origin-Assist om nästa segment eller fragment ännu inte är tillgängligt för livestreaming?

    I det här fallet kommer mediatjänsternas ursprung inte att tillhandahålla `CDN-Origin-Assist-Prefetch-Path` huvud och CDN-prefetch kommer inte att inträffa.

* Hur `Origin-Assist CDN-Prefetch` fungerar med dynamiska manifestfilter?

    Den här funktionen fungerar oberoende av manifestfilter. När nästa fragment är utanför ett filterfönster, kommer dess URL fortfarande att finnas genom att titta in i raw-klientmanifestet och sedan returneras som CDN prefetch svarshuvud. Så CDN kommer att få webbadressen till ett fragment som filtreras bort från DASH / HLS / Smooth manifest. Spelaren kommer dock aldrig att göra en GET-begäran till CDN för att hämta det fragmentet, eftersom det fragmentet inte ingår i DASH/HLS/Smooth-manifestet som innehas av spelaren (spelaren vet inte att fragmentets existens).

* Kan DASH MPD/ HLS spellista / Smooth manifest prefetched?

    Nej, DASH MPD, HLS-huvudspellista, HLS-variantspellista eller jämn manifest-URL läggs inte till i prefetch-huvudet.

* Är prefetch webbadresser relativa eller absoluta?

    Akamai CDN tillåter båda, men med ursprunget mediatjänster finns det bara relativa webbadresser för sökvägen förefekde eftersom det inte finns någon uppenbar fördel med att använda absoluta webbadresser.

* Fungerar den här funktionen med DRM-skyddat innehåll?

    Ja, eftersom den här funktionen fungerar på HTTP-nivå avkodas eller tolkas inga segment/fragment. Det bryr sig inte om innehållet är krypterat eller inte.

* Fungerar den här funktionen med SSAI (Server Side Ad Insertion)?
    
    Det gör för original/ huvudinnehåll (det ursprungliga videoinnehållet före insättning av annonser) fungerar, eftersom SSAI inte ändrar tidsstämpeln för källinnehållet från mediatjänsternas ursprung. Om den här funktionen fungerar med annonsinnehåll beror på om annonsursprung stöder Origin-Assist. Om annonsinnehåll till exempel också finns i Azure Media Services (samma eller separata ursprung) kommer annonsinnehållet också att prefetched.

* Fungerar den här funktionen med UHD/HEVC-innehåll?

    Ja.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

* Se till att granska dokumentet [för slutpunkt för direktuppspelning.Make](streaming-endpoint-concept.md) sure to review the Streaming Endpoint (origin) document.
* Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar standardslutpunkten för direktuppspelning med .NET.
