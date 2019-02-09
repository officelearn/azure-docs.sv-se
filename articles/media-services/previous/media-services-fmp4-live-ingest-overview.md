---
title: Specifikation för Azure Media Services fragmenterad MP4 live-inmatning | Microsoft Docs
description: Den här specifikationen beskriver protokollet och format för fragmenterad MP4-baserade live direktuppspelning inmatning för Azure Media Services. Du kan använda Azure Media Services för att strömma direktsändningar och skicka innehållet i realtid genom att använda Azure som molnplattformen. Det här dokumentet beskriver också bästa metoder för att skapa mycket redundant och robust live mata in mekanismer.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: cenkd;juliako
ms.openlocfilehash: 16b8b5a012c5d2073a3472a70cf2064b8b0e59cd
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984842"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification-legacy"></a>Specifikation (äldre) för Azure Media Services fragmenterad MP4 live-inmatning

Den här specifikationen beskriver protokollet och format för fragmenterad MP4-baserade live direktuppspelning inmatning för Azure Media Services. Media Services tillhandahåller en liveuppspelningstjänst som kunder kan använda för att strömma direktsändningar och skicka innehållet i realtid genom att använda Azure som molnplattformen. Det här dokumentet beskriver också bästa metoder för att skapa mycket redundant och robust live mata in mekanismer.

## <a name="1-conformance-notation"></a>1. Efterlevnadsstatus notation
Nyckeln ”måste”, ”måste inte”, ”krävs”, ”skall”, ”skall inte”, ”SHOULD”, ”bör inte” ord ”rekommenderade”, ”MAY” och ”valfritt” i det här dokumentet är att tolkas som de beskrivs i RFC 2119.

## <a name="2-service-diagram"></a>2. Tjänstdiagram
Följande diagram visar den övergripande arkitekturen i tjänsten för live direktuppspelning i Media Services:

1. En livekodare skickar live feeds till kanaler som skapas och etablerats via Azure Media Services SDK.
1. Kanaler, program och slutpunkter för direktuppspelning i Media Services hanterar alla liveuppspelningsfunktioner, bland annat inmatning, formatering, DVR, säkerhet, skalbarhet och redundans i molnet.
1. Kunder kan också välja att distribuera ett Azure Content Delivery Network-lager mellan slutpunkten för direktuppspelning och klientslutpunkterna.
1. Klienten slutpunkter ström från slutpunkten för direktuppspelning med hjälp av anpassad strömning av HTTP-protokoll. Exempel innefattar Microsoft Smooth Streaming, Dynamic Adaptive Streaming via HTTP (DASH eller MPEG-DASH) och Apple HTTP Live Streaming (HLS).

