---
title: Azure Media Services fragmenterad MP4 live infognings-specifikationen | Microsoft Docs
description: Den här specifikationen beskriver protokollet och format för fragmenterad MP4-baserade direktsänd strömning införandet för Azure Media Services. Du kan använda Azure Media Services för att strömma direktsända händelser och broadcast innehåll i realtid med hjälp av Azure som molnplattform. Det här dokumentet beskriver också bästa praxis för att skapa mycket överflödiga och robusta live infognings-metoder.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 367066b0073775ae10fd5e9d084c5bc78ebf6677
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788979"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmenterad MP4 live infognings-specifikationen
Den här specifikationen beskriver protokollet och format för fragmenterad MP4-baserade direktsänd strömning införandet för Azure Media Services. Media Services tillhandahåller en liveströmningstjänst som kunder använder för att strömma direktsända händelser och broadcast innehåll i realtid med hjälp av Azure som molnplattform. Det här dokumentet beskriver också bästa praxis för att skapa mycket överflödiga och robusta live infognings-metoder.

## <a name="1-conformance-notation"></a>1. Överensstämmelse notation
Nyckeln ord ”måste”, ”får inte”, ”krävs”, ”skall”, ”skall inte”, ”SHOULD”, ”bör inte”, ”rekommenderade” ”får” och ”valfritt” i det här dokumentet är tolkas som de beskrivs i RFC 2119.

## <a name="2-service-diagram"></a>2. Diagram för tjänsten
Följande diagram visar liveströmningstjänst övergripande arkitektur i Media Services:

1. En livekodare skickar live feeds till kanaler som skapas och etableras via Azure Media Services SDK.
2. Kanaler, program och strömningsslutpunkter i Media Services hanterar alla funktioner för liveströmning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans för moln.
3. Kunder kan du kan också välja att distribuera ett Azure Content Delivery Network lager mellan den strömmande slutpunkten och klientslutpunkterna.
4. Klienten slutpunkter ström från den strömmande slutpunkten med hjälp av anpassningsbar strömning av HTTP-protokoll. Exempel: Microsoft Smooth Streaming, dynamiska anpassningsbar strömning via HTTP (TANKSTRECK eller MPEG-DASH) och Apple HTTP Live Streaming (HLS).

