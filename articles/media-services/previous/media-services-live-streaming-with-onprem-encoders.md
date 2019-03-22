---
title: Stream live med lokala kodare som skapar strömmar med flera bithastigheter – Azure | Microsoft Docs
description: 'Det här avsnittet beskriver hur du konfigurerar en kanal som tar emot en direktsänd dataström med flera bithastigheter från en lokala kodare. Dataströmmen kan sedan levereras till klienten uppspelning program via en eller flera slutpunkter för direktuppspelning, med någon av följande protokoll för anpassningsbar strömning: HLS, Smooth Streaming, DASH.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: cenkd;juliako
ms.openlocfilehash: da20e4601b75bcb22546d21f6ad218ac9ba2728b
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188364"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare

> [!NOTE]
> Startar den 12 maj 2018 livekanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) inmatningsprotokollen.

## <a name="overview"></a>Översikt
I Azure Media Services, en *kanal* representerar en pipeline för bearbetning av live-streaming-innehåll. En kanal tar emot inkommande direktsändningar i ett av två sätt:

* En lokal livekodare skickar en med flera bithastigheter RTMP eller Smooth Streaming (fragmenterad MP4) strömma till den kanal som inte är aktiverad för att utföra live encoding med Media Services. De infogade strömmarna passerar via kanaler utan vidare bearbetning. Den här metoden anropas *direkt*. En livekodare kan även skicka en dataström med enkel bithastighet till en kanal som inte är aktiverad för live encoding, men vi rekommenderar inte som. Media Services ger strömmen till kunder som begär den.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning.


* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan live encoding av den inkommande dataströmmen med enkel bithastighet till en med flera bithastigheter (anpassningsbar) videoström. Media Services ger strömmen till kunder som begär den.

Från och med Media Services 2.10-versionen när du skapar en kanal kan ange du hur du vill att din kanal tar emot Indataströmmen. Du kan också ange om du vill att kanalen för att utföra live encoding av strömmen. Du kan välja mellan två alternativ:

* **Passera**: Ange det här värdet om du planerar att använda en lokal livekodare som har en dataström med multibithastighet (en direkt stream) som utdata. I det här fallet passerar den inkommande dataströmmen genom att utdata utan kodning. Det här är beteendet för en kanal innan 2,10 utgåvan. Den här artikeln innehåller information om hur du arbetar med kanaler för den här typen.
* **Direktsänd kodning**: Välj det här värdet om du planerar att använda Media Services för att koda din direktsänd dataström med enkel bithastighet till en dataström med multibithastighet. Lämna en direktsänd kodning kanal i en **kör** tillstånd medför debiterade avgifterna. Vi rekommenderar att du omedelbart stoppa dina kanaler som körs när din live-streaming event är klar att undvika extra debitering. Media Services ger strömmen till kunder som begär den.

> [!NOTE]
> Den här artikeln beskrivs attributen för kanaler som inte har aktiverats att utföra live encoding. Information om hur du arbetar med kanaler som är aktiverade för att utföra live encoding finns i [direktsänd strömning med Azure Media Services för att skapa dataströmmar med flera bithastigheter](media-services-manage-live-encoder-enabled-channels.md).
>
>Mer information om rekommenderade på lokala kodare finns i [rekommenderas på lokala kodare](media-services-recommended-encoders.md).

Följande diagram visar en live-streaming-arbetsflöde som använder en lokal livekodare ha flera bithastigheter RTMP eller fragmenterad MP4 (Smooth Streaming) strömmar som utdata.

![Live-arbetsflöde][live-overview]

## <a id="scenario"></a>Vanligt scenario för livestreaming
Följande steg beskriver uppgifter som ingår i att skapa vanliga program för livestreaming.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som har en med flera bithastigheter RTMP eller fragmenterad MP4 (Smooth Streaming) stream som utdata. Mer information finns i [Support och livekodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824).

    Du kan också utföra det här steget när du har skapat din kanal.
2. Skapa och starta en kanal.

3. Hämta kanalens infognings-URL.

    Live Encoding använder inmatning URL: en för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program.

    När du använder Azure portal, skapar skapa ett program även en tillgång.

    När du använder .NET SDK eller REST, måste du skapa en tillgång och ange om du vill använda den här tillgången när du skapar ett program.
6. Publicera tillgången som associeras med programmet.   

    >[!NOTE]
    >När ditt Media Services-konto skapas en **standard** slutpunkt för direktuppspelning har lagts till i ditt konto i den **stoppad** tillstånd. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

7. Starta programmet när du är redo att påbörja strömning och arkivering.

8. Som alternativ kan livekodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.

9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

10. Ta bort programmet (och ta eventuellt bort tillgången).     

