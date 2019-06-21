---
title: Kodning i molnet med Media Services – Azure | Microsoft Docs
description: Det här avsnittet beskriver hur du kodning med Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303937"
---
# <a name="encoding-with-media-services"></a>Encoding med Media Services

Termen kodning i Media Services gäller för att konvertera filer som innehåller digital video och/eller ljud från ett format som standard till en annan, att (a) minska storleken på filerna och/eller (b) producera ett format som är kompatibel med en brett utbud av enheter och program. Den här processen kallas också video komprimering och medieomkodning nätverk. Se den [datakomprimering](https://en.wikipedia.org/wiki/Data_compression) och [vad är kodning och Transkodning?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) ytterligare beskrivning av begrepp.

Videor levereras vanligtvis till enheter och program genom att [Progressiv nedladdning](https://en.wikipedia.org/wiki/Progressive_download) eller via [strömning med anpassad bithastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* För att leverera progressiv hämtning, kan du använda Azure Media Services för att konvertera en digital mediefil (mezzanine) till en [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) filen som innehåller video som har kodats med den [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) codec, och ljud som har kodats med den [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) codec. Den här MP4-filen skrivs till en tillgång i ditt storage-konto. Du kan använda Azure Storage API: er eller SDK: er (till exempel [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), eller [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) att hämta filen direkt. Om du har skapat utdata tillgång med ett specifikt behållarnamn i lagring, använda den platsen. Annars kan du använda Media Services för att [lista över URL: er för tillgången behållare](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* För att förbereda innehåll för leverans av strömning med anpassad bithastighet, måste av mezzaninfilen kodas på flera olika bithastigheter (högt till lågt). För att säkerställa rätt övergång av kvalitet, eftersom bithastigheten sjunker, så är lösning av videon. Detta resulterar i en s.k. kodning ladder – en tabell med upplösningar och bithastigheter för utdata (se [automatiskt genererade anpassningsbar bithastighetsstege](autogen-bitrate-ladder.md)). Du kan använda Media Services för att koda ditt mezzanine-filer på flera olika bithastigheter – gör det, du får en uppsättning MP4-filer och tillhörande strömmande konfigurationsfiler som skrivs till en tillgång i ditt storage-konto. Du kan sedan använda den [dynamisk paketering](dynamic-packaging-overview.md) kapaciteten i Media Services för att leverera video via direktuppspelning protokoll som [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) och [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Detta måste du skapa en [Strömningspositionerare](streaming-locators-concept.md) och skapa strömmande URL: er som motsvarar de protokoll som stöds, som kan sedan lämnas till enheter/program baserat på deras funktioner.

Följande diagram visar arbetsflödet för på begäran kodning med dynamisk paketering.

![Dynamisk paketering](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Det här avsnittet ger vägledning om att koda ditt innehåll med Media Services v3.

## <a name="transforms-and-jobs"></a>Transformeringar och jobb

Om du vill koda med Media Services v3, måste du skapa en [transformera](https://docs.microsoft.com/rest/api/media/transforms) och en [jobbet](https://docs.microsoft.com/rest/api/media/jobs). Vi definierar ett recept för kodning inställningar och utdata; jobbet har en instans av receptet. Mer information finns i [transformeringar och jobb](transforms-jobs-concept.md)

När encoding med Media Services, använder du förinställningar som talar om kodaren hur inkommande mediefiler ska bearbetas. Du kan till exempel ange video upplösning och/eller antalet ljud kanaler som du vill i det kodade innehållet. 

Du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningar baserat på branschens bästa praxis eller du kan välja att skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet. Mer information finns i [koda med en anpassad transformering](customize-encoder-presets-how-to.md). 

Från och med januari 2019 när encoding med Media Encoder Standard till producerar MP4-filer, en ny .mpi-fil och lagts till i utdata tillgången. Den här MPI-filen är avsedd att förbättra prestanda för [dynamisk paketering](dynamic-packaging-overview.md) och strömning av scenarier.

> [!NOTE]
> Du bör inte ändra eller ta bort filen MPI eller ta alla beroenden i din tjänst med befintliga (eller inte) av sådan fil.

### <a name="creating-job-input-from-an-https-url"></a>Skapa jobbindata från en HTTPS-URL

När du skickar in jobb för att bearbeta videor måste du ange var du hittar indatavideon för Media Services. Något av alternativen är att ange en HTTPS-URL som ett jobb som indata. Media Services v3 stöder för närvarande inte Chunked-kodning över HTTPS-adresser. 

#### <a name="examples"></a>Exempel

* [Koda från en HTTPS-URL med .NET](stream-files-dotnet-quickstart.md)
* [Koda från en HTTPS-URL med REST](stream-files-tutorial-with-rest.md)
* [Koda från en HTTPS-URL med CLI](stream-files-cli-quickstart.md)
* [Koda från en HTTPS-URL med Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Skapa jobbindata från en lokal fil

Indatavideon kan lagras som en Media Service-tillgång då du skapar en indatatillgången som baseras på en fil (som lagras lokalt eller i Azure Blob storage). 

#### <a name="examples"></a>Exempel

[Koda en lokal fil med hjälp av inbyggda förinställningar](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Skapa jobbindata med underklippning

När kodning en video, kan du ange för att även trimma eller källfilen och producera utdata med endast en önskad del av indatavideon ska ha. Den här funktionen fungerar med alla [transformera](https://docs.microsoft.com/rest/api/media/transforms) som skapats med hjälp av antingen den [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) förinställningar, eller [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) förinställningar. 

Du kan ange om du vill skapa en [jobbet](https://docs.microsoft.com/rest/api/media/jobs/create) med ett enda klipp för en video på begäran eller live-arkivet (en inspelade händelse). Jobbindata kan vara en tillgång eller en HTTPS-URL.

> [!TIP]
> Om du vill strömma ett sublip av videon utan reencoding videon kan du använda [före filtrering manifest med dynamiska Paketeraren](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exempel

Se exempel:

* [Underklipp en video med .NET](subclip-video-dotnet-howto.md)
* [Underklipp en video med REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder för närvarande följande inbyggda förinställningar för kodning:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) används för att ange en inbyggd förinställning för encoding indatavideon med Standard-kodare. 

Följande förinställningar stöds för närvarande:

- **EncoderNamedPreset.AACGoodQualityAudio** -producerar en enda MP4-fil som innehåller endast stereo ljud kodade med 192 kbit/s.
- **EncoderNamedPreset.AdaptiveStreaming** (rekommenderas). Mer information finns i [autogenerering en bithastighetsstege](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -Exponerar en experimentella förinställd för innehåll-medvetna encoding. Angivna indata innehåll försöker tjänsten automatiskt fastställa det optimala antalet lager, lämpliga bithastighet och upplösningen för leverans av Adaptiv direktuppspelning. De underliggande algoritmerna fortsätter att utvecklas över tid. Utdata innehåller MP4-filer med video och ljud överlagrad. Mer information finns i [Experimental förinställning för innehåll-medvetna encoding](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** -producerar en uppsättning 8 GOP-justerad MP4-filer, sträcker sig från 6000 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas vid 1080p och ned 360 p.
- **EncoderNamedPreset.H264MultipleBitrate720p** -producerar en uppsättning 6 GOP-justerad MP4-filer, sträcker sig från 3400 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas vid 720p och ned 360 p.
- **EncoderNamedPreset.H264MultipleBitrateSD** -producerar en uppsättning 5 GOP-justerad MP4-filer, sträcker sig från 1600 kbit/s till 400 kbit/s och stereo AAC-ljud. Lösning startas på 480 pixlar och ned 360 p.
- **EncoderNamedPreset.H264SingleBitrate1080p** -producerar en MP4-fil där videon kodas med H.264-codec i 6750 kbit/s och höjdvärdet bild 1 080 bildpunkter och stereo ljudet är kodad med AAC-LC codec med 64 kbit/s.
- **EncoderNamedPreset.H264SingleBitrate720p** -producerar en MP4-fil där videon kodas med H.264-codec på 4500 kbit/s och en Bildhöjd på 720 bildpunkter och stereo ljudet är kodad med AAC-LC codec med 64 kbit/s.
- **EncoderNamedPreset.H264SingleBitrateSD** -producerar en MP4-fil där videon kodas med H.264-codec på 2200 kbit/s och en bild höjden 480 pixlar och stereo ljudet är kodad med AAC-LC codec med 64 kbit/s.

Den senaste listan finns i [inbyggda förinställningar som ska användas för kodning videor](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Om du vill se hur förinställningarna används, Kolla in [överför, koda och strömma filer](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beskriver inställningar som ska användas när kodning indatavideon med Standard-kodare. Använd denna förinställning när du anpassar transformeringen förinställningar. 

#### <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar, gäller följande:

- Alla värden för höjd och bredd på AVC innehåll måste vara en multipel av 4.
- I Azure Media Services v3 är alla kodning bithastighet i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som används kilobit per sekund som enheten. Till exempel om bithastigheten i v2 har angetts som 128 (kbit/s) skulle i v3 det ställas in till 128000 (bitar per sekund).

### <a name="customizing-presets"></a>Anpassa förinställningar

Media Services stöder helt anpassa alla värden i förinställningar för att uppfylla dina specifika kodning behov och krav. Exempel som visar hur du anpassar encoder förinställningar finns:

#### <a name="examples"></a>Exempel

- [Anpassa förinställningar med .NET](customize-encoder-presets-how-to.md)
- [Anpassa förinställningar med CLI](custom-preset-cli-howto.md)
- [Anpassa förinställningar med REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Förinställda schemat

Förinställningar finns i Media Services v3, starkt typifierad entiteter i själva API: T. Du kan hitta ”schema” definitionen för dessa objekt i [Open API Specification (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också visa de förinställda definitionerna (t.ex. **StandardEncoderPreset**) i den [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (eller andra Media Services v3 SDK referensdokumentation).

## <a name="scaling-encoding-in-v3"></a>Skala kodning i v3

Om du vill skala mediebearbetning Se [skala med CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Ladda upp, koda och strömma med Media Services](stream-files-tutorial-with-api.md)
* [Koda från en HTTPS-URL med hjälp av inbyggda förinställningar](job-input-from-http-how-to.md)
* [Koda en lokal fil med hjälp av inbyggda förinställningar](job-input-from-local-file-how-to.md)
* [Skapa en anpassad förinställning om du vill rikta in dina specifika krav för scenario eller enhet](customize-encoder-presets-how-to.md)