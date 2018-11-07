---
title: Avanced Media Encoder Premium Workflow självstudier
description: Det här dokumentet innehåller genomgångar som visar hur du utför avancerade åtgärder med Media Encoder Premium Workflow och hur du skapar komplexa arbetsflöden med Workflow Designer.
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
ms.date: 10/30/2018
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 0a20f7629fbc102ae05c51c7388bbfd6915d6204
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257392"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Avancerade självstudier för Media Encoder Premium Workflow
## <a name="overview"></a>Översikt
Det här dokumentet innehåller genomgångar som visar hur du anpassar arbetsflöden med **Arbetsflödesdesigner**. Du hittar de faktiska Arbetsflödesfilerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>INNEHÅLLSFÖRTECKNING
I följande avsnitt beskrivs:

* [MXF-kodning till en enda bithastighet MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Startar ett nytt arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Med den Media fil som indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Kontrollera strömmar av media](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Lägger till en videokodare för. Generera filen för MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodning i ljudström](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexering ljud och Video strömmar till en MP4-behållare](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Skriver MP4-fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Skapa ett Media Services tillgång från utdatafilen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testa klar arbetsflödet lokalt](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [MXF-kodning i multibithastighet MP4s - dynamisk paketering som aktiverats](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Att lägga till en eller flera ytterligare MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurera utdata-filnamn](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Att lägga till en separat ljudspår](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Att lägga till filen ”ISM” SMIL](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodning MXF till MP4 - förbättrad skissen i multibithastighet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Översikt över arbetsflöde för att förbättra](#workflow-overview-to-enhance)
  * [Filen namngivningskonventioner](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicera komponentegenskaper till roten för arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Har genererat utdatafilen namn är beroende av publicerade egenskapsvärden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Om du lägger till miniatyrbilder multibithastighet MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Översikt över arbetsflöde för att lägga till miniatyrbilder till](#workflow-overview-to-add-thumbnails-to)
  * [Att lägga till JPG-kodning](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Hantera färg utrymme konvertering](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Skriva miniatyrbilderna](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Identifiering av fel i ett arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Klar arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Tidsbaserade trimning MP4 i multibithastighet utdata](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Översikt över arbetsflödet att börja lägga till trimning till](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Med hjälp av Stream Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Klar arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduktion till komponenten skriptbaserade](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skript i ett arbetsflöde: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [RAM-baserade trimning MP4 i multibithastighet utdata](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Skissen översikten om du vill börja lägga till trimning till](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Med hjälp av listan Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Ändra listan clip från en skriptade komponent](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Lägga till en ClippingEnabled bekvämlighet egenskap](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>MXF-kodning till en enda bithastighet MP4
Det här avsnittet visar hur du skapar en enkel bithastighet. MP4-fil med AAC-HE kodat ljud från en. MXF indatafilen.

### <a id="MXF_to_MP4_start_new"></a>Startar ett nytt arbetsflöde
Öppna Arbetsflödesdesigner och Välj fil > Ny arbetsyta > koda om skiss

Det nya arbetsflödet visar tre element:

* Primär källfilen
* Clip List-XML
* Filen/Utdatatillgången  

![Ny Kodningsarbetsflöden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Ny Kodningsarbetsflöden*

### <a id="MXF_to_MP4_with_file_input"></a>Med den Media fil som indata
För att kunna acceptera inkommande mediefilen startar med att lägga till en Media filen inkommande komponent. Om du vill lägga till en komponent i arbetsflödet, söker efter den i sökrutan lagringsplats och dra den önskade posten till fönstret designer. Upprepa åtgärden för Media filen indata och Anslut komponenten primära källfilen till Filename inkommande PIN-koden från Media filen indata.

![Anslutna mediefil indata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Anslutna mediefil indata*

Först identifiera en lämplig exempelfil att använda när du skapar ett anpassat arbetsflöde. Om du vill göra det klickar du på bakgrunden designer fönstret och leta efter egenskapen primära källfilen i fönstret till höger egenskapen. Klicka på mappikonen och välj önskad fil för att testa arbetsflödet. Media-fil som indata-komponent undersöker filen och fyller på dess utdata PIN-koder för att återspegla information om den kontrolleras-exempelfilen.

![Ifyllt mediefil indata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Ifyllt mediefil indata*

Nu när indata är ifylld, är nästa steg att konfigurera utdata encoding-inställningar. Liknar hur primära källfilen har konfigurerats, nu konfigurera egenskapen utdata mappen variabeln under den.

![Konfigurerade egenskaper för indata och utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurerade egenskaper för indata och utdata*

### <a id="MXF_to_MP4_streams"></a>Kontrollera strömmar av media
Det har ofta vill veta att dataströmmen ser ut som när den förs vidare genom arbetsflödet. Om du vill kontrollera en ström när som helst i arbetsflödet, klicka bara på ett utgående eller inkommande PIN-kod på någon av komponenterna. I det här fallet ska du använda kommandot på PIN-kod för okomprimerade Video-utdata från den Media-fil som indata. En dialogruta öppnas som gör för att inspektera utgående videon.

![Kontrollera okomprimerade Video utdata PIN-koden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Kontrollera okomprimerade Video utdata PIN-koden*

I det här fallet den visar att videon innehåller en 1920 x 1080 inmatningen vid 24 bildrutor per sekund i 4:2:2 sampling en video om nästan 2 minuter.

### <a id="MXF_to_MP4_file_generation"></a>Lägger till en videokodare för. Generera filen för MP4
En okomprimerad Video och flera okomprimerat ljud utdata PIN-koder som är tillgängliga för Använd nu på den Media-fil som indata. En kodning komponenten måste läggas till i arbetsflödet – i det här fallet för att generera för att koda inkommande videon. MP4-filer.

Lägg till komponenten AVC videokodare till designytan för att koda videoströmmen till H.264. Den här komponenten tar en Expandera videoströmmen som indata och levererar en AVC komprimerade videoströmmen på dess utdata PIN-kod.

![Är anslutna AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Är anslutna AVC-kodare*

Egenskaperna avgör hur kodningen exakt händer. Låt oss ta en titt på några av de viktigaste inställningarna:

* Utdata bredd och höjd för utdata: upplösning VS. videon. I det här fallet är 640 x 360 en bra inställning.
* Bildfrekvens: när inställningen genomströmning den bara antar bildfrekvens källan, är det möjligt att åsidosätta detta dock. Sådana ramhastighet konverteringen kompenseras inte rörelse.
* Profilen och nivå: avgör vilka AVC profil och en nivå. För att enkelt få mer information om olika nivåer och -profiler, klickar du på frågetecken-ikonen på AVC Video Encoder-komponenten och på hjälpsidan visas mer information om var och en av nivåerna. I det här exemplet använder du Main-profil på nivå 3.2 (standard).
* Hastighetsbegränsning kontrolläge och bithastighet (kbit/s): i det här scenariot, välja en konstant bithastighet (CBR) som utdata i 1200 kbit/s
* Videoformat: innehåller information om VUI (Video användbarhet Information) som hämtar skrivits till H.264-dataströmmen (sida information som kanske används av en avkodare att förbättra visning men är inget krav att avkoda korrekt):
* NTSC (typisk för USA eller Japan, med hjälp av 30 bilder per sekund)
* PAL (typisk för Europa, med hjälp av 25 bildrutor per sekund)
* GOP storlek läge: Ange fast GOP-storlek för våra syften med ett intervall med nyckel 2 sekunder med stängd GOPs. Inställningen för 2 sekunder garanterar kompatibilitet med dynamisk paketering Azure Media Services tillhandahåller.

Om du vill flöde AVC kodaren eller ansluta du okomprimerade Video utdata PIN-koden från komponenten Media filen indata till okomprimerade Video inkommande PIN-koden från AVC kodaren.

![Anslutna AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Anslutna AVC Main-kodare*

### <a id="MXF_to_MP4_audio"></a>Kodning i ljudström
Nu måste fortfarande den ursprungliga okomprimerade ljudströmmen ska komprimeras. Lägga till ett AAC-kodningskomponent (Dolby) i arbetsflödet för komprimering av ljud dataströmmen.

![Är anslutna AVC-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Är anslutna AAC-kodare*

Nu finns det en inkompatibilitet: det finns bara en enda okomprimerade ljud inkommande PIN-kod från AAC-kodaren medan mycket sannolikt Media filen indata har två olika okomprimerade ljudströmmar tillgängliga: en för den vänstra ljud kanalen och en för höger. (Om du hanterar omge ljud får är sex kanaler.) Så går det inte att ansluta direkt ljudet från käll-Media-fil som indata till ljud AAC-kodaren. AAC-komponenten förväntar sig en s.k. ”överlagrad” ljudström: en enda dataström som har både till vänster och höger kanalerna överlagrad med varandra. När vi vet i vår källfilen media att ljudspår på vilka position i källan, kan vi skapa sådana överlagrad ljudström med korrekt tilldelade talare positioner för vänster och höger.

Först måste vill en Generera en överlagrad ström från krävs källa ljud kanaler. Komponenten ljud Stream Interleaver hanterar det för oss. Lägga till den i arbetsflödet och ansluta till ljud utdatan från den Media-fil som indata till den.

![Anslutna ljud Stream Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Anslutna ljud Stream Interleaver*

Nu när vi har en överlagrad ljudström ange vi fortfarande inte var du vill tilldela vänster eller höger talare positioner till. För att ange detta kan vi utnyttja talare Position Tilldelare.

![Att lägga till en talare Position Tilldelare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Att lägga till en talare Position Tilldelare*

Konfigurera talare Position Tilldelare för användning med en stereo Indataströmmen genom en kodare förinställda Filter av ”anpassad” och den kanal som förinställning kallas ”2.0 (L, R)”. (Den här koden tilldelar vänstra talare position på channel 1 och rätt API-placering att kanalen 2.)

Anslut utdataporten för talare Position Tilldelare till indata för AAC-kodaren. Sedan anger du den AAC-kodaren att fungera med en ”2.0 (L, R)” Channel bitrate så att den vet för att hantera stereo ljud som indata.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexering ljud och Video strömmar till en MP4-behållare
Beroende på vår AVC kodade video-ström och våra AAC kodad ljudström, vi kan samla in båda två i en. MP4-behållaren. Processen att blanda olika strömmar till en enda kallas ”multiplexing” (eller ”muxing”). I det här fallet vi interleaving ljudet och videoströmmar i en enda sammanhängande. MP4-paketet. Den komponent som samordnar detta för en. MP4 behållaren heter ISO MPEG-4-multiplexor. Lägg till en till designytan och Anslut både AVC Video Encoder och AAC-kodaren till dess indata.

![Anslutna MPEG4 multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Anslutna MPEG4 multiplexor*

### <a id="MXF_to_MP4_writing_mp4"></a>Skriver MP4-fil
När du skriver en utdatafil används filen utdata-komponenten. Vi kan du ansluta det till utdataporten för ISO MPEG-4-multiplexor så att dess utdata skrivs till disk. Gör detta genom att ansluta behållare (MPEG-4) utdata PIN-koden för skrivna indata PIN-kod på fil-utdata.

![Ansluten filen utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ansluten filen utdata*

Filnamnet som används fastställs av egenskapen. Egenskapen kan vara hårdkodad till ett visst värde, vill troligen en anges via ett uttryck i stället.

Ha arbetsflödet automatiskt fastställa utdata filen namnegenskapen från ett uttryck, klickar på knappen bredvid namnet på filen (bredvid mappikonen). Från den nedrullningsbara menyn väljer du sedan ”uttryck”. Detta öppnar uttrycksredigeraren. Radera innehållet i redigeraren först.

![Tom uttrycksredigeraren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Tom uttrycksredigeraren*

Uttrycksredigeraren kan du ange valfri literalvärde blandat med en eller flera variabler. Variabler som börjar med ett dollartecken. När du når nyckeln $ redigeraren visas en listruta med över tillgängliga variabler. I vårt fall använder vi en kombination av utdata directory variabeln och den grundläggande indatafilen namn:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Fylls ut uttrycksredigeraren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Fylls ut uttrycksredigeraren*

> [!NOTE]
> Om du vill se en utdatafil för kodning jobbet i Azure måste du ange ett värde i uttrycksredigeraren.
>
>

När du har bekräftat uttrycket genom att trycka på ok, förhandsgranskar fönstret egenskapen vilket värde som egenskapen-löser fil vid denna tidpunkt.

![Uttryck för filen matchar utdata dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Uttryck för filen matchar utdata dir*

### <a id="MXF_to_MP4_asset_from_output"></a>Skapa ett Media Services tillgång från utdatafilen
När vi har skrivit en MP4-utdatafil, behöver vi fortfarande indikerar att den här filen tillhör utdatatillgången vilka medietjänster genererar ett resultat av körning av arbetsflödet. Därför används filen/Utdatatillgången nod på arbetsytan för arbetsflödet. Alla inkommande filer i den här noden gör en del av denna Azure Media Services tillgång.

Koppla filen utdata-komponenten till komponenten fil/Utdatatillgången att slutföra arbetsflödet.

![Klar arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Klar arbetsflöde*

### <a id="MXF_to_MP4_test"></a>Testa klar arbetsflödet lokalt
Om du vill testa arbetsflödet lokalt, trycker du på knappen Spela upp i verktygsfältet högst upp. När arbetsflödet slutförts kör du inspektera utdata genereras i konfigurerade utdatamappen. Du ser klar MP4 utdatafilen som kodats från filen MXF Indatakällan.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kodning MXF till MP4 - multibithastighet aktiverad dynamisk paketering
Den här genomgången skapar en uppsättning MP4-filer med flera bithastigheter med AAC-kodade ljud från en enda. MXF indatafilen.

När en tillgång med flera bithastigheter-utdata är det önskade för användning i kombination med dynamisk paketering-funktioner som erbjuds av Azure Media Services, flera GOP-justerad MP4-filer för var och en med flera olika bithastigheter och upplösning måste skapas. Gör den [kodning MXF i en enda bithastighet MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) genomgången ger oss en bra utgångspunkt.

![Starta arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Starta arbetsflöde*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Att lägga till en eller flera ytterligare MP4-utdata
Alla MP4-filer i vår Azure Media Services tillgång har stöd för en annan bithastighet och upplösning. Vi lägger till en eller flera MP4-utdatafilerna till arbetsflödet.

Om du vill se till att vi har vår video kodare som skapats med samma inställningar, är det enklaste är att kopiera den befintliga AVC-videokodare och konfigurera en annan kombination av upplösning och bithastighet (Nu ska vi lägga till en av 960 x 540 med 25 bildrutor per sekund med 2,5 Mbit/s ). Om du vill duplicera den befintliga kodaren klistra kopiera in den på designytan.

Ansluta okomprimerade Video utdata PIN-koden för den Media-fil som indata till vår nya AVC-komponenten.

![Den andra AVC encoder ansluten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Den andra AVC encoder ansluten*

Nu anpassa konfigurationen för vårt nya AVC encoder att mata ut 960 x 540 med 2,5 Mbit/s. (Använda egenskaper ”utdata bredd”, ”utdata höjd” och ”bithastighet (kbit/s)” för detta.)

Får vi vill använda denna tillgång tillsammans med Azure Media Services dynamisk paketering, slutpunkten för direktuppspelning måste kunna generera från dessa MP4-filer HLS/fragmenterad MP4/DASH fragment som justeras exakt till varandra på ett sätt som klienter som byter mellan olika bithastighet få en enda smidig kontinuerlig video och ljud. Om du vill aktivera den här funktionen, som vi behöver kontrollera att storleken för båda MP4-filer i egenskaperna för båda AVC kodare GOP (”grupp av bilder”) anges till 2 sekunder, som kan göras genom:

* Ange läget för GOP-storlek till fast GOP storlek och
* Ramens nyckel intervall till två sekunder.
* också ange GOP IDR kontrollen till stängd GOP för att se till att alla GOPs står på sina egna utan beroenden

Om du vill göra det här arbetsflödet ska vara lättare att förstå att byta namn på den första AVC kodaren till ”AVC Video Encoder 640 x 360 1200 kbit/s” och den andra AVC kodaren ”AVC Video Encoder 960 x 540 2 500 kbit/s”.

Nu ska du lägga till en andra ISO MPEG-4-multiplexor och en andra fil-utdata. Anslut den multiplexor till den nya AVC encoder och kontrollera att dess utdata omdirigeras till filen utdata. Sedan också ansluta AAC ljud encoder utdata till den nya multiplexor indata. Fil-utdata anslutas i sin tur sedan till noden fil/Utdatatillgången lägga till den i Media Services tillgången som ska skapas.

![Andra Muxer och filen utdata anslutna](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Andra Muxer och filen utdata anslutna*

Konfigurera den multiplexor segment läge GOP antal eller varaktighet för kompatibilitet med Azure Media Services dynamisk paketering och ange GOPs per segment till 1. (Detta bör vara standard.)

![Muxer segment lägen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer segment lägen*

Obs: du kanske vill upprepa proceduren för alla andra bithastighet och upplösning kombinationer du har lagt till i utdata för tillgången.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurera utdata-filnamn
Vi har fler än en fil har lagts till för utdatatillgången. Detta ger ett behov av att kontrollera filnamnen för var och en av utdatafilerna skiljer sig från varandra och kanske även tillämpa en namngivningskonvention med filen så att det blir tydligt filnamnet vad du hanterar med.

Namngivning av utdata kan styras via uttryck i designern. Öppna fönstret egenskap för en av komponenterna filen utdata och öppna uttrycksredigeraren för egenskapen fil. Vårt första utdatafilen har konfigurerats med följande uttryck (finns i självstudierna för att gå från [MXF till en enda bithastighet MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Det innebär att vår filename bestäms av två variabler: utdatakatalogen att skriva i och det grundläggande namnet för käll-filen. Den tidigare versionen visas som en egenskap i roten för arbetsflödet och bestäms av den importerade filen. Utdatakatalogen är det du använder för lokal testning; den här egenskapen åsidosätts av arbetsflödesmotorn när arbetsflödet körs av molnbaserade mediebearbetare i Azure Media Services.
Om du vill ge både våra utdatafilerna namngivning av konsekvent utdata, ändrar du den första filen namngivning av uttryck som:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

och andra för:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Köra ett mellanliggande test som körs för att se till att båda filerna för MP4-utdata genereras korrekt.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Att lägga till en separat ljudspår
Som vi ser senare när vi skapar en .ism-fil att gå med vår MP4-filer för utdata visar kräver vi också en ljuddata MP4-fil som ljudspåret för vår Adaptiv direktuppspelning. Att skapa den här filen, lägga till en ytterligare muxer i arbetsflödet (ISO-MPEG-4 multiplexor) och ansluta den AAC-kodaren utdata PIN-kod med dess indata PIN-kod för spåra 1.

![Ljud Muxer har lagts till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Ljud Muxer har lagts till*

Skapa en tredje filen utdata komponenten utdata utgående dataströmmen från muxer och konfigurera filnamn uttryck som:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Ljud Muxer skapa filen utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Ljud Muxer skapa filen utdata*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Att lägga till den. ISM SMIL-fil
Att arbeta i kombination med både MP4-filer (och enbart ljud MP4) i vår tillgång med Media Services dynamisk paketering måste vi även en manifestfil (kallas även en ”SMIL”-fil: synkronisera Multimedia Integration språk). Den här filen anger till Azure Media Services vilka MP4-filer är tillgängliga för dynamisk paketering och vilka av dessa att tänka på för den strömning av ljud. En typisk manifestfil för en uppsättning MP4's med en enda ljudström ser ut så här:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
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

.Ism-fil innehåller inom en referens till var och en av de enskilda MP4-filerna och utöver dessa också ett (eller mer) ljudfil referenser till en MP4 som endast innehåller ljudet en switch-sats.

Generera manifestfilen för vår uppsättning MP4's kan göras via en komponent som kallas ”AMS Manifest skrivare”. För att använda den drar det till på ytan och Anslut ”skriva slutförd” utdata PIN-koder från de tre komponenterna för filen utdata till AMS-Manifest skrivaren indata. Kontrollerar du att ansluta till filen/Utdatatillgången för utdata från AMS Manifest-skrivaren.

Precis som med våra andra filen utdata komponenter, konfigurerar du utdatanamnet .ism-fil med ett uttryck:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Vårt klar arbetsflöde ser ut som den nedan:

![Klar MXF till arbetsflödet för MP4 i multibithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Klar MXF till arbetsflödet för MP4 i multibithastighet*

## <a id="MXF_to__multibitrate_MP4"></a>Kodning MXF till MP4 - förbättrad skissen i multibithastighet
I den [tidigare arbetsflöde genomgången](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) vi har sett hur en enda MXF indatatillgången kan konverteras till en tillgång i utdata med MP4-filer, en ljuddata MP4-fil och en manifestfil som används i samband med Azure Media Tjänster för dynamisk paketering.

Den här genomgången visar hur några av aspekterna kan förbättras och görs enklare.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Översikt över arbetsflöde för att förbättra
![Multibitrate MP4-arbetsflöde för att förbättra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4-arbetsflöde för att förbättra*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Filen namngivningskonventioner
I föregående arbetsflödet bestämde vi ett enkelt uttryck som grund för att generera utdata-filnamn. Vi har några duplicering dock: alla enskilda utdata filen komponenter anges sådana uttryck.

Vår fil utdata-komponenten för den första videofilen har exempelvis konfigurerats med det här uttrycket:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Och för den andra utdata video har vi ett uttryck som:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Skulle det inte vara tydligare mindre felbenägna och mer praktiskt om vi kan ta bort några av duplicering och göra det mer kan konfigureras i stället? Som tur är kan vi: i designer uttryck funktioner i kombination med möjligheten att skapa anpassade egenskaper i vår rot-arbetsflöde ger ytterligare ett lager med bekvämlighet.

Anta att vi ska hårddiskkonfiguration filename bland olika bithastigheter enskilda MP4-filer. Dessa olika bithastigheter vi ska försöka konfigurera på en central plats (i roten på vår graph), där de kan nås så här konfigurerar du och filnamnet för enheten. Detta gör börjar vi genom att publicera egenskapen med flera bithastigheter från båda AVC kodare till roten i vårt arbetsflöde så att den blir tillgänglig från båda roten samt från och med den AVC kodare. (Även om visas i två olika platser, det är bara en underliggande värde.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publicera komponentegenskaper till roten för arbetsflöde
Öppna den första AVC kodaren går du till egenskapen bithastighet (kbit/s) och välj Publicera i listrutan.

![Publicera egenskapen bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicera egenskapen bithastighet*

Konfigurera dialogrutan Publicera att publicera till roten i vår arbetsflöde diagram med en publicerade namnet ”video1bitrate” och en läsbar visningsnamnet för ”Video 1 bithastighet”. Konfigurera en anpassad gruppnamn kallas ”Streaming bithastighet” och trycker på Publish.

![Publicera egenskapen bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Publiceringsdialogrutan för bithastighet egenskapen*

Upprepa samma för egenskapen med flera bithastigheter för den andra AVC kodaren och ge den namnet ”video2bitrate” med ett visningsnamn för ”videon 2 bithastighet” i samma anpassade grupp ”Streaming bithastighet”.

Om vi nu granska egenskaperna för arbetsflödet rot, ser vi vår anpassad grupp med två publicerade egenskaper visas. Båda återger värdet för sina respektive AVC encoder bithastighet.

![Egenskaper för publicerade bithastighet i rot-arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

När vi vill ha åtkomst till de här egenskaperna från kod eller ett uttryck kan vi göra det så här:

* från infogad kod från en komponent precis under roten: node.getPropertyAsString('.. / video1bitrate ”, null)
* i ett uttryck: ${ROOT_video1bitrate}

Låt oss avsluta ”Streaming bithastighet”-grupp genom att publicera vår ljudspår bithastighet på den också. I egenskaperna för den AAC-kodaren kan söka efter inställningen med flera bithastigheter och välj Publicera i listrutan bredvid den. Publicera till roten i diagrammet med namnet ”audio1bitrate” och visningsnamnet ”ljud 1 bithastighet” i våra anpassade grupp ”Streaming bithastighet”.

![Publiceringsdialogrutan för ljud bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Publiceringsdialogrutan för ljud bithastighet*

![Resulterande video och ljud sammanställer på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterande video och ljud sammanställer på rot*

Ändra någon av dessa tre värden också Omkonfigurerar och ändrar värden om de är kopplade till respektive komponenter (och där publicerats från).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Har genererat utdatafilen namn är beroende av publicerade egenskapsvärden
I stället för hardcoding vår genererade filnamn, vi kan nu ändra vårt filename uttryck på varje fil utdata förlita dig på bithastighet egenskaperna som vi har publicerat graph rot. Från och med vår första filen utdata, hitta filegenskapen och redigera uttryck så här:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

De olika parametrarna i det här uttrycket kan nås och anges genom att trycka dollartecken på tangentbordet i uttrycksfönstret. En av de tillgängliga parametrarna är vår video1bitrate-egenskap som vi har publicerat tidigare.

![Åtkomst till parametrar i ett uttryck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Åtkomst till parametrar i ett uttryck*

Gör samma sak för fil-utdata för våra andra video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

och för filen med enbart ljud-utdata:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Om vi nu ändra bithastigheter för någon av video- eller ljudinnehåll filerna respektive kodaren ska konfigureras och bithastighet-baserade filen namnet konvention kommer att användas automatiskt.

## <a id="thumbnails_to__multibitrate_MP4"></a>Om du lägger till miniatyrbilder multibithastighet MP4-utdata
Med början från ett arbetsflöde som genererar [en MP4 i multibithastighet utdata från en MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på när du lägger till miniatyrbilder till utdata.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Översikt över arbetsflöde för att lägga till miniatyrbilder till
![Multibitrate MP4-arbetsflöde för att starta från](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4-arbetsflöde för att starta från*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Att lägga till JPG-kodning
Helt i linje med våra miniatyrbilder kommer att komponenten JPG-kodare kunna JPG utdatafiler.

![JPG-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-kodare*

Vi kan inte ansluta vår okomprimerade videoström men direkt från den Media-fil som indata till JPG-kodaren. I stället förväntar sig att lämnas enskilda bildrutor. Detta kan vi göra via Video ramens Gate-komponenten.

![Ansluta en ram gate till JPG-kodare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ansluta en ram gate till JPG-kodare*

Gate för ramens var så många: e sekund eller bildrutor låter en bildruta att skicka. Intervall och tider med det här inträffar kan konfigureras i egenskaperna.

![Egenskaper för video ramens Gate](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Egenskaper för video ramens Gate*

Nu ska vi skapa en miniatyrbild varje minut genom att ange läget till (sekunder) och 60 intervallet.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Hantera färg utrymme konvertering
Medan verkar det logiskt kan nu vara ansluten båda okomprimerade Video PIN-koder för ram-gate och den Media-fil som indata, skulle vi få en varning om vi vill göra detta.

![Indatafärgen utrymme fel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Indatafärgen utrymme fel*

Detta beror på hur vilken färg information representeras i vår ursprungliga rådata okomprimerade videoström, kommer från våra MXF skiljer sig från vad JPG-kodaren förväntar. Mer specifikt förväntas en s.k. ”färg utrymme” av ”RGB” eller ”gråskala” flöda. Det innebär att den Video ramens Gate inkommande videoströmmen måste ha en konvertering som appliceras först om dess färg utrymme.

Dra till arbetsflödet färg utrymme Converter - Intel och ansluter den till vår ramens gate.

![Ansluta en färg utrymme konverteraren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ansluta en färg utrymme konverteraren*

Välj posten BGR 24 från listan över förinställning i egenskapsfönstret.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Skriva miniatyrbilderna
Olika från våra MP4 video, JPG-kodningskomponent returnerar mer än en fil. För att kunna hantera detta, en scen Search JPG-filen Writer-komponenten kan användas: det tar inkommande JPG miniatyrbilderna och skriver ut dem varje filnamn som suffix med ett annat nummer. (Det tal som anger hur många sekunder/enheter i strömmen som miniatyren ritades från vanligtvis.)

![Introduktion till scen Search JPG-filen skrivare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduktion till scen Search JPG-filen skrivare*

Konfigurera egenskapen utdata mappsökväg med uttrycket: ${ROOT_outputWriteDirectory}

och egenskapen Filename Prefix med:

    ${ROOT_sourceFileBaseName}_thumb_

Prefixet avgör hur miniatyr filer namnges. De är suffix med ett tal som anger reglagets position i dataströmmen.

![Egenskaper för scen Search JPG-filen Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Egenskaper för scen Search JPG-filen Writer*

Anslut scen Search JPG-filen skrivaren till filen/Utdatatillgången-nod.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Identifiering av fel i ett arbetsflöde
Ansluta indata för färg utrymme konverterare till rå okomprimerade videoutdata. Nu ska du utföra en lokal testkörning för arbetsflödet. Det finns en bra risk under arbetsflödet plötsligt avbrytas och ange med en röd ram på den komponent som påträffade ett fel:

![Färg utrymme Converter fel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Färg utrymme Converter fel*

Klicka på den lilla red ”E”-ikonen i övre högra hörnet av färg utrymme Converter-komponenten för att se vad som är orsaken kodning försöket misslyckades.

![Färg utrymme Converter feldialogruta](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Färg utrymme Converter feldialogruta*

Det visar sig, som du kan se att det inkommande färg utrymmet som är standard för färg utrymme converter måste vara rec601 för våra begärda konvertering av YUV till RGB. Vår stream anger uppenbarligen inte dess rec601. (Rek 601 är en standard för kodning sammanflätade analoga video signaler i digital video form. Den anger en aktiv-region som täcker 720 ljusstyrka exempel och 360 krominans exempel per rad. Färgen kodning system som kallas YCbCr 4:2:2.)

Lös problemet genom ska vi ange på metadata för vår stream som vi hanterar rec601 innehåll. Vi ska använda en Video Data typ Updater-komponent som vi lägger in mellan våra raw käll- och färgkomponent för konvertering av utrymme för att göra detta. Den här updater för typ av data kan manuell uppdatering av vissa videodata egenskaperna för anslutningstypen. Konfigurera den för att ange en färg utrymme Standard av ”Rec 601”. Detta gör videon Data typ Updater att tagga dataströmmen med ”Rec 601” färg utrymme om det fanns inget utrymme för färg som har definierats. (Inte åsidosätter alla befintliga metadata, såvida inte Åsidosätt kryssrutan checkades.)

![Uppdatera färg utrymme Standard på Data typ Updater](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Uppdatera färg utrymme Standard på Data typ Updater*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Klar arbetsflöde
Nu när våra arbetsflöde är klar kan du göra en annan virtuell test körning så att den skickar.

![Klar arbetsflöde för multi-mp4-utdata med miniatyrbilder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Klar arbetsflöde för multi-mp4-utdata med miniatyrbilder*

## <a id="time_based_trim"></a>Tidsbaserade trimning MP4 i multibithastighet utdata
Med början från ett arbetsflöde som genererar [en MP4 i multibithastighet utdata från en MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på trimmar källa videon baserat på tidsstämplar.

### <a id="time_based_trim_start"></a>Översikt över arbetsflödet att börja lägga till trimning till
![Starta arbetsflöde för att lägga till trimning till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Starta arbetsflöde för att lägga till trimning till*

### <a id="time_based_trim_use_stream_trimmer"></a>Med hjälp av Stream Trimmer
Komponenten Stream Trimmer möjligt att trimma början och slutet av en indataström som baseras på tidsgränsen information (sekunder, minuter,...). Kanstskäraren stöder inte ram-baserade optimering.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

I stället för att länka AVC kodare och talare position tilldelare till den fil som indata Media direkt, lägger vi stream trimmer mellan de. (En för video signalen och för överlagrad ljudsignalen.)

![Placera Stream Trimmer mellan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Placera Stream Trimmer mellan*

Nu ska vi konfigurera kanstskäraren så att vi endast kommer att bearbeta video och ljud mellan 15 sekunder och 60 sekunder i videon.

Gå till egenskaperna för Video Stream Trimmer och konfigurera båda Starttid (15 s) och sluttid (60 s) egenskaper. Om du vill kontrollera att både våra ljud och video trimmer alltid är konfigurerade för samma start- och slutvärdena, publicerar vi dem till roten i arbetsflödet.

![Publicera start tid-egenskap från Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicera start tid-egenskap från Stream Trimmer*

![Egenskapsdialogrutan publicera för starttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Egenskapsdialogrutan publicera för starttid*

![Egenskapsdialogrutan publicera för sluttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Egenskapsdialogrutan publicera för sluttid*

Om vi nu inspektera roten av vårt arbetsflöde, är båda egenskaperna snyggt visas och konfigurerbara därifrån.

![Publicerade egenskaper som är tillgängliga på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicerade egenskaper som är tillgängliga på rot*

Öppna Egenskaper för trimning från ljud trimmer och konfigurera både start- och sluttider med ett uttryck som refererar till de publicerade egenskaperna i roten på vårt arbetsflöde.

Ljudet trimmar starttid:

    ${ROOT_TrimmingStartTime}

och för dess Sluttid:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Klar arbetsflöde
![Klar arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Klar arbetsflöde*

## <a id="scripting"></a>Introduktion till komponenten skriptbaserade
Skriptbaserade komponenter kan köra godtyckliga skript under körning faser av vårt arbetsflöde. Det finns fyra olika skript som kan utföras med specifika egenskaper och deras egen plats i arbetsflödet livscykeln:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

I dokumentationen för komponenten skriptade går i detalj för var och en av ovanstående. I [nedan](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** scripting komponent som används för att konstruera ett cliplist xml direkt när arbetsflödet startas. Det här skriptet kallas under installationen komponent som görs bara en gång i livscykeln.

### <a id="scripting_hello_world"></a>Skript i ett arbetsflöde: hello world
Dra en skriptade komponent till designytan och Byt namn på den (till exempel ”SetClipListXML”).

![Att lägga till en skriptbaserade komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Att lägga till en skriptbaserade komponent*

När du granska egenskaperna för komponenten skriptade kommer de fyra typerna av annat skript att visas och varje konfigurerbara till ett annat skript.

![Komponentegenskaper för skript](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Komponentegenskaper för skript*

Rensa processInputScript och öppna Redigeraren för realizeScript. Nu är vi ställa in och kan nu starta skriptet.

Skript är skrivna i Groovy, ett dynamiskt kompilerade språk för Java-plattformen som behåller kompatibilitet med Java. Faktiskt, de flesta Java-kod är giltig Groovy kod.

Dags att skriva ett enkelt hello world groovy skript i samband med vår realizeScript. Ange följande i redigeraren:

    node.log("hello world");

Nu ska du köra en lokal testkörning. Granska (via fliken System på komponenten skriptade) egenskapen loggar efter den här körningen.

![Hello world-loggutdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world-loggutdata*

Nodobjektet som vi anropa metoden log, refererar till våra aktuella ”nod” eller en komponent som vi skript i. Alla komponenter har därför möjlighet att utdata loggningsdata, tillgängligt via fliken system. I det här fallet ut vi stränglitteral ”hello world”. Viktigt att förstå här är att det kan visa sig bli en ovärderliga felsökningsverktyget att ge dig information om vad skriptet faktiskt gör.

Från inom vår skriptmiljö har vi också tillgång till egenskaper på andra komponenter. Prova följande:

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

Vår loggfönstret visar följande:

![Loggutdata för att komma åt noden sökvägar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Loggutdata för att komma åt noden sökvägar*

## <a id="frame_based_trim"></a>RAM-baserade trimning MP4 i multibithastighet utdata
Med början från ett arbetsflöde som genererar [en MP4 i multibithastighet utdata från en MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på trimmar källa videon baserat på antalet för ramen.

### <a id="frame_based_trim_start"></a>Skissen översikten om du vill börja lägga till trimning till
![Arbetsflöde för att börja lägga till trimning till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Arbetsflöde för att börja lägga till trimning till*

### <a id="frame_based_trim_clip_list"></a>Med hjälp av listan Clip XML
I alla tidigare arbetsflöde-självstudier använde vi Media filen inkommande komponenten som våra video Indatakällan. För det här scenariot men ska vi använda Clip lista Källkomponenten i stället. Det får inte vara det bästa sättet att arbeta; bara använda Clip källan när det finns en verklig till att göra detta (precis som i följande fall där vi gör clip lista trimning funktioner).

Dra komponenten Clip Listkälla till designytan om du vill växla från våra Media-fil som indata till Clip källan, och Anslut Clip lista XML PIN-koden till Clip lista XML-nod i Arbetsflödesdesignern. Clip källan med utdata PIN-koder, fylls enligt våra indatavideo. Nu ansluta okomprimerade Video och ljud okomprimerade PIN-koder från Clip källan till respektive AVC kodare och ljud Stream Interleaver. Nu ska du ta bort den Media-fil som indata.

![Ersätts den Media-fil som indata med Clip källan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Ersätts den Media-fil som indata med Clip källan*

Clip lista Källkomponenten tar som indata en ”Clip lista XML”. När du väljer den källfil som testar lokalt med fylls den här listan clip-xml i automatiskt åt dig.

![Fylls Clip lista XML-egenskapen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Fylls Clip lista XML-egenskapen*

Titta lite närmare till XML-datatypen, är detta hur det ser ut som:

![Clip lista dialogrutan Redigera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Clip lista dialogrutan Redigera*

Detta men avspeglar inte funktionerna i clip lista xml. Ett alternativ som vi har är att lägga till ett ”Rensa”-element under både video och ljud källan, så här:

![Lägger till ett trim element i listan clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Lägger till ett trim element i listan clip*

Om du ändrar clip lista xml så här ovan och utföra en lokal testkörning, visas videon har korrekt tas bort mellan 10 och 20 sekunder i videon.

Sätt som strider mot vad som händer när du gör en lokal körning dock, har det här samma cliplist xml inte samma effekt när tillämpas i ett arbetsflöde som körs i Azure Media Services. När Azure Premium-kodare startar skapas cliplist XML-filen varje gång igen, baserat på indatafilen kodningsjobbet angavs. Det innebär att alla ändringar som vi gör på XML-filen tyvärr skulle kan åsidosättas.

Om du vill hantera cliplist xml rensas när ett kodningsjobb startas måste återskapa vi det direkt bara efter början av vårt arbetsflöde. Dessa anpassade åtgärder kan utföras via något som kallas en ”skriptbaserade Component”. Mer information finns i [introduktion till komponenten skriptade](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Dra en skriptade komponent till designytan och byta namn till ”SetClipListXML”.

![Att lägga till en skriptbaserade komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Att lägga till en skriptbaserade komponent*

När du granska egenskaperna för komponenten skriptade är de fyra olika skript visas och varje konfigurerbara till ett annat skript.

![Komponentegenskaper för skript](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Komponentegenskaper för skript*

### <a id="frame_based_trim_modify_clip_list"></a>Ändra listan clip från en skriptade komponent
Innan vi kan skriva om cliplist XML-filen som genereras under starten av arbetsflödet, måste du ha åtkomst till cliplist XML-egenskapen och innehåll. Vi kan göra det så här:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Inkommande clip-listan som du loggar in](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Inkommande clip-listan som du loggar in*

Vi måste först ett sätt att bestämma från vilken punkt tills återautentiseras vi vill trimma videon. Om du vill göra detta praktiskt för tekniskt användare av arbetsflödet, publicera två egenskaper till roten i diagrammet. Gör detta genom att högerklicka på designytan och välj ”Lägg till egenskap”:

* Första egenskapen: ”ClippingTimeStart” av typen: ”TIDSKOD”
* Andra egenskapen: ”ClippingTimeEnd” av typen: ”TIDSKOD”

![Lägg till egenskapsdialogrutan för urklippet starttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Lägg till egenskapsdialogrutan för urklippet starttid*

![Publicerade avklippta tid sammanställer i rot-arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicerade avklippta tid sammanställer i rot-arbetsflöde*

Konfigurera båda egenskaperna till ett lämpligt värde:

![Ställa in urklippet start och sluta egenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Ställa in urklippet start och sluta egenskaper*

Nu från våra skript, vi kan komma åt båda egenskaperna så här:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Fönstret som visar början och slutet av urklippet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Fönstret som visar början och slutet av urklippet*

Nu ska vi parsa tidskod strängar till en mer praktiskt att använda i form av ett enkelt reguljära uttryck:

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

![Fönstret med utdata från parsade tidskod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Fönstret med utdata från parsade tidskod*

Med den här vi informationen kan vi nu ändra cliplist XML-filen för att återspegla start- och sluttider för önskad ramen exakta urklippet i filmen.

![Skriptkod att lägga till trim element](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Skriptkod att lägga till trim element*

Detta gjordes via normal manipulering av strängåtgärder. Den resulterande ändrade clip lista xml skrivs tillbaka till egenskapen clipListXML arbetsflöde rot via metoden ”setProperty”. Loggfönstret när en annan testkörning skulle visa oss följande:

![Loggning av den resulterande clip-listan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Loggning av den resulterande clip-listan*

Gör en testkörning om du vill se hur klipps strömmar video och ljud. När du ska göra mer än en testkörning med olika värden för ta bort punkter, ser du att de inte kommer beaktas men! Anledningen är att designern, till skillnad från Azure-körning, inte åsidosätter cliplist xml varje körning. Det innebär att endast första gången du har angett start- och slutpunkter, leder till XML-filen att omvandla, de andra tider vår guard-satsen (om (clipListXML.indexOf (”<trim>”) == -1)) förhindrar att arbetsflödet lägger till ett annat trim element om det finns redan finns en.

Vårt arbetsflöde praktiskt att testa lokalt vi bästa lägger du till kod dagliga rutiner som kontrollerar om ett trim element redan finns. I så fall kan vi ta bort den innan du fortsätter genom att ändra XML-filen med de nya värdena. I stället för vanlig strängändringar, är det förmodligen säkrast att göra detta via objektmodellen för verkliga xml-parsning.

Innan vi kan lägga till sådan kod även om vi måste först lägga till ett antal importuttryck i början av våra skript:

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

Därefter kan vi lägga till nödvändiga rengöringsband koden:

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

Den här koden kommer strax ovanför den punkt där vi lägger du till de trim element cliplist XML-filen.

Vi kan nu köra och ändra vårt arbetsflöde så mycket som vi vill samtidigt som du har de ändringar som tillämpats någonsin tid.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Lägga till en ClippingEnabled bekvämlighet egenskap
Eftersom du inte kanske alltid vill trimma ska inträffa, låt oss avsluta av vårt arbetsflöde genom att lägga till en lämplig boolesk flagga som anger om vi vill att trimma / avklippta.

Som tidigare publicera en ny egenskap i roten på vårt arbetsflöde som kallas ”ClippingEnabled” av typen ”BOOLEAN”.

![Publicerat en egenskap för att aktivera urklippet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicerat en egenskap för att aktivera urklippet*

Med den nedan enkla guard-satsen, kan vi kontrollera om trimning krävs och bestämma om vår clip-lista som sådana ska ändras eller inte.

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

### <a id="code"></a>Fullständiga koden

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
[Vi presenterar Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Hur du använder Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)

[Arbetsflödesformat och codecs för Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exempelfilerna för arbetsflöde](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-verktyget](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
