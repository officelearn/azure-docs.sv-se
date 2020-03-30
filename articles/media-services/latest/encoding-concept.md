---
title: Koda video och ljud med Medietjänster
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs kodning av video och ljud med Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366578"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Koda video och ljud med Medietjänster

Termen kodning i Media Services gäller för konvertering av filer som innehåller digital video och/eller ljud från ett standardformat till ett annat, i syfte att (a) minska storleken på filerna och/eller (b) producera ett format som är kompatibelt med ett brett spektrum av enheter och appar. Den här processen kallas även videokomprimering eller omkodning. Mer information om begreppen finns i [datakomprimeringen](https://en.wikipedia.org/wiki/Data_compression) [och kodningen?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

Videor levereras vanligtvis till enheter och appar genom [progressiv nedladdning](https://en.wikipedia.org/wiki/Progressive_download) eller genom [adaptiv bithastighetsstreaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Media Services fakturerar inte för avbrutna eller felade jobb. Ett jobb som har nått 50 % förlopp och som har avbrutits faktureras till exempel inte till 50 % av jobbminuterna. Du debiteras bara för färdiga jobb.

* Om du vill leverera genom progressiv nedladdning kan du använda Azure Media Services för att konvertera en digital mediefil (mezzanine) till en [MP4-fil,](https://en.wikipedia.org/wiki/MPEG-4_Part_14) som innehåller video som har kodats med [H.264-codec](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) och ljud som har kodats med [AAC-codec.](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) Den här MP4-filen skrivs till en tillgång i ditt lagringskonto. Du kan använda Azure Storage API:er eller SDK:er (till exempel [STORAGE REST API](../../storage/common/storage-rest-api-auth.md) eller [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)för att hämta filen direkt. Om du skapade utdatatillgången med ett visst behållarnamn i lager använder du den platsen. Annars kan du använda Media Services för att [lista url:erna för tillgångsbehållare](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* För att förbereda innehåll för leverans genom adaptiv bitrate streaming, mezzanine filen måste kodas på flera bithastigheter (hög till låg). För att säkerställa en graciös övergång av kvalitet sänks videons upplösning när bithastigheten sänks. Detta resulterar i en så kallad kodning stege-en tabell med upplösningar och bithastigheter (se [automatiskt genererade adaptiv bitrate stege](autogen-bitrate-ladder.md)). Du kan använda Media Services för att koda mezzaninfilerna med flera bithastigheter. På så sätt får du en uppsättning MP4-filer och associerade konfigurationsfiler för direktuppspelning skrivna till en tillgång i ditt lagringskonto. Du kan sedan använda funktionen [Dynamisk paketering](dynamic-packaging-overview.md) i Media Services för att leverera videon via direktuppspelningsprotokoll som [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) och [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Detta kräver att du skapar en [streaming locator](streaming-locators-concept.md) och bygga streaming webbadresser som motsvarar de protokoll som stöds, som sedan kan överlämnas till enheter / appar baserat på deras funktioner.

I följande diagram visas arbetsflödet för kodning på begäran med dynamisk förpackning.

![Arbetsflöde för kodning på begäran med dynamisk förpackning](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

I det här avsnittet får du vägledning om hur du kodar ditt innehåll med Media Services v3.

## <a name="transforms-and-jobs"></a>Transformeringar och jobb

Om du vill koda med Media Services v3 måste du skapa en [transformering](https://docs.microsoft.com/rest/api/media/transforms) och ett [jobb](https://docs.microsoft.com/rest/api/media/jobs). Transformeringen definierar ett recept för kodningsinställningar och utdata. jobbet är en instans av receptet. Mer information finns i [Transformeringar och jobb](transforms-jobs-concept.md).

När du kodar med Media Services använder du förinställningar för att tala om för kodaren hur indatamediefilerna ska bearbetas. I Media Services v3 använder du Standard encoder för att koda dina filer. Du kan till exempel ange videoupplösning och/eller hur många ljudkanaler du vill ha i det kodade innehållet.

Du kan komma igång snabbt med en av de rekommenderade inbyggda förinställningarna baserat på branschens bästa praxis eller så kan du välja att skapa en anpassad förinställning för att rikta in dig på ditt specifika scenario eller enhetskrav. Mer information finns i [Koda med en anpassad transformering](customize-encoder-presets-how-to.md).

Från och med januari 2019, när kodning med standardkodaren för att producera MP4-filer, genereras en ny MPI-fil och läggs till i utdatatillgången. Den här MPI-filen är avsedd att förbättra prestanda för [dynamiska paketerings-](dynamic-packaging-overview.md) och direktuppspelningsscenarier.

> [!NOTE]
> Du bör inte ändra eller ta bort MPI-filen, eller ta något beroende i din tjänst på förekomsten (eller inte) av en sådan fil.

### <a name="creating-job-input-from-an-https-url"></a>Skapa jobbindata från en HTTPS-URL

När du skickar jobb för att bearbeta dina videor måste du tala om för Media Services var indatavideon finns. Ett av alternativen är att ange en HTTPS-URL som jobbinmatning. För närvarande stöder Media Services v3 inte segmenterad överföringskodning via HTTPS-url:er.

#### <a name="examples"></a>Exempel

* [Koda från en HTTPS-URL med .NET](stream-files-dotnet-quickstart.md)
* [Koda från en HTTPS-URL med REST](stream-files-tutorial-with-rest.md)
* [Koda från en HTTPS-URL med CLI](stream-files-cli-quickstart.md)
* [Koda från en HTTPS-URL med Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Skapa jobbindata från en lokal fil

Indatavideon kan lagras som en medietjänsttillgång, i vilket fall du skapar en indatatillgång baserad på en fil (lagras lokalt eller i Azure Blob-lagring).

#### <a name="examples"></a>Exempel

[Koda en lokal fil med inbyggda förinställningar](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Skapa jobbindata med underklipp

När du kodar en video kan du ange att även trimma eller klippa källfilen och producera en utdata som bara har en önskad del av indatavideon. Den här funktionen fungerar med alla [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) som har skapats med antingen [förinställningarna BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) eller förinställningarna Förinställningar för [StandardEncoderPreset.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)

Du kan ange att ett [jobb](https://docs.microsoft.com/rest/api/media/jobs/create) ska skapas med ett enda klipp av ett videobaserat eller livearkiv (en inspelad händelse). Jobbindata kan vara en tillgång eller en HTTPS-URL.

> [!TIP]
> Om du vill strömma ett underklipp till videon utan att koda om videon kan du överväga att använda [Förfiltreringsmanifest med Dynamisk paketerare](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Exempel

Se exempel:

* [Subclip en video med .NET](subclip-video-dotnet-howto.md)
* [Subclip en video med REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder följande inbyggda kodningsförinställningar:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) används för att ställa in en inbyggd förinställning för kodning av indatavideon med standardkodaren.

Följande förinställningar stöds för närvarande:

- **EncoderNamedPreset.AACGoodQualityAudio**: producerar en enda MP4-fil som bara innehåller stereoljud kodat vid 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (rekommenderas): Mer information finns i [automatiskt generera en bithastighet stege](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: exponerar en experimentell förinställning för innehållsmedveten kodning. Med tanke på eventuellt indatainnehåll försöker tjänsten automatiskt bestämma det optimala antalet lager och lämpliga bithastighets- och upplösningsinställningar för leverans genom adaptiv direktuppspelning. De underliggande algoritmerna kommer att fortsätta att utvecklas med tiden. Utdata kommer att innehålla MP4-filer med video och ljud interfolierade. Mer information finns i [Experimentell förinställning för innehållsmedveten kodning](content-aware-encoding.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p**: producerar en uppsättning av åtta GOP-anpassade MP4-filer, allt från 6000 kbps till 400 kbps och stereo AAC-ljud. Upplösningen börjar på 1080p och går ner till 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: producerar en uppsättning av sex GOP-anpassade MP4-filer, allt från 3400 kbps till 400 kbps och stereo AAC-ljud. Upplösning börjar på 720p och går ner till 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: producerar en uppsättning av fem GOP-anpassade MP4-filer, från 1600 kbps till 400 kbps och stereo AAC-ljud. Upplösningen börjar på 480p och går ner till 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: producerar en MP4-fil där videon kodas med H.264 codec på 6750 kbps och en bildhöjd på 1080 pixlar, och stereoljudet kodas med AAC-LC-codec på 64 kbit/ kbit/
- **EncoderNamedPreset.H264SingleBitrate720p**: producerar en MP4-fil där videon är kodad med H.264 codec på 4500 kbps och en bildhöjd på 720 pixlar, och stereoljudet kodas med AAC-LC-codec på 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD**: producerar en MP4-fil där videon kodas med H.264 codec på 2200 kbps och en bildhöjd på 480 pixlar, och stereoljudet kodas med AAC-LC-codec på 64 kbps.

Den senaste listan över förinställningar finns [i inbyggda förinställningar som ska användas för kodning av videor](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Mer om du vill se hur förinställningarna används finns i [Ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) beskriver inställningar som ska användas vid kodning av indatavideon med standardkodaren. Använd den här förinställningen när du anpassar Transform-förinställningar.

#### <a name="considerations"></a>Överväganden

När du skapar anpassade förinställningar gäller följande överväganden:

- Alla värden för höjd och bredd på AVC-innehåll måste vara en multipel av fyra.
- I Azure Media Services v3 är alla kodningbithastigheter i bitar per sekund. Detta skiljer sig från förinställningar med våra v2 API: er, som använde kilobit / sekund som enhet. Om bithastigheten i v2 till exempel angavs som 128 (kilobit/sekund) i v3 skulle den vara inställd på 128000 (bitar/sekund).

### <a name="customizing-presets"></a>Anpassa förinställningar

Media Services stöder fullt ut att anpassa alla värden i förinställningar för att uppfylla dina specifika kodningsbehov och krav. Exempel som visar hur du anpassar kodarförinställningar finns i listan nedan:

#### <a name="examples"></a>Exempel

- [Anpassa förinställningar med .NET](customize-encoder-presets-how-to.md)
- [Anpassa förinställningar med CLI](custom-preset-cli-howto.md)
- [Anpassa förinställningar med REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Förinställt schema

I Media Services v3 skrivs förinställningar starkt till att entiteterna i själva API:et skrivs. Du hittar "schema"-definitionen för dessa objekt i [Open API Specification (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också visa de förinställda definitionerna (till exempel **StandardEncoderPreset)** i [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (eller annan Media Services v3 SDK-referensdokumentation).

## <a name="scaling-encoding-in-v3"></a>Skalningskodning i v3

Om du vill skala mediebearbetningen finns i [Skala med CLI](media-reserved-units-cli-how-to.md).

## <a name="billing"></a>Fakturering

Media Services fakturerar inte för avbrutna eller felade jobb. Ett jobb som har nått 50 % förlopp och som har avbrutits faktureras till exempel inte till 50 % av jobbminuterna. Du debiteras bara för färdiga jobb.

Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="next-steps"></a>Nästa steg

* [Ladda upp, koda och strömma med Hjälp av Media Services](stream-files-tutorial-with-api.md).
* [Koda från en HTTPS-URL med inbyggda förinställningar](job-input-from-http-how-to.md).
* [Koda en lokal fil med inbyggda förinställningar](job-input-from-local-file-how-to.md).
* [Skapa en anpassad förinställning för att rikta in dig på ditt specifika scenario eller enhetskrav](customize-encoder-presets-how-to.md).