## <a id="channel"></a>Beskrivning av en kanal och dess relaterade komponenter
### <a id="channel_input"></a>Kanal-indata (hämta) konfigurationer
#### <a id="ingest_protocols"></a>Mata in strömmande protokoll
Media Services stöder mata in live flöden med flera bithastigheter fragmenterad MP4 och flera bithastigheter RTMP som strömningsprotokoll. När RTMP mata in Direktuppspelningsprotokoll väljs, implementering, två (indata) slutpunkter har skapats för kanalen:

* **Primär URL**: Anger den fullständiga URL: en för kanalens primära RTMP mata in slutpunkt.
* **Sekundär URL** (valfritt): Anger den fullständiga URL: en för kanalens sekundära RTMP mata in slutpunkt.

Använd sekundära URL: en om du vill förbättra tillförlitlighet och feltolerans för din inmatning stream (samt encoder redundans och feltolerans), särskilt för följande scenarier:

- Enkel encoder double-push-överföring till både primära och sekundära URL: er:

    Det huvudsakliga syftet med det här scenariot är att tillhandahålla mer återhämtning till nätverket variationer och skakningar. Hanterar inte vissa RTMP-kodare nätverksavbrott bra. När ett nätverk frånkoppling sker kan en kodare stoppa kodning och sedan skickar de buffrade data när en återanslutning händer. Detta leder till avbrott och dataförlust. Nätverksavbrott kan inträffa på grund av ett felaktigt nätverk eller underhåll på Azure-sidan. URL: er för primär/sekundär minska problem med nätverket och ger en kontrollerad uppgraderingsprocessen. Varje gång som en schemalagd nätverk frånkoppling händer, Media Services hanterar primärt och sekundära kopplar bort och ger en fördröjd koppla bort mellan två. Kodare har tid att fortsätta skicka data och ansluta igen. Ordningen på de kopplas från kan vara slumpmässiga, men det finns alltid en fördröjning mellan primär/sekundär eller sekundära eller den primära webbadresser. I det här scenariot är kodaren fortfarande enskild felpunkt.

- Flera kodare med varje encoder push-överföra till en särskild tidpunkt:

    Det här scenariot ger både encoder och matar in redundans. I det här scenariot encoder1 push-meddelanden till den primära URL och encoder2 push-meddelanden till den sekundära URL. När en kodare misslyckas, kan den andra kodaren fortsätta skicka data. Dataredundans kan underhållas eftersom medietjänster inte kopplar primära och sekundära URL: er på samma gång. Det här scenariot förutsätter att kodare är synkroniserad tid och ange exakt samma data.  

- Flera kodare double-push-överföring till både primära och sekundära URL: er:

    I det här scenariot skicka båda kodare data till både primära och sekundära URL: er. Detta ger den bästa möjliga tillförlitlighet och feltolerans, samt dataredundans. Det här scenariot kan tolerera fel i båda kodaren och kopplar från, även om en kodare slutar fungera. Det förutsätts att kodare är synkroniserad tid och ange exakt samma data.  

