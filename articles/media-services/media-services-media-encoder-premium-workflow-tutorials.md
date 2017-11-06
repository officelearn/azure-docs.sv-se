---
title: "Avanced Media Encoder Premium arbetsflödet självstudier"
description: "Det här dokumentet innehåller genomgångar som visar hur du utför avancerade uppgifter med Media Encoder Premium arbetsflöde och hur du skapar komplexa arbetsflöden med Arbetsflödesdesignern."
services: media-services
documentationcenter: 
author: xstof
manager: cfowler
editor: 
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 565497bd5a35e3c4d69d29512307cf3ca2364bdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Avancerade Media Encoder Premium arbetsflödet självstudier
## <a name="overview"></a>Översikt
Det här dokumentet innehåller genomgång som visar hur du anpassar arbetsflöden med **Arbetsflödesdesignern**. Du kan hitta de faktiska Arbetsflödesfilerna [här](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>INNEHÅLLSFÖRTECKNING
I följande avsnitt beskrivs:

* [Kodning MXF till en MP4 med enkel bithastighet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Starta ett nytt arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [Med hjälp av Media filen indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Kontrollera dataströmmar media](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Lägger till en videokodare för. Generera filen för MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kodning i ljudström](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Multiplexering ljud och Video strömmar till en MP4-behållare](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Skriver MP4-fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Skapa ett Media Services tillgångsinformation från utdatafilen](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [Testa arbetsflödet slutförts lokalt](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kodning MXF till multibitrate MP4s - dynamisk paketering aktiverad](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Lägga till en eller flera ytterligare MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [Konfigurera filnamnen för utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Lägga till ett separat spår för ljud](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Att lägga till den. ISM SMIL-fil](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kodning MXF till multibitrate MP4 - förbättrad utkast](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Översikt över arbetsflöde för att förbättra](#workflow-overview-to-enhance)
  * [Filen namngivningskonventioner](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Publicera komponentegenskaper till roten för arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Har genererat utdatafilen namn är beroende av publicerade egenskapsvärden](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Om du lägger till miniatyrbilder multibitrate MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Översikt över att lägga till miniatyrbilder till arbetsflöde](#workflow-overview-to-add-thumbnails-to)
  * [Lägga till JPG kodning](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Hantera färg utrymme konvertering](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [Skriva miniatyrerna](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Identifiering av fel i ett arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Klar arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Tidsbaserade trimning multibitrate MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Översikt över arbetsflöde börja lägga till trimning till](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Med hjälp av dataströmmen Trimmer](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Klar arbetsflöde](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [Introduktion till komponenten skriptbaserade](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Skript i ett arbetsflöde: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [RAM-baserade trimning multibitrate MP4-utdata](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Översikt över utkast börja lägga till trimning till](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Med hjälp av listan Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Ändra listan clip från en skript-komponent](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Lägga till en ClippingEnabled bekvämlighet egenskap](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kodning MXF till en MP4 med enkel bithastighet
I den här genomgången ska vi skapa en enkel bithastighet. MP4-fil med AAC HAN kodat ljud från en. MXF indatafilen.

### <a id="MXF_to_MP4_start_new"></a>Starta ett nytt arbetsflöde
Öppna Workflow Designer och väljer ”fil”-”ny arbetsyta”-”Omkoda modell”

Det nya arbetsflödet visar 3 element:

* Primär källfilen
* Beskär List-XML
* Filen/Utdatatillgången  

![Arbetsflöde för ny kodning](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Arbetsflöde för ny kodning*

### <a id="MXF_to_MP4_with_file_input"></a>Med hjälp av Media filen indata
För att kunna acceptera våra inkommande mediefilen, startar med att lägga till en Media filen inkommande komponent. Om du vill lägga till en komponent i arbetsflödet, leta efter den i sökrutan databasen och dra den aktuella posten till fönstret designer. Gör detta för Media filen inkommande och ansluta komponenten primära källfilen till Filename inkommande PIN-koden från Media filen indata.

![Anslutna mediafilen indata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Anslutna mediafilen indata*

Vi behöver först att visa vilka exempelfilen som vi vill använda för att utforma våra arbetsflöde med Arbetsflödesdesignern innan vi kan göra mycket annat. Klicka på bakgrunden i designern rutan gör du genom och leta efter egenskapen primära källfilen i den högra egenskap rutan. Klicka på mappikonen och välj önskad fil att testa arbetsflödet med. När det är klart komponenten Media filen indata granska filen och fylla i dess utdata PIN-koder för att återspegla det kontrolleras filen.

![Ifyllda mediafilen indata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Ifyllda mediafilen indata*

När du anger med vilken indata som ska arbeta med den tala inte ännu där kodade utdata ska gå till. Liknar hur primära källfilen har konfigurerats, nu konfigurera egenskapen utdata mappen variabeln under den.

![Konfigurerade ingående och utgående egenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurerade ingående och utgående egenskaper*

### <a id="MXF_to_MP4_streams"></a>Kontrollera dataströmmar media
Det har ofta vill veta hur dataströmmen ser ut precis som flödar genom arbetsflödet. Om du vill kontrollera en dataström när som helst i arbetsflödet, klicka bara på en utgående eller inkommande PIN-kod på någon av komponenterna. I det här fallet, försök att klicka på PIN-kod för okomprimerade Video-utdata från våra Media filen indata. En dialogruta öppnas som gör för att inspektera utgående videon.

![Kontrollera okomprimerade Video utdata PIN-koden](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Kontrollera okomprimerade Video utdata PIN-koden*

I vårt fall meddelar oss till exempel att vi hantera en 1 920 x 1 080 inmatning med 24 bildrutor per sekund i 4:2:2 provtagning en video om nästan 2 minuter.

### <a id="MXF_to_MP4_file_generation"></a>Lägger till en videokodare för. Generera filen för MP4
Observera att nu, en okomprimerad Video och flera utdata PIN-koder för okomprimerade ljud är tillgängliga för användning på vår Media filen indata. För att koda inkommande videon måste en kodning komponent - i det här fallet för att generera. MP4-filer.

Om du vill koda video-ström till H.264 att lägga till komponenten AVC-videokodare till designytan. Den här komponenten tar en video Expandera-dataström som indata och levererar en AVC komprimerade video ström på dess utdata PIN-kod.

![Ansluten AVC-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Ansluten AVC-kodaren*

Egenskaperna avgör hur kodning exakt händer. Låt oss ta en titt på några av de viktigaste inställningarna:

* Utdata bredd och höjd för utdata: dessa avgör lösning av kodade videon. I vårt fall vi väljer 640 x 360
* Bildfrekvens: Om värdet är passthrough den bara antar bildfrekvens källa, är det möjligt att åsidosätta detta om. Observera att sådana ramhastighet konvertering inte rörelse-ersätts.
* Profil och nivå: dessa avgör AVC-profilen och nivå. Klicka på ikonen frågetecken i komponenten AVC Video Encoder för att lätt få mer information om olika nivåer och profiler, och hjälpsidan ska visa mer information om de olika nivåerna. För exemplet lägger vi väljer Main profil på nivå 3.2 (standard).
* Betygsätt läge och bithastighet (kbps): i vårt scenario vi välja en konstant bithastighet (CBR) som utdata med 1200 kbit/s
* Video Format: Detta är om VUI (Video användbarhet Information) som hämtar skrivs till H.264 dataströmmen (sida information som kanske används av en avkodare att förbättra visning men är inget krav att avkoda korrekt):
* NTSC (typisk för USA eller Japan med 30 fps)
* PAL (typisk för Europa, med 25 bildrutor per sekund)
* GOP storleksläget: vi konfigurerar fast GOP storlek för våra ändamål med ett intervall med nyckel 2 sekunder med stängd GOPs. Detta säkerställer att kompatibilitet med dynamisk paketering Azure Media Services tillhandahåller.

Om du vill feed våra AVC-kodaren du ansluta okomprimerade Video utdata PIN-koden från komponenten Media filen inkommande till okomprimerade Video inkommande PIN-koden från AVC-kodaren.

![Anslutna AVC-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Anslutna AVC Main-kodaren*

### <a id="MXF_to_MP4_audio"></a>Kodning i ljudström
Nu har vi kodad video men den ursprungliga okomprimerade ljudströmmen fortfarande måste komprimeras. För detta ska vi gå AAC kodning av komponenten AAC-kodare (Dolby). Lägga till den i arbetsflödet.

![Ansluten AVC-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Ansluten AAC kodaren*

Nu finns det en inkompatibilitet: det finns bara en enda okomprimerade ljud inkommande PIN-kod från kodaren AAC även mycket sannolikt Media filen indata har två olika okomprimerade ljudström tillgängliga: en för den vänstra ljud kanalen och en till höger. (Om du hantera surroundljud, som är 6 kanaler). Så går det inte att ansluta direkt ljuduppspelningen från Media filen Indatakällan till AAC ljud kodaren. Komponenten AAC förväntar sig en så kallad ”överlagrad” ljudström: en enda ström som har både till vänster och höger kanaler överlagrat med varandra. När vi vet från våra källfil media är vilka ljud spår på vilken position i källan, kan vi generera sådana överlagrad ljudström med talare korrekt tilldelade positioner för vänster och höger.

Först ska en genereras en överlagrad dataström från krävs källa ljud kanaler. Komponenten ljud dataströmmen Interleaver hantera det för oss. Lägga till den i arbetsflödet och ansluter ljud utdata från Media filen indata till den.

![Ansluten ljudström Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Ansluten ljudström Interleaver*

Nu när vi har en överlagrad ljudström vi fortfarande inte har angett var åt vänster eller höger talare platser ska tilldelas. För att ange det kan vi utnyttja talare Position du.

![Lägga till en talare Position du](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Lägga till en talare Position du*

Konfigurera talare Position du för användning med en stereo Indataströmmen genom en kodare förinställda Filter ”anpassad” och kanal förinställningen kallas ”2.0 (L, R)”. (Detta tilldelas vänstra högtalaren möjlighet att kanalen 1 och rätt talare positionen till kanalen 2.)

Resultatet av talare Position du att ansluta till indata för AAC-kodaren. Sedan anger du AAC kodaren att fungera med en ”2.0 (L, R)” kanal förinställningen så att den vet för att hantera stereoljud som indata.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexering ljud och Video strömmar till en MP4-behållare
Beroende på vår AVC kodade video-ström och våra AAC kodade ljudström, vi kan samla in båda till en. MP4-behållare. Processen att blanda olika dataströmmar i en enda kallas ”multiplexering” (eller ”muxing”). I det här fallet vi interfoliering ljud- och videoströmmar i en enda konsekvent. MP4-paketet. Den komponent som samordnar för en. MP4-behållaren kallas ISO MPEG-4-multiplexor. Lägg till ett till designytan och Anslut både AVC Video Encoder och AAC kodaren till indata.

![Anslutna MPEG4 multiplexor](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Anslutna MPEG4 multiplexor*

### <a id="MXF_to_MP4_writing_mp4"></a>Skriver MP4-fil
När du skriver en utdatafil används utdata från komponenten. Vi kan ansluta det till utdataporten för ISO MPEG-4-multiplexor så att dess utdata skrivs till disk. Gör du genom att ansluta behållare (MPEG-4) utdata PIN-koden till skrivåtgärder inkommande PIN-koden för fil-utdata.

![Ansluten utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Ansluten utdata*

Det filnamn som kommer att användas bestäms av egenskapen. Egenskapen kan vara hårdkodad till ett visst värde, vill troligen en anges via ett uttryck i stället.

Att arbetsflödet automatiskt avgöra utdata har filen namnegenskapen från ett uttryck, klickar du på buton bredvid namnet på filen (bredvid mappikonen). I nedrullningsbara menyn väljer du sedan ”uttryck”. Appskärmen redigerare för uttryck. Radera innehållet i redigeraren först.

![Tom uttrycksredigerare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Tom uttrycksredigerare*

Uttrycksredigeraren för kan ange eventuella litteralvärde blandas med en eller flera variabler. Variabler börja med ett dollartecken. Eftersom du träffar nyckeln $ visas redigeraren listrutan med ett urval av tillgängliga variabler. I vårt fall använder vi en kombination av utdata directory variabel och variabeln grundläggande indatafilen namn:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Fylls ut uttrycksredigerare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Fylls ut uttrycksredigerare*

> [!NOTE]
> För att kunna se finns en utdatafil från kodning jobbet i Azure måste du ange ett värde i uttrycksredigeraren för.
>
>

När du bekräftar uttrycket genom att träffa ok kommer fönstret egenskapen Förhandsgranska vad värdet filen egenskapen matchar vid denna tidpunkt.

![Fil-uttrycket matchar utdata dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fil-uttrycket matchar utdata dir*

### <a id="MXF_to_MP4_asset_from_output"></a>Skapa ett Media Services tillgångsinformation från utdatafilen
När vi har skapat en MP4-utdatafil behöver vi ange att den här filen tillhör utdatatillgången som media services genererar ett resultat av körning av arbetsflödet. Noden fil/Utdatatillgången på arbetsytan arbetsflöde används för detta ändamål. Alla inkommande filer till den här noden gör en del av denna Azure Media Services tillgång.

Ansluta komponenten utdata till filen/Utdatatillgången komponenten att slutföra arbetsflödet.

![Klar arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Klar arbetsflöde*

### <a id="MXF_to_MP4_test"></a>Testa arbetsflödet slutförts lokalt
Om du vill testa arbetsflödet lokalt, trycka på knappen Spela upp i verktygsfältet högst upp. Granska utdata som genereras i den konfigurerade Utdatamappen när arbetsflödet slutförts körs. Klar MP4 utdatafilen som har kodats från filen MXF Indatakällan visas.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kodning MXF till MP4 - multibitrate dynamisk paketering aktiverad
I den här genomgången skapar vi en uppsättning flera MP4-filer med AAC kodade ljud från en enda. MXF indatafilen.

När en multibithastighet tillgången utdata önskas för användning i kombination med dynamisk paketering funktioner som erbjuds av Azure Media Services hanterar flera GOP-justerad MP4-filer för var och en annan bithastighet och upplösning måste skapas. Gör den [kodning MXF i en enkel bithastighet MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) genomgången ger oss en bra utgångspunkt.

![Starta arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Starta arbetsflöde*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Lägga till en eller flera ytterligare MP4-utdata
Alla MP4-filer i vårt Azure Media Services tillgång stöder en annan bithastighet och upplösning. Lägg till en eller flera MP4-utdatafilerna till arbetsflödet.

Om du vill se till att vi har vår video kodare som skapats med samma inställningar, är det mest praktiskt att kopiera den befintliga AVC-videokodare och konfigurera en annan kombination av upplösning och bithastighet (Lägg till en 960 x 540 med 25 bildrutor per sekund med 2,5 Mbit/s). Om du vill duplicera befintliga kodaren klistra kopiera in den på designytan.

Ansluta okomprimerade Video utdata PIN-koden för Media filen indata till vår nya AVC-komponent.

![Andra AVC-kodaren som är ansluten](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Andra AVC-kodaren som är ansluten*

Nu anpassa konfigurationen för vår nya AVC-kodaren till utdata 960 x 540 med 2,5 Mbit/s. (Använd egenskaper ”utdata bredd”, ”utdata höjd” och ”bithastighet (kbps)” för detta.)

Får vi vill använda den resulterande tillgången tillsammans med Azure Media Services dynamisk paketering, strömmande slutpunkten behöver för att kunna generera från dessa MP4-filer HLS/fragmenterad MP4/DASH fragment som exakt justeras till varandra på ett sätt att klienter som byter mellan olika bithastighet får en enda smidig kontinuerlig video och ljud erfarenhet. Om du vill aktivera den här funktionen behöver vi säkerställa att, storlek för båda MP4-filer har angetts i egenskaperna för både AVC-kodare GOP (”grupp av bilder”) till 2 sekunder, vilket kan göra genom:

* Ange storleksläget GOP till fast GOP storlek och
* Nyckeln ram intervallet till två sekunderna.
* du också ange GOP IDR på stängd GOP så alla GOP står på sina egna utan beroenden

Om du vill göra våra arbetsflöde praktiskt att förstå att byta namn på första AVC-kodaren till ”AVC Video Encoder 640 x 360 1200 kbit/s” och andra AVC-kodaren ”AVC Video Encoder 960 x 540 2500 kbit/s”.

Nu ska du lägga till en andra ISO MPEG-4-multiplexor och en andra fil-utdata. Anslut den multiplexor till den nya AVC-kodaren och kontrollera att dess utdata dirigeras till filen utdata. Sedan också ansluta AAC ljud kodare utdata till den nya multiplexor indata. Filen utdata kan i sin tur sedan anslutas till noden fil/Utdatatillgången att lägga till Media Services tillgången som kommer att skapas.

![Andra Muxer och utdata från anslutna](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Andra Muxer och utdata från anslutna*

Konfigurera den multiplexor segmentet läge GOP antal eller varaktighet för kompatibilitet med Azure Media Services dynamisk paketering och ange GOPs per segment till 1. (Detta ska vara standard.)

![Muxer segment lägen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer segment lägen*

Obs: du kanske vill upprepa processen för alla andra bithastighet upplösning kombinationer och du vill ska läggas till tillgången utdata.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Konfigurera filnamnen för utdata
Vi har mer än en enskild fil som läggs till för utdatatillgången. Detta ger behöver kontrollera filnamnen för varje utdatafilerna skiljer sig från varandra och kanske även tillämpa ett filnamn så att det blir tydligt filnamnet vad du arbetar med.

Utdata filnamngivning kan styras via uttryck i designern. Öppna fönstret egenskap för en av komponenterna utdata och uttrycksredigerare för egenskapen. Vårt första utdatafilen konfigurerades via följande uttryck (Se Självstudierna för att gå från [MXF för en enda bithastighet MP4 utdata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Det innebär att vår filename bestäms av två variabler: den angivna katalogen för att skriva i och källfilens grundläggande namn. Den tidigare visas som en egenskap i roten för arbetsflödet och bestäms av den importerade filen. Observera att den angivna katalogen är det du använder för att testa lokala; den här egenskapen åsidosätts av arbetsflödesmotorn när arbetsflödet körs av processor för molnbaserade media i Azure Media Services.
Ändra den första filen namngivning av uttryck som ger både våra utdatafilerna namngivning av enhetligt genom:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

och andra för:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Köra ett mellanliggande test som körs för att kontrollera att båda utdata MP4-filer skapas korrekt.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Lägga till ett separat spår för ljud
Som vi ser senare när vi skapar en .ism-fil att gå med vår MP4-filer för utdata visar kräver vi också en ljuddata MP4-fil som ljud spår för våra anpassningsbar strömning. Skapa den här filen, lägga till en ytterligare muxer till arbetsflödet (ISO-MPEG-4 multiplexor) och ansluta kodaren AAC utdata PIN-kod med dess inkommande PIN-kod för spår 1.

![Ljud Muxer som lagts till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Ljud Muxer som lagts till*

Skapa en tredje utdata från komponenten utdata från muxer utgående dataströmmen och konfigurera filnamn uttryck som:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Ljud Muxer skapa filen utdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Ljud Muxer skapa filen utdata*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Att lägga till den. ISM SMIL-fil
För dynamisk paketering ska fungera i kombination med både MP4-filer (och ljuddata MP4) i vår Media Services tillgången, men vi behöver också en manifestfil (kallas även en ”SMIL”-fil: synkroniserade Multimedia Integration Language). Den här filen anger Azure Media Services vilka MP4-filer är tillgängliga för dynamisk paketering och vilka av dessa väga in för ljud strömning. En typisk manifestfil för en uppsättning MP4's med en enda ljudström ser ut så här:

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

.Ism-fil innehåller inom en referens till var och en av de enskilda MP4-filerna och utöver dessa också (minst) ljudfil referenser till en MP4 som endast innehåller ljuduppspelningen en switch-sats.

Generering av manifestfil vår uppsättning MP4's kan göras via en komponent som kallas ”AMS Manifest Writer”. För att använda den drar den till ytan och ansluter ”skriva slutförd” utdata PIN-koder från tre utdata komponenter till AMS Manifest Writer indata. Kontrollera att ansluta till filen/Utdatatillgången för utdata från AMS Manifest-skrivaren.

Precis som med våra andra filen utdata komponenter, konfigurerar du .ism utdata filnamnet med ett uttryck:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Vår klar arbetsflöde ser ut som den nedan:

![Klar MXF multibitrate MP4 arbetsflödet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Klar MXF multibitrate MP4 arbetsflödet*

## <a id="MXF_to__multibitrate_MP4"></a>Kodning MXF till multibitrate MP4 - förbättrad utkast
I den [tidigare arbetsflöde genomgången](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) vi har sett hur en enda MXF inkommande tillgång kan konverteras till en utdatatillgången med flera bithastigheter MP4-filer, en ljuddata MP4-fil och en manifestfil för användning tillsammans med Azure Media Services dynamisk paketering.

Den här genomgången visar hur vissa aspekter kan förbättras och görs enklare.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Översikt över arbetsflöde för att förbättra
![Multibitrate MP4-arbetsflöde för att förbättra](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4-arbetsflöde för att förbättra*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>Filen namngivningskonventioner
I tidigare arbetsflödet angetts vi ett enkelt uttryck som grund för att generera utdata-filnamn. Vi har några duplicering men: alla de enskilda utdata filen komponenter angivna sådana uttrycket.

Till exempel har våra filen utdata-komponenten för den första videofilen konfigurerats med det här uttrycket:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Medan för andra utdata video, har vi ett uttryck som:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Skulle det vara tydligare mindre fel felbenägna och bekvämare om vi kan ta bort några av duplicering och göra det mer konfigureras i stället? Som tur kan vi: i designer uttryck funktioner i kombination med möjlighet att skapa anpassade egenskaper på vår arbetsflödet rot ger oss ett extra lager av bekvämlighet.

Antar vi att vi ska hårddiskkonfiguration filename från bithastighet enskilda MP4-filer. Dessa bithastighet kommer ni att konfigurera på en central plats (i roten på vår diagram), där de kommer åt för att konfigurera och filnamnet för enheten. Detta gör börja vi med att publicera egenskapen bithastighet från båda AVC-kodare till roten i vår arbetsflödet, så att den blir tillgänglig från båda roten samt från och med AVC-kodare. (Även om visas i två olika platser, det är endast ett underliggande värde.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>Publicera komponentegenskaper till roten för arbetsflöde
Öppna första AVC-kodaren, gå till egenskapen bithastighet (kbps) och välj Publicera i listrutan.

![Publicering av egenskapen bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Publicering av egenskapen bithastighet*

Konfigurera dialogrutan Publicera om du vill publicera till roten i vår arbetsflöde diagram med en publicerade namnet ”video1bitrate” och en läsbar visningsnamn ”Video 1 bithastighet”. Konfigurera ett eget gruppnamn kallas ”strömning bithastighet” och klicka på Publicera.

![Publicering av egenskapen bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Publishing dialogrutan för egenskapen bithastighet*

Upprepa samma för egenskapen bithastighet för andra AVC-kodaren och ge den namnet ”video2bitrate” med ”Video 2 bithastighet” visa namnet i samma anpassade grupp ”strömning bithastighet”.

Om vi nu granska egenskaperna för arbetsflödet rot, se vi vår anpassad grupp med två publicerade egenskaper visas. Både reflektion värdet för deras respektive AVC-kodaren bithastighet.

![Egenskaper för publicerade bithastighet i rot-arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

När vi vill komma åt dessa egenskaper från kod eller ett uttryck kan vi göra det så här:

* från infogad kod från en komponent under roten: node.getPropertyAsString('../ video1bitrate', null)
* i ett uttryck: ${ROOT_video1bitrate}

Vi Slutför ”strömning bithastighet”-gruppen genom att publicera våra ljud spåra bithastighet på den också. Sök efter inställningen bithastighet i egenskaperna för AAC kodaren och välj Publicera i listrutan bredvid den. Publicera till roten i diagrammet med namnet ”audio1bitrate” och visningsnamnet ”ljud 1 bithastighet” i vårt anpassade grupp ”strömning bithastighet”.

![Publishing dialogrutan för ljud bithastighet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Publishing dialogrutan för ljud bithastighet*

![Resulterande video och ljud sammanställer på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Resulterande video och ljud sammanställer på rot*

Observera att ändra någon av dessa tre värden också konfigurerar igen och ändrar värdena på respektive komponenter som de är kopplade till (och där publicerade från).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Har genererat utdatafilen namn är beroende av publicerade egenskapsvärden
I stället för hardcoding våra genererade filnamn, vi kan nu ändra våra filename-uttrycket på varje fil utdata-komponenter kan förlita sig på egenskaperna bithastighet vi just publicerad på graph-roten. Med vår utdata från första början för att hitta egenskapen i och redigera uttrycket så här:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Olika parametrar i det här uttrycket kan kommas åt och anges genom att träffa dollartecken på tangentbordet i uttrycksfönstret. En av de tillgängliga parametrarna är vår video1bitrate-egenskap som vi tidigare publicerade.

![Åtkomst till parametrar i ett uttryck](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Åtkomst till parametrar i ett uttryck*

Gör samma sak för filen utdata för våra andra video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

och ljuddata filen utdata för:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Om vi nu ändra bithastighet för någon av filerna video eller ljud respektive kodaren ska konfigureras och konventionen bithastighet-baserat namn att användas automatiskt.

## <a id="thumbnails_to__multibitrate_MP4"></a>Om du lägger till miniatyrbilder multibitrate MP4-utdata
Från ett arbetsflöde som genererar [en multibitrate MP4-utdata från ett MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på när du lägger till miniatyrbilder utdata.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Översikt över att lägga till miniatyrbilder till arbetsflöde
![Multibitrate MP4-arbetsflöde för att starta från](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Multibitrate MP4-arbetsflöde för att starta från*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Lägga till JPG kodning
Hjärtat i vår miniatyr generation kommer att komponenten JPG kodare kunna utdata JPG-filer.

![JPG-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-kodaren*

Vi kan inte men direkt ansluta våra okomprimerade Video-ström från Media filen indata till JPG-kodaren. Det förväntas att distribuera enskilda ramar. Detta kan vi kan göra via Video ram Gate-komponent.

![Ansluter en ram gate till JPG-kodaren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Ansluter en ram gate till JPG-kodaren*

RAM-gate var så många: e sekund eller ramar kan en bildruta att skicka. Intervallet och tidsförskjutningen med det här inträffar kan konfigureras i egenskaperna.

![Video ram Gate-egenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Video ram Gate-egenskaper*

Nu ska vi skapa en miniatyr i minuten genom att ange läget (sekunder) och intervall och 60.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Hantera färg utrymme konvertering
Medan verkar det logiskt båda okomprimerade Video PIN-koder för ram-gate och Media filen indata kan nu vara ansluten, skulle vi får en varning om vi skulle göra.

![Indatafärgen utrymme fel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Indatafärgen utrymme fel*

Det beror på sätt som i vilken färg information visas i vår ursprungliga rådata okomprimerade video-ström, kommer från våra MXF skiljer sig från vad JPG-kodaren förväntar sig. Mer specifikt förväntas en så kallad ”färg utrymme” ”RGB” eller ”gråskala” på. Det innebär att den Video ram Gate inkommande video-ström måste ha en konvertering som tillämpas om dess färg utrymme först.

Dra till arbetsflödet färg utrymme konverteraren - Intel och ansluta till vår ram gate.

![Ansluta en färg utrymme konverteraren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Ansluta en färg utrymme konverteraren*

Välj posten BGR 24 förinställda listan i egenskapsfönstret.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Skriva miniatyrerna
Skiljer sig från våra MP4-video, JPG kodare komponenten kommer att skrivas ut mer än en fil. För att hantera detta, en komponent som scen Sök JPG-fil skrivaren kan användas: det tar inkommande JPG-miniatyrbilder och skriva ut dem varje filnamn som suffixet av ett annat nummer. (Det tal som anger antalet sekunder/enheter i strömmen som miniatyren ritades från vanligtvis.)

![Introduktion till skrivaren scen Sök JPG-fil](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduktion till skrivaren scen Sök JPG-fil*

Konfigurera utdata mappsökväg med uttryck: ${ROOT_outputWriteDirectory}

och egenskapen Filename Prefix med:

    ${ROOT_sourceFileBaseName}_thumb_

Prefixet avgör hur miniatyrer filer namnges. De kommer suffixet med ett tal som anger den USB position i dataströmmen.

![Egenskaper för scen Sök JPG-fil Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Egenskaper för scen Sök JPG-fil Writer*

Anslut skrivaren scen Sök JPG-fil till noden fil/Utdatatillgången.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Identifiering av fel i ett arbetsflöde
Ansluta indata för färg utrymme konverteraren till rå okomprimerade videoutdata. Nu utföra lokala testa för arbetsflödet. Det är troligt arbetsflödet plötsligt kan avbrytas och ange med en röd ram på den komponent som ett fel uppstod:

![Färg utrymme konverteraren fel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Färg utrymme konverteraren fel*

Klicka på lite red ”E”-ikonen i övre högra hörnet av komponenten färg utrymme konverterare och se vad orsaken kodning försöket misslyckades.

![Färg utrymme konverteraren feldialogrutan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Färg utrymme konverteraren feldialogrutan*

Det visar sig, som du kan se att det inkommande färg utrymmet som är standard för färg utrymme konverteraren måste vara rec601 för våra begärda konvertering av YUV till RGB. Vår dataströmmen anger uppenbarligen inte dess rec601. (Rek 601 är en standard för kodning sammanflätade analoga video signaler i digital video form. Det anger en aktiva regionen som täcker 720 ljusstyrkan exemplen och 360 krominans prover per rad. Färgen kodning system kallas YCbCr 4:2:2.)

För att åtgärda detta ska vi ange på metadata för våra dataström som vi behandlar rec601 innehåll. Gör vi använder en Video Data typen Updater-komponent som vi ska placera between våra rådata käll- och komponenten färg utrymme konvertering. Denna uppdateringsfil för typ av data kan manuell uppdatering av vissa video data Typegenskaper. Konfigurera den för att ange en färg utrymme Standard för ”Rec 601”. Detta innebär att videon Data typen Updater att tagga dataströmmen med ”Rec 601” färg utrymme om det fanns ingen färg utrymme har definierats ännu. (Inte åsidosätter eventuella befintliga metadata om kryssrutan åsidosättning checkades.)

![Uppdatera färg utrymme Standard på typen uppdateringsfilen Data](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Uppdatera färg utrymme Standard på typen uppdateringsfilen Data*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Klar arbetsflöde
Nu när våra våra arbetsflödet har slutförts, gör ett annat test kör för att se den skicka.

![Klar arbetsflöde för flera mp4 utdata med miniatyrbilder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Klar arbetsflöde för flera mp4 utdata med miniatyrbilder*

## <a id="time_based_trim"></a>Tidsbaserade trimning multibitrate MP4-utdata
Från ett arbetsflöde som genererar [en multibitrate MP4-utdata från ett MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på trimning källa videon baserat på tidsstämplar.

### <a id="time_based_trim_start"></a>Översikt över arbetsflöde börja lägga till trimning till
![Starta arbetsflöde för att lägga till trimning till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Starta arbetsflöde för att lägga till trimning till*

### <a id="time_based_trim_use_stream_trimmer"></a>Med hjälp av dataströmmen Trimmer
Komponenten dataströmmen Trimmer kan rensa början och slutet av en indataström som baseras på tidsuppgifterna information (sekunder, minuter,...). Kanstskäraren stöder inte ram-baserade trimning.

![Dataströmmen Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Dataströmmen Trimmer*

I stället för att länka AVC kodare och talare position du Media filen inkommande direkt kan ska vi placera mellan de dataströmmen trimmer. (En för video signalen och en för överlagrad ljud signalen.)

![Placera dataströmmen Trimmer mellan](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Placera dataströmmen Trimmer mellan*

Nu ska vi konfigurera kanstskäraren så att vi endast bearbeta video och ljud mellan 15 sekunder och 60 sekunder i videon.

Gå till egenskaperna för Video dataströmmen Trimmer och konfigurera både starttid (15 sek) och sluttid (60 s)-egenskaper. Kontrollera att både våra ljud och video trimmer alltid är konfigurerade att samma start- och slutvärden genom publiceras dem till roten i arbetsflödet.

![Publicera start tid egenskap från dataströmmen Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Publicera start tid egenskap från dataströmmen Trimmer*

![Publicera egenskapsdialogrutan för starttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Publicera egenskapsdialogrutan för starttid*

![Publicera egenskapsdialogrutan för sluttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Publicera egenskapsdialogrutan för sluttid*

Om vi nu granska roten för våra arbetsflödet, blir snyggt visas och konfigureras i båda egenskaperna därifrån.

![Publicerade egenskaper som är tillgängliga på rot](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Publicerade egenskaper som är tillgängliga på rot*

Öppna egenskaperna trimning från ljud trimmer och konfigurera både start- och sluttider med ett uttryck som refererar till de publicerade egenskaperna i roten på vår arbetsflöde.

För ljud trimning starttid:

    ${ROOT_TrimmingStartTime}

och för dess Sluttid:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Klar arbetsflöde
![Klar arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Klar arbetsflöde*

## <a id="scripting"></a>Introduktion till komponenten skriptbaserade
Skriptbaserade komponenter kan köra godtycklig skript under körningen faser i vår arbetsflöde. Det finns fyra olika skript som kan utföras med specifika egenskaper och deras egen plats i arbetsflödet livscykel:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

I dokumentationen för komponenten skripta går i detalj för varje ovan. I [i följande avsnitt](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), **realizeScript** scripting komponent används för att skapa en cliplist xml direkt när arbetsflödet startar. Det här skriptet anropas under installationen av komponenten, som görs bara en gång i dess livscykel.

### <a id="scripting_hello_world"></a>Skript i ett arbetsflöde: hello world
Dra en skripta komponent till designytan och byta namn på den (till exempel ”SetClipListXML”).

![Lägga till en skriptbaserade komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptbaserade komponent*

När du kontrollerar egenskaperna för komponenten skripta kommer de fyra typerna av olika skript att visas, varje konfigureras för att ett annat skript.

![Egenskaperna för komponenten för skript](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaperna för komponenten för skript*

Avmarkera processInputScript och öppna Redigeraren för realizeScript. Nu ska vi har skapat och redo att börja skript.

Skript är skrivna i Groovy, ett dynamiskt kompilerade skriptspråk för Java platform som behåller kompatibilitet med Java. De flesta Java-kod är faktiskt, giltig Groovy kod.

Dags att skriva ett enkelt hello world groovy skript i samband med vår realizeScript. Ange följande i redigeraren:

    node.log("hello world");

Nu ska du köra en lokal testkörning. När du kör, kontrollera (via fliken System komponenten skripta) egenskapen loggar.

![Hello world loggutdata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world loggutdata*

Nodobjektet som vi anropa metoden loggen, refererar till vår aktuella ”noden” eller komponenten vi skript i. Alla komponenter har som möjlighet att loggning utdata, tillgängliga via fliken system. I det här fallet utdata vi stränglitteral ”hello world”. Viktigt att förstå här är att det kan bevisa att ett ovärderligt verktyg för felsökning, vilket ger dig information om vilka skriptet faktiskt göra.

Från inom vårt skriptmiljö har vi också åtkomst till egenskaper på andra komponenter. Prova följande:

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

Vår visas i loggfönstret vi följande:

![Utdata för att komma åt noden sökvägar](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Utdata för att komma åt noden sökvägar*

## <a id="frame_based_trim"></a>RAM-baserade trimning multibitrate MP4-utdata
Från ett arbetsflöde som genererar [en multibitrate MP4-utdata från ett MXF indata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), vi kommer nu att titta på trimning källa videon baserat på ramen.

### <a id="frame_based_trim_start"></a>Översikt över utkast börja lägga till trimning till
![Arbetsflöde för att börja lägga till trimning till](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Arbetsflöde för att börja lägga till trimning till*

### <a id="frame_based_trim_clip_list"></a>Med hjälp av listan Clip XML
I alla tidigare arbetsflöde självstudier använde vi Media filen inkommande komponenten som våra video Indatakällan. För dessa specifika fall men ska vi använda komponenten Clip datakälla i stället. Observera att detta inte är det bästa sättet att arbeta; bara använda Clip källan när det finns en verklig anledning att göra det (precis som i den nedan fall där vi gör användningen av klipp listan trimning funktioner).

Om du vill växla från våra Media filen indata till Clip källan dra komponenten Clip datakälla till designytan och Anslut Clip listan XML PIN-koden till Clip listan XML-noden i Arbetsflödesdesignern. Detta ska fylla Clip källan med utdata PIN-koder, enligt våra videoinmatning. Ansluta nu de okomprimerade Video- och okomprimerade PIN-koderna från den Clip källan till respektive AVC kodare och ljud dataströmmen Interleaver. Nu ska du ta bort filen indata Media.

![Ersätts Clip källan Media filen indata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Ersätts Clip källan Media filen indata*

Datakälla för Clip komponenten tar som indata en ”Clip listan XML”. När du väljer att testa med lokalt källfilen, är den här clip listan xml fylls automatiskt åt dig.

![Automatisk fyllts Clip listan XML-egenskapen](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatisk fyllts Clip listan XML-egenskapen*

Titta närmare lite xml, är detta hur det ser ut:

![Clip Listdialogrutan redigera](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Clip Listdialogrutan redigera*

Detta men avspeglar inte funktionerna i clip listan xml. Vi har kan du lägga till en ”Rensa” element under både video och ljud källan, så här:

![Lägger till ett trim element i listan clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Lägger till ett trim element i listan clip*

Om du ändrar clip listan xml så här ovan och utföra lokala testa videon visas korrekt tagits bort mellan 10 och 20 sekunder i videon.

Den här samma cliplist xml strider mot vad som händer när du gör en lokal körning om, skulle inte ha samma effekt när det används i ett arbetsflöde som körs i Azure Media Services. När Azure Premium-kodare startar genereras cliplist xml varje gång igen, baserat på indatafilen kodningsjobbet har angetts. Det innebär att alla ändringar som vi göra på xml tyvärr skulle åsidosättas.

För att hantera cliplist XML-filen som ska rensas när ett kodningsjobb startas måste kan vi generera den direkt precis efter start av våra arbetsflöde. Dessa anpassade åtgärder kan hämtas via något som kallas en ”Skriptfönster Component”. Mer information finns i [introduktion till komponenten skripta](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Dra en skripta komponent till designytan och Byt till ”SetClipListXML”.

![Lägga till en skriptbaserade komponent](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Lägga till en skriptbaserade komponent*

När du kontrollerar egenskaperna för komponenten skripta kommer de fyra typerna av olika skript att visas, varje konfigureras för att ett annat skript.

![Egenskaperna för komponenten för skript](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Egenskaperna för komponenten för skript*

### <a id="frame_based_trim_modify_clip_list"></a>Ändra listan clip från en skript-komponent
Innan vi kan Skriv cliplist XML-filen som skapas under start av arbetsflödet, måste du ha tillgång till cliplist XML-egenskapen och innehåll. Vi kan göra det så här:

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Inkommande clip lista som loggas](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Inkommande clip lista som loggas*

Vi måste först ett sätt att bestämma från vilken plats till vilken plats som vi vill trim videon. För att göra det enkelt för tekniskt användaren av arbetsflödet, publicera två egenskaper till roten i diagrammet. Gör detta genom att högerklicka på designytan och välj ”Lägg till egenskap”:

* Första egenskapen: ”ClippingTimeStart” av typen: ”TIDSKOD”
* Andra egenskapen: ”ClippingTimeEnd” av typen: ”TIDSKOD”

![Lägg till dialogrutan för egenskaper för urklippet starttid](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Lägg till dialogrutan för egenskaper för urklippet starttid*

![Publicerade urklippet tid sammanställer i rot-arbetsflöde](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Publicerade urklippet tid sammanställer i rot-arbetsflöde*

Konfigurera båda egenskaperna till ett lämpligt värde:

![Konfigurera urklippet start och sluta egenskaper](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurera urklippet start och sluta egenskaper*

Nu från i vår skript vi kan komma åt båda egenskaperna så här:

    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Log-fönster med början och slutet av Urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Log-fönster med början och slutet av Urklipp*

Vi parsa tidskod strängar till en mer praktiskt att använda i form av ett enkelt reguljära uttryck:

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

![Fönstret med utdata från parsade tidskod](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Fönstret med utdata från parsade tidskod*

Med den här vi information ändra vi nu cliplist xml för att återspegla start- och sluttider för önskad ram korrekt urklippet av detta.

![Skriptkod att lägga till trim element](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Skriptkod att lägga till trim element*

Detta gjordes via normala sträng manipulering åtgärder. Den resulterande ändrade clip listan xml skrivs tillbaka till egenskapen clipListXML i arbetsflödet roten via metoden ”setProperty”. Loggfönstret när du kör ett annat test skulle visar följande:

![Loggning listan clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Loggning listan clip*

Gör en testkörning om du vill se hur klipps strömmar ljud och video. När du ska göra mer än en testkörning med olika värden för trimning punkter, Lägg märke till att de inte kommer beaktas men! Anledningen är att designer, till skillnad från Azure körning, inte åsidosätter cliplist xml var kör. Detta innebär att endast den första gången du har angett start- och slutpunkter, kommer xml för att omvandla, alla andra tider, vår guard-satsen (om (clipListXML.indexOf (”<trim>”) == -1)) förhindrar att arbetsflödet lägger till en annan trim elementet när det redan finns en.

Vår arbetsflödet bra att testa lokalt vi bästa lägger du till kod dagliga rutiner som kontrollerar om en trim elementet fanns redan. I så fall, kan vi ta bort det innan du fortsätter genom att ändra XML-filen med de nya värdena. I stället för vanliga strängändringar, är det förmodligen säkrare att göra detta via objektmodellen för verkliga XML-parsning.

Innan vi kan lägga till sådan kod om du behöver lägga till ett antal importuttryck i början av våra skriptet först:

    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

Sedan kan vi lägga till nödvändiga rengöringsband koden:

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

Den här koden går ovanför den punkt där vi lägga till trim elementen till cliplist xml.

Vi kan nu köra och ändra våra arbetsflödet så mycket som vi vill medan ändringarna tillämpas någonsin har tid.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Lägga till en ClippingEnabled bekvämlighet egenskap
Du kanske inte alltid vill reducera att inträffa, vi slutför av våra arbetsflöde genom att lägga till en lämplig boolesk flagga som anger om vi vill att reducera / urklippet.

Publicera en ny egenskap till roten i vår arbetsflöde kallas ”ClippingEnabled” typ ”boolesk” före, precis som.

![Publicerade en egenskap för att aktivera Urklipp](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Publicerade en egenskap för att aktivera Urklipp*

Med den nedan enkla guard satsen vi kontrollera om trimning krävs och bestämma om vår clip lista som behöver ändras eller inte.

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }


### <a id="code"></a>Fullständiga koden
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


## <a name="also-see"></a>Se även
[Introduktion till Premium-kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Hur du använder Premium kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Koda innehåll på begäran med Azure Media Service](media-services-encode-asset.md#media-encoder-premium-workflow)

[Arbetsflödesformat och codecs för Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exempelfiler för arbetsflöde](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer-verktyget](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
