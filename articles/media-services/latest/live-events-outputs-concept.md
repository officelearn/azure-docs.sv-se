---
title: Live-händelser och liveutdata begrepp i Azure Media Services v3
titleSuffix: Azure Media Services
description: Det här avsnittet innehåller en översikt över livehändelser och liveutdata i Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e6f2ad2c5c30e3c75e8d3588e386ea14e8e3350b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065952"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Live-evenemang och liveutgångar i medietjänster

Med Azure Media Services kan du leverera livehändelser till dina kunder i Azure-molnet. Om du vill konfigurera dina livestreaminghändelser i Media Services v3 måste du förstå de begrepp som beskrivs i den här artikeln.

> [!TIP]
> För kunder som migrerar från Api:er för mediatjänster v2 ersätter **livehändelseentiteten** **Kanal** i v2 och **Live Output** ersätter **Program**.

## <a name="live-events"></a>Livehändelser

[Livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en livehändelse skapas en primär och sekundär indataslutpunkt som du kan använda för att skicka en livesignal från en fjärrkodare. Fjärrströmgivaren skickar bidragsfeeden till den indataslutpunkten med hjälp av indataprotokollet [RTMP](https://www.adobe.com/devnet/rtmp.html) eller [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmenterad-MP4). För RTMP-intängningsprotokollet kan innehållet skickas`rtmp://`i det genomskinliga (`rtmps://`) eller krypteras säkert på tråden( ). För protokollet För utjämna direktuppspelning är `http://` de `https://`URL-scheman som stöds eller .  

## <a name="live-event-types"></a>Typer av livehändelse

En [livehändelse](https://docs.microsoft.com/rest/api/media/liveevents) kan ställas in på antingen en *genomströmning* (en lokal live-kodare skickar en multibitrate-ström) eller *livekodning* (en lokal live-kodare skickar en enda bitrate-ström). Typerna anges när [liveEventEncodingType skapas](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)under skapandet:

* **LiveEventEncodingType.None**: En lokal live-kodare skickar en multibitrate-ström. Den intövda strömmen passerar genom Live Event utan ytterligare bearbetning. Kallas även pass-through-läge.
* **LiveEventEncodingType.Standard**: En lokal live-kodare skickar en enda bitrate-ström till Live Event och Media Services skapar flera bithastighetsströmmar. Om bidragsflödet är 720p eller högre upplösning kodar **standardinställningen 720p** en uppsättning par med 6 upplösning/bithastigheter.
* **LiveEventEncodingType.Premium1080p**: En lokal live-kodare skickar en enda bitrate-ström till Live Event och Media Services skapar flera bithastighetsströmmar. Förinställningen Standard1080p anger utdatauppsättningen med upplösnings-/bithastighetspar.

### <a name="pass-through"></a>Direkt

![Genomgående Live Event med exempeldiagram för Medietjänster](./media/live-streaming/pass-through.svg)

När du använder en genomströmning av en **livehändelse** förlitar du dig på din lokala livekodare för att generera en videoström för flera bithastigheter och skicka den som bidragsflöde till livehändelsen (med RTMP eller fragmenterat MP4-protokoll). Livehändelsen passerar sedan via inkommande videoströmmar utan vidare bearbetning. En sådan livehändelse är optimerad för långvariga liveevenemang eller 24x365 linjär livestreaming. När du skapar den här typen av livehändelse kan du ange Ingen (LiveEventEncodingType.None).

Du kan skicka bidragsflödet med upplösningar på upp till 4K och i en bildfrekvens på 60 bilder/sekund, med antingen videocodecen H.264/AVC eller H.265/HEVC och ljudcodecen AAC (AAC-LC, HE-AACv1 eller HE-AACv2). Mer information finns i [Jämförelse med livehändelsetyper](live-event-types-comparison.md).

> [!NOTE]
> Att använda en genomströmningsmetod är det mest ekonomiska sättet att direktstreama när du gör flera händelser under en lång tidsperiod, och du har redan investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
>

Visa ett .NET-kodexempel i [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![live-kodning med exempeldiagram för Media Services](./media/live-streaming/live-encoding.svg)

När du använder direktkodning med Media Services konfigurerar du den lokala livekodaren för att skicka en enda bithastighetsvideo som bidragsfeed till Live Event (med RTMP- eller Fragmented-Mp4-protokollet). Du ställer sedan in en Live Event så att den kodar den inkommande enbitrate-strömmen till en [multibitrate-videoström](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)och gör utdata tillgängliga för leverans för att spela upp enheter via protokoll som MPEG-DASH, HLS och Smooth Streaming.

När du använder live-kodning kan du endast skicka bidragsflödet med upplösningar på upp till 1080p-upplösning med en bildhastighet på 30 bildrutor/sekund, med H.264/AVC-videocodec och AAC (AAC-LC, HE-AACv1 eller HE-AACv2) ljud codec. Observera att direktupppassning livehändelser kan stödja upplösningar på upp till 4K vid 60 bildrutor/sekund. Mer information finns i [Jämförelse med livehändelsetyper](live-event-types-comparison.md).

Upplösningarna och bithastigheterna i utdata från strömningen bestäms av förinställningen. Om du använder en **Standard** live-kodare (LiveEventEncodingType.Standard) anger förinställningen *Standard720p* en uppsättning par med sex upplösnings-/bithastigheter, från 720p vid 3,5 Mbit/s ner till 192p vid 200 kbps. Annars, om du använder en **Premium1080p** live-kodare (LiveEventEncodingType.Premium1080p), anger *förinställningen Standard1080p* en uppsättning par med sex upplösnings-/bithastigheter, från 1080p vid 3,5 Mbps ner till 180p vid 200 kbps. Mer information finns i [Systemförinställningar](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Om du behöver anpassa förinställningen för live-kodning öppnar du en supportbiljett via Azure-portalen. Ange önskad tabell med upplösning och bithastigheter. Kontrollera att det bara finns ett lager på 720p (om du begär en förinställning för en standard live-kodare) eller på 1080p (om du begär en förinställning för en Premium1080p live-kodare) och högst 6 lager.

## <a name="creating-live-events"></a>Skapa livehändelser

### <a name="options"></a>Alternativ

När du skapar en livehändelse kan du ange följande alternativ:

* Strömningsprotokollet för livehändelsen (för närvarande stöds protokollen RTMP och Smooth Streaming).<br/>Du kan inte ändra protokollalternativet medan Live-händelsen eller dess associerade liveutdata körs. Om du behöver olika protokoll skapar du en separat livehändelse för varje direktuppspelningsprotokoll.  
* När du skapar händelsen kan du ange att den ska startas automatiskt. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Faktureringen startar så snart livehändelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Du kan också starta händelsen när du är redo att börja streama.

    Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).

* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser anges och det inte finns någon regeldefinition tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IPV4-adress med fyra nummer eller CIDR-adressintervall.

    Om du vill aktivera vissa IP-adresser på dina egna brandväggar eller vill begränsa indata till dina livehändelser till Azure IP-adresser, laddar du ned en JSON-fil från [Azure Datacenter IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). Om du vill ha mer information om den här filen väljer du avsnittet **Information** på sidan.
    
* När du skapar händelsen kan du välja att aktivera Live Transkriptioner. <br/> Som standard är direkt transkription inaktiverad. Du kan inte ändra den här egenskapen medan Live Event eller dess associerade liveutdata körs. 

### <a name="naming-rules"></a>Namngivningsregler

* Max namn på liveevenemang är 32 tecken.
* Namnet ska följa detta [regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) mönster: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`.

Se även [namngivningskonventioner för strömmande slutpunkter](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> För att garantera unika för ditt livehändelsenamn kan du generera ett GUID och sedan ta bort alla bindestreck och kladdparenteser (om sådana finns). Strängen kommer att vara unik över alla live-evenemang och dess längd är garanterad att vara 32.

## <a name="live-event-ingest-urls"></a>Live Event inta webbadresser

När Live-händelsen har skapats kan du få ingående webbadresser som du tillhandahåller den aktiva lokala kodaren. Livekodaren använder dessa URL:er för att mata in en direktsänd dataström. Mer information finns i [Rekommenderade lokala live-kodare](recommended-on-premises-live-encoders.md).

Du kan antingen använda icke-anpassade eller anpassade URL:er.

> [!NOTE] 
> För att en webbadress ska vara prediktiv ställer du in "fåfänga"-läget.

* URL för icke-fåfänga

    Url för icke-fåfänga är standardläget i Media Services v3. Du eventuellt hämta livehändelsen snabbt, men inmatnings-URL är endast känd när livehändelsen startas. URL:en ändras om du stoppar/startar livehändelsen. <br/>Icke-Fåfänga är användbart i scenarier när en slutanvändare vill strömma med hjälp av en app där appen vill få en live-händelse ASAP och har en dynamisk intags-URL är inte ett problem.

    Om en klientapp inte behöver generera en intbeteringsadress innan Live-händelsen skapas kan du låta Media Services generera åtkomsttoken automatiskt för livehändelsen.

* Url till fåfänga

    Fåfänga-läget föredras av stora medieprogram som använder maskinvarusändningskodare och inte vill konfigurera om sina kodare när de startar Live Event. De vill ha en prediktiv intags-URL, som inte ändras med tiden.

    Om du vill ange `vanityUrl` `true` det här läget `false`ställer du in på vid skapande (standard är ). Du måste också skicka din`LiveEventInput.accessToken`egen åtkomsttoken ( ) vid skapande tid. Du anger tokenvärdet för att undvika en slumpmässig token i URL:en. Åtkomsttoken måste vara en giltig GUID-sträng (med eller utan bindestreck). När läget har ställts in kan det inte uppdateras.

    Åtkomsttoken måste vara unik i ditt datacenter. Om din app behöver använda en url för fåfänga rekommenderar vi att du alltid skapar en ny GUID-instans för din åtkomsttoken (i stället för att återanvända någon befintlig GUID).

    Använd följande API:er för att aktivera URL:en för fåfänga och ange åtkomsttoken till ett giltigt GUID (till exempel `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  

    |Språk|Aktivera URL för fåfänga|Ange åtkomst-token|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--fåfänga-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.accessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Namngivningsregler för live ingest-url

* Den *slumpmässiga* strängen nedan är ett 128-bitars hexadecimalt tal (som består av 32 tecken mellan 0 och 9 och a–f).
* *din åtkomsttoken:* Den giltiga GUID-strängen som du ställer in när du använder fåfänga-läget. Till exempel `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *strömnamn*: Anger strömnamnet för en viss anslutning. Flödesnamnvärdet läggs vanligtvis till av den live-kodare som du använder. Du kan konfigurera live-kodaren så att den använder vilket namn som helst för att beskriva anslutningen, till exempel: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-vanity-url"></a>URL för icke-fåfänga

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Url till fåfänga

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Url för förhandsversionen av Live Event

När Live-händelsen börjar ta emot bidragsflödet kan du använda förhandsgranskningsslutpunkten för att förhandsgranska och verifiera att du får livestreamen innan du publicerar den vidare. När du har kontrollerat att förhandsgranskningsströmmen är bra kan du använda Live-händelsen för att göra livestreamen tillgänglig för leverans via en eller flera (förskapade) slutpunkter för direktuppspelning. För att åstadkomma detta, skapa en ny [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs) på Live Event.

> [!IMPORTANT]
> Se till att videon flödar till förhandsgranskningsadressen innan du fortsätter!

## <a name="live-event-long-running-operations"></a>Live Event långvariga åtgärder

Mer information finns i [tidskrävande åtgärder](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Liveutdata

När du har strömmen flödar in i Live Event, kan du börja strömmande händelse genom att skapa en [tillgång,](https://docs.microsoft.com/rest/api/media/assets) [Live Output](https://docs.microsoft.com/rest/api/media/liveoutputs)och [Streaming Locator](https://docs.microsoft.com/rest/api/media/streaminglocators). Live Output arkiverar strömmen och gör den tillgänglig för tittare via [slutpunkten för direktuppspelning](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

Detaljerad information om liveutdata finns i [Använda en moln-DVR](live-event-cloud-dvr.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Se artikeln Vanliga frågor och [svar.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-and-get-updates"></a>Ställ frågor och få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs för livestreaming](stream-live-tutorial-with-api.md)