Läs om hur RTMP livekodare [Azure Media Services RTMP-Support och Livekodare](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Mata in URL: er (slutpunkter)
En kanal som innehåller en slutpunkt för indata (infognings-URL) att du anger i live-kodare så kodaren kan skicka dataströmmar till dina kanaler.   

Du kan hämta URL: er för inmatning när du skapar kanalen. Om du vill få dessa URL: er måste kanalen behöver inte finnas i den **kör** tillstånd. När du är redo att börja skicka data till kanalen kanalen måste vara i den **kör** tillstånd. När kanalen har startat mata in data kan du förhandsgranska dataströmmen via förhandsgransknings-URL.

Du har en möjlighet att mata in en fragmenterad MP4 (Smooth Streaming) live stream via en SSL-anslutning. För att mata in via SSL, se till att uppdatera URL: en inmatning till HTTPS. För närvarande kan du mata in RTMP via SSL.

#### <a id="keyframe_interval"></a>Bildrutan intervall
När du använder en lokal livekodare för att generera med flera bithastigheter stream anger intervallet bildrutan varaktighet för gruppen med bilder (GOP) som används av den externa kodaren. När kanalen har hämtat den här inkommande strömmen kommer kan du leverera din liveuppspelning för uppspelning av klientprogram i någon av följande format: Smooth Streaming, Dynamic Adaptive Streaming via HTTP (DASH) och HTTP Live Streaming (HLS). När du utför direktsänd strömning är HLS alltid paketerat dynamiskt. Som standard beräknar Media Services automatiskt HLS segment paketering förhållandet (fragment per segment) baserat på det bildrutan intervall som tas emot från live-kodare.

I följande tabell visar hur segment varaktighet beräknas:

| Bildrutan intervall | HLS segment paketering förhållande (FragmentsPerSegment) | Exempel |
| --- | --- | --- |
| Mindre än eller lika med 3 sekunder |3:1 |Om KeyFrameInterval (eller GOP) är 2 sekunder, är standard HLS segment paketering kvoten 3 till 1. Detta skapar ett 6-sekund HLS segment. |
| 3 till 5 sekunder |2:1 |Om KeyFrameInterval (eller GOP) är 4 sekunder, är standard HLS segment paketering förhållandet 2 till 1. Detta skapar ett 8-sekund HLS segment. |
| Större än 5 sekunder |1:1 |Om KeyFrameInterval (eller GOP) är 6 sekunder, är standard HLS segment paketering kvoten 1 till 1. Detta skapar ett 6-sekund HLS segment. |

Du kan ändra förhållandet fragment per segment genom att konfigurera kanalens utdata och inställningen FragmentsPerSegment på ChannelOutputHls.

Du kan också ändra värdet för intervallet bildrutan genom att ange egenskapen KeyFrameInterval på ChannelInput. Om du uttryckligen anger KeyFrameInterval segmentera HLS paketering förhållandet FragmentsPerSegment beräknas via de regler som beskrivs tidigare.  

Om du uttryckligen ställa in både KeyFrameInterval och FragmentsPerSegment använder Media Services de värden som du anger.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som får publicera video i den här kanalen. Tillåtna IP-adressen kan anges som något av följande:

* En IP-adress (exempelvis 10.0.0.1)
* Ett IP-adressintervall som använder en IP-adress och en CIDR-nätmask (exempelvis 10.0.0.1/22)
* Ett IP-adressintervall som använder en IP-adress och en decimal undernätsmask med punktavgränsad (till exempel 10.0.0.1(255.255.252.0))

Om inga IP-adresser har angetts och det saknas Regeldefinitioner tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

### <a name="channel-preview"></a>Förhandsgranskning av kanal
#### <a name="preview-urls"></a>Granska URL: erna
Kanaler ger förhandsgranskningsslutpunkten (förhandsgransknings-URL) som används för att förhandsgranska och validera dataströmmen inför vidare behandling och leverans.

Du kan hämta förhandsgransknings-URL när du skapar kanalen. Om du vill hämta URL: en måste kanalen behöver inte finnas i den **kör** tillstånd. När kanalen har startat mata in data kan du förhandsgranska dataströmmen.

För närvarande kan förhandsgranska dataströmmen levereras endast i fragmenterad MP4 (Smooth Streaming)-format, oavsett angivna Indatatyp. Du kan använda den [Smooth Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) player att testa smooth stream. Du kan också använda en spelare som ligger i Azure portal för att visa din dataström.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som ska kunna ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser anges, tillåts IP-adresser. Tillåtna IP-adressen kan anges som något av följande:

* En IP-adress (exempelvis 10.0.0.1)
* Ett IP-adressintervall som använder en IP-adress och en CIDR-nätmask (exempelvis 10.0.0.1/22)
* Ett IP-adressintervall som använder en IP-adress och en decimal undernätsmask med punktavgränsad (till exempel 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Kanal-utdata
Information om kanalen utdata finns i den [bildrutan intervall](#keyframe_interval) avsnittet.

### <a name="channel-managed-programs"></a>Kanal-hanterade program
En kanal är associerad till program som du kan använda för att styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiveringsfönstret avgör också det maximala antalet klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program som är associerad med en tillgång som lagrar strömmas innehåll. En tillgång är mappad till en block blob-behållare i Azure storage-kontot och filerna i tillgången lagras som blobar i behållaren. Om du vill publicera programmet så att dina kunder kan visa dataströmmen, måste du skapa en OnDemand-positionerare för den associerade tillgången. Du kan använda den här positioneraren för att skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt, så att du kan skapa flera Arkiv för samma inkommande dataström. Du kan publicera och arkivera olika delar av en händelse efter behov. Anta exempelvis att ditt verksamhetsbehov kan vara att arkivera 6 timmar av ett program, men att sända endast de senaste 10 minuterna. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs att arkivera sex timmar av händelsen, men programmet publiceras inte. Det andra programmet är inställd på att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. I stället skapa ett nytt program för varje händelse. Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

Om du vill ta bort arkiverat innehåll, stoppa och ta bort programmet och sedan ta bort den associerade tillgången. En tillgång kan inte tas bort om ett program som använder den. Programmet måste tas bort först.

Även efter att du stoppar och ta bort programmet kan användarna strömma ditt arkiverade innehåll som en video på begäran, tills du tar bort tillgången. Om du vill behålla det arkiverade innehållet, men inte har det tillgängligt för strömning, tar du bort strömningslokaliseraren.

## <a id="states"></a>Kanaltillstånd och fakturering
Möjliga värden för det aktuella tillståndet för en kanal är:

* **Stoppad**: Det här är starttillståndet för kanalen när den har skapats. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**: Kanalen startas. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till den **stoppad** tillstånd.
* **Körning**: Kanalen kan bearbeta direktsända strömmar.
* **Stoppa**: Kanalen stoppas. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Tar bort**: Kanalen tas bort. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläget.

| Kanaltillstånd | Portalgränssnittsindikatorer | Fakturerad? |
| --- | --- | --- |
| **Startar** |**Startar** |Nej (övergångsläge) |
| **Körs** |**Redo** (inga program som körs)<p><p>eller<p>**Strömning** (minst ett aktivt program) |Ja |
| **Stoppar** |**Stoppar** |Nej (övergångsläge) |
| **Stoppad** |**Stoppad** |Nej |

## <a id="cc_and_ads"></a>Dold textning och ad-insättning
I följande tabell visar stöds standarder för dold textning och ad-insättning.

| Standard | Anteckningar |
| --- | --- |
| CEA-708 och EIA 608 (708/608) |CEA-708 och EIA 608 är textning standarder för USA och Kanada.<p><p>Textning är för närvarande endast om transporteras i kodat Indataströmmen. Du måste använda en live media encoder som kan infoga 608 eller 708-undertexter i kodat dataströmmen som skickas till Media Services. Media Services levererar innehåll med infogad textning till användarna. |
| TTML inuti .ismt (Smooth Streaming textspår) |Media Services dynamisk paketering gör det möjligt för dina klienter att spela upp innehåll i någon av följande format: DASH, HLS eller Smooth Streaming. Men om du inmatning av fragmenterad MP4 (Smooth Streaming) med bildtexter inuti .ismt (Smooth Streaming textspår) kan du leverera dataströmmen till endast Smooth Streaming-klienter. |
| SCTE-35 |SCTE 35 är ett digitalt signaling system som fungerar som en utlösare reklam infogning. Underordnade mottagare använda signalen för att splice reklam i dataströmmen för den tilldelade tiden. SCTE 35 måste skickas som ett null-optimerade spår i Indataströmmen.<p><p>För närvarande endast stöds Indataströmmen formatera de innehåller ad signaler är fragmenterad MP4 (Smooth Streaming). Det går endast att utdata formatet är också Smooth Streaming. |

## <a id="considerations"></a>Att tänka på
När du använder en lokal livekodare för att skicka en dataström med multibithastighet till en kanal, gäller följande begränsningar:

* Kontrollera att du har tillräckligt med ledigt Internetanslutning för att skicka data till inmatning punkter.
* Med hjälp av en sekundär infognings-URL kräver ytterligare bandbredd.
* Den inkommande dataströmmen i multibithastighet kan ha högst 10 videokvalitet nivåer (skikt) och högst 5 ljudspår.
* Den högsta genomsnittliga bithastigheten för någon av de videokvalitet nivåerna måste ha färre än 10 Mbit/s.
* Summan av den genomsnittliga bithastigheter för utdata för alla video och ljud strömmar måste ha färre än 25 Mbit/s.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du kan mata in en enkel bithastighet i din kanal. Men eftersom kanalen inte bearbetar dataströmmen, klientprogrammen får också ett en enda bithastighet. (Vi rekommenderar inte det här alternativet.)

Här följer andra överväganden som rör arbetar med kanaler och relaterade komponenter:

* Varje gång du konfigurera om livekodaren anropa den **återställa** metoden på kanalen. Du måste stoppa programmet innan du återställer kanalen. Starta om programmet när du har återställt kanalen.

  > [!NOTE]
  > När du startar om programmet, måste du koppla den till en ny tillgång och skapa en ny. 
  
* En kanal kan stoppas endast när den har den **kör** tillstånd och alla program i kanalen har stoppats.
* Som standard kan du lägga till endast fem kanaler Media Services-kontot. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du debiteras bara när din kanal har den **kör** tillstånd. Mer information finns i den [Channel tillstånd och fakturering](media-services-live-streaming-with-onprem-encoders.md#states) avsnittet.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Rekommenderas på lokala kodare](media-services-recommended-encoders.md)

[Specifikation för Azure Media Services fragmenterad MP4 liv-inmatning](media-services-fmp4-live-ingest-overview.md)

[Översikt över Azure Media Services och vanliga scenarier](media-services-overview.md)

[Media Services-koncepten](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
