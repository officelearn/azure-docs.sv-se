---
title: "Live med lokala kodare som skapar dataströmmar i multibithastighet - Azure strömmen | Microsoft Docs"
description: "Det här avsnittet beskriver hur du ställer in en kanal som tar emot en direktsänd dataström med multibithastighet från en lokala kodare. Dataströmmen kan sedan levereras till klienten uppspelning program via en eller flera strömningsslutpunkter, med någon av följande protokoll för anpassningsbar strömning: HLS, Smooth Streaming STRECK."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/12/2017
ms.author: cenkd;juliako
ms.openlocfilehash: 3f6569d32708c42247e0ffec70389f2e0f07389e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="live-streaming-with-on-premises-encoders-that-create-multi-bitrate-streams"></a>Direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet
## <a name="overview"></a>Översikt
I Azure Media Services en *kanal* representerar en pipeline för bearbetning av live-streaming innehåll. En kanal som tar emot live indata i ett av två sätt:

* En lokal livekodare skickar en multibithastighet RTMP eller Smooth Streaming (fragmenterad MP4) strömma till den kanal som inte är aktiverad för att utföra live encoding med Media Services. De infogade strömmarna passerar genom kanalerna utan vidare bearbetning. Den här metoden anropas *direkt*. Du kan använda följande livekodare som har flera bithastigheter Smooth Streaming som utdata: Media Excel, Ateme, anta kommunikation, Envivio, Cisco och Elemental. Följande livekodare har RTMP som utdata: Adobe Flash Live mediekodare, Telestream Wirecast, Haivision, Teradek och TriCaster. En livekodare kan även skicka en dataström med enkel bithastighet till en kanal som inte är aktiverad för live encoding, men vi rekommenderar inte som. Media Services ger strömmen till kunder som begär den.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveströmning.


* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTP (MPEG-TS) RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan live encoding av den inkommande dataströmmen i enkel bithastighet till en flera bithastigheter (anpassningsbar) video-ström. Media Services ger strömmen till kunder som begär den.

Från och med Media Services 2.10-versionen när du skapar en kanal kan ange du hur du vill att din kanal tar emot Indataströmmen. Du kan också ange om du vill att utföra live encoding av strömmen kanalen. Du kan välja mellan två alternativ:

* **Gå igenom**: Ange det här värdet om du planerar att använda en lokal livekodare som ska ha en dataström med multibithastighet (en direkt dataström) som utdata. I det här fallet passerar den inkommande dataströmmen genom att utdata utan kodning. Det här är beteendet för en kanal innan 2,10. Det här avsnittet ger information om att arbeta med kanaler av den här typen.
* **Live kodning**: Välj det här värdet om du planerar att använda Media Services för att koda en direktsänd dataström med enkel bithastighet till en dataström med multibithastighet. Tänk som lämnar en direktsänd kodning kanaler i en **kör** tillstånd kommer avgifter faktureringsinformation. Vi rekommenderar att du stoppar du omedelbart dina kanaler som körs när din live-streaming-händelse är klar att undvika extra timvis avgifter. Media Services ger strömmen till kunder som begär den.

> [!NOTE]
> Det här avsnittet beskrivs attributen för kanaler som inte är aktiverade för att utföra live encoding. Information om att arbeta med kanaler som är aktiverade för att utföra live encoding finns [direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).
>
>

Följande diagram representerar en live-streaming-arbetsflöde som använder en lokal livekodare för att ha flera bithastigheter RTMP eller fragmenterad MP4 (Smooth Streaming) dataströmmar som utdata.

![Live-arbetsflöde][live-overview]

