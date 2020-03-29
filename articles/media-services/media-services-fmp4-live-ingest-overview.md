---
title: Azure Media Services fragmenterad MP4-specifikation för ingest - Microsoft-dokument
description: Den här specifikationen beskriver protokollet och formatet för fragmenterade MP4-baserade live streaming-inmatning för Azure Media Services. I det här dokumentet beskrivs också bästa praxis för att bygga mycket överflödiga och robusta mekanismer för levande intag.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 507afad294e8233ea4de4130795f29925870fcdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888061"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services fragmenterad MP4-specifikation för levande ingest 

Den här specifikationen beskriver protokollet och formatet för fragmenterade MP4-baserade live streaming-inmatning för Azure Media Services. Media Services tillhandahåller en livestreamingtjänst som kunderna kan använda för att strömma livehändelser och sända innehåll i realtid med hjälp av Azure som molnplattform. I det här dokumentet beskrivs också bästa praxis för att bygga mycket överflödiga och robusta mekanismer för levande intag.

## <a name="1-conformance-notation"></a>1. Konformationsansering
Nyckelorden "MÅSTE", "FÅR INTE", "KRÄVS", "SKALL", "SKALL INTE", "BÖR", "BÖR INTE", "REKOMMENDERAS", "MAJ" och "VALFRITT" i detta dokument ska tolkas som de beskrivs i RFC 2119.

## <a name="2-service-diagram"></a>2. Servicediagram
Följande diagram visar arkitekturen på hög nivå för livestreamingtjänsten i Media Services:

1. En live-kodare skickar live-feeds till kanaler som skapas och etableras via Azure Media Services SDK.
1. Slutpunkter för kanaler, program och direktuppspelning i Media Services hanterar alla funktioner för direktuppspelning, inklusive intag, formatering, moln-DVR, säkerhet, skalbarhet och redundans.
1. Du kan också välja att distribuera ett Azure Content Delivery Network-lager mellan slutpunkten för direktuppspelning och klientslutpunkterna.
1. Klientslutpunkter strömmas från slutpunkten för direktuppspelning med hjälp av HTTP Adaptive Streaming-protokoll. Exempel på detta är Microsoft Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH, or MPEG-DASH) och Apple HTTP Live Streaming (HLS).

