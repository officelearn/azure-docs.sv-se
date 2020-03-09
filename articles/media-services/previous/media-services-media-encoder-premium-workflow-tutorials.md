---
title: Avancerade Media Encoder Premium Workflow självstudier
description: Det här dokumentet innehåller genom gångar som visar hur du utför avancerade aktiviteter med Media Encoder Premium Workflow och hur du skapar komplexa arbets flöden med arbetsflödesdesigner.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392968"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Avancerade Media Encoder Premium Workflow självstudier
## <a name="overview"></a>Översikt
Det här dokumentet innehåller genom gångar som visar hur du anpassar arbets flöden med **arbetsflödesdesigner**. Du kan hitta de faktiska arbets flödes filerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>ORGANISK
Följande ämnen tas upp:

* [Koda MXF till en enkel bit hastighets MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Starta ett nytt arbets flöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Använda medie filens indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Inspekterar medie strömmar](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Lägga till en video kodare för. Skapa MP4-filer](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Koda ljud strömmen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexering av ljud-och video strömmar i en MP4-behållare](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Skriva MP4-filen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Skapa en Media Services till gång från utdatafilen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testa det avslutade arbets flödet lokalt](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodning av MXF till hastigheter-dynamisk paketering med multibit funktioner](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Lägga till en eller flera ytterligare MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurera utdatafilernas namn](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Lägga till ett separat ljud spår](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Lägga till SMIL-filen "ISM"](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Koda MXF till en MP4-förbättrad och förbättrad skiss med multibit hastighet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * Översikt över arbets flöde för att förbättra
  * [Fil namns konventioner](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Egenskaper för publicering av komponenten till arbets flödes roten](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Har genererade utdatafiler förlitar sig på publicerade egenskaps värden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Lägga till miniatyrer i MP4-utdata med multibit hastighet](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * Översikt över arbets flöde för att lägga till miniatyrer i
  * [Lägga till JPG-kodning](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Hantera färg områdes konvertering](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Skriva miniatyr bilderna](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Identifiera fel i ett arbets flöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Avslutade arbets flöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Tidsbaserad trimning av MP4-utdata med multibit hastighet](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Översikt över arbets flöde för att börja lägga till trimning i](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Använda Stream-trimmern](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Avslutade arbets flöde](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduktion till den skriptbaserade komponenten](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skript i ett arbets flöde: Hej världen](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Bildruta-baserad trimning av MP4-utdata med multibit hastighet](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Skiss översikt som börjar lägga till trimning till](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Använda Clip List-XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Ändra klipp listan från en skriptad komponent](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Lägga till en ClippingEnabled-egenskap](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Koda MXF till en enkel bit hastighets MP4
Det här avsnittet visar hur du skapar en enkel bit hastighet. MP4-fil med AAC – HE kodade ljudet från en. MXF-indatafil.

### <a id="MXF_to_MP4_start_new"></a>Starta ett nytt arbets flöde
Öppna arbetsflödesdesigner och välj fil > ny arbets yta > Omkoda skiss

Det nya arbets flödet visar tre element:

* Primär käll fil
* Clip List-XML
* Utdatafil/till gång  

![Nytt kodnings arbets flöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nytt kodnings arbets flöde*

### <a id="MXF_to_MP4_with_file_input"></a>Använda medie filens indata
För att kunna godkänna indatafilen börjar en med att lägga till en medie fil indataport. Om du vill lägga till en komponent i arbets flödet tittar du på den i sökrutan för databasen och drar önskad post till fönstret designer. Upprepa åtgärden för medie filens indata och Anslut den primära käll fil komponenten till fil namns-PIN-koden för indata från medie filen.

![Indata för anslutna medie filer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Indata för anslutna medie filer*

Börja med att identifiera en lämplig exempel fil som ska användas när du skapar ett anpassat arbets flöde. Det gör du genom att klicka på bakgrunden i design fönstret och leta efter egenskapen primär käll fil i den högra egenskaps rutan. Klicka på mappikonen och välj den önskade filen för att testa arbets flödet. Komponenten för indata från medie filen söker igenom filen och fyller i sina utmatnings-PIN-filer för att avspegla information om exempel filen som den har inspekterat.

![Indata för den ifyllda medie filen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Indata för den ifyllda medie filen*

Nu när indata är ifyllda är nästa steg att konfigurera inställningar för utgående kodning. Precis som den primära käll filen har kon figurer ATS, konfigurerar du nu variabeln för mappen utdata, strax under den.

![Konfigurerade egenskaper för indata och utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurerade egenskaper för indata och utdata*

### <a id="MXF_to_MP4_streams"></a>Inspekterar medie strömmar
Det är ofta bra att veta hur data strömmen ser ut när den flödar genom arbets flödet. Om du vill inspektera en data ström när som helst i arbets flödet klickar du bara på utdata eller PIN-kod för någon av komponenterna. I det här fallet kan du prova att klicka på den okomprimerade video utmatningens PIN-kod från medie filens indata. En dialog ruta öppnas som gör det möjligt att inspektera den utgående videon.

![Inspektera den okomprimerade Video utgången PIN-kod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Inspektera den okomprimerade Video utgången PIN-kod*

I det här fallet visar det att videon innehåller en 1920x1080-indata vid 24 ramar per sekund i 4:2:2-sampling för en video på nästan 2 minuter.

### <a id="MXF_to_MP4_file_generation"></a>Lägga till en video kodare för. Skapa MP4-filer
Nu är en okomprimerad video och flera okomprimerade ljud utmatningar tillgängliga för användning i medie filens indata. För att kunna koda den inkommande videon måste en kodnings komponent läggas till i arbets flödet – i det här fallet för att generera. MP4-filer.

Om du vill koda video strömmen till H. 264 lägger du till komponenten AVC video-kodare till design ytan. Den här komponenten tar en expanderad video ström som indata och levererar en AVC-komprimerad video ström på PIN-koden för utdata.

![Frånkopplad AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Frånkopplad AVC-kodare*

Egenskaperna avgör hur kodningen sker. Låt oss ta en titt på några av de viktiga inställningarna:

* Utmatnings bredd och utmatnings höjd: anger den kodade videons upplösning. I det här fallet är 640 x 360 en lämplig inställning.
* Bild Rute frekvens: när det är inställt på genom strömning kommer det bara att anta käll bilds frekvensen. det är möjligt att åsidosätta detta även. Sådan konvertering av den här hastigheten är inte rörelse-kompenserad.
* Profil och nivå: bestämmer AVC-profilen och-nivån. Om du vill ha mer information om de olika nivåerna och profilerna klickar du på frågetecknet på komponenten AVC video-kodare så visas mer information om var och en av nivåerna i hjälp sidan. I det här exemplet använder du Main Profile på nivå 3,2 (standard).
* Hastighets kontroll läge och bit hastighet (kbit/s): i det här scenariot väljer du en konstant bit hastighet (CBR) på 1200 kbit/s
* Video format: innehåller information om VUI (information om video användning) som skrivs till data strömmen H. 264 (sid information som kan användas av en avkodare för att förbättra visningen men inte nödvändig för korrekt avkodning):
* NTSC (vanligt för USA eller Japan, med 30 fps)
* PAL (vanligt för Europa, med 25 fps)
* Läge för GOP-storlek: Ange fast GOP storlek för våra behov med ett nyckel intervall på 2 sekunder med stängd GOPs. Inställningen på 2 sekunder garanterar kompatibilitet med den dynamiska packnings Azure Media Services tillhandahåller.

Om du vill mata in AVC-kodaren ansluter du den okomprimerade video utmatnings-PIN-koden från media-filen indata till den okomprimerade video ingången från AVC-kodaren.

![Ansluten AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Ansluten AVC main-kodare*

### <a id="MXF_to_MP4_audio"></a>Koda ljud strömmen
I det här läget måste den ursprungliga okomprimerade ljud strömmen fortfarande komprimeras. För komprimering av ljud strömmen lägger du till en AAC Encoder-komponent (Dolby Encoder) i arbets flödet.

![Frånkopplad AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Frånkopplad AAC-kodare*

Det finns nu en inkompatibilitet: det finns bara en enda okomprimerad PIN-kod för ljud inspelning från AAC-kodaren medan medie filens indata har två olika okomprimerade ljud strömmar: en för den vänstra ljud kanalen och en till höger. (Om du arbetar med Surround Sound är det sex kanaler.) Det går därför inte att ansluta ljudet direkt från medie filens indatakälla till AAC Audio-kodaren. AAC-komponenten förväntar sig en så kallad "överlagrad" ljud ström: en enda ström som har både vänster och höger kanal överlagrad med varandra. När vi vet från vår käll medie fil som ljud spår finns på vilken position i källan, kan vi generera sådan överlagrad ljud ström med de korrekt tilldelade högtalar positionerna för vänster och höger.

Först vill man skapa en överlagrad ström från de nödvändiga käll ljud kanalerna. Komponenten ljud Ströms överlagrare hanterar detta för oss. Lägg till den i arbets flödet och anslut ljud utmatningarna från medie filen till den.

![Ansluten ljud Ströms överlagrare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ansluten ljud Ströms överlagrare*

Nu när vi har en överlagrad ljud ström anger vi fortfarande inte var du vill tilldela den vänstra eller högra högtalar positionen. För att kunna ange detta kan vi utnyttja den tilldelade högtalar positionen.

![Lägga till en tilldelad högtalar position](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Lägga till en tilldelad högtalar position*

Konfigurera Föredrags hållarens förbrukare för användning med en stereo ingång via ett kodare för inställnings filter för anpassad och kanal för inställning med namnet "2,0 (L, R)". (Detta tilldelar den vänstra högtalar positionen till kanal 1 och den högra högtalar positionen för kanal 2.)

Anslut utmatningen från högtalar positionen till indata för AAC-kodaren. Se sedan till att AAC-kodaren fungerar med en "2,0 (L, R)"-kanal för inställning, så att den kan hantera stereo ljud som indata.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexering av ljud-och video strömmar i en MP4-behållare
Med tanke på vår AVC-kodade video ström och vår AAC-kodade ljud ström kan vi samla in både i en. MP4-behållare. Processen att blanda olika strömmar till en enda kallas för "Multiplexing" (eller "muxing"). I det här fallet lämnar vi ut ljudet och video strömmarna i samma enhetlighet. MP4-paket. Komponenten som samordnar detta för en. MP4-behållaren kallas för ISO MPEG-4-multiplexor. Lägg till en till design ytan och Anslut både AVC-video-kodaren och AAC-kodaren till dess indata.

![Anslutet MPEG4-multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Anslutet MPEG4-multiplexor*

### <a id="MXF_to_MP4_writing_mp4"></a>Skriva MP4-filen
När du skriver en utdatafil används komponenten för att mata ut filer. Vi kan ansluta detta till utdata från ISO MPEG-4-multiplexorn så att dess utdata skrivs till disk. Det gör du genom att ansluta utgångs punkten för behållare (MPEG-4) till Skriv-PIN-koden för filutdata.

![Utdata från anslutna filer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Utdata från anslutna filer*

Det fil namn som används bestäms av fil egenskapen. Den här egenskapen kan vara hårdkodad till ett angivet värde, men förmodligen vill man ange det genom ett uttryck i stället.

Om du vill att arbets flödet automatiskt ska fastställa egenskapen namn för utdatafilen från ett uttryck, klickar du på knappen bredvid fil namnet (bredvid mappikonen). Välj "uttryck" på den nedrullningsbara menyn. Detta öppnar uttrycks redigeraren. Ta bort redigerarens innehåll först.

![Tom uttrycks redigerare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Tom uttrycks redigerare*

Med uttrycks redigeraren kan du ange valfritt litteralt värde, blandat med en eller flera variabler. Variabler börjar med ett dollar tecken. När du trycker på $-tangenten visas en listruta med tillgängliga variabler i redigeraren. I vårt fall ska vi använda en kombination av variablerna för utdata-katalogen och variabeln för bas fil namn:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Utifylld uttrycks redigerare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Utifylld uttrycks redigerare*

> [!NOTE]
> Du måste ange ett värde i uttrycks redigeraren för att kunna se en utdatafil för kodnings jobbet i Azure.
>
>

När du bekräftar uttrycket genom att trycka på OK, för hands Grans kar egenskaps fönstret vilket värde som fil egenskapen matchar vid den här tidpunkten.

![Fil uttryck matchar utdata dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fil uttryck matchar utdata dir*

### <a id="MXF_to_MP4_asset_from_output"></a>Skapa en Media Services till gång från utdatafilen
Medan vi har skrivit en MP4-utdatafil måste vi fortfarande ange att den här filen tillhör den utgående till gång som Media Services genererar som ett resultat av att det här arbets flödet körs. Till det här slutet används noden utdatafil/till gång på arbets ytans arbets yta. Alla inkommande filer till den här noden gör en del av den resulterande Azure Media Services till gången.

Slutför arbets flödet genom att ansluta filen output-komponenten till utdatafilen/till gångs komponenten.

![Avslutade arbets flöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Avslutade arbets flöde*

### <a id="MXF_to_MP4_test"></a>Testa det avslutade arbets flödet lokalt
Testa arbets flödet lokalt genom att trycka på uppspelnings knappen i verktygsfältet längst upp. När arbets flödet har slutförts bör du kontrol lera utdata som genererats i mappen konfigurerade utdata. Du ser den färdiga MP4-utdatafilen som kodades från MXF-indatakällans källfil.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kodning av MXF till MP4 – dynamisk paketering med multibit hastighet aktiverat
Den här genom gången skapar en uppsättning MP4-filer med flera bit hastigheter med AAC-kodat ljud från en enda. MXF-indatafil.

När en till gång med flera bit hastigheter önskas för användning i kombination med de dynamiska packnings funktionerna som erbjuds av Azure Media Services, måste flera GOP MP4-filer med en annan bit hastighet och lösning skapas. För att göra det, ger [kodningen MXF till en enkel bit hastighet på MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) -genom gången att vi har en lämplig start punkt.

![Startar arbets flöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Startar arbets flöde*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Lägga till en eller flera ytterligare MP4-utdata
Varje MP4-fil i vår resulterande Azure Media Services till gång har stöd för en annan bit hastighet och lösning. Nu ska vi lägga till en eller flera MP4-utdatafiler i arbets flödet.

För att se till att vi har alla våra video kodare som skapats med samma inställningar, är det lämpligast att duplicera den befintliga AVC video-kodaren och konfigurera en annan kombination av upplösning och bit hastighet (vi lägger till en av 960 x 540 med 25 bild rutor per sekund vid 2,5 Mbit/s ). Kopiera den befintliga kodaren genom att kopiera den till design ytan.

Anslut den okomprimerade video utmatnings-PIN-koden för medie filen till vår nya AVC-komponent.

![Andra AVC-kodaren är ansluten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Andra AVC-kodaren är ansluten*

Anpassa nu konfigurationen för vår nya AVC-kodare för att mata ut 960x540 vid 2,5 Mbit/s. (Använd dess egenskaper "utmatnings bredd", "utmatnings höjd" och "bit hastighet (kbps)" för detta.)

Med tanke på att vi vill använda den resulterande till gången tillsammans med Azure Media Services "dynamisk paketering" måste direkt uppspelnings slut punkten kunna generera från dessa MP4-filer HLS/fragmenterade MP4/streck-fragment som är exakt justerade mot varandra på ett sätt som klienter som växlar mellan olika bit hastigheter får en enda smidig kontinuerlig video-och ljud upplevelse. För att göra detta måste vi se till att, i egenskaperna för båda AVC-kodarna, GOP ("grupp med bilder") för båda MP4-filerna har värdet 2 sekunder, som kan göras av:

* Ange GOP storleks läge till fast GOP-storlek och
* Nyckel bilds intervallet till två sekunder.
* Ange också GOP IDR-kontrollen till Closed GOP för att säkerställa att alla GOPs hålls på egen hand utan beroenden

Om du vill göra det här arbets flödet lättare att förstå byter du namn på den första AVC-kodaren till "AVC video encoder 640 x 360 1200 kbps" och den andra AVC-kodaren "AVC video encoder 960x540 2500 kbps".

Nu ska du lägga till en andra ISO MPEG-4-multiplexor och en andra fil utmatning. Anslut multiplexorn till den nya AVC-kodaren och se till att dess utdata dirigeras till filens utdata. Anslut sedan till den nya multiplexorn för AAC Audio-kodare till den nya multiplexor-indata. Filens utdata i Turn kan sedan anslutas till noden utdatafil/till gång för att lägga till den till den Media Services till gång som ska skapas.

![Andra muxer och utdatafilen är ansluten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Andra muxer och utdatafilen är ansluten*

För kompatibilitet med Azure Media Services dynamisk paketering konfigurerar du multiplexorns segment läge till GOP antal eller varaktighet och anger GOPs per segment till 1. (Detta bör vara standard.)

![Muxer segment lägen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer segment lägen*

Obs! Du kanske vill upprepa den här processen för alla andra bit hastighets-och matchnings kombinationer som du vill ha lagt till i till gångs resultatet.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurera utdatafilernas namn
Det finns fler än en fil som har lagts till i utmatnings till gången. Detta är ett behov av att se till att fil namnen för var och en av utdatafilerna skiljer sig från varandra och kanske även använder en fil namns konvention så att den blir tydlig från det fil namn som du hanterar med.

Namngivning av fil namn kan styras genom uttryck i designern. Öppna egenskaps rutan för en av komponenterna för fil utdata och öppna uttrycks redigeraren för fil egenskapen. Vår första utdatafil konfigurerades genom följande uttryck (se självstudien för att gå från [MXF till en enkel bit hastighet MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Det innebär att vårt fil namn bestäms av två variabler: den utmatnings katalog som ska skrivas i och käll filens bas namn. Den tidigare visas som en egenskap i arbets flödes roten och den senare bestäms av den inkommande filen. Den utgående katalogen är det du använder för lokal testning. den här egenskapen åsidosätts av arbets flödes motorn när arbets flödet körs av den molnbaserade medie processorn i Azure Media Services.
Om du vill ge båda våra utdatafiler ett konsekvent namn för utdata ändrar du det första fil namns uttrycket till:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

och den andra för att:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Kör en mellanliggande test körning för att se till att båda MP4-utdatafilerna skapas korrekt.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Lägga till ett separat ljud spår
Som vi ser senare när vi genererar en. ISM-fil för att gå med i dina MP4-filer, behöver vi också en MP4-fil som ljud spår för vår anpassningsbara strömning. Om du vill skapa den här filen lägger du till ytterligare en muxer i arbets flödet (ISO-MPEG-4-multiplexor) och ansluter AAC-kodarens utdata-PIN-kod med inmatnings-PIN för spår 1.

![Ljud-muxer har lagts till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Ljud-muxer har lagts till*

Skapa en tredje fil utgående komponent för att mata ut den utgående strömmen från muxer och konfigurera fil namns uttrycket som:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Ljud muxer skapa utdata för filer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Ljud muxer skapa utdata för filer*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Lägger till. ISM SMIL-fil
För att den dynamiska packningen ska fungera tillsammans med både MP4-filer (och endast för ljud-MP4) i vår Media Services till gång, behöver vi också en manifest fil (kallas även en "SMIL"-fil: synkroniserad multimedia integrerings språk). Den här filen visar Azure Media Services vilka MP4-filer som är tillgängliga för dynamisk paketering och vilka av dem som ska övervägas för ljud strömningen. En typisk manifest fil för en uppsättning MP4's med en enda ljud ström ser ut så här:

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

. ISM-filen innehåller i en switch-instruktion, en referens till var och en av de enskilda MP4-videofilerna och förutom de som också är en (eller flera) ljud fil referenser till en MP4 som bara innehåller ljudet.

Generering av manifest filen för vår uppsättning MP4's kan göras via en komponent som kallas "AMS manifest Writer". Om du vill använda den, drar du den till ytan och ansluter utgångs resultatet "Skriv Slutför" från de tre fil utmatnings komponenterna till AMS manifest Writer-indata. Se sedan till att ansluta utdata från manifest skrivaren för AMS till utdatafilen/till gången.

Precis som med våra andra komponenter för fil utdata konfigurerar du. ISM-filens utdatafilen med ett uttryck:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Vårt avslutade arbets flöde ser ut ungefär så här:

![Slut på MXF till MP4-arbetsflöde med multibit hastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Slut på MXF till MP4-arbetsflöde med multibit hastighet*

## <a id="MXF_to__multibitrate_MP4"></a>Koda MXF till en MP4-förbättrad och förbättrad skiss med multibit hastighet
I [föregående arbets flödes genom gång](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) har vi sett hur en enda MXF indata-till gång kan konverteras till en utmatnings till gång med MP4-filer med flera bit hastigheter, en MP4-fil med enbart ljud och en manifest fil som används tillsammans med Azure Media Services dynamisk paketering.

Den här genom gången visar hur några av aspekterna kan förbättras och blir bekvämare.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Översikt över arbets flöde för att förbättra
![MP4-arbetsflöde med multibit för att förbättra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*MP4-arbetsflöde med multibit för att förbättra*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Fil namns konventioner
I det tidigare arbets flödet angav vi ett enkelt uttryck som grund för att generera fil namn. Vi har några dupliceringar, men: alla enskilda utdatafiler har angett ett sådant uttryck.

Till exempel konfigureras vår fil utmatnings komponent för den första video filen med det här uttrycket:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

För den andra utdata-videon har vi ett uttryck som:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Skulle det inte vara renare, mindre fel känsligt och mer bekvämt om vi skulle kunna ta bort några av dessa dupliceringar och göra saker mer konfigurerbara i stället? Som tur är vi kan: design programmets uttrycks funktioner i kombination med möjligheten att skapa anpassade egenskaper i vår arbets flödes rot ger ett extra lager av bekvämligheten.

Vi antar att vi kommer att använda fil namns konfigurationen från bit hastigheterna för de enskilda MP4-filerna. Dessa bit hastigheter kan vi konfigurera på en central plats (i roten i vårt diagram), från var de ska användas för att konfigurera och skapa enhets namns generering. För att göra detta börjar vi genom att publicera egenskapen bit hastighet från båda AVC-kodare till roten i vårt arbets flöde, så att det blir tillgängligt både i roten och från AVC-kodare. (Även om det visas i två olika punkter finns det bara ett underliggande värde.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Egenskaper för publicering av komponenten till arbets flödes roten
Öppna den första AVC-kodaren, gå till egenskapen bit hastighet (kbps) och välj publicera i list rutan.

![Publicera egenskapen bit hastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicera egenskapen bit hastighet*

Konfigurera dialog rutan Publicera för att publicera till roten i vårt arbets flödes diagram med det publicerade namnet "video1bitrate" och ett läsbart visnings namn för "video 1 bit hastighet". Konfigurera ett anpassat grupp namn med namnet "strömmande bit hastigheter" och tryck på Publicera.

![Publicera egenskapen bit hastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Publicerings dialog ruta för egenskapen bit hastighet*

Upprepa samma för egenskapen bit hastighet för den andra AVC-kodaren och ge den namnet "video2bitrate" med visnings namnet "video 2 bit hastighet" i samma anpassade grupp "strömmande bit hastigheter".

Om vi nu inspekterar rot egenskaperna för arbets flödet ser vi vår anpassade grupp med de två publicerade egenskaperna som visas. Båda motsvarar värdet för deras respektive AVC-kodares bit hastighet.

![Publicerade bit hastighets propor i arbets flödets rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

När vi vill komma åt dessa egenskaper från kod eller från ett uttryck, kan vi göra detta på följande sätt:

* från infogad kod från en komponent direkt under roten: Node. getPropertyAsString ('.. /video1bitrate ', null)
* i ett uttryck: $ {ROOT_video1bitrate}

Nu ska du gå igenom gruppen "strömmande bit hastigheter" genom att publicera vår bit hastighet för ljud spår. I egenskaperna för AAC-kodaren söker du efter bit hastighet-inställningen och väljer publicera i list rutan bredvid. Publicera till roten i grafen med namnet "audio1bitrate" och visnings namnet "ljud 1 bit hastighet" i vår anpassade grupp bit hastigheter.

![Publicerings dialog ruta för ljud bit hastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Publicerings dialog ruta för ljud bit hastighet*

![Resulterande video-och ljud propor i roten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterande video-och ljud propor i roten*

Att ändra något av dessa tre värden omkonfigureras också och ändrar värdena för respektive komponent som de är länkade till (och där de publiceras från).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Har genererade utdatafiler förlitar sig på publicerade egenskaps värden
I stället för att hårdkoda våra genererade fil namn kan vi nu ändra ditt filename-uttryck på varje fil utmatnings komponent för att förlita dig på egenskaperna för bit hastighet som vi publicerat i graf-roten. Börja med vår första filutdata, leta upp fil egenskapen och redigera uttrycket så här:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Du kan komma åt de olika parametrarna i det här uttrycket genom att trycka på dollar tecknet på tangent bordet i uttrycks fönstret. En av de tillgängliga parametrarna är vår video1bitrate-egenskap som vi publicerade tidigare.

![Åtkomst till parametrar i ett uttryck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Åtkomst till parametrar i ett uttryck*

Gör samma sak för utdatan för vår andra video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

och för utdata för endast ljudfiler:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Om vi nu ändrar bit hastigheten för någon av video-eller ljudfilerna, konfigureras respektive kodare om och den bitbaserade fil namns konventionen kommer att behållas alla automatiskt.

## <a id="thumbnails_to__multibitrate_MP4"></a>Lägga till miniatyrer i MP4-utdata med multibit hastighet
Från ett arbets flöde som genererar [en MP4-utdata med multibit hastighet från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att titta på att lägga till miniatyrer i utdata.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Översikt över arbets flöde för att lägga till miniatyrer i
![MP4-arbetsflöde med multibit hastighet att starta från](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*MP4-arbetsflöde med multibit hastighet att starta från*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Lägga till JPG-kodning
Hjärtat i vår miniatyr av miniatyren är komponenten JPG-kodare som kan skicka JPG-filer.

![JPG-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-kodare*

Det går inte att ansluta direkt till den okomprimerade video strömmen från medie filens indata till JPG-kodaren. I stället förväntas enskilda ramar visas. Detta kan du göra genom komponenten för video RAM porten.

![Ansluta en Frame-grind till JPG-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ansluta en Frame-grind till JPG-kodare*

Med ram porten en gång så många sekunder eller ramar kan en video ruta skickas. Det intervall och den tids förskjutning som detta sker i är konfigurerbart i egenskaperna.

![Egenskaper för grind av video ramar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Egenskaper för grind av video ramar*

Nu ska vi skapa en miniatyr varje minut genom att ange läget till tid (sekunder) och intervallet till 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Hantera färg områdes konvertering
Även om det ser till att logiska både okomprimerade video-PIN-filer och medie filens indata kan vara anslutna, skulle vi få en varning om vi skulle göra det.

![Fel i inmatat färg utrymme](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Fel i inmatat färg utrymme*

Detta beror på hur färg information visas i den ursprungliga RAW okomprimerade video strömmen, som kommer från vår MXF, skiljer sig från vad JPG-kodaren förväntar sig. Mer specifikt, ett så kallat "färg utrymme" i "RGB" eller "grå" förväntas flöda in. Det innebär att video bilds portens inkommande video ström måste ha en konvertering som gäller för dess färg utrymme först.

Dra till arbets flödet med färg områdets konverterare – Intel och Anslut den till vår port.

![Ansluta ett färg rymds convertor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ansluta ett färg rymds convertor*

I fönstret Egenskaper väljer du posten BGR 24 i listan för för inställningar.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Skriva miniatyr bilderna
Som skiljer sig från våra MP4-videor, och komponenten JPG-kodare matar ut mer än en fil. För att kunna hantera detta kan du använda en scen Sök-komponent för JPG-fil som kan användas: den tar de inkommande JPG-miniatyrerna och skriver ut dem, varje fil namn som suffixs med ett annat tal. (Talet visar vanligt vis antalet sekunder/enheter i strömmen som miniatyren ritades från.)

![Introduktion till Scene search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduktion till Scene search JPG File Writer*

Konfigurera egenskapen sökväg för målmapp med uttrycket: $ {ROOT_outputWriteDirectory}

och egenskapen filename med:

    ${ROOT_sourceFileBaseName}_thumb_

Prefixet avgör hur thumbnail-filerna får namn. De har suffix med ett tal som visar Skjutreglagets position i strömmen.

![Egenskaper för scen Sök-JPG-filen skrivare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Egenskaper för scen Sök-JPG-filen skrivare*

Anslut scenen search JPG File Writer till noden utdatafil/till gång.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Identifiera fel i ett arbets flöde
Anslut indata från färg områdes konverteraren till RAW okomprimerad video-utdata. Utför nu en lokal test körning för arbets flödet. Det är en utmärkt chans att arbets flödet plötsligt slutar att köras och indikerar en röd disposition på komponenten som påträffade ett fel:

![Konverterings fel för färg område](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Konverterings fel för färg område*

Klicka på ikonen för det lilla röda "E" i det övre högra hörnet av komponenten för färg områdes omvandlare för att se vad som är orsaken till att kodnings försöket misslyckades.

![Fel dialog ruta för konverterare för färg område](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Fel dialog ruta för konverterare för färg område*

Som du ser visas att den inkommande färg utrymmes standarden för konverteraren för färg området måste vara rec601 för den begärda konverteringen av YUV till RGB. Det verkar som att data strömmen inte visar sin rec601. (Rec 601 är en standard för kodning av sammanflätade analoga video signaler i digital video form. Den specificerar en aktiv region som omfattar 720-luminans och 360 chrominance-exempel per rad. Färg kodnings systemet kallas YCbCr 4:2:2.)

För att åtgärda detta visar vi metadata för vår data ström som vi hanterar med rec601-innehåll. För att göra det använder vi en uppdaterings komponent för en video data typ som vi sätter i mellan vår RAW-källa och konverterings komponenten för färg område. Den här data typs uppdateringen möjliggör manuell uppdatering av vissa egenskaper för video data typer. Konfigurera det för att ange en färg områdes standard för "REC 601". Detta gör att video data typs uppdaterings funktionen kan tagga strömmen med färg området "REC 601" om inget färg utrymme har definierats än. (Alla befintliga metadata åsidosätts inte om inte kryss rutan åsidosätter visas.)

![Uppdaterar färg utrymmes standard på data typs uppdatering](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Uppdaterar färg utrymmes standard på data typs uppdatering*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Avslutade arbets flöde
Nu när arbets flödet är klart gör du ett annat test för att se hur det fungerar.

![Avslutat arbets flöde för multi-MP4-utdata med miniatyrer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Avslutat arbets flöde för multi-MP4-utdata med miniatyrer*

## <a id="time_based_trim"></a>Tidsbaserad trimning av MP4-utdata med multibit hastighet
Från ett arbets flöde som genererar [en MP4-utdata med multibit hastighet från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att titta på att trimma käll videon baserat på tidsstämplar.

### <a id="time_based_trim_start"></a>Översikt över arbets flöde för att börja lägga till trimning i
![Startar arbets flöde för att lägga till trimning i](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Startar arbets flöde för att lägga till trimning i*

### <a id="time_based_trim_use_stream_trimmer"></a>Använda Stream-trimmern
Med komponenten Stream trimmer kan du trimma början och slutet av en data Ströms bas på timing-information (sekunder, minuter,...). Trimmer har inte stöd för bildruta-baserad trimning.

![Stream-trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream-trimmer*

I stället för att länka AVC-kodare och föredrags hållare till media-filens indata direkt, kommer vi att placeras mellan dem i Stream-trimmern. (En för video signalen och en för den överlagrade ljud signalen.)

![Lägg Stream-trimmer i mellan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Lägg Stream-trimmer i mellan*

Nu ska vi konfigurera en trimmer så att vi endast bearbetar video och ljud mellan 15 sekunder och 60 sekunder i videon.

Gå till egenskaperna för videos Stream-trimmern och konfigurera både start tid (15 s) och slut tid (60 s) egenskaper. För att se till att både vår ljud-och videotrimmer är alltid konfigurerade till samma start-och slut värden, publicerar vi dem till roten i arbets flödet.

![Publicera start tid-egenskapen från Stream trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicera start tid-egenskapen från Stream trimmer*

![Dialog rutan publicera egenskap för start tid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Dialog rutan publicera egenskap för start tid*

![Dialog rutan publicera egenskap för slut tid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Dialog rutan publicera egenskap för slut tid*

Om vi nu inspekterar roten i vårt arbets flöde, visas båda egenskaperna snyggt och konfigurerbara därifrån.

![Publicerade egenskaper som är tillgängliga i roten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicerade egenskaper som är tillgängliga i roten*

Öppna nu egenskaperna för trimning från ljud Trimmaren och konfigurera både start-och slut tider med ett uttryck som refererar till de publicerade egenskaperna i roten i vårt arbets flöde.

För start tiden för ljud trimning:

    ${ROOT_TrimmingStartTime}

och för dess slut tid:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Avslutade arbets flöde
![Avslutade arbets flöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Avslutade arbets flöde*

## <a id="scripting"></a>Introduktion till den skriptbaserade komponenten
Skriptbaserade komponenter kan köra godtyckliga skript under körnings faserna i vårt arbets flöde. Det finns fyra olika skript som kan köras, var och en med specifika egenskaper, och deras egna plats i livs cykeln för arbets flödet:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

Dokumentationen för den skriptbaserade komponenten går mer detaljerat för var och en av ovanstående. I [följande avsnitt](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)används skript komponenten **realizeScript** för att skapa en cliplist-XML i farten när arbets flödet startar. Det här skriptet anropas under komponent installationen, vilket sker bara en gång i livs cykeln.

### <a id="scripting_hello_world"></a>Skript i ett arbets flöde: Hej världen
Dra en skriptad komponent till design ytan och Byt namn på den (till exempel "SetClipListXML").

![Lägga till en skriptad komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptad komponent*

När du kontrollerar egenskaperna för den skriptbaserade komponenten, visas de fyra olika skript typerna, som var och en kan konfigureras till ett annat skript.

![Egenskaper för skript komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaper för skript komponent*

Rensa processInputScript och öppna redigeraren för realizeScript. Nu har vi konfigurerat och redo att starta skript.

Skripten är skrivna i Groove, ett dynamiskt kompilerat skript språk för Java-plattformen som bibehåller kompatibilitet med Java. De flesta Java-koder är faktiskt en giltig Groove-kod.

Nu ska vi skriva ett enkelt Hello World Groove-skript i samband med vårt realizeScript. Ange följande i redigeraren:

    node.log("hello world");

Kör nu en lokal test körning. Efter den här körningen kontrollerar du (via fliken system i den skriptbaserade komponenten) egenskapen loggar.

![Hello World-loggens utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello World-loggens utdata*

Node-objektet som vi kallar logg metoden på, refererar till vår aktuella "nod" eller komponenten som vi använder för att köra skript i. Varje komponent som sådan har möjlighet att skriva ut loggnings data, tillgängliga via fliken system. I det här fallet skriver vi strängen "Hello World". Viktigt att förstå här är att detta kan bevisa att det är ett användbart fel söknings verktyg, vilket ger dig information om vad skriptet faktiskt gör.

I vår skript miljö har vi också till gång till egenskaper för andra komponenter. Prova följande:

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

Vårt logg fönster visar oss följande:

![Logga utdata för åtkomst till Node-sökvägar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Logga utdata för åtkomst till Node-sökvägar*

## <a id="frame_based_trim"></a>Bildruta-baserad trimning av MP4-utdata med multibit hastighet
Från ett arbets flöde som genererar [en MP4-utdata med multibit hastighet från en MXF-indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)kommer vi nu att titta på att trimma käll videon baserat på antalet bild rutor.

### <a id="frame_based_trim_start"></a>Skiss översikt som börjar lägga till trimning till
![Arbets flöde för att börja lägga till trimning till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Arbets flöde för att börja lägga till trimning till*

### <a id="frame_based_trim_clip_list"></a>Använda Clip List-XML
I alla tidigare självstudier för arbets flöden använde vi medie filens indata-komponent som vår video ingångs källa. För det här scenariot kan vi använda klipp listans käll komponent i stället. Detta bör inte vara det bästa sättet att arbeta. Använd bara Clip List-källan när det finns en riktig anledning att göra det (som i följande fall, där vi använder Clip List trimnings funktioner).

Om du vill växla från vår Media-indata till klipp listens källa, drar du käll komponenten för klipp listan till design ytan och ansluter List-XML-noden för klipp listan i arbets flödes designern. Detta fyller på bildens List källa med utgångs stift, enligt vår inspelnings video. Anslut nu den okomprimerade videon och okomprimerade ljud stift från klipp List källan till respektive AVC-kodare och en överlagrad ljud Ströms över gång. Ta nu bort medie filens indata.

![Ersatt medie filens indata med klipp List källan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Ersatt medie filens indata med klipp List källan*

Klipp listens käll komponent tar som indata till en "Clip List-XML". När du väljer käll filen för att testa med lokalt fylls den här Clip List-XML automatiskt i.

![Automatiskt ifylld XML-egenskap för klipp lista](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatiskt ifylld XML-egenskap för klipp lista*

Titta närmare på XML-koden så att den ser ut så här:

![Redigera dialog rutan för Clip List](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Redigera dialog rutan för Clip List*

Detta återspeglar dock inte funktionerna i Clip List-XML. Ett alternativ är att lägga till ett "trim"-element under både video-och ljud källan, så här:

![Lägga till ett trimnings element i klipp listan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Lägga till ett trimnings element i klipp listan*

Om du ändrar Clip List-XML-filen så här ovan och utför en lokal testkörning, ser du att videon har trimmats på rätt sätt mellan 10 och 20 sekunder i videon.

I motsats till vad som händer när du gör en lokal körning, har samma cliplist-XML inte samma effekt när den tillämpas i ett arbets flöde som körs i Azure Media Services. När Azure Premium Encoder startar genereras XML-cliplist varje gång igen, baserat på indatafilen som kodnings jobbet angavs. Det innebär att alla ändringar som vi gör i XML-filen skulle ignoreras.

För att räkna antalet cliplist-XML som rensas när ett kodnings jobb startas, kan vi återskapa det i farten precis efter starten av vårt arbets flöde. Sådana anpassade åtgärder kan vidtas genom vad som kallas för en "skriptad komponent". Mer information finns i [Introduktion till den skriptbaserade komponenten](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Dra en skriptad komponent till design ytan och Byt namn på den till "SetClipListXML".

![Lägga till en skriptad komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptad komponent*

När du kontrollerar egenskaperna för den skriptbaserade komponenten visas de fyra olika skript typerna, som var och en kan konfigureras till ett annat skript.

![Egenskaper för skript komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaper för skript komponent*

### <a id="frame_based_trim_modify_clip_list"></a>Ändra klipp listan från en skriptad komponent
Innan vi kan skriva om cliplist-XML som genereras när arbets flödet startas måste du ha åtkomst till cliplist XML-egenskapen och innehållet. Vi kan göra detta på följande sätt:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Inkommande klipp lista loggas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Inkommande klipp lista loggas*

Först behöver vi ett sätt att avgöra från vilken punkt du vill trimma videon. För att göra det praktiskt för arbets flödets mindre tekniska användare, publicera två egenskaper till diagrammets rot. Det gör du genom att högerklicka på design ytan och välja Lägg till egenskap:

* Första egenskapen: "ClippingTimeStart" av typen: "TIDSKOD"
* Andra egenskap: "ClippingTimeEnd" av typen: "TIDSKOD"

![Dialog rutan Lägg till egenskap för urklipps start tid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Dialog rutan Lägg till egenskap för urklipps start tid*

![Publicerings tids egenskapar i arbets flödets rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicerings tids egenskapar i arbets flödets rot*

Konfigurera båda egenskaperna till ett lämpligt värde:

![Konfigurera urklipps start-och slut egenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurera urklipps start-och slut egenskaper*

I vårt skript har vi nu till gång till båda egenskaperna, så här:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Logg fönster som visar start och slut på Urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Logg fönster som visar start och slut på Urklipp*

Låt oss parsa tidskods-strängarna i ett bekvämare att använda formulär med ett enkelt reguljärt uttryck:

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

![Logg fönster med utdata från parsad tidskod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Logg fönster med utdata från parsad tidskod*

Med den här informationen kan vi nu ändra cliplist-XML-koden så att den återspeglar start-och slut tiderna för den önskade ram-korrekta urklipps bilden.

![Skript kod för att lägga till trimnings element](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Skript kod för att lägga till trimnings element*

Detta skedde genom vanliga sträng Manipulerings åtgärder. Den resulterande ändrade Clip List-XML-filen skrivs tillbaka till clipListXML-egenskapen i arbets flödes roten via metoden "setProperty". Logg fönstret efter en annan testkörning visar oss följande:

![Logga den resulterande klipp listan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Logga den resulterande klipp listan*

Gör en test körning för att se hur video-och ljud strömmar har klippts av. Eftersom du ska göra mer än en test körning med olika värden för trimnings punkterna ser du till att de inte tas med i beräkningen. Orsaken till detta är att designern, till skillnad från Azure-körningsmiljön, inte åsidosätter XML-cliplist varje körning. Det innebär att endast första gången du har ställt in och ut punkter, gör att XML-transformeringen omvandlas, alla andra gånger, vår Guard-sats (IF (`clipListXML.indexOf("<trim>") == -1`)) hindrar arbets flödet från att lägga till ett annat trimnings element när det redan finns ett.

För att det ska vara enkelt att testa arbets flödet lokalt lägger vi till lite kod som kontrollerar om ett trimnings element redan finns. I så fall kan vi ta bort den innan du fortsätter genom att ändra XML med de nya värdena. I stället för att använda enkla sträng manipulationer är det förmodligen säkrare att göra detta genom parsning av faktiska XML-objektmodellen.

Innan vi kan lägga till sådan kod, måste vi lägga till ett antal import-instruktioner i början av vårt skript först:

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

Efter detta kan vi lägga till den rensnings kod som krävs:

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

Den här koden går strax ovanför den punkt där vi lägger till trimnings elementen i XML-cliplist.

Vi kan nu köra och ändra vårt arbets flöde så mycket som vi vill samtidigt som ändringarna har använts.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Lägga till en ClippingEnabled-egenskap
Som du kanske inte alltid vill att trimning ska ske kan du avsluta vårt arbets flöde genom att lägga till en lämplig boolesk flagga som anger om vi vill aktivera trimning/Urklipp.

Som tidigare kan du publicera en ny egenskap till roten i vårt arbets flöde med namnet "ClippingEnabled" av typen "BOOLEAN".

![Publicerat en egenskap för att aktivera Urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicerat en egenskap för att aktivera Urklipp*

Med nedanstående enkla Guard-sats kan vi kontrol lera om trimning krävs och bestämma om vår klipp lista ska ändras eller inte.

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

### <a id="code"></a>Fullständig kod

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
[Vi presenterar Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Så här använder du Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Koda innehåll på begäran med Azure Media service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Arbetsflödesformat och codecs för Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exempel på arbetsflödesmallar](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-verktyg](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