## <a id="scenario"></a>Vanligt scenario för live-direktuppspelning
Följande steg beskriver uppgifter som ingår i att skapa vanliga live-streaming-program.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som har en multibithastighet RTMP eller fragmenterad MP4 (Smooth Streaming) dataströmmen som utdata. Mer information finns i [Support och livekodare för Azure Media Services RTMP](http://go.microsoft.com/fwlink/?LinkId=532824).

    Du kan också utföra det här steget när du har skapat din kanal.
2. Skapa och starta en kanal.

3. Hämta kanalens infognings-URL.

    Live Encoding använder infognings-URL: en för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program.

    Det skapas även en tillgång till när du använder Azure-portalen för att skapa ett program.

    När du använder .NET SDK eller REST, måste du skapa en tillgång och ange om du vill använda den här tillgången när du skapar ett program.
6. Publicera tillgången som är kopplad till programmet.   

    >[!NOTE]
    >När Azure Media Services-konto skapas en **standard** strömningsslutpunkt har lagts till i ditt konto i den **stoppad** tillstånd. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

7. Starta programmet när du är redo att påbörja strömning och arkivering.

8. Som alternativ kan livekodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.

9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

10. Ta bort programmet (och ta eventuellt bort tillgången).     

## <a id="channel"></a>Beskrivning av en kanal och dess relaterade komponenter
### <a id="channel_input"></a>Kanalen indata (infognings) konfigurationer
#### <a id="ingest_protocols"></a>Strömmande infogningsprotokollet
Media Services stöder vill föra in levande feeds med flera bithastigheter fragmenterad MP4 och flera bithastigheter RTMP som strömningsprotokoll. När infognings-RTMP strömning protokoll har valts, två infognings-(indata) slutpunkter har skapats för kanalen:

* **Primär URL**: Anger den fullständiga URL-Adressen för kanalens primära RTMP infognings-slutpunkt.
* **Sekundär URL** (valfritt): Anger den fullständiga URL-Adressen för kanalens sekundära RTMP infognings-slutpunkten.

Använd sekundära URL Om du vill förbättra hållbarhet och feltolerans för din infognings dataström (samt kodare redundans och feltolerans), särskilt för följande scenarier:

- Enskild kodare double-push-installation till både primär och sekundär URL: er:

    Det huvudsakliga syftet med det här scenariot är att ge bättre återhämtning i nätverket variationer och skakningar. Vissa RTMP kodare inte hantera nätverksavbrott bra. När det sker ett kopplas från, kan en kodare stoppa kodning och sedan inte att skicka alla buffrade data när en återanslutning sker. Detta leder till avbrott och förlorade data. Nätverksavbrott kan inträffa på grund av ett felaktigt nätverk eller underhåll på Azure-sidan. Primära och sekundära URL: er minska problem med nätverket och ger en kontrollerad uppgraderingsprocessen. Varje gång som sker en schemalagd nätverk koppla från Media Services hanterar primära och sekundära kopplar ifrån och ger en fördröjd koppla bort mellan två. Kodare har sedan tid att behålla skicka data och ansluta igen. Ordningen på de kopplas från kan vara slumpmässiga, men det kommer alltid att vara en fördröjning mellan primära och sekundära eller sekundär eller den primära URL-adresser. I det här scenariot är kodaren fortfarande enskild felpunkt.

- Flera kodare med varje encoder push-installation till en särskild plats:

    Det här scenariot ger både kodare och infognings-redundans. I det här scenariot encoder1 push-meddelanden till den primära URL och encoder2 push-meddelanden till den sekundära URL. När en kodare misslyckas, kan andra kodaren hålla skickar data. Dataredundans kan upprätthållas eftersom Media Services inte kopplar primära och sekundära URL: er på samma gång. Det här scenariot förutsätter att kodare gång synkroniseras och ange exakt samma data.  

- Flera kodare double-push-installation till både primär och sekundär URL: er:

    I det här scenariot vidarebefordra båda kodare data till både primär och sekundär URL: er. Detta ger den bästa tillförlitlighet och feltolerans, samt dataredundans. Det här scenariot kan tolerera fel i båda kodaren och kopplar, även om en kodare slutar fungera. Den förutsätter att kodare gång synkroniseras och ange exakt samma data.  

Information om RTMP livekodare finns [Azure Media Services RTMP Support och direktsända kodare](http://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Infognings-URL: er (slutpunkter)
En kanal som tillhandahåller en slutpunkt för indata (infognings-URL) som du anger i livekodaren, så kan push-kodaren dataströmmar till din kanaler.   

När du skapar kanalen kan du få infognings-URL: er. Om du vill hämta dessa URL: er måste kanalen behöver inte finnas i den **kör** tillstånd. När du är redo att börja skicka data till kanalen kanalen måste finnas i den **kör** tillstånd. När kanalen har startat mata in data kan du förhandsgranska dataströmmen via förhandsgransknings-URL.

Du har ett alternativ för vill föra in fragmenterad MP4 (Smooth Streaming) direktsänd dataström via en SSL-anslutning. För att mata in via SSL, se till att uppdatera infognings-URL: en till HTTPS. Du kan för närvarande mata in RTMP via SSL.

#### <a id="keyframe_interval"></a>Keyframe intervall
När du använder en lokal livekodare för att generera dataström i multibithastighet varaktighet keyframe intervall för grupp med bilder (GOP) som används av den externa kodaren. När kanalen har tagit emot den här inkommande dataström, du kan leverera din liveströmning för uppspelning av klientprogram i något av följande format: Smooth Streaming, dynamiska anpassningsbar strömning via HTTP-(bindestreck) och HTTP Live Streaming (HLS). När du utför direktsänd strömning paketeras HLS alltid dynamiskt. Som standard beräknas automatiskt Media Services HLS segment paketering förhållandet (fragment per segment) baserat på det keyframe intervall som tas emot från livekodaren.

Följande tabell visar hur segment varaktighet beräknas:

| Keyframe intervall | HLS segment paketering förhållandet (FragmentsPerSegment) | Exempel |
| --- | --- | --- |
| Mindre än eller lika med 3 sekunder |3:1 |Om KeyFrameInterval (eller GOP) är 2 sekunder, är standard HLS segment paketering kvoten 3 till 1. Detta skapar ett 6 sekund HLS segment. |
| 3-5 sekunder |2:1 |Om KeyFrameInterval (eller GOP) är 4 sekunder är standard HLS segment paketering förhållandet 2 till 1. Detta skapar ett 8-sekund HLS segment. |
| Större än 5 sekunder |1:1 |Om KeyFrameInterval (eller GOP) är 6 sekunder, är standard HLS segment paketering kvoten 1 till 1. Detta skapar ett 6 sekund HLS segment. |

Du kan ändra fragment per segment förhållandet genom att konfigurera kanalens utdata och inställningen FragmentsPerSegment på ChannelOutputHls.

Du kan också ändra intervallvärdet keyframe genom att ange egenskapen KeyFrameInterval på ChanneInput. Om du uttryckligen ställa in KeyFrameInterval segmentera HLS paketering förhållandet FragmentsPerSegment beräknas med hjälp av regler som beskrivs ovan.  

Om du uttryckligen ställa in både KeyFrameInterval och FragmentsPerSegment använder Media Services de värden som du anger.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan ange IP-adresser som får publicera video i den här kanalen. Tillåtna IP-adressen kan anges som något av följande:

* En IP-adress (t.ex, 10.0.0.1)
* Ett IP-adressintervall som använder en IP-adress och nätmask CIDR (till exempel 10.0.0.1/22)
* Ett IP-adressintervall som använder en IP-adress och en prickad decimal nätmask (till exempel 10.0.0.1(255.255.252.0))

Om inga IP-adresser har angetts och det finns inga Regeldefinitionen tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

### <a name="channel-preview"></a>Kanal preview
#### <a name="preview-urls"></a>URL: er för förhandsgranskning
Kanaler ange förhandsgranskningsslutpunkten (förhandsgranskning URL) som används för att förhandsgranska och verifiera strömmen innan ytterligare bearbetning och leverans.

När du skapar kanalen kan du få förhandsgransknings-URL. Om du vill hämta Webbadress måste kanalen behöver inte finnas i den **kör** tillstånd. När kanalen har startat mata in data kan du förhandsgranska dataströmmen.

För närvarande Förhandsgranska dataströmmen levereras endast i fragmenterad MP4 (Smooth Streaming) format, oavsett angivna Indatatyp. Du kan använda den [Smooth Streaming hälsoövervakning](http://smf.cloudapp.net/healthmonitor) player att testa smooth stream. Du kan också använda en spelare som finns i Azure portal för att visa din dataström.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan ange IP-adresser som tillåts att ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser anges, får alla IP-adresser. Tillåtna IP-adressen kan anges som något av följande:

* En IP-adress (t.ex, 10.0.0.1)
* Ett IP-adressintervall som använder en IP-adress och nätmask CIDR (till exempel 10.0.0.1/22)
* Ett IP-adressintervall som använder en IP-adress och en prickad decimal nätmask (till exempel 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Kanal-utdata
Information om kanalen utdata finns i [Keyframe intervall](#keyframe_interval) avsnitt.

### <a name="channel-managed-programs"></a>Kanal-hanterade program
En kanal är associerad till program som du kan använda för att styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program som är associerad med en tillgång som lagrar direktuppspelat innehåll. En tillgång som är mappad till en block-blob-behållare i Azure storage-konto och lagras filerna i tillgången som blobbar i behållaren. Om du vill publicera programmet så att kunderna kan visa dataströmmen, måste du skapa en positionerare för den associerade tillgången. Du kan använda den här lokaliseraren för att skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt, så att du kan skapa flera Arkiv för samma inkommande dataström. Du kan publicera och arkivera olika delar av en händelse efter behov. Anta exempelvis att dina affärsbehov är att arkivera 6 timmar av ett program, men att sända endast de senaste 10 minuterna. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet har publicerats.

Du bör inte återanvända befintliga program för nya händelser. I stället skapa ett nytt program för varje händelse. Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

Om du vill ta bort arkiverat innehåll, stoppa och ta bort programmet och ta sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först.

När du stoppar och ta bort programmet kan användarna strömma ditt arkiverade innehåll som en video på begäran, tills du tar bort tillgången. Om du vill behålla det arkiverade innehållet, men inte har det tillgängligt för strömning, tar du bort strömningspositioneraren.

## <a id="states"></a>Kanal tillstånd och fakturering
Möjliga värden för det aktuella tillståndet för en kanal är:

* **Stoppats**: Detta är inledningsvis i kanalen när skapandet. Kanal-egenskaper kan uppdateras men strömning tillåts inte i det här tillståndet.
* **Starta**: kanalen startas. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar, kanalen återgår till den **stoppad** tillstånd.
* **Kör**: kanalen kan bearbeta live dataströmmar.
* **Stoppa**: kanalen har stoppats. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Ta bort**: kanalen tas bort. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanal anger mappning till fakturerings-läge.

| Kanaltillstånd | Portalen UI-indikatorer | Fakturerad? |
| --- | --- | --- | --- |
| **Starta** |**Starta** |Nej (övergångsläge) |
| **Kör** |**Redo** (inget program som körs)<p><p>eller<p>**Strömning** (minst ett aktivt program) |Ja |
| **Stoppa** |**Stoppa** |Nej (övergångsläge) |
| **Stoppades** |**Stoppades** |Nej |

## <a id="cc_and_ads"></a>Stängd textning och ad infogning
Följande tabell visar standarder som stöds för stängd textning och ad infogning.

| Standard | Anteckningar |
| --- | --- |
| CEA 708 och EIA 608 (708/608) |CEA 708 och EIA 608 är textning standarder för USA och Kanada.<p><p>För närvarande stöds textning endast om transporteras i den kodade Indataströmmen. Du måste använda en aktiva mediekodare som kan infoga 608 eller 708 beskrivningar i kodad strömmen som skickades till Media Services. Media Services levererar innehåll med infogade etiketter till dina användare. |
| TTML i .ismt (Smooth Streaming textspår) |Media Services dynamisk paketering gör det möjligt för dina klienter att strömma innehåll i något av följande format: DASH, HLS eller Smooth Streaming. Om du vill mata in fragmenterad MP4 (Smooth Streaming) med etiketter i .ismt (Smooth Streaming textspår) du kan skicka dataströmmen till endast Smooth Streaming-klienter. |
| SCTE 35 |SCTE 35 är ett digitalt signaling system som används för att spola reklam infogning. Underordnade mottagarna att signalen splice reklam i dataströmmen för den angivna tiden. SCTE 35 måste skickas som ett null-optimerade spår i Indataströmmen.<p><p>För närvarande endast stöds Indataströmmen formatera som utför ad signaler fragmenterad MP4 (Smooth Streaming). Det går endast att utdata format är också Smooth Streaming. |

## <a id="considerations"></a>Överväganden
När du använder en lokal livekodare för att skicka en dataström med multibithastighet till en kanal, gäller följande begränsningar:

* Kontrollera att du har tillräckligt ledigt Internet-anslutning för att skicka data till infognings-punkter.
* Med hjälp av en sekundär infognings-URL krävs ytterligare bandbredd.
* Den inkommande dataströmmen i flera bithastigheter kan ha högst 10 bildkvaliteten nivåer (lager) och högst 5 ljud spår.
* Högsta genomsnittliga bithastighet för någon av nivåerna bildkvaliteten bör vara mindre än 10 Mbit/s.
* Aggregering av genomsnittlig bithastighet för alla video och ljud dataströmmar måste ha färre än 25 Mbit/s.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du kan mata in en enkel bithastighet i din kanal. Men eftersom kanalen inte bearbetar dataströmmen, klientprogrammen även ta emot en dataström med enkel bithastighet. (Vi rekommenderar inte det här alternativet.)

Här följer andra överväganden som rör arbeta med kanaler och relaterade komponenter:

* Varje gång du konfigurera om livekodaren anropa den **återställa** metod på kanalen. Du måste stoppa programmet innan du återställer kanalen. Starta om programmet när du har återställt kanalen.
* En kanal kan stoppas endast när den är i den **kör** tillstånd och alla program i kanalen har stoppats.
* Som standard kan du lägga till endast 5 kanaler Media Services-kontot. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du debiteras endast när din kanal är i den **kör** tillstånd. Mer information finns i den [tillstånd och fakturering kanaler](media-services-live-streaming-with-onprem-encoders.md#states) avsnitt.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Azure Media Services fragmenterad MP4 live infognings-specifikationen](media-services-fmp4-live-ingest-overview.md)

[Översikt över Azure Media Services och vanliga scenarier](media-services-overview.md)

[Media Services-koncepten](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