![intag flöde][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream format - ISO 14496-12 fragmenterad MP4
Trådformatet för livestreaming som diskuteras i detta dokument är baserat på [ISO-14496-12]. En detaljerad förklaring av fragmenterat MP4-format och tillägg både för video-on-demand-filer och livestreaming intag, se [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).

### <a name="live-ingest-format-definitions"></a>Definitioner för live-inta-format
I följande lista beskrivs särskilda formatdefinitioner som gäller för live-intag i Azure Media Services:

1. **Ftyp,** **Live Server Manifest Box**och **moov** lådor måste skickas med varje begäran (HTTP POST). Dessa rutor MÅSTE skickas i början av strömmen och varje gång kodaren måste återansluta för att återuppta strömmen inta. Mer information finns i avsnitt 6 i [1].
1. Avsnitt 3.3.2 i [1] definierar en valfri ruta som heter **StreamManifestBox** för levande mat. På grund av routningslogiken för Azure-belastningsutjämnaren är det inaktuellt att använda den här rutan. Rutan SKA INTE finnas i media när du intas i Media Services. Om den här rutan finns ignorerar Media Services den tyst.
1. **Rutan TrackFragmentExtendedHeaderBox** som definieras i 3.2.3.2 i [1] MÅSTE finnas för varje fragment.
1. Version 2 av rutan **TrackFragmentExtendedHeaderBox** BÖR användas för att generera mediesegment som har identiska webbadresser i flera datacenter. Fältet fragmentindex krävs för redundans över flera datacenter av indexbaserade direktuppspelningsformat som Apple HLS och indexbaserad MPEG-DASH. För att möjliggöra redundans över flera datacenter måste fragmentindexet synkroniseras mellan flera kodare och ökas med 1 för varje efterföljande mediefragment, även över omstarter eller fel för kodaren.
1. Avsnitt 3.3.6 i [1] definierar en ruta som heter **MovieFragmentRandomAccessBox** (**mfra**) som kan skickas i slutet av levande intag för att indikera end-of-stream (EOS) till kanalen. På grund av medietjänsternas inmatningslogik är användning av EOS inaktuell och **mfra-rutan** för direkt intag bör INTE skickas. Om det skickas ignorerar Media Services det tyst. Om du vill återställa tillståndet för intempelplatsen rekommenderar vi att du använder [Kanalåterställning](https://docs.microsoft.com/rest/api/media/operations/channel#reset_channels). Vi rekommenderar också att du använder [Programstopp](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) för att avsluta en presentation och ström.
1. MP4-fragmentets varaktighet bör vara konstant, för att minska storleken på klientmanifesten. En konstant MP4 fragment varaktighet förbättrar också klient nedladdning heuristik med hjälp av upprepade taggar. Varaktigheten kan variera för att kompensera för icke-heltalsbildhastigheter.
1. MP4-fragmentets varaktighet bör vara mellan cirka 2 och 6 sekunder.
1. MP4 fragment tidsstämplar och index (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` och `fragment_index`) BÖR anlända i ökande ordning. Även om Media Services är motståndskraftigt mot dubblettfragment har de begränsad möjligheten att ordna om fragment enligt medietidslinjen.

## <a name="4-protocol-format--http"></a>4. Protokollformat – HTTP
ISO-fragmenterad MP4-baserad live-inta för Media Services använder en standard långvarig HTTP POST-begäran för att överföra kodade mediedata som är paketerade i fragmenterat MP4-format till tjänsten. Varje HTTP POST skickar en komplett fragmenterad MP4-bitström ("ström"), från början med rubrikrutor (**ftyp,** **Live Server Manifest Box**och **moov-rutor)** och fortsätter med en sekvens av fragment **(moof** och **mdat-rutor).** Url-syntax för HTTP POST-begäran finns i avsnitt 9.2 i [1]. Ett exempel på POST-url:en är: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

### <a name="requirements"></a>Krav
Här är de detaljerade kraven:

1. Kodaren ska starta sändningen genom att skicka en HTTP POST-begäran med en tom "brödtext" (noll innehållslängd) med samma inmatnings-URL. Detta kan hjälpa kodaren att snabbt identifiera om slutpunkten för direkt inmatning är giltig och om det finns någon autentisering eller andra villkor som krävs. Enligt HTTP-protokollet kan servern inte skicka tillbaka ett HTTP-svar förrän hela begäran, inklusive post-brödtexten, tas emot. Med tanke på den långvariga karaktären hos en livehändelse, utan det här steget, kanske kodaren inte kan identifiera något fel förrän den har skickat alla data.
1. Kodaren MÅSTE hantera eventuella fel eller autentiseringsutmaningar på grund av (1). Om (1) lyckas med ett 200 svar, fortsätt.
1. Kodaren MÅSTE starta en ny HTTP POST-begäran med den fragmenterade MP4-strömmen. Nyttolasten MÅSTE börja med rubrikrutorna, följt av fragment. Observera att **ftyp-**, **Live Server Manifest Box**och **moov-rutorna** (i den här ordningen) måste skickas med varje begäran, även om kodaren måste återansluta eftersom den tidigare begäran avslutades före slutet av strömmen. 
1. Kodaren MÅSTE använda segmenterad överföringskodning för uppladdning, eftersom det är omöjligt att förutsäga hela innehållslängden för livehändelsen.
1. När händelsen är över, efter att ha skickat det sista fragmentet, måste kodaren avsluta den segmenterade överföringskodningsmeddelandesekvensen (de flesta HTTP-klientstaplar hanterar det automatiskt). Kodaren MÅSTE vänta tills tjänsten returnerar den slutliga svarskoden och avslutar sedan anslutningen. 
1. Kodaren FÅR INTE använda `Events()` substantiv enligt beskrivningen i 9.2 i [1] för direkt intag i Media Services.
1. Om HTTP POST-begäran avslutas eller timeout med ett TCP-fel före slutet av dataströmmen, måste kodaren utfärda en ny POST-begäran med hjälp av en ny anslutning och följa de föregående kraven. Dessutom måste kodaren skicka de tidigare två MP4-fragmenten för varje spår i strömmen och återuppta utan att införa en diskontinuitet i mediatidslinjen. Om du skickar om de två senaste MP4-fragmenten för varje spår säkerställer du att det inte finns någon dataförlust. Med andra ord, om en ström innehåller både ett ljud- och ett videospår och den aktuella POST-begäran misslyckas, måste kodaren återansluta och skicka de två sista fragmenten för ljudspåret, som tidigare har skickats, och de två sista fragmenten för videon som tidigare har skickats för att säkerställa att det inte sker någon dataförlust. Kodaren MÅSTE behålla en "framåt" buffert av mediefragment, som den skickar om när den återansluts.

## <a name="5-timescale"></a>5. Tidsskala
[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) beskriver användningen av tidsskala för **SmoothStreamingMedia** (avsnitt 2.2.2.1), **StreamElement** (avsnitt 2.2.2.3), **StreamFragmentElement** (avsnitt 2.2.2.6) och **LiveSMIL** (avsnitt 2.2.7.3.1). Om tidsskalevärdet inte finns är standardvärdet 10 000 000 (10 MHz). Även om specifikationen för formatet Smooth Streaming inte blockerar användningen av andra tidsskalevärden använder de flesta kodarimplementeringar det här standardvärdet (10 MHz) för att generera smooth streaming-intagsdata. På grund av Azure [Media Dynamic Packaging-funktionen](media-services-dynamic-packaging-overview.md) rekommenderar vi att du använder en tidsskala på 90 KHz för videoströmmar och 44,1 KHz eller 48,1 KHz för ljudströmmar. Om olika tidsskalevärden används för olika strömmar måste tidsskalan på strömnivå skickas. Mer information finns i [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

## <a name="6-definition-of-stream"></a>6. Definition av "ström"
Stream är den grundläggande enheten för drift i live inmatning för att komponera livepresentationer, hantera strömmande redundans och redundansscenarier. Stream definieras som en unik, fragmenterad MP4-bitström som kan innehålla ett enda spår eller flera spår. En fullständig livepresentation kan innehålla en eller flera strömmar, beroende på konfigurationen av live-kodarna. Följande exempel illustrerar olika alternativ för att använda strömmar för att skriva en fullständig livepresentation.

**Exempel:** 

En kund vill skapa en livestreamingpresentation som innehåller följande ljud-/videobithastigheter:

Video – 3 000 kbps, 1 500 kbps, 750 kbps

Ljud – 128 kbit/s

### <a name="option-1-all-tracks-in-one-stream"></a>Alternativ 1: Alla spår i en ström
I det här alternativet genererar en enda kodare alla ljud-/videospår och buntar sedan ihop dem till en fragmenterad MP4-bitström. Den fragmenterade MP4-bitströmmen skickas sedan via en enda HTTP POST-anslutning. I det här exemplet finns det bara en ström för den här livepresentationen.

![Strömmar-ett spår][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Alternativ 2: Varje spår i en separat ström
I det här alternativet placerar kodaren ett spår i varje fragment MP4-bitström och bokför sedan alla strömmar via separata HTTP-anslutningar. Detta kan göras med en kodare eller med flera kodare. Live intag ser denna levande presentation som består av fyra strömmar.

![Strömmar-separata spår][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Alternativ 3: Bunta ihop ljudspår med det lägsta bitratevideospåret i en ström
I det här alternativet väljer kunden att bunta ljudspåret med det lägsta bitrate-videospåret i ett fragment MP4-bitström och lämna de andra två videospåren som separata strömmar. 

![Strömmar-ljud- och videospår][image4]

### <a name="summary"></a>Sammanfattning
Detta är inte en uttömmande lista över alla möjliga intagsalternativ för det här exemplet. I själva verket stöds varje gruppering av spår i strömmar av levande intag. Kunder och kodarleverantörer kan välja sina egna implementeringar baserat på teknisk komplexitet, kodarkapacitet och redundans- och redundansöverväganden. I de flesta fall finns det dock bara ett ljudspår för hela livepresentationen. Så det är viktigt att säkerställa sundheten i den intagsström som innehåller ljudspåret. Detta övervägande resulterar ofta i att sätta ljudspåret i sin egen ström (som i alternativ 2) eller bunta det med den lägsta bitrate videospår (som i alternativ 3). För bättre redundans och feltolerans skickar du också samma ljudspår i två olika strömmar (alternativ 2 med redundanta ljudspår) eller buntar ljudspåret med minst två av de lägsta bitrate-videospåren (alternativ 3 med ljud som levereras i minst två videoströmmar) rekommenderas starkt för live intas i Media Services.

## <a name="7-service-failover"></a>7. Redundans för tjänsten
Med tanke på den typ av live streaming, bra redundans stöd är avgörande för att säkerställa tillgängligheten av tjänsten. Media Services är utformat för att hantera olika typer av fel, inklusive nätverksfel, serverfel och lagringsproblem. När kunderna används tillsammans med rätt redundanslogik från live-kodarsidan kan de uppnå en mycket tillförlitlig livestreamingtjänst från molnet.

I det här avsnittet diskuterar vi scenarier för växling av tjänster. I det här fallet inträffar felet någonstans i tjänsten och det visar sig som ett nätverksfel. Här är några rekommendationer för kodarimplementering för hantering av serviceväxling:

1. Använd en 10-sekunders timeout för att upprätta TCP-anslutningen. Om ett försök att upprätta anslutningen tar längre tid än 10 sekunder avbryter du åtgärden och försöker igen. 
1. Använd en kort timeout för att skicka HTTP-meddelandesegmenten. Om varaktigheten för mål-MP4-fragmentet är N sekunder använder du en tidsgräns för sändning mellan N och 2 N sekunder. Om mp4-fragmentets varaktighet till exempel är 6 sekunder använder du en timeout på 6 till 12 sekunder. Om en timeout inträffar återställer du anslutningen, öppnar en ny anslutning och återupptar strömmen på den nya anslutningen. 
1. Underhåll en rullande buffert som har de två sista fragmenten för varje spår som har skickats till tjänsten.  Om HTTP POST-begäran om en ström avslutas eller timeout före slutet av strömmen öppnar du en ny anslutning och startar en annan HTTP POST-begäran, skickar om streamhuvudena, skickar de två sista fragmenten för varje spår och återupptar strömmen utan att införa en diskontinuitet i media tidslinjen. Detta minskar risken för dataförlust.
1. Vi rekommenderar att kodaren INTE begränsar antalet försök för att upprätta en anslutning eller återuppta direktuppspelning efter att ett TCP-fel inträffar.
1. Efter ett TCP-fel:
  
    a. Den aktuella anslutningen MÅSTE stängas och en ny anslutning måste skapas för en ny HTTP POST-begäran.

    b. Den nya HTTP POST-URL:en måste vara samma som den ursprungliga POST-URL:en.
  
    c. De nya HTTP POST MÅSTE innehålla strömma rubriker (**ftyp**, **Live Server Manifest Box**och **moov** lådor) som är identiska med strömma rubriker i den ursprungliga POST.
  
    d. De två sista fragmenten som skickas för varje spår måste skickas förbi och direktuppspelningen måste återupptas utan att en diskontinuitet i medietidslinjen introduceras. MP4-fragmenttidsstämplarna måste öka kontinuerligt, även över HTTP POST-begäranden.
1. Kodaren bör avsluta HTTP POST-begäran om data inte skickas med en hastighet som motsvarar MP4-fragmentets varaktighet.  En HTTP POST-begäran som inte skickar data kan hindra Media Services från att snabbt koppla från kodaren i händelse av en tjänstuppdatering. Därför bör HTTP POST för glesa (annonssignal) spår vara kortlivade, avsluta så snart det glesa fragmentet skickas.

## <a name="8-encoder-failover"></a>8. Encoder redundans
Encoder redundans är den andra typen av redundansscenario som måste åtgärdas för leverans från direktuppspelning från till. I det här fallet uppstår feltillståndet på kodarsidan. 

![redundansväxling för kodare][image5]

Följande förväntningar gäller från slutpunkten för direkt inmatning när redundansväxlingen för kodaren inträffar:

1. En ny kodarinstans bör skapas för att fortsätta direktuppspelningen, vilket illustreras i diagrammet (Stream for 3000k video, med streckad linje).
1. Den nya kodaren måste använda samma URL för HTTP POST-begäranden som den misslyckade instansen.
1. Den nya kodarens POST-begäran måste innehålla samma fragmenterade MP4-huvudrutor som den misslyckade instansen.
1. Den nya kodaren MÅSTE synkroniseras korrekt med alla andra löpgivare för samma livepresentation för att generera synkroniserade ljud-/videoexempel med justerade fragmentgränser.
1. Den nya strömmen MÅSTE vara semantiskt likvärdig med föregående ström och utbytbar på sidhuvuds- och fragmentnivå.
1. Den nya kodaren bör försöka minimera dataförlust. Och `fragment_absolute_time` `fragment_index` av mediefragment bör öka från den punkt där kodaren senast stoppas. Och `fragment_absolute_time` `fragment_index` BÖR öka kontinuerligt, men det är tillåtet att införa en diskontinuitet, om det behövs. Media Services ignorerar fragment som redan har tagits emot och bearbetats, så det är bättre att göra om på sidan av omskolningsfragment än att införa avbrott i medias tidslinje. 

## <a name="9-encoder-redundancy"></a>9. Redundans för kodare
För vissa kritiska livehändelser som kräver ännu högre tillgänglighet och kvalitet på upplevelsen rekommenderar vi att du använder aktiva redundanta kodare för att uppnå sömlös redundans utan dataförlust.

![redundans för kodare][image6]

Som illustreras i det här diagrammet, två grupper av kodare skjuta två kopior av varje ström samtidigt i live-tjänsten. Den här inställningen stöds eftersom Media Services kan filtrera bort dubblettfragment baserat på dataflödes-ID och fragmenttidsstämpel. Den resulterande live stream och arkiv är en enda kopia av alla strömmar som är den bästa möjliga aggregering från de två källorna. Till exempel, i ett hypotetiskt extremt fall, så länge det finns en kodare (det behöver inte vara samma) som körs vid en viss tidpunkt för varje ström, är den resulterande liveströmmen från tjänsten kontinuerlig utan dataförlust. 

Kraven för det här scenariot är nästan desamma som kraven i "Encoder redundans"-fallet, med undantag för att den andra uppsättningen kodare körs samtidigt som de primära kodarna.

## <a name="10-service-redundancy"></a>10. Service redundans
För mycket redundant global distribution måste du ibland ha säkerhetskopiering över flera regioner för att hantera regionala katastrofer. Om du expanderar på topologin "Encoder redundancy" kan kunderna välja att ha en redundant tjänstdistribution i en annan region som är ansluten till den andra uppsättningen kodare. Kunder kan också arbeta med en Content Delivery Network-leverantör för att distribuera en Global Traffic Manager framför de två tjänstdistributionerna för att sömlöst dirigera klienttrafik. Kraven för kodarna är desamma som "Encoder redundans"-fallet. Det enda undantaget är att den andra uppsättningen kodare måste pekas på en annan levande intag slutpunkt. Följande diagram visar den här inställningen:

![redundans för tjänsten][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. Särskilda typer av intagsformat
I det här avsnittet beskrivs särskilda typer av live-inmatningsformat som är utformade för att hantera specifika scenarier.

### <a name="sparse-track"></a>Gles spår
När du levererar en livestreamingpresentation med en rik klientupplevelse är det ofta nödvändigt att överföra tidsynkroniserade händelser eller signaler i bandet med de viktigaste mediedata. Ett exempel på detta är dynamisk live-insättning. Denna typ av händelse signalering skiljer sig från vanliga ljud / video streaming på grund av dess glesa karaktär. Med andra ord sker signaldata vanligtvis inte kontinuerligt, och intervallet kan vara svårt att förutsäga. Begreppet glesspår var utformat för att inta och sända in-band signaldata.

Följande steg är en rekommenderad implementering för intag av glesspår:

1. Skapa en separat fragmenterad MP4-bitström som bara innehåller glesa spår, utan ljud-/videospår.
1. I **rutan Visa liveserver** enligt definitionen i avsnitt 6 i [1] använder du parametern *parentTrackName* för att ange namnet på det överordnade spåret. Mer information finns i avsnitt 4.2.1.2.1.2 i [1].
1. I **manifestrutan för Live Server**måste **manifestOutput** vara inställt på **true**.
1. Med tanke på signalhändelsens glesa natur rekommenderade vi följande:
   
    a. I början av livehändelsen skickar kodaren de första rubrikrutorna till tjänsten, vilket gör att tjänsten kan registrera det glesa spåret i klientmanifestet.
   
    b. Kodaren ska avsluta HTTP POST-begäran när data inte skickas. Ett långvarigt HTTP POST som inte skickar data kan hindra Media Services från att snabbt koppla från kodaren i händelse av en tjänstuppdatering eller omstart av servern. I dessa fall blockeras medieservern tillfälligt i en mottagningsåtgärd på socketen.
   
    c. Under den tid då signaldata inte är tillgängliga bör kodaren stänga HTTP POST-begäran. Medan POST-begäran är aktiv ska kodaren skicka data.

    d. När du skickar glesa fragment kan kodaren ange ett explicit innehållslängdshuvud om det är tillgängligt.

    e. När du skickar glesa fragment med en ny anslutning bör kodaren börja skicka från rubrikrutorna, följt av de nya fragmenten. Detta gäller i de fall där redundans sker däremellan, och den nya glesa anslutningen upprättas till en ny server som inte har sett det glesa spåret tidigare.

    f. Det glesa spårfragmentet blir tillgängligt för klienten när motsvarande överordnade spårfragment som har ett lika stort eller större tidsstämpelvärde görs tillgängligt för klienten. Om det glesa fragmentet till exempel har en tidsstämpel på t=1000, förväntas det att när klienten ser "video" (förutsatt att det överordnade spårnamnet är "video") fragmenttidsstämpel 1000 eller senare, kan den hämta det glesa fragmentet t=1000. Observera att den faktiska signalen kan användas för en annan position i presentationstidslinjen för det avsedda syftet. I det här exemplet är det möjligt att det glesa fragmentet av t=1000 har en XML-nyttolast, vilket är till för att infoga en annons på en position som är några sekunder senare.

    g. Nyttolasten för glesa spårfragment kan vara i olika format (till exempel XML, text eller binär), beroende på scenariot.

### <a name="redundant-audio-track"></a>Redundant ljudspår
I ett typiskt HTTP-anpassat direktuppspelningsscenario (till exempel Smooth Streaming eller DASH) finns det ofta bara ett ljudspår i hela presentationen. Till skillnad från videospår, som har flera kvalitetsnivåer för klienten att välja mellan i felförhållanden, kan ljudspåret vara en enda felpunkt om inmatningen av strömmen som innehåller ljudspåret är trasigt. 

För att lösa det här problemet stöder Media Services direkt inmatning av redundanta ljudspår. Tanken är att samma ljudspår kan skickas flera gånger i olika strömmar. Även om tjänsten bara registrerar ljudspåret en gång i klientmanifestet, kan den använda redundanta ljudspår som säkerhetskopior för att hämta ljudfragment om det primära ljudspåret har problem. För att få in redundanta ljudspår måste kodaren:

1. Skapa samma ljudspår i mp4-bitarströmmar med flera fragment. De redundanta ljudspåren MÅSTE vara semantiskt likvärdiga, med samma fragmenttidsstämplar, och vara utbytbara på sidhuvuds- och fragmentnivå.
1. Kontrollera att "ljud"-posten i Live Server Manifestet (avsnitt 6 i [1]) är densamma för alla redundanta ljudspår.

Följande implementering rekommenderas för redundanta ljudspår:

1. Skicka varje unikt ljudspår i en ström av sig själv. Skicka också en redundant ström för var och en av dessa ljudspårsströmmar, där den andra strömmen skiljer sig från den första endast av identifieraren i HTTP POST-URL:en: {protocol}://{server address}/{publishing point path}/Streams({identifier}).
1. Använd separata strömmar för att skicka de två lägsta videobitrates. Var och en av dessa strömmar bör också innehålla en kopia av varje unikt ljudspår. När flera språk till exempel stöds bör dessa strömmar innehålla ljudspår för varje språk.
1. Använd separata serverinstanser (kodare) för att koda och skicka de redundanta strömmar som nämns i (1) och (2). 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
