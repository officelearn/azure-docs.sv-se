---
title: Självstudier för avancerat Media Encoder Premium Workflow
description: Det här dokumentet innehåller genomgångar som visar hur du utför avancerade uppgifter med Media Encoder Premium Workflow och även hur du skapar komplexa arbetsflöden med Arbetsflödesdesignern.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251016"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Självstudier för avancerat Media Encoder Premium Workflow
## <a name="overview"></a>Översikt
Det här dokumentet innehåller genomgångar som visar hur du anpassar arbetsflöden med **Arbetsflödesdesignern**. Du hittar de faktiska arbetsflödesfilerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>Innehållsförteckningen
Följande ämnen behandlas:

* [Kodning MXF till en enda bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Starta ett nytt arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Använda mediefilinmatningen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Granska medieströmmar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Lägga till en videokodare för . MP4-filgenerering](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Koda ljudströmmen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexing Audio och video strömmar till en MP4-behållare](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Skriva MP4-filen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Skapa en Medietjänsttillgång från utdatafilen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testa det färdiga arbetsflödet lokalt](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodning MXF till multibitrate MP4s - dynamisk förpackning aktiverad](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Lägga till ytterligare en eller flera MP4-utgångar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurera filutdatanamnen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Lägga till ett separat ljudspår](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Lägga till "ISM" SMIL-filen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodning MXF till multibitrate MP4 - förbättrad ritning](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Översikt över arbetsflöden för att förbättra
  * [Namngivningskonventioner för filer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicera komponentegenskaper på arbetsflödesroten](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Har genererade utdatafilnamn förlita sig på publicerade egenskapsvärden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Lägga till miniatyrer i multibitrate MP4-utgång](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Översikt över arbetsflöde för att lägga till miniatyrer i
  * [Lägga till JPG-kodning](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Hantera konvertering av färgrymd](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Skriva miniatyrer](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Identifiera fel i ett arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Slutfört arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Tidsbaserad trimning av multibitrate MP4-utgång](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Arbetsflödesöversikt för att börja lägga till trimning i](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Använda Stream Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Slutfört arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduktion till skriptkomponenten](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skript i ett arbetsflöde: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Rambaserad trimning av multibitrate MP4-utgång](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Skissöversikt för att börja lägga till trimning i](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Använda XML-koden för klipplistan](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Ändra klipplistan från en skriptkomponent](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Lägga till en bekvämlighetsegenskap för Klippbare](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Kodning MXF till en enda bitrate MP4
Det här avsnittet visar hur du skapar en enda bithastighet . MP4-fil med AAC-HE kodade ljud från en . MXF-indatafil.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Starta ett nytt arbetsflöde
Öppna Arbetsflödesdesignern och välj Fil > ny arbetsyta > koda om skiss

Det nya arbetsflödet visar tre element:

* Primär källfil
* XML för klipplista
* Utdatafil/tillgång  

![Nytt kodningsarbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nytt kodningsarbetsflöde*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>Använda mediefilinmatningen
För att acceptera indatamediefilen börjar man med att lägga till en komponent för mediefilinmatning. Om du vill lägga till en komponent i arbetsflödet letar du efter den i sökrutan Databas och drar önskad post till designerfönstret. Upprepa åtgärden för indata för mediefil och anslut komponenten Primär källfil till indatanålen Filnamn från indata för mediefil.

![Indata för ansluten mediefil](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Indata för ansluten mediefil*

Identifiera först en lämplig exempelfil som ska användas när du utformar ett anpassat arbetsflöde. Det gör du genom att klicka på bakgrundsfönstrets designerfönster och leta efter egenskapen Primär källfil i egenskapsfönstret till höger. Klicka på mappikonen och välj önskad fil för att testa arbetsflödet. Komponenten Mediefilinmatning kontrollerar filen och fyller i dess utdatastift för att återspegla information om exempelfilen som den inspekterade.

![Indata för ifylld mediefil](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Indata för ifylld mediefil*

Nu när indata är ifylld är nästa steg att ställa in inställningarna för utdatakodning. På samma sätt som den primära källfilen har konfigurerats konfigurerar du nu egenskapen Variabel för utdatamapp, precis under den.

![Konfigurerade in- och utdataegenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurerade in- och utdataegenskaper*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Granska medieströmmar
Ofta är det önskvärt att veta hur strömmen ser ut när den flödar genom arbetsflödet. Om du vill granska en ström när som helst i arbetsflödet klickar du bara på en utdata- eller indatanål på någon av komponenterna. Försök i så fall att klicka på den okomprimerade videoutdatastiftet från mediefilinmatningen. En dialogruta öppnas som gör det möjligt att inspektera den utgående videon.

![Granska den okomprimerade utdatastiftet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Granska den okomprimerade utdatastiftet*

I det här fallet visar det att videon innehåller en 1920x1080 ingång på 24 bilder per sekund i 4:2:2 sampling för en video på nästan 2 minuter.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Lägga till en videokodare för . MP4-filgenerering
Nu är en okomprimerad video och flera okomprimerade ljudutdatanålar tillgängliga för användning i mediefilinmatningen. För att koda den inkommande videon måste en kodningskomponent läggas till i arbetsflödet - i det här fallet för att generera . MP4-filer.

Om du vill koda videoströmmen till H.264 lägger du till AVC Video Encoder-komponenten på designerytan. Den här komponenten tar en okomprimera videoström som indata och levererar en AVC-komprimerad videoström på utdatastiftet.

![Oansluten AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Oansluten AVC-kodare*

Dess egenskaper avgör exakt hur kodningen sker. Låt oss ta en titt på några av de viktigare inställningarna:

* Utdatabredd och utdatahöjd: bestämmer upplösningen på den kodade videon. I det här fallet är 640x360 en bra inställning.
* Bildhastighet: när den är inställd på att passera det kommer bara att anta källbildhastigheten, är det möjligt att åsidosätta detta ändå. Sådan framerate konvertering är inte rörelsekompenserade.
* Profil och nivå: bestämmer AVC-profilen och -nivån. Om du enkelt vill få mer information om de olika nivåerna och profilerna klickar du på frågetecknet på AVC Video Encoder-komponenten och hjälpsidan visar mer information om var och en av nivåerna. I det här exemplet använder du Huvudprofilen på nivå 3.2 (standard).
* Rate Control Mode och Bitrate (kbps): i det här scenariot väljer du en konstant bithastighet (CBR) utgång vid 1200 kbps
* Videoformat: innehåller information om VUI (Video Användbarhetsinformation) som skrivs in i H.264-strömmen (sidoinformation som kan användas av en avkodare för att förbättra bildskärmen men inte nödvändigt att avkoda på rätt sätt):
* NTSC (typiskt för USA eller Japan, med 30 fps)
* PAL (typiskt för Europa, med 25 fps)
* GOP-storleksläge: ställ fast GOP-storlek för våra ändamål med ett nyckelintervall på 2 sekunder med stängda GOPs. Inställningen på 2 sekunder säkerställer kompatibilitet med den dynamiska paketering som Azure Media Services tillhandahåller.

Om du vill mata AVC-kodaren ansluter du den okomprimerade videoutdatastiftet från komponenten Mediefilinmatning till indatastiftet Okomprimerad video från AVC-kodaren.

![Ansluten AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Ansluten AVC Huvudkodare*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Koda ljudströmmen
Nu måste den ursprungliga okomprimerade ljudströmmen fortfarande komprimeras. För komprimering av ljudströmmen lägga till en AAC Encoder (Dolby) komponent i arbetsflödet.

![Oansluten AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Osammanhängande AAC-kodare*

Nu finns det en inkompatibilitet: det finns bara en enda okomprimerad ljudingång stift från AAC Encoder medan mer än sannolikt Media File Input kommer att ha två olika okomprimerade ljudströmmar tillgängliga: en för den vänstra ljudkanalen och en till höger. (Om du har att göra med surroundljud, det är sex kanaler.) Så det är inte möjligt att direkt ansluta ljudet från Media File Input källa till AAC ljudkodare. AAC-komponenten förväntar sig en så kallad "interfolierad" ljudström: en enda ström som har både vänster och höger kanal sammanflätade med varandra. När vi vet från vår källa mediefil att ljudspår är på vilken position i källan, kan vi generera en sådan interfolierad ljudström med korrekt tilldelade högtalare positioner för vänster och höger.

Först vill man generera en interfolierad ström från de nödvändiga källljudkanalerna. Audio Stream Interleaver-komponenten hanterar detta åt oss. Lägg till det i arbetsflödet och anslut ljudutgångarna från mediefilinmatningen till det.

![Ansluten ljudström Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ansluten ljudström Interleaver*

Nu när vi har en interfolierad ljudström, angav vi fortfarande inte var du ska tilldela vänster eller höger högtalare positioner till. För att specificera detta kan vi utnyttja speakerpositionstilldelaren.

![Lägga till en speakerpositionstilldelare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Lägga till en speakerpositionstilldelare*

Konfigurera speakerpositionstilldelaren för användning med en stereoinmatningsström via ett förinställt kodfilter av "Anpassad" och kanalförinställningen "2.0 (L,R)." (Detta tilldelar den vänstra högtalarpositionen kanal 1 och höger högtalarposition till kanal 2.)

Anslut utdata från speakerpositionstilldelaren till ingången till AAC-kodaren. Tala sedan om för AAC-kodaren att arbeta med en "2.0 (L,R)" Channel Preset, så att den vet att hantera stereoljud som ingång.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing Audio och video strömmar till en MP4-behållare
Med tanke på vår AVC kodade videoström och vår AAC kodade ljudström, kan vi fånga både i en . MP4-behållare. Processen att blanda olika strömmar till en enda kallas "multiplexing" (eller "muxing"). I det här fallet är vi interleaving ljudet och videoströmmar i en enda sammanhängande . MP4-paketet. Den komponent som koordinerar detta för en . MP4-behållaren kallas ISO MPEG-4 Multiplexer. Lägg till en till designern ytan och anslut både AVC Video Encoder och AAC Encoder till sina ingångar.

![Ansluten MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Ansluten MPEG4 Multiplexer*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Skriva MP4-filen
När du skriver en utdatafil används komponenten Filutdata. Vi kan ansluta detta till produktionen av ISO MPEG-4 Multiplexer så att dess utgång blir skriven till disk. Det gör du genom att ansluta utdatastiftet Container (MPEG-4) till indatastiftet för filutdata.

![Ansluten filutdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ansluten filutdata*

Filnamnet som används bestäms av egenskapen File. Även om den egenskapen kan hårdkodas till ett visst värde, vill man troligen ställa in den genom ett uttryck i stället.

Om du vill att arbetsflödet automatiskt ska bestämma egenskapen för utdatafilnamn från ett uttryck klickar du på knappen bredvid filnamnet (bredvid mappikonen). Välj "Uttryck" på den nedrullningsbara menyn. Detta tar upp uttrycksredigeraren. Rensa innehållet i redigeraren först.

![Redigeraren för tomt uttryck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Redigeraren för tomt uttryck*

Med uttrycksredigeraren kan du ange ett litteralt värde, blandat med en eller flera variabler. Variabler börjar med ett dollartecken. När du trycker på $-tangenten visar redigeraren en rullgardinsruta med ett urval av tillgängliga variabler. I vårt fall använder vi en kombination av utdatakatalogvariabeln och basindatafilnamnsvariabeln:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Ifylld uttrycksredigerare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Ifylld uttrycksredigerare*

> [!NOTE]
> För att kunna se en utdatafil för ditt kodningsjobb i Azure måste du ange ett värde i uttrycksredigeraren.
>
>

När du bekräftar uttrycket genom att trycka ok förhandsgranskas vilket värde egenskapen File löser vid den här tidpunkten.

![Filuttryck löser utdatadir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Filuttryck löser utdatadir*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Skapa en Medietjänsttillgång från utdatafilen
Medan vi har skrivit en MP4-utdatafil måste vi fortfarande ange att den här filen tillhör den utdatatillgång som medietjänster genererar som ett resultat av att arbetsflödet körs. För detta ändamål används noden Utdatafil/tillgång på arbetsflödesarbetsytan. Alla inkommande filer till den här noden utgör en del av den resulterande Azure Media Services-tillgången.

Anslut komponenten Filutdata till komponenten Utdatafil/tillgång för att slutföra arbetsflödet.

![Slutfört arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Slutfört arbetsflöde*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>Testa det färdiga arbetsflödet lokalt
Om du vill testa arbetsflödet lokalt trycker du på uppspelningsknappen i verktygsfältet högst upp. När arbetsflödet har körts klart kontrollerar du utdata som genereras i den konfigurerade utdatamappen. Du ser den färdiga MP4-utdatafilen som har kodats från MXF-indatakällfilen.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Kodning MXF till MP4 - multibitrate dynamisk förpackning aktiverad
Den här genomgången skapar en uppsättning flera bithastighet MP4-filer med AAC-kodat ljud från en enda . MXF-indatafil.

När en utdata för flera bithastigheter önskas för användning i kombination med de dynamiska paketeringsfunktionerna som erbjuds av Azure Media Services, måste flera GOP-anpassade MP4-filer för varje bithastighet och upplösning genereras. För att göra det ger [kodning MXF till en enda bitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) genomgång oss med en bra utgångspunkt.

![Starta arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Starta arbetsflöde*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Lägga till ytterligare en eller flera MP4-utgångar
Varje MP4-fil i vår resulterande Azure Media Services-tillgång stöder olika bithastighet och lösning. Nu ska vi lägga till en eller flera MP4-utdatafiler i arbetsflödet.

För att se till att vi har alla våra videokodare skapade med samma inställningar är det mest praktiskt att duplicera den redan befintliga AVC Video Encoder och konfigurera en annan kombination av upplösning och bithastighet (låt oss lägga till en av 960 x 540 vid 25 bilder per sekund på 2,5 Mbps ). Om du vill duplicera den befintliga kodaren kopierar du den på designerns yta.

Anslut den okomprimerade videoutdatastiftet för mediefilinmatningen till vår nya AVC-komponent.

![Andra AVC-kodaren ansluten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Andra AVC-kodaren ansluten*

Nu anpassa konfigurationen för vår nya AVC-kodare till utgång 960x540 vid 2,5 Mbps. (Använd dess egenskaper "Utdatabredd", "Utdatahöjd" och "Bitrate (kbps)" för detta.)

Med tanke på att vi vill använda den resulterande tillgången tillsammans med Azure Media Services dynamiska paketering måste slutpunkten för direktuppspelning kunna generera från dessa MP4-filer HLS/Fragmented MP4/DASH-fragment som är exakt anpassade till varandra på ett sätt som klienter som växlar mellan olika bithastigheter får en enda smidig kontinuerlig video- och ljudupplevelse. För att göra detta måste vi se till att, i egenskaperna hos både AVC-kodare GOP ("grupp av bilder") storlek för båda MP4-filer är inställd på 2 sekunder, vilket kan göras genom att:

* ställa in GOP-storleksläget till fast GOP-storlek och
* nyckelbildrutasintervall till två sekunder.
* också ställa in GOP IDR Control till closed gop för att säkerställa att alla gop står på egen hand utan beroenden

För att göra detta arbetsflöde lättare att förstå, byta namn på den första AVC-kodaren till "AVC Video Encoder 640x360 1200 kbps" och den andra AVC-kodaren "AVC Video Encoder 960x540 2500 kbps."

Lägg nu till en andra ISO MPEG-4 Multiplexer och en andra filutdata. Anslut multiplexern till den nya AVC-kodaren och se till att dess utdata dirigeras till filutdata. Anslut sedan även AAC-ljudkodarens utgång till den nya multiplexerns ingång. Filutdata kan i sin tur anslutas till noden Utdatafil/tillgång för att lägga till den i medietjänsttillgången som ska skapas.

![Andra Muxer- och filutdata anslutna](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Andra Muxer- och filutdata anslutna*

Konfigurera multiplexerns chunk-läge till GOP-antal eller varaktighet för kompatibilitet med dynamisk Azure Media Services-förpackning och ange startleverantörerna per segment till 1. (Detta bör vara standard.)

![Muxer-segmentlägen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer-segmentlägen*

Du kanske vill upprepa den här processen för alla andra bithastighets- och upplösningskombinationer som du vill ha lagt till i tillgångsutdata.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurera filutdatanamnen
Vi har mer än en enda fil som läggs till i utdatatillgången. Detta ger ett behov av att se till att filnamnen för var och en av utdatafilerna skiljer sig från varandra och kanske till och med tillämpar en filnamnskonvention så att det framgår av filnamnet vad du har att göra med.

Namngivning av filutdata kan styras genom uttryck i designern. Öppna egenskapsfönstret för en av filutdatakomponenterna och öppna uttrycksredigeraren för egenskapen Arkiv. Vår första utdatafil konfigurerades genom följande uttryck (se självstudien för att gå från [MXF till en enda bitrate MP4-utgång):](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Det innebär att vårt filnamn bestäms av två variabler: utdatakatalogen som ska skrivas in och källfilbasnamnet. Den förstnämnda är exponerad som en egenskap på arbetsflödesroten och den senare bestäms av den inkommande filen. Utdatakatalogen är vad du använder för lokal testning. Den här egenskapen åsidosätts av arbetsflödesmotorn när arbetsflödet körs av den molnbaserade medieprocessorn i Azure Media Services.
Om du vill ge båda våra utdatafiler ett konsekvent utdatanamn ändrar du det första filnamnsuttrycket till:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

och den andra till:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Kör en mellanliggande testkörning för att se till att båda MP4-utdatafilerna har genererats korrekt.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Lägga till ett separat ljudspår
Som vi får se senare när vi genererar en .ism-fil för att gå med våra MP4-utgångsfiler, kommer vi också att kräva en ljud-bara MP4-fil som ljudspår för vår adaptiva streaming. Om du vill skapa den här filen lägger du till ytterligare en muxer i arbetsflödet (ISO-MPEG-4 Multiplexer) och ansluter AAC-kodarens utdatastift med indatastiftet för spår 1.

![Audio Muxer Tillagd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer Tillagd*

Skapa en tredje filutdatakomponent för att mata ut den utgående strömmen från muxer och konfigurera filnamnsuttrycket som:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Ljud Muxer skapa filutdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Ljud Muxer skapa filutdata*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Lägga till . ISM SMIL-fil
För att den dynamiska förpackningen ska fungera i kombination med både MP4-filer (och mp4-ljud) i vår Media Services-tillgång behöver vi också en manifestfil (även kallad "SMIL"-fil: Synkroniserat multimediaintegrationsspråk). Den här filen anger för Azure Media Services vilka MP4-filer som är tillgängliga för dynamisk paketering och vilka av dem som ska övervägas för ljudströmning. En typisk manifestfil för en uppsättning MP4 med en enda ljudström ser ut så här:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

Ism-filen innehåller i en switch-sats, en referens till var och en av de enskilda MP4-videofilerna och utöver de också en (eller flera) ljudfilreferenser till en MP4 som bara innehåller ljudet.

Generera manifestfilen för vår uppsättning MP4: s kan göras genom en komponent som kallas "AMS Manifest Writer." Om du vill använda den drar du den till ytan och ansluter utdatastiften "Write Complete" från de tre filutmatningskomponenterna till AMS Manifest Writer-indata. Se sedan till att ansluta utdata från AMS Manifest Writer till utdatafilen / tillgången.

Precis som med våra andra filutdatakomponenter konfigurerar du filutdatanamnet .ism med ett uttryck:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Vårt färdiga arbetsflöde ser ut som nedan:

![Färdiga MXF för att multibitrate MP4 arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Färdiga MXF för att multibitrate MP4 arbetsflöde*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Kodning MXF till multibitrate MP4 - förbättrad ritning
I föregående [arbetsflödesgenomgång](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) har vi sett hur en enda MXF-indatatillgång kan konverteras till en utdatatillgång med MP4-filer med flera bithastigheter, en MP4-fil med endast ljud och en manifestfil som kan användas tillsammans med dynamisk Azure Media Services-paketering.

Den här genomgången visar hur några av de aspekter som kan förbättras och göras bekvämare.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>Översikt över arbetsflöden för att förbättra
![Multibitrate MP4-arbetsflöde för att förbättra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4-arbetsflöde för att förbättra*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Namngivningskonventioner för filer
I föregående arbetsflöde angav vi ett enkelt uttryck som grund för att generera utdatafilnamn. Vi har några dubbelarbete dock: alla de enskilda utdatafilkomponenter anges sådant uttryck.

Vår filutdatakomponent för den första videofilen är till exempel konfigurerad med det här uttrycket:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Även för den andra produktionen video, har vi ett uttryck som:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Skulle det inte vara renare, mindre felbenägna, och bekvämare om vi kunde ta bort en del av denna dubblering och göra saker mer konfigurerbara istället? Lyckligtvis kan vi: designerns uttryck kapacitet i kombination med möjligheten att skapa anpassade egenskaper på vårt arbetsflöde rot kommer att ge ett extra lager av bekvämlighet.

Låt oss anta att vi kommer att driva filnamn konfiguration från bitrates av de enskilda MP4-filer. Dessa bithastigheter vi syftar till att konfigurera på en central plats (på roten i vårt diagram), varifrån de kommer att nås för att konfigurera och driva filnamn generation. För att göra detta börjar vi med att publicera bitrate-egenskapen från både AVC-kodare till roten i vårt arbetsflöde, så att den blir tillgänglig från både roten och från AVC-kodarna. (Även om det visas på två olika platser finns det bara ett underliggande värde.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Publicera komponentegenskaper på arbetsflödesroten
Öppna den första AVC-kodaren, gå till egenskapen Bitrate (kbps) och välj Publicera i listrutan.

![Publicera egenskapen bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicera egenskapen bitrate*

Konfigurera publiceringsdialogrutan för att publicera till roten i vårt arbetsflödesdiagram, med ett publicerat namn på "video1bitrate" och ett läsbart visningsnamn för "Video 1 Bitrate". Konfigurera ett anpassat gruppnamn som heter "Strömmande bithastigheter" och tryck på Publicera.

![Publicera egenskapen bitrate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Dialogrutan Publicera för bitruterutning*

Upprepa samma för bitrate egenskapen för den andra AVC-kodaren och ge den namnet "video2bitrate" med visningsnamnet "Video 2 Bitrate", i samma anpassade grupp "Streaming Bitrates".

Om vi nu inspekterar arbetsflödesrotegenskaperna ser vi vår anpassade grupp med de två publicerade egenskaperna visas. Båda återspeglar värdet av deras respektive AVC-kodare bitrate.

![Publicerade bitratrekvisita på arbetsflödesrot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

När vi vill komma åt dessa egenskaper från kod eller från ett uttryck, kan vi göra så här:

* från infogad kod från en komponent precis under roten: node.getPropertyAsString('.. /video1bitrate', null)
* inom ett uttryck: ${ROOT_video1bitrate}

Låt oss slutföra gruppen "Streaming Bitrates" genom att publicera vårt ljudspår bitrate på den också. Sök efter bitrate-inställningen i egenskaperna för AAC-kodaren och välj Publicera i listrutan bredvid den. Publicera till roten av diagrammet med namnet "audio1bitrate" och visa namnet "Audio 1 Bitrate" i vår anpassade grupp "Streaming Bitrates".

![Dialogrutan Publicera för ljudbithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Dialogrutan Publicera för ljudbithastighet*

![Resulterande video- och ljudrekvisita på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterande video- och ljudrekvisita på rot*

Om du ändrar något av dessa tre värden konfigureras även om och ändrar värdena på respektive komponenter som de är länkade till (och var de publiceras från).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>Har genererade utdatafilnamn förlita sig på publicerade egenskapsvärden
I stället för att hårdkoda våra genererade filnamn kan vi nu ändra vårt filnamnsuttryck på var och en av filutdatakomponenterna för att förlita oss på de bithastighetsegenskaper som vi publicerade på grafroten. Börja med vår första filutdata, leta reda på egenskapen Arkiv och redigera uttrycket så här:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

De olika parametrarna i det här uttrycket kan nås och anges genom att trycka på dollartecknet på tangentbordet medan du är i uttrycksfönstret. En av de tillgängliga parametrarna är vår video1bitrate egendom som vi publicerade tidigare.

![Komma åt parametrar i ett uttryck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Komma åt parametrar i ett uttryck*

Gör samma sak för filutgången för vår andra video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

och för filutmatningen endast för ljud:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Om vi nu ändrar bithastigheten för någon av video- eller ljudfilerna kommer respektive kodare att konfigureras om och den bitrate-baserade filnamnskonventionen kommer att hedras alla automatiska.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Lägga till miniatyrer i multibitrate MP4-utgång
Från ett arbetsflöde som genererar [en multibitrate MP4-utgång från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att titta på att lägga till miniatyrer till utdata.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>Översikt över arbetsflöde för att lägga till miniatyrer i
![Multibitrate MP4-arbetsflöde för att starta från](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4-arbetsflöde för att starta från*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Lägga till JPG-kodning
Hjärtat i vår miniatyr generation kommer att jpg encoder komponent, kunna mata ut JPG-filer.

![JPG-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-kodare*

Vi kan dock inte direkt ansluta vår okomprimerade videoström från Media File Input till JPG-kodaren. I stället förväntar den sig att lämnas enskilda ramar. Detta kan vi göra genom Video Frame Gate komponenten.

![Ansluta en ramport till JPG-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ansluta en ramport till JPG-kodaren*

Ramporten en gång varannan sekund eller bildrutor gör att en videobildruta kan passera. Intervallet och tidsförskjutningen som detta händer med kan konfigureras i egenskaperna.

![Egenskaper för videoramgate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Egenskaper för videoramgate*

Låt oss skapa en miniatyr bild varje minut genom att ställa in läget till Tid (sekunder) och intervallet till 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>Hantera konvertering av färgrymd
Även om det verkar logiskt både Okomprimerad Video stift av ram gate och Media File Input kan nu anslutas, skulle vi få en varning om vi skulle göra det.

![Fel i indatafärgrymd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Fel i indatafärgrymd*

Detta beror på att det sätt på vilket färginformation representeras i vår ursprungliga råa okomprimerade videoström, som kommer från vår MXF, skiljer sig från vad JPG Encoder förväntar sig. Mer specifikt förväntas en så kallad "färgrymd" av "RGB" eller "Gråskala" flöda in. Det innebär att Video Frame Gates inkommande videoström måste ha en konvertering som tillämpas när det gäller dess färgrymd först.

Dra till arbetsflödet Color Space Converter - Intel och anslut den till vår ramgrind.

![Ansluta en färgrymdskonvertator](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ansluta en färgrymdskonvertator*

Välj BGR 24-posten i listan Förinställning i egenskapsfönstret.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Skriva miniatyrer
Skiljer sig från våra MP4-video, jpg encoder komponent utgångar mer än en fil. För att hantera detta kan en Scene Search JPG File Writer komponent användas: det tar inkommande JPG miniatyrer och skriver ut dem, varje filnamn som suffixed av ett annat nummer. (Talet anger vanligtvis antalet sekunder/enheter i flödet som miniatyren hämtades från.)

![Introduktion till Scensökning JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduktion till Scensökning JPG File Writer*

Konfigurera egenskapen Utdatamappsökväg med uttrycket :{ROOT_outputWriteDirectory}

och egenskapen Filnamnsprefix med:

    ${ROOT_sourceFileBaseName}_thumb_

Prefixet avgör hur miniatyrfilerna namnges. De suffixeras med ett nummer som anger tummens position i strömmen.

![Egenskaper för scensökning JPG-filförfattare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Egenskaper för scensökning JPG-filförfattare*

Anslut scene Search JPG File Writer till noden Utdatafil/tillgång.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Identifiera fel i ett arbetsflöde
Anslut indata från färgrymdskonverteraren till den råa okomprimerade videoutgången. Utför nu en lokal testkörning för arbetsflödet. Det finns en god chans att arbetsflödet plötsligt slutar köra och indikera med en röd kontur på komponenten som påträffade ett fel:

![Fel i konverteringsfel för färgrymd](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Fel i konverteringsfel för färgrymd*

Klicka på den lilla röda "E"-ikonen i det övre högra hörnet av color space converter-komponenten för att se vad som är anledningen till att kodningsförsöket misslyckades.

![Feldialog för färgrymdskonverterare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Feldialog för färgrymdskonverterare*

Det visar sig, som ni kan se, att den inkommande färgrymdsstandarden för färgrymdskonverteraren måste vara rec601 för vår begärda konvertering av YUV till RGB. Tydligen vår ström inte anger sin rec601. (Rec 601 är en standard för kodning sammanflätade analoga videosignaler i digital videoform. Den anger en aktiv region som omfattar 720 luminansprover och 360 krominansprover per rad. Färgkodningssystemet kallas YCbCr 4:2:2.)

För att åtgärda detta anger vi på metadata för vår ström att vi hanterar rec601-innehåll. För att göra det använder vi en videodatatypsuppdateringskomponent, som vi lägger in mellan vår råa källa och färgrymdskonverteringskomponenten. Den här datatypsuppdateringaren möjliggör manuell uppdatering av vissa egenskaper för videodatatyp. Konfigurera den för att ange en färgrymdsstandard för "Rec 601". Detta medför att videodatatypsuppdateringaren taggar strömmen med färgrymden "Rec 601" om det inte fanns någon färgrymd definierad ännu. (Den åsidosätter inte befintliga metadata, såvida inte kryssrutan Åsidosätt har markerats.)

![Uppdatera färgrymdsstandard på datatypsuppdateringaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Uppdatera färgrymdsstandard på datatypsuppdateringaren*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Slutfört arbetsflöde
Nu när vårt arbetsflöde är klart, gör en annan testkörning för att se den passera.

![Avslutat arbetsflöde för flera mp4-utdata med miniatyrer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Avslutat arbetsflöde för flera mp4-utdata med miniatyrer*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Tidsbaserad trimning av multibitrate MP4-utgång
Från ett arbetsflöde som genererar [en multibitrate MP4-utgång från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att undersöka trimning av källvideon baserat på tidsstämplar.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Arbetsflödesöversikt för att börja lägga till trimning i
![Starta arbetsflöde för att lägga till trimning i](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Starta arbetsflöde för att lägga till trimning i*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Använda Stream Trimmer
Med komponenten Stream Trimmer kan du trimma början och slutet av en indataströmsbas på tidsinformation (sekunder, minuter, ...). Trimmern stöder inte rambaserad trimning.

![Ström trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Ström trimmer*

Istället för att länka AVC-kodare och högtalare position assigner till Media File Input direkt, kommer vi att sätta in mellan dem ström trimmer. (En för videosignalen och en för den interfolierade ljudsignalen.)

![Sätt Stream Trimmer i mellan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Sätt Stream Trimmer i mellan*

Låt oss konfigurera trimmern så att vi bara bearbetar video och ljud mellan 15 sekunder och 60 sekunder i videon.

Gå till egenskaperna för videoströmtrimmern och konfigurera egenskaperna både starttid (15 s) och sluttid (60 s). För att se till att både vår ljud- och videotrimmer alltid är konfigurerad till samma start- och slutvärden publicerar vi dem till roten i arbetsflödet.

![Publicera egenskapen starttid från Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicera egenskapen starttid från Stream Trimmer*

![Dialogrutan Publicera egenskap för starttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialogrutan Publicera egenskap för starttid*

![Dialogrutan Publicera egenskap för sluttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialogrutan Publicera egenskap för sluttid*

Om vi nu inspekterar roten i vårt arbetsflöde visas båda egenskaperna snyggt och kan konfigureras därifrån.

![Publicerade egenskaper som är tillgängliga på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicerade egenskaper som är tillgängliga på rot*

Öppna nu trimningsegenskaperna från ljudtrimmern och konfigurera både start- och sluttider med ett uttryck som refererar till de publicerade egenskaperna på roten i vårt arbetsflöde.

För ljudtrimningsstarttid:

    ${ROOT_TrimmingStartTime}

och för sin sluttid:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Slutfört arbetsflöde
![Slutfört arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Slutfört arbetsflöde*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>Introduktion till skriptkomponenten
Skriptkomponenter kan köra godtyckliga skript under körningsfaserna i vårt arbetsflöde. Det finns fyra olika skript som kan köras, var och en med specifika egenskaper och sin egen plats i arbetsflödets livscykel:

* **kommandoScript**
* **realiseScript (realScript)**
* **processInputScript**
* **lifeCycleScript**

Dokumentationen av skriptkomponenten går mer i detalj för vart och ett av ovanstående. I [följande avsnitt](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)används skriptkomponenten **realizeScript** för att skapa en cliplist-xml i farten när arbetsflödet startar. Det här skriptet anropas under komponentinställningarna, vilket bara sker en gång i livscykeln.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Skript i ett arbetsflöde: hello world
Dra en skriptkomponent till designerns yta och byt namn på den (till exempel "SetClipListXML").

![Lägga till en skriptkomponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptkomponent*

När du kontrollerar egenskaperna för den skriptade komponenten visas de fyra olika skripttyperna, som kan konfigureras till ett annat skript.

![Egenskaper för skriptkomponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaper för skriptkomponent*

Rensa processInputScript och öppna redigeraren för realizeScript. Nu är vi redo att börja skriva.

Skript är skrivna i Groovy, ett dynamiskt kompilerat skriptspråk för Java-plattformen som behåller kompatibiliteten med Java. Faktiskt, de flesta Java-kod är giltig Groovy kod.

Låt oss skriva en enkel hej världen groovy manus i samband med vår realizeScript. Ange följande i redigeraren:

    node.log("hello world");

Kör nu en lokal testkörning. Efter den här körningen bör du inspektera (via fliken System på egenskapen Scripted Component) loggar.

![Hej världen logg utgång](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hej världen logg utgång*

Nodobjektet som vi anropar inloggningsmetoden på, refererar till vår aktuella "nod" eller komponenten vi skriptar inom. Varje komponent som sådan har möjlighet att mata ut loggningsdata, tillgängliga via systemfliken. I det här fallet matar vi ut strängen bokstavlig "hello world". Viktigt att förstå här är att detta kan visa sig vara ett ovärderligt felsökningsverktyg, vilket ger dig insikt om vad skriptet faktiskt gör.

Inifrån vår skriptmiljö har vi även tillgång till egenskaper på andra komponenter. Testa det här:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

Vårt loggfönster visar oss följande:

![Logga utdata för åtkomst till nodsökvägar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Logga utdata för åtkomst till nodsökvägar*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Rambaserad trimning av multibitrate MP4-utgång
Från och med ett arbetsflöde som genererar [en multibitrate MP4-utgång från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att undersöka trimning av källvideon baserat på bildruteantal.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>Skissöversikt för att börja lägga till trimning i
![Arbetsflöde för att börja lägga till trimning i](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Arbetsflöde för att börja lägga till trimning i*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>Använda XML-koden för klipplistan
I alla tidigare arbetsflödeshandledningar använde vi komponenten Media File Input som vår videoingångskälla. För det här specifika scenariot använder vi dock komponenten Klipplista källa i stället. Detta bör inte vara det bästa sättet att arbeta. Använd bara klipplistekällan när det finns en verklig anledning att göra det (som i följande fall, där vi använder klipplistans trimningsfunktioner).

Om du vill växla från mediefilindata till klipplistekällan drar du komponenten Klipplistakälla till designytan och ansluter XML-fästet för klipplistan till XML-noden klipplista för arbetsflödesdesignern. Detta fyller klipplistekällan med utdatastift, enligt vår indatavideo. Anslut nu de okomprimerade video- och okomprimerade ljudstiften från klipplistekällan till respektive AVC-kodare och Audio Stream Interleaver. Ta nu bort mediefilinmatningen.

![Ersatt mediefilindata med klipplistekällan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Ersatt mediefilindata med klipplistekällan*

Komponenten Klipplistakälla tar som indata en "Clip List XML". När du väljer källfilen som ska testas lokalt fylls den här klipplistan xml i automatiskt för dig.

![XML-egenskapen Clip List i automatiskt](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*XML-egenskapen Clip List i automatiskt*

Letar du lite närmare xml, är detta hur det ser ut:

![Dialogrutan Redigera klipplista](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Dialogrutan Redigera klipplista*

Detta återspeglar dock inte funktionerna i klippet lista xml. Ett alternativ vi har är att lägga till en "Trim" element under både video och ljudkälla, så här:

![Lägga till ett trimelement i klipplistan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Lägga till ett trimelement i klipplistan*

Om du ändrar xml-koden för klipplistan så här ovan och utför en lokal testkörning ser du videon korrekt trimmad mellan 10 och 20 sekunder i videon.

I motsats till vad som händer när du gör en lokal körning dock, samma cliplist xml skulle inte ha samma effekt när den tillämpas i ett arbetsflöde som körs i Azure Media Services. När Azure Premium Encoder startar genereras cliplist xml varje gång igen, baserat på indatafilen som kodningsjobbet gavs. Detta innebär att alla ändringar vi gör på xml skulle tyvärr åsidosättas.

För att motverka cliplist xml torkas när en kodning jobb startas, kan vi återskapa den i farten strax efter starten av vårt arbetsflöde. Sådana anpassade åtgärder kan vidtas genom vad som kallas en "Skriptkomponent". Mer information finns i [Introduktion till skriptkomponenten](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Dra en skriptkomponent till designerytan och byt namn på den till "SetClipListXML".

![Lägga till en skriptkomponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptkomponent*

När du kontrollerar egenskaperna för skriptkomponenten visas de fyra olika skripttyperna, som alla kan konfigureras till ett annat skript.

![Egenskaper för skriptkomponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaper för skriptkomponent*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>Ändra klipplistan från en skriptkomponent
Innan vi kan skriva om den cliplist-xml som genereras under arbetsflödesstart måste vi ha åtkomst till egenskapen och innehållet i cliplist xml. Vi kan göra så här:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Lista över inkommande klipp loggas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Lista över inkommande klipp loggas*

Först behöver vi ett sätt att avgöra från vilken punkt fram till vilken punkt vi vill trimma videon. Om du vill göra detta bekvämt för den mindre tekniska användaren av arbetsflödet publicerar du två egenskaper till diagrammets rot. För att göra detta, högerklicka på designerytan och välj "Lägg till egenskap":

* Första egenskapen: "UrklippTimeStart" av typen: "TIMECODE"
* Andra egenskapen: "UrklippTimeEnd" av typen: "TIMECODE"

![Dialogrutan Lägg till egenskap för urklippsstart](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialogrutan Lägg till egenskap för urklippsstart*

![Publicerade urklippstidsrekvisita på arbetsflödesrot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicerade urklippstidsrekvisita på arbetsflödesrot*

Konfigurera båda egenskaperna till ett lämpligt värde:

![Konfigurera start- och slutegenskaperna för urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurera start- och slutegenskaperna för urklipp*

Nu, inifrån vårt skript, kan vi komma åt båda egenskaperna, så här:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Loggfönster som visar början och slutet av urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Loggfönster som visar början och slutet av urklipp*

Låt oss tolka tidskodsträngarna i ett bekvämare att använda formulär med ett enkelt reguljärt uttryck:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Loggfönster med utdata av tolkad tidskod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Loggfönster med utdata av tolkad tidskod*

Med den här informationen till hands kan vi nu ändra cliplist xml för att återspegla start- och sluttider för önskat bildruterekta klippning av filmen.

![Skriptkod för att lägga till trimelement](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Skriptkod för att lägga till trimelement*

Detta gjordes genom normala sträng manipulation operationer. Den resulterande ändrade klipplistan xml skrivs tillbaka till clipListXML-egenskapen på arbetsflödesroten med metoden "setProperty". Loggfönstret efter en annan provkörning skulle visa oss följande:

![Logga den resulterande klipplistan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Logga den resulterande klipplistan*

Gör en testkörning för att se hur video- och ljudströmmarna har klippts. Eftersom du kommer att göra mer än en testkörning med olika värden för trimningspunkter, kommer du att märka att de inte kommer att beaktas dock! Anledningen till detta är att designern, till skillnad från Azure-körningen, INTE åsidosätter cliplist xml varje körning. Detta innebär att endast första gången du har ställt in och ut punkter, kommer att orsaka`clipListXML.indexOf("<trim>") == -1`xml att omvandla, alla andra gånger, kommer vår vakt klausul (om( )) hindrar arbetsflödet från att lägga till en annan trim element när det redan finns en närvarande.

För att göra vårt arbetsflöde bekvämt att testa lokalt, lägger vi bäst till några hushållningskod som kontrollerar om ett trimelement redan fanns. Om så är fallet kan vi ta bort den innan du fortsätter genom att ändra xml med de nya värdena. I stället för att använda vanliga strängmanipuleringar är det förmodligen säkrare att göra detta genom verklig xml-objektmodelltolsning.

Innan vi kan lägga till sådan kod måste vi dock lägga till ett antal importsatser i början av vårt skript först:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Efter detta kan vi lägga till den nödvändiga rengöringskoden:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Denna kod går precis ovanför den punkt där vi lägger till trim element till cliplist xml.

Vid denna punkt kan vi köra och ändra vårt arbetsflöde så mycket vi vill samtidigt som ändringarna tillämpas någonsin tid.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>Lägga till en bekvämlighetsegenskap för Klippbare
Som du kanske inte alltid vill trimma att hända, låt oss avsluta vårt arbetsflöde genom att lägga till en bekväm boolesk flagga som anger om vi vill aktivera trimning / klippning.

Precis som tidigare publicerar du en ny egenskap till roten i vårt arbetsflöde som kallas "UrklippEnabled" av typen "BOOLEAN".

![Publicerat en egenskap för att aktivera urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicerat en egenskap för att aktivera urklipp*

Med nedanstående enkel skydd klausul, kan vi kontrollera om trimning krävs och besluta om vår klipplista som sådan behöver ändras eller inte.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a name="complete-code"></a><a id="code"></a>Fullständig kod

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Se även
[Introduktion till Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Så här använder du Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Koda innehåll på begäran med Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Arbetsflödesformat och codecs för Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exempel på arbetsflödesfiler](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-verktyg](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