![mata in flödet][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream format – ISO 14496 – 12 fragmenterad MP4
Kabelformat för direktsänd strömning mata in beskrivs i det här dokumentet är baserat på [ISO-14496-12]. En detaljerad förklaring av fragmenterad MP4-format och tillägg både för filer för video på begäran och direktsänd strömning datainmatning, se [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Liveinmatning filformatet
I följande lista beskrivs specialformat definitioner som tillämpas på live-inmatning i Azure Media Services:

1. Den **ftyp**, **Live Server Manifest Box**, och **moov** rutorna måste skickas med varje begäran (HTTP-POST). Dessa rutor måste skickas i början av strömmen och när som helst kodaren måste ansluta igen om du vill återuppta stream mata in. Mer information finns i avsnitt 6 i [1].
1. Avsnitt 3.3.2 i [1] definierar en valfri ruta som kallas **StreamManifestBox** för liveinmatning. På grund av Routning logik på Azure-belastningsutjämnaren är med hjälp av den här rutan inaktuell. Rutan bör inte vara närvarande när mata in till Media Services. Om den här rutan finns ignorerar medietjänster tyst den.
1. Den **TrackFragmentExtendedHeaderBox** box som definierats i 3.2.3.2 i [1] måste vara tillgänglig för varje fragment.
1. Version 2 av den **TrackFragmentExtendedHeaderBox** box som ska användas för att generera media-segment som har identiska URL: er i flera datacenter. Fragment index-fältet är obligatoriskt för redundans över flera datacenter för index-baserade strömningsformat, till exempel Apple HLS och index-baserade MPEG-DASH. För att aktivera redundans över flera datacenter, fragment indexet måste synkroniseras mellan flera kodare och öka med 1 för varje efterföljande media-fragment även över encoder omstarter eller fel.
1. Avsnittet 3.3.6 i [1] definierar en ruta som kallas **MovieFragmentRandomAccessBox** (**mfra**) som kan skickas i slutet av live-inmatning att ange slutet av stream (EOS) till kanalen. På grund av logiken för inmatning av Media Services, med hjälp av EOS är inaktuell, och **mfra** rutan för live-inmatning inte bör skickas. Om skickas ignorerar medietjänster tyst den. Du kan återställa punktens inmatning, rekommenderar vi att du använder [kanal återställa](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Vi rekommenderar också att du använder [programmet stoppa](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) att avsluta en presentation och stream.
1. MP4 fragment varaktighet måste vara konstanta att minska storleken på klientmanifesten. En konstant MP4 fragment varaktighet förbättrar också klienten ladda ned heuristik med Upprepa taggar. Varaktigheten kan variera för att kompensera för heltal bildrutehastigheter.
1. MP4 fragment varaktighet måste vara mellan 2 och cirka 6 sekunder.
1. MP4 Fragmentera tidsstämplar och index (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` och `fragment_index`) ska tas emot i stigande ordning. Även om Media Services är motståndskraftig mot duplicerade fragment, har det begränsad möjlighet att ändra ordning på fragment enligt media-tidslinje.

## <a name="4-protocol-format--http"></a>4. Protokollet formatet – HTTP
ISO fragmenterad MP4-baserade live mata in för Media Services använder en standard tidskrävande HTTP POST-begäran för att överföra kodade media data som är paketerat i fragmenterad MP4-format till tjänsten. Varje HTTP POST-skickar ett komplett fragmenterad MP4 bitstream (”stream”), med start i början med rubriken rutorna (**ftyp**, **Live Server Manifest Box**, och **moov** rutor ), och du fortsätter med en sekvens med fragment (**moof** och **mdat** rutorna). URL-syntax för HTTP POST-begäran, finns i avsnittet 9.2 in [1]. Ett exempel på POST-URL är: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Krav
Här följer de detaljerade krav:

1. Kodaren starta sändningen genom att skicka en HTTP POST-begäran med en tom ”body” (noll innehållslängd) med hjälp av URL: en för samma inmatning. Detta kan hjälpa kodaren snabbt identifiera om live-inmatning slutpunkten är giltig och om det inte finns någon autentisering eller andra villkor som krävs. Per HTTP-protokollet servern kan inte skicka tillbaka ett HTTP-svar tills hela begäran, inklusive själva POST tas emot. Den tidskrävande naturen för en direktsändning, utan det här steget kanske kodaren inte kan identifiera eventuella fel tills den är klar skickar alla data.
1. Kodaren måste hantera några fel eller autentiseringsfrågor på grund av (1). Om (1) lyckas med svaret 200 fortsätta.
1. Kodaren måste börja en ny HTTP POST-begäran med fragmenterad MP4-dataström. Nyttolasten måste börja med rutorna rubrik, följt av fragment. Observera att den **ftyp**, **Live Server Manifest Box**, och **moov** rutorna (i den ordningen) måste skickas med varje begäran, även om kodaren måste ansluta igen eftersom den tidigare begäran avbröts innan slutet av dataströmmen. 
1. Kodaren måste använda Chunked-kodning för att ladda upp, eftersom det är omöjligt att förutsäga direktsändningen hela innehåll längd.
1. När händelsen är över, när du har skickat det senaste fragmentet måste kodaren ett smidigt sätt att sluta den Chunked-kodning Meddelandeordningen (de flesta stackar för HTTP-klienten hanterar det automatiskt). Kodaren måste vänta på att tjänsten ska returnera den sista svarskoden och avslutar anslutningen. 
1. Kodaren måste inte använda den `Events()` substantiv enligt beskrivningen i 9.2 in [1] för live-inmatning till Media Services.
1. Om HTTP POST-begäran avslutas eller tidsgränsen uppnås med en TCP-fel före slutet av strömmen, måste kodaren skicka en ny POST-begäran med hjälp av en ny anslutning och följ föregående kraven. Kodaren måste dessutom skicka om de föregående två MP4-fragment för varje spår i strömmen och återuppta utan introduktion till en avvikelse i media-tidslinje. Skicka om de två sista MP4-fragment för varje spår säkerställer att det finns inga data går förlorade. Med andra ord, om en dataström som innehåller både en ljud och video reda och aktuell POST-begäran misslyckas, måste kodaren återansluta och skicka om de två sista fragment för ljudspår, som tidigare har skickats, och de två sista fragment för videon spåra, som tidigare har skickats, så att det finns inga data går förlorade. Kodaren måste ha en ”vidarebefordra” buffert för media fragment som innehållsuppsättning när anslutningen upprättas.

## <a name="5-timescale"></a>5. Tidsskala
[[MS-SSTR] ](https://msdn.microsoft.com/library/ff469518.aspx) beskriver användningen av tidsram för **SmoothStreamingMedia** (avsnittet 2.2.2.1), **StreamElement** (avsnittet 2.2.2.3), **StreamFragmentElement** () Avsnittet 2.2.2.6), och **LiveSMIL** (punkt 2.2.7.3.1). Om värdet för tidsskalan inte är tillgänglig, är standardvärdet används 10 000 000 (10 MHz). Även om formatspecifikationen Smooth Streaming inte blockerar användningen av andra tidsskalan värden, de flesta implementeringar av kodare använda denna standardinställning värde (10 MHz) för att generera Smooth Streaming mata in data. På grund av den [dynamisk paketering för Azure Media](media-services-dynamic-packaging-overview.md) funktion, rekommenderar vi att du använder en 90-KHz tid för video strömmar och 44,1 eller 48.1 KHz för ljudströmmar. Om du använder olika tidsskalan värden för olika dataströmmar, måste stream på servernivå tidsskalan skickas. Mer information finns i [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definitionen av ”stream”
Stream är den grundläggande körningsenheten på åtgärden i live-inmatning för att skapa presentationer, hantering av strömmande redundans och scenarier för redundans. Stream har definierats som en unik, fragmenterad MP4-bitstream som kan innehålla ett enda spår eller flera band. En fullständig direktpresentation kan innehålla en eller flera strömmar, beroende på konfigurationen av livekodare. I följande exempel visas olika alternativ för att använda strömmar till att skapa en fullständig direktpresentation.

**Exempel:** 

En kund vill skapa en live direktuppspelning presentation som innehåller följande ljud/video olika bithastigheter:

Video – 3000 kbps, 1500 kbps, 750 kbps

Ljud – 128 kbit/s

### <a name="option-1-all-tracks-in-one-stream"></a>Alternativ 1: Alla spår i en ström
I det här alternativet kan en enda encoder genererar alla ljud/video spår och paketerar dem i en fragmenterad MP4-bitstream. Fragmenterad MP4-bitstream skickas via en enkel HTTP POST-anslutning. I det här exemplet har endast en ström för den här direktsändningen.

![Strömmar-ett spår][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Alternativ 2: Varje spår i en separat ström
I det här alternativet kodaren placerar ett spår i varje fragment MP4-bitstream och publicerar alla strömmar över separat HTTP-anslutningar. Detta kan göras med en kodare eller med flera kodare. Live-inmatning ser den här direktsändningen som består av fyra strömmar.

![Strömmar separat spår][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Alternativ 3: Samlings ljudspår med bithastighet lägsta video spåret till en ström
Det här alternativet kan väljer kunden du paketerar ljudspår med lägsta bithastigheter video spår i ett fragment MP4 bitstream och lämna de andra två video spår som separata strömmar. 

![Strömmar ljud och video spår][image4]

### <a name="summary"></a>Sammanfattning
Detta är inte en uttömmande förteckning över alla möjliga inmatning alternativ för det här exemplet. Så det är faktiskt, stöds någon gruppering av spår till byteströmmar av live-inmatning. Kunder och leverantörer encoder kan välja sina egna implementeringar baserat på tekniska komplexiteten, encoder kapacitet och redundans och tänka på vid. I de flesta fall är det dock endast ett ljudspår för hela live-presentationen. Därför är det viktigt att säkerställa att dataströmmen inmatning som innehåller ljudspåret hälsa. Den här beräkningen ofta ger placera ljudspåret i sin egen stream (som i alternativ 2) eller paketera med lägsta bithastigheter video banan (som alternativ 3). Dessutom för bättre dataredundans och feltolerans, skicka samma ljudspåret i två olika dataströmmar (alternativ 2 med redundant ljudspår) eller paketera ljudspår med minst två av lägsta bithastigheter video spår (alternativ 3 med ljud paketeras i minst två video strömmar) rekommenderas starkt live för inmatning i Media Services.

## <a name="7-service-failover"></a>7. Tjänstredundans
Den angivna natur direktsänd strömning, är bra redundansstöd viktiga för att säkerställa tillgängligheten för tjänsten. Media Services är utformad för att hantera olika typer av fel, inklusive nätverksfel, fel och problem med lagring. När detta används tillsammans med rätt redundanslogiken från live-kodare sida kan kan kunderna nå en mycket pålitlig liveuppspelningstjänst från molnet.

I det här avsnittet diskuterar vi redundansscenarier för tjänsten. I det här fallet felet inträffar någonstans i tjänsten och det visar sig som ett nätverksfel. Här följer några rekommendationer för kodare implementeringen för hantering av tjänstredundans:

1. Använd en 10 sekunder timeout för TCP-anslutningen upprättas. Om ett försök att upprätta anslutningen tar längre tid än tio sekunder, avbryta åtgärden och försök igen. 
1. Använd en kort timeout för att skicka HTTP-begäran meddelande segment. Om målet MP4 fragment varaktighet är N sekunder Använd en timeout för sändning mellan N och 2 N sekunder. till exempel om hela MP4 fragment är 6 sekunder, använda en tidsgräns på 6 och 12 sekunder. Om en timeout inträffar, Återställ anslutningen, öppnar en ny anslutning och återuppta stream mata in på den nya anslutningen. 
1. Underhålla en löpande buffert som har de två sista fragment för varje spår som har och helt skickades till tjänsten.  Om HTTP POST-begäran för en dataström avslutas eller tidsgränsen uppnås före slutet på strömmen, öppnar en ny anslutning och börjar en annan HTTP POST-begäran, skicka om stream-rubriker, skicka om de två sista fragment för varje spår och återuppta dataströmmen utan att en d iscontinuity media-tidslinje. Detta minskar risken för dataförlust.
1. Vi rekommenderar att kodaren inte begränsar antalet försök att upprätta en anslutning eller återuppta strömning efter ett TCP-fel uppstår.
1. När du har en TCP-fel:
  
    a. Den aktuella anslutningen avslutas och en ny anslutning måste skapas för en ny HTTP POST-begäran.

    b. Den nya HTTP POST-URL måste vara samma som den första POST-URL.
  
    c. Den nya måste för HTTP POST inkludera stream rubriker (**ftyp**, **Live Server Manifest Box**, och **moov** rutorna) som är identiska till stream-rubriker i det första INLÄGGET.
  
    d. De två sista fragment skickas för varje spår skickas och direktuppspelning måste fortsätta utan introduktion till en avvikelse i media-tidslinje. MP4 fragment tidsstämplar måste öka kontinuerligt, även över HTTP POST-förfrågningar.
1. Kodaren ska avsluta HTTP POST-begäran om data inte skickas till en kostnad som proportion till MP4 fragment varaktighet.  En HTTP POST-begäran som inte skickar data kan förhindra att Media Services snabbt kopplar från kodaren i händelse av en tjänstuppdatering. Därför HTTP-POST för null-optimerade (ad signal) spårar bör vara kortvarigt och avslutar så snart det null-optimerade fragmentet skickas.

## <a name="8-encoder-failover"></a>8. Kodaren redundans
Kodaren redundans är den andra typen av redundansscenario som måste åtgärdas för slutpunkt till slutpunkt live strömmande leverans. I det här scenariot inträffar feltillstånd på encoder-sida. 

![Kodaren redundans][image5]

Följande förväntningar gäller från slutpunkten för live inmatning när encoder redundans inträffar:

1. En ny encoder-instans ska skapas om du vill fortsätta streaming, vilket visas i diagrammet (Stream för 3000k video med streckad linje).
1. Den nya encoder måste använda samma URL: en för HTTP POST-begäran som den felaktiga instansen.
1. Den nya encoder POST-begäran måste innehålla samma fragmenterad MP4-huvud rutorna som den felaktiga instansen.
1. Den nya encoder måste synkroniseras korrekt med alla andra pågående kodare för samma direktpresentation att generera synkroniserade ljud/video-exempel med justerade fragment gränser.
1. Ny dataströmmen måste vara samma sak med föregående stream och utbytbara på rubriken och fragment.
1. Den nya encoder bör försöka minimera dataförlust. Den `fragment_absolute_time` och `fragment_index` av media fragment bör öka från den punkt där kodaren senast stoppad. Den `fragment_absolute_time` och `fragment_index` bör öka i ett kontinuerligt sätt, men det är möjligt att introducera ett avbrott, om det behövs. Media Services ignorerar fragment som den redan har tagit emot och bearbetas, så är det bättre att ta till en viss skickas igen fragment än att introducera avbrott på tidslinjen för media. 

## <a name="9-encoder-redundancy"></a>9. Kodaren redundans
För vissa viktiga live-händelser som kräver ännu högre tillgänglighet och kvalitet rekommenderar vi att du använder redundanta aktiv-aktiv-kodare för problemfri sömlös redundans utan att förlora data.

![Kodaren redundans][image6]

Enligt beskrivningen i det här diagrammet, skicka två grupper av kodare två kopior av varje dataström samtidigt till live-tjänsten. Den här konfigurationen stöds eftersom Media Services kan filtrera bort duplicerade fragment baserat på stream-ID och i fragmentbegäran tidsstämpel. Resulterande direktsänd ström och arkivlagring är en enda kopia av alla dataströmmar som är den bästa möjliga sammansättning från två källor. Till exempel i hypotetisk extrema fall så länge det finns en kodare (det inte behöver vara samma konto) som körs vid en given tidpunkt för varje dataström resulterande live stream från tjänsten är kontinuerligt utan dataförlust. 

Kraven för det här scenariot är nästan samma sätt kraven i ”Encoder redundans” fall med undantag som den andra uppsättningen av kodare körs samtidigt som de primära kodarna.

## <a name="10-service-redundancy"></a>10. Tjänstredundans
För mycket redundant global distribution, måste ibland du ha interregionala säkerhetskopiering för att hantera regionala katastrofer. Som ett led i ”Encoder redundans”-topologi, kan kunderna välja att ha en redundant tjänstdistribution i en annan region som är kopplad till den andra uppsättningen av kodare. Kunder kan också arbeta med en Content Delivery Network-provider för att distribuera en Global Traffic Manager framför distributionerna av två tjänster för att dirigera sömlöst klienttrafik. Kraven för för kodarna som är samma som ”Encoder redundans”. Det enda undantaget är att den andra uppsättningen av kodare måste vara pekar på en annan live mata in slutpunkt. Följande diagram visar den här konfigurationen:

![Tjänstredundans][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Särskilda typer av inmatning format
Det här avsnittet beskriver särskilda typer av live-inmatning format som är utformade för att hantera specifika scenarier.

### <a name="sparse-track"></a>Null-optimerade spåra
När du levererar direktuppspelning direktpresentation en rich-klient-upplevelse, ofta det krävs för att överföra tid-synkroniserade händelser eller signalerar till band med de viktigaste media-data. Ett exempel på detta är dynamiska live ad-insättning. Den här typen av händelse signalering skiljer sig från vanlig ljud/video på grund av sin natur som null-optimerade för direktuppspelning. Med andra ord signaling data sker vanligtvis inte kontinuerligt och intervallet kan vara svårt att förutse. Begreppet sparse spåra har utformats för att mata in och skicka in-band-signaling data.

Följande är en rekommenderad implementering för att föra in sparse track:

1. Skapa en separat fragmenterad MP4-bitstream som innehåller endast sparse spårar utan ljud/video spår.
1. I den **Live Server Manifest Box** enligt definitionen i avsnitt 6 i [1], använda den *parentTrackName* parametern för att ange namnet på den överordnade kursen. Mer information finns i avsnitt 4.2.1.2.1.2 i [1].
1. I den **Live Server Manifest Box**, **manifestOutput** måste anges till **SANT**.
1. Den null-optimerade naturen för händelsen signaling, rekommenderar vi följande:
   
    a. I början av direktsändningen skickar kodaren rutorna inledande rubriken till tjänsten, som kan registrera den null-optimerade kursen i klient-manifest-tjänsten.
   
    b. Kodaren ska upphöra HTTP POST-begäran om data inte skickas. En tidskrävande HTTP POST som inte skickar data kan förhindra att Media Services snabbt kopplar från kodaren i händelse av service update eller server startas om. I dessa fall kan blockeras temporärt mediaserver i en receive-åtgärd på socket.
   
    c. Under den tid då signaling data inte är tillgänglig, kodaren SHOULD Stäng HTTP POST för begäran. POST-begäran är aktiv, ska vilken kodare som skicka data.

    d. När du skickar sparse fragment kan kodaren ange ett explicit content-length-huvud, om den är tillgänglig.

    e. När du skickar sparse fragment med en ny anslutning ska kodaren börja skicka från rutorna rubrik, följt av de nya fragment. Det här är i de fall som redundans inträffar däremellan, och nya sparse anslutningen upprättas till en ny server som inte påträffats sparse spåra innan.

    f. Null-optimerade spåra fragment blir tillgängliga för klienten när motsvarande överordnade track-fragment som har en lika stor eller större tidsstämpelvärde görs tillgänglig för klienten. Till exempel om null-optimerade fragment har en tidsstämpel för t = 1000, förväntas som när klienten ser ”video” (förutsatt att dess överordnade namn är ”video”) Fragmentera tidsstämpel 1000 eller senare, den kan hämta sparse fragment t = 1000. Observera att den faktiska signalen kan användas för en annan plats i presentationen tidslinjen för sitt avsedda ändamål. I det här exemplet är det möjligt som null-optimerade fragment av t = 1000 har en nyttolast för XML, vilket är för att infoga en annons i ett läge som är ett par sekunder senare.

    g. Nyttolasten för null-optimerade spåra fragment kan finnas i olika format (till exempel XML, text eller binär), beroende på scenario.

### <a name="redundant-audio-track"></a>Redundant ljudspår
I en typisk HTTP anpassningsbar direktuppspelningsredundans (till exempel Smooth Streaming eller DASH), ofta finns bara ett ljudspår i hela presentationen. Till skillnad från video spår, som har flera servicenivåer för klienten att välja bland i felvillkor kan ljudspåret vara en enskild felpunkt om inmatning av strömmen som innehåller ljudspåret är bruten. 

Lös problemet, stöder Media Services live inmatning av redundanta ljudspår. Tanken är att samma ljudspåret kan skickas som flera gånger i olika dataströmmar. Även om tjänsten registrerar endast ljudspåret en gång i manifestet klienten, kan den använda redundanta ljudspår som säkerhetskopieringar för att hämta ljud fragment om det primära ljudspåret har problem med. För att mata in redundant ljudspår kodaren måste:

1. Skapa samma ljudspåret i flera fragment MP4 bitstreams. Redundant ljudspår måste vara samma sak med samma fragment tidsstämplarna och vara utbytbara på rubriken och fragment.
1. Kontrollera att posten ”ljud” i den Live Server Manifest (avsnitt 6 i [1]) är detsamma för alla redundant ljudspår.

Följande implementering rekommenderas för redundant ljudspår:

1. Skicka varje unik ljudspår i en ström med sig själv. Dessutom skicka en redundant ström för var och en av dessa ljudspår strömmar, där andra dataströmmen skiljer sig från först bara av ID i HTTP POST-URL: {protokollet} :// {serveradress} / {publicera punkt path}/Streams({identifier}).
1. Använda separata strömmar för att skicka två lägsta video bithastighet. Var och en av dessa strömmar bör också innehålla en kopia av varje unikt ljudspår. När flera språk stöds ska dessa strömmar innehålla ljudspår för varje språk.
1. Använda separata serverinstanser (kodare) för att koda och skicka redundant ström nämns i (1) och (2). 

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