![infognings-flöde][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Formatet för Bitstream – ISO 14496 12 fragmenterad MP4
Kabelformat för direktsänd strömning infognings som beskrivs i det här dokumentet är baserad på [ISO-14496-12]. En detaljerad förklaring av fragmenterad MP4-format och tillägg både för video-on-demand-filer och direktsänd strömning införandet finns [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Live infognings-format definitioner
I följande lista beskrivs specialformat definitioner som gäller för levande mata in i Azure Media Services:

1. Den **ftyp**, **Live Manifest serverrutan**, och **moov** rutor måste skickas med varje begäran (HTTP POST). Dessa rutor måste skickas i början av dataströmmen och infognings-helst kodaren måste ansluta för att återuppta dataströmmen. Mer information finns i avsnittet 6 i [1].
2. [1] avsnitt 3.3.2 definierar en valfri ruta som kallas **StreamManifestBox** för live mata in. På grund av Routning logiken för Azure belastningsutjämnare är med den här rutan föråldrad. Rutan bör inte när du vill föra in till Media Services. Om den här rutan finns ignoreras Media Services tyst det.
3. Den **TrackFragmentExtendedHeaderBox** rutan som definierats i 3.2.3.2 i [1] måste finnas för varje fragment.
4. Version 2 av den **TrackFragmentExtendedHeaderBox** kryssrutan ska användas för att generera media-segment som har identiska URL: er i flera datacenter. Fältet fragment index är REQUIRED mellan datacenter växling vid fel i index-baserad strömningsformat, till exempel Apple HLS och indexbaserade MPEG-DASH. Om du vill aktivera cross-datacenter redundans fragment-index måste synkroniseras mellan flera kodare och ökas med 1 för varje efterföljande media fragment även över kodare omstarter eller fel.
5. [1] avsnitt 3.3.6 definierar en ruta som kallas **MovieFragmentRandomAccessBox** (**mfra**) som kan skickas i slutet av live införandet anger slutet på dataströmmen (EOS) till kanalen. På grund av infognings-logiken för Media Services med EOS är inaktuell och **mfra** rutan för live införandet inte ska skickas. Om skickas ignoreras Media Services tyst det. Du kan återställa infognings-punkt, rekommenderar vi att du använder [kanal återställa](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Vi rekommenderar också att du använder [programmet avslutas](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) att avsluta en presentation och dataströmmen.
6. MP4 fragment varaktigheten ska vara konstant, för att minska storleken på klientmanifesten. En konstant MP4 fragment varaktighet förbättrar också klienten hämta heuristik med Upprepa taggar. Varaktigheten kan variera för att kompensera för hastigheterna heltal.
7. MP4 fragment varaktigheten ska vara mellan 2 och cirka 6 sekunder.
8. MP4 Fragmentera tidsstämplar och index (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` och `fragment_index`) ska komma in i stigande ordning. Även om Media Services är motståndskraftiga mot dubbla fragment, har den begränsad möjlighet att ordna om fragment enligt media tidslinjen.

## <a name="4-protocol-format--http"></a>4. Protokollet formatet – HTTP
ISO fragmenterad MP4-baserade live infognings för Media Services använder en standard tidskrävande HTTP POST-begäran för att överföra data för kodade media som är packade fragmenterad MP4-format till tjänsten. Varje HTTP POST skickar en fullständig fragmenterad MP4 bitstream (”stream”), från början med rubriken rutor (**ftyp**, **Live Manifest serverrutan**, och **moov** rutor ), och fortsätter med en sekvens av fragment (**moof** och **mdat** rutor). URL-syntax för HTTP POST-begäran, finns i avsnittet 9.2 i [1]. Ett exempel på POST-URL är: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Krav
Här följer detaljerade krav:

1. Kodaren ska starta sändningen genom att skicka en HTTP POST-begäran med ett tomt ”text” (nollängd innehåll) med hjälp av samma införandet URL. Detta kan hjälpa kodaren snabbt identifiera om live införandet slutpunkten är giltig och om det inte finns någon autentisering eller andra villkor som krävs. Per HTTP-protokollet på servern kan inte skicka tillbaka ett HTTP-svar förrän hela begäran, inklusive POST meddelandetexten, tas emot. Eftersom en direktsänd händelse, utan det här steget tidskrävande kanske kodaren inte kan identifiera eventuella fel tills den är klar skickar alla data.
2. Kodaren måste hantera fel eller autentiseringsfrågor på grund av (1). Om (1) lyckas med svaret 200 fortsätta.
3. Kodaren måste starta en ny HTTP POST-begäran med fragmenterad MP4-dataström. Nyttolasten måste börja med rutorna sidhuvud följt av fragment. Observera att den **ftyp**, **Live Manifest serverrutan**, och **moov** rutorna (i ordning) måste skickas med varje begäran, även om kodaren måste ansluta eftersom den tidigare begäran avbröts innan slutet på dataströmmen. 
4. Kodaren måste använda chunked-överföringskodning för att ladda upp, eftersom det är omöjligt att förutsäga hela innehållslängden för direktsänd händelse.
5. När händelsen är över, när du har skickat det senaste fragmentet måste avslutas kodaren avslutas chunked överföringskodning meddelandesekvens (de flesta HTTP klientstackar hantera automatiskt). Kodaren måste vänta på att tjänsten för att returnera den sista svarskoden och avslutar anslutningen. 
6. Kodaren måste inte använda den `Events()` substantiv enligt beskrivningen i 9.2 i [1] för live införandet till Media Services.
7. Om HTTP POST-begäran avslutas eller med ett TCP-fel innan slutet på dataströmmen, måste kodaren utfärda en ny POST-begäran med hjälp av en ny anslutning och följ de föregående krav. Kodaren måste dessutom skicka föregående två MP4 fragment för varje spår i dataströmmen och fortsätta utan att en avvikelse i tidslinjen för media. Skicka om de två sista MP4-fragment för varje spår säkerställer att det finns inga data går förlorade. Med andra ord om en dataström som innehåller både ljud och video spåra och aktuell POST-begäran misslyckas, måste kodaren återansluta och skicka om två sista fragment för ljud spår som tidigare har skickats, och de två sista fragment för videon spåra, som tidigare har skickats, så att det finns inga data går förlorade. Kodaren måste upprätthålla en ”vidarebefordra” buffert på media fragment som skickas igen när anslutningen upprättas.

## <a name="5-timescale"></a>5. tidsrymd
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) beskriver användningen av tidsrymd för **SmoothStreamingMedia** (avsnittet 2.2.2.1) **StreamElement** (avsnittet 2.2.2.3) **StreamFragmentElement** () Avsnittet 2.2.2.6) och **LiveSMIL** (punkt 2.2.7.3.1). Om tidsrymd värdet inte finns, är används standardvärdet 10 000 000 (10 MHz). Även om formatspecifikationen Smooth Streaming inte blockerar användningen av andra tidsrymd värden, de flesta implementeringar av encoder använda denna standardinställning värde (10 MHz) för att generera Smooth Streaming mata in data. På grund av att den [Azure Media dynamisk paketering](media-services-dynamic-packaging-overview.md) funktion, rekommenderar vi att du använder en 90-KHz tidsrymd för videoströmmar och 44,1 eller 48.1 KHz för ljudströmmar. Om du använder olika tidsrymd värden för olika dataströmmar, måste dataström-nivå tidsrymd skickas. Mer information finns i [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definitionen för ”dataströmmen”
Dataströmmen är den grundläggande enheten av åtgärden i live införandet för att skapa direktsända presentationer hantering strömmande växling vid fel och redundans scenarier. Dataströmmen har definierats som en unik, fragmenterad MP4-bitstream som kan innehålla en enda spåra eller flera band. En fullständig presentation kan innehålla en eller flera strömmar, beroende på konfigurationen av direktsänd kodare. Följande exempel visar olika alternativ för att använda dataströmmar för att skapa en fullständig presentation.

**Exempel:** 

En kund vill skapa en direktsänd strömning presentation som innehåller följande ljud/video bithastighet:

Video – 3000 kbit/s, 1500 kbit/s, 750 kbit/s

Ljud – 128 kbit/s

### <a name="option-1-all-tracks-in-one-stream"></a>Alternativ 1: Alla spår i en dataström
I det här alternativet, en enda kodare genererar alla ljud/video spår och paketerar dem till en fragmenterad MP4-bitstream. Fragmenterad MP4-bitstream skickas via en HTTP POST-anslutning. I det här exemplet finns bara en dataström för live presentationen.

![Dataströmmar-ett spåra][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Alternativ 2: Varje spår i en separat ström
I det här alternativet kodaren placerar en spårning i varje fragment MP4-bitstream och publicerar alla strömmar över separat HTTP-anslutningar. Detta kan göras med en kodare eller flera kodare. Live införandet ser live presentationen som består av fyra dataströmmar.

![Dataströmmar separat spår][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Alternativ 3: Paketera ljud spåra med lägsta bithastighet video spåret till en dataström
I det här alternativet väljer kunden att paketera ljud spåra med lägsta bithastighet video spår i en fragment MP4 bitstream och lämna de andra två video spår som separata dataströmmar. 

![Spårar dataströmmar ljud och video][image4]

### <a name="summary"></a>Sammanfattning
Detta är inte en fullständig förteckning över alla möjliga införandet alternativ för det här exemplet. Som ett faktiskt stöds grupperingen spår i dataströmmar av live införandet. Kunder och kodare leverantörer kan välja sina egna implementeringar baserat på engineering komplexitet, kodare kapacitet och redundans och överväganden för växling vid fel. I de flesta fall är det dock bara en ljud spåra för hela live presentationen. Därför är det viktigt att säkerställa infognings-dataström som innehåller ljud spåra hälsa. Den här beräkningen ofta resulterar i att placera ljud spåra i sin egen dataström (som alternativ 2) eller paketering med lägsta bithastighet video spår (som alternativ 3). Dessutom för bättre redundans och feltolerans skicka samma ljud spår i två olika dataströmmar (alternativ 2 med redundant ljud spår) eller paketering ljud spåra med minst två av video spårar lägsta bithastighet (alternativ 3 med ljud paketerade i minst två videoströmmar) rekommenderas för Direktmigrering mata in i Media Services.

## <a name="7-service-failover"></a>7. -Redundans
Hänsyn till direktsänd strömning, är stöd för bra redundans viktigt för att säkerställa tillgängligheten för tjänsten. Media Services har utformats för att hantera olika typer av fel, inklusive nätverksfel, fel och problem med lagring. När den används tillsammans med rätt redundanslogiken från livekodaren sida, kan kunder uppnå en hög tillförlitlig tjänst för liveströmning från molnet.

I det här avsnittet diskuterar vi tjänsten redundanssituationer. I det här fallet felet sker någonstans i tjänsten och den visar sig som ett nätverksfel. Här följer några rekommendationer för kodare implementeringen för att hantera redundans för tjänsten:

1. Använd en 10 sekunder timeout för TCP-anslutningen upprättas. Om ett försök att upprätta anslutningen tar längre än 10 sekunder, avbryta åtgärden och försök igen. 
2. Använd en kort tidsgräns för att skicka meddelandet segment för HTTP-begäran. Om mål MP4 fragment varaktighet N sekunder Använd en skicka-timeout mellan N och 2 N: e sekund. till exempel om MP4 fragment varaktighet 6 sekunder, använda en tidsgräns på 6 och 12 sekunder. Om en timeout uppstår, återställa anslutningen, öppna en ny anslutning och återgå till dataströmmen mata in på den nya anslutningen. 
3. Underhålla en rullande buffert som har två sista fragment för varje spår som har och helt skickades till tjänsten.  Om HTTP POST-begäran för en dataström avslutas eller timeout före slutet på dataströmmen öppna en ny anslutning och börjar en annan HTTP POST-begäran, skicka dataströmmen huvuden, skicka de senaste två fragment för varje spår och återuppta dataströmmen utan att en d iscontinuity i tidslinjen media. Detta minskar risken för dataförluster.
4. Vi rekommenderar att kodaren inte begränsar antalet försök att upprätta en anslutning eller återuppta strömning efter ett TCP-fel har inträffat.
5. Efter ett TCP-fel:
  
    a. Den aktuella anslutningen måste stängas och en ny anslutning måste skapas för en ny HTTP POST-begäran.

    b. Den nya HTTP POST URL måste vara samma som den första POST-URL.
  
    c. Den nya HTTP POST inkludera dataströmmen huvuden (**ftyp**, **Live Manifest serverrutan**, och **moov** rutor) som är identiska med stream-huvudena i första POST.
  
    d. De två sista fragment skickas för varje spår skickas och strömning måste fortsätta utan att en avvikelse i tidslinjen för media. MP4 fragment tidsstämplar öka kontinuerligt, även över HTTP POST-förfrågningar.
6. Kodaren ska avsluta HTTP POST-begäran om data inte skickas med en hastighet som motsvarar MP4 fragment varaktighet.  En HTTP POST-begäran som inte skickar data kan förhindra Media Services snabbt kopplar från kodaren vid en tjänstuppdatering. Därför HTTP POST för sparse (ad signal) spårar bör vara kortvarigt och avslutar som sparse fragment skickas.

## <a name="8-encoder-failover"></a>8. kodaren växling vid fel
Kodaren redundans är den andra typen av failover-scenariot som måste åtgärdas för slutpunkt till slutpunkt live strömmande leverans. Felet inträffar på kodare sida i det här scenariot. 

![kodaren växling vid fel][image5]

Följande förväntningar gäller från slutpunkten live införandet när kodaren redundans inträffar:

1. En ny instans av kodaren ska skapas om du vill fortsätta direktuppspelning, enligt beskrivningen i diagrammet (dataströmmen för 3000k video med streckad linje).
2. Nya kodaren måste använda samma URL för HTTP POST-begäranden som misslyckats instans.
3. Nya kodaren POST-begäran måste innehålla samma fragmenterad MP4-huvud rutorna instans som misslyckats.
4. Nya kodaren måste synkroniseras korrekt med alla andra körs kodare för samma live presentation att generera synkroniserade ljud/video prover med justerade fragment gränser.
5. Nya dataströmmen måste vara samma sak med föregående dataströmmen och utbytbara på rubriken och fragment.
6. Nya kodaren bör försöka minimera dataförlust. Den `fragment_absolute_time` och `fragment_index` av media fragment bör öka från den punkt där kodaren senast avbröts. Den `fragment_absolute_time` och `fragment_index` bör öka i ett kontinuerligt sätt, men det är tillåtet att införa en avvikelse om det behövs. Media Services ignorerar fragment som den redan har tagit emot och bearbetas, så är det bättre att ta till en viss skicka fragment än att införa avbrott i tidslinjen media. 

## <a name="9-encoder-redundancy"></a>9. kodaren redundans
För vissa viktiga live-händelser att begäran ännu högre tillgänglighet och kvalitet rekommenderar vi att du gör aktiv / aktiv-kodare för sömlös redundans utan att förlora data.

![kodaren redundans][image6]

Enligt beskrivningen i det här diagrammet push två grupper av kodare två kopior av varje dataström samtidigt i live-tjänsten. Den här inställningen stöds eftersom Media Services kan filtrera ut dubbla fragment baserat på dataström-ID och fragment tidsstämpel. Resulterande direktsänd dataström och arkivera är en enda kopia av alla dataströmmar som är den bästa möjliga aggregeringen från två källor. Till exempel i hypotetiska extrema fall så länge det finns en kodare (det inte behöver vara samma) körs vid en viss tidpunkt för varje ström, den resulterande direktsända dataströmmen från tjänsten är kontinuerligt utan dataförlust. 

Kraven för det här scenariot är nästan desamma som kraven i ”kodare redundans” fallet, med undantaget att den andra uppsättningen kodare körs vid samma tillfälle som de primära kodare.

## <a name="10-service-redundancy"></a>10. tjänsten redundans
För hög redundant global distributionsplatsen måste ibland du ha mellan region säkerhetskopiering ska hantera regionala katastrofer. Expandera ”kodare redundans”-topologi kan kunder välja att en redundant tjänstdistribution i en annan region som är kopplad till den andra uppsättningen kodare. Kunder kan arbeta med en innehållsleveransnätverk provider för att distribuera en Global Traffic Manager framför de två distributionerna för att skicka sömlöst klienttrafik. Kraven för kodare är samma som ”kodare redundans”. Det enda undantaget är att den andra uppsättningen kodare måste vara pekar på en annan live infognings-slutpunkten. Följande diagram visar den här installationen:

![tjänsten redundans][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Särskilda typer av införandet format
Det här avsnittet beskriver särskilda typer av live införandet som utformats för att hantera specifika scenarier.

### <a name="sparse-track"></a>Sparse spåra
När en presentation direktsänd strömning med en rich-klient-upplevelse, ofta det är nödvändigt att överföra tid-synkroniserade händelser eller signalerar band med huvudsakliga media-data. Ett exempel på detta är dynamiska live ad infogning. Den här typen av händelse-signalering skiljer sig från vanlig ljud/video streaming sin sparse natur. Med andra ord signaling data sker vanligtvis inte kontinuerligt och intervallet kan vara svårt att förutse. Begreppet sparse spåra har utformats för att mata in och broadcast signaling i band-data.

Följande är en rekommenderad implementering för att föra in sparse spåra:

1. Skapa en separat fragmenterad MP4-bitstream som innehåller endast sparse spårar utan ljud/video spår.
2. I den **Live Manifest serverrutan** som definieras i avsnitt 6 i [1], använda den *parentTrackName* parametern för att ange namnet på det överordnade spåret. Mer information finns i avsnittet 4.2.1.2.1.2 i [1].
3. I den **Live Manifest serverrutan**, **manifestOutput** måste anges till **SANT**.
4. Eftersom sparse signaling händelsen, rekommenderar vi följande:
   
    a. I början av direktsänd händelse skickar kodaren rutorna första huvud tjänsten, vilket gör att tjänsten för att registrera sparse spåra i manifestet för klienten.
   
    b. Kodaren ska upphöra HTTP POST-begäran om data inte skickas. En tidskrävande HTTP POST som inte skickar data kan förhindra Media Services snabbt kopplar från kodaren vid service update eller server startas om. I dessa fall har mediaserver blockerats tillfälligt i en receive-åtgärd på socketen.
   
    c. Under den tid när signaling data inte är tillgänglig, kodaren SHOULD Stäng HTTP POST för begäran. När POST-begäran är aktiv ska kodaren skicka data.

    d. När du skickar sparse fragment kan kodaren ange ett explicit content-length-huvud, om den är tillgänglig.

    e. När du skickar sparse fragment med en ny anslutning ska kodaren börja skicka i rutorna sidhuvud, följt av de nya fragment. Detta är i de fall där växling sker mellan och den nya sparse-anslutningen upprättas till en ny server som inte har visas sparse spåra innan.

    f. Sparse spåra fragment blir tillgängliga för klienten när motsvarande överordnade spåra-fragment som har ett lika mycket eller större tidsstämpelvärde görs tillgänglig för klienten. Till exempel om sparse fragment har en tidsstämpel för t = 1000, förväntas som när klienten ser ”video” (förutsatt att namnet på överordnade spåra är ”video”) Fragmentera tidsstämpel 1000 eller senare, den kan hämta sparse fragment-t = 1000. Observera att den faktiska signalen kunde användas för en annan plats i en presentation tidslinje för den avsedda syften. I det här exemplet är det möjligt som sparse fragment av t = 1000 har en nyttolast för XML, vilket är för att infoga en annons i ett läge som ett par sekunder senare.

    g. Nyttolasten för sparse spåra fragment kan finnas i olika format (till exempel XML, text eller binär), beroende på scenario.

### <a name="redundant-audio-track"></a>Redundant ljud spåra
I ett typiskt HTTP anpassningsbar strömning scenario (till exempel Smooth Streaming eller TANKSTRECK), ofta finns bara ett ljud spår i hela presentationen. Till skillnad från video spår som har flera olika för klienten i felvillkor välja kan ljud spåra vara en enskild felpunkt om införandet av strömmen som innehåller ljud spåra har brutits. 

Media Services stöder live införandet av redundanta ljud spår för att lösa problemet. Tanken är att samma ljud spår kan skickas flera gånger i olika dataströmmar. Även om tjänsten registrerar endast ljud spåra en gång i manifestet för klienten, kan den använda redundant ljud spår som reserver för att hämta ljud fragment om det primära ljud spåret har problem. För att mata in redundant ljud spårar kodaren måste:

1. Skapa samma ljud spår i flera fragment MP4 bitstreams. Redundant ljud spårar måste vara samma sak med samma fragment tidsstämplar och vara utbytbara på rubriken och fragment.
2. Kontrollera att posten ”ljud” i den Live Server Manifest (avsnittet 6 i [1]) är detsamma för alla redundant ljud spår.

Följande implementeringen rekommenderas för redundant ljud spårar:

1. Skicka varje unik ljud spår i en dataström med sig själv. Dessutom skicka en redundant dataström för var och en av dessa ljud spåra strömmar, där andra dataströmmen skiljer sig från först endast av identifieraren i HTTP POST-URL: {protokollet} :// {server address} / {publicera punkt path}/Streams({identifier}).
2. Använda separata dataströmmar för att skicka två lägsta video bithastighet. Var och en av dessa strömmar måste även innehålla en kopia av varje unik ljud spår. När flera språk stöds ska dessa strömmar innehålla ljud spår för varje språk.
3. Använda separata serverinstanser (kodare) för att koda och skicka redundant strömmar som nämns i (1) och (2). 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
