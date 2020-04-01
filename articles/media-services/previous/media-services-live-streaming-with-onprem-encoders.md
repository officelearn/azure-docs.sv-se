---
title: Streama live med lokala kodare som skapar multibitrate-strömmar – Azure | Microsoft-dokument
description: I det här avsnittet beskrivs hur du konfigurerar en kanal som tar emot en livestream med flera bitar från en lokal kodare.
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
ms.author: juliako
ms.openlocfilehash: c0d19d68d016a47762fb5d2646ea6ccf74d3ef75
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476553"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Arbeta med kanaler som får multibitrate live stream från lokala kodare

> [!NOTE]
> Från och med den 12 maj 2018 stöder livekanalerna inte längre rtp/MPEG-2-transportströmmens introllningsprotokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmenterade MP4-protokoll (Smooth Streaming).

## <a name="overview"></a>Översikt
I Azure Media Services representerar en *kanal* en pipeline för bearbetning av live-strömmande innehåll. En kanal tar emot direktuppspelningar på två sätt:

* En lokal live-kodare skickar en rtmp-ström (multibitrate RTMP eller Smooth Streaming) till kanalen som inte är aktiverad för att utföra live-kodning med Media Services. De intövda strömmarna passerar genom kanaler utan ytterligare bearbetning. Den här metoden kallas *genomströmning*. En live-kodare kan också skicka en dataström med en bithastighet till en kanal som inte är aktiverad för live-kodning, men det rekommenderar vi inte. Media Services levererar strömmen till kunder som begär det.

  > [!NOTE]
  > Att använda en genomströmningsmetod är det mest ekonomiska sättet att göra livestreaming.


* En lokal live-kodare skickar en enbitrate-ström till kanalen som är aktiverad för att utföra live-kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan live-kodning av den inkommande enbitrate-strömmen till en videoström med flera bithastigheter (adaptiv). Media Services levererar strömmen till kunder som begär det.

Från och med mediatjänst 2.10-versionen kan du när du skapar en kanal ange hur du vill att kanalen ska ta emot indataströmmen. Du kan också ange om du vill att kanalen ska utföra live-kodning av din ström. Du kan välja mellan två alternativ:

* **Passera:** Ange det här värdet om du planerar att använda en lokal live-kodare som har en multibitrate-ström (en direktström) som utdata. I det här fallet passerar den inkommande strömmen vidare till utdata utan kodning. Detta är beteendet hos en kanal innan 2,10-versionen. Den här artikeln innehåller information om hur du arbetar med kanaler av den här typen.
* **Live-kodning:** Välj det här värdet om du planerar att använda Media Services för att koda din livestream med en bithastighet till en flerbitrateström. Om du lämnar en direktkodningskanal i **körtillstånd** medför faktureringsavgifter. Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när ditt livestreamingevenemang är klart för att undvika extra timkostnader. Media Services levererar strömmen till kunder som begär det.

> [!NOTE]
> I den här artikeln beskrivs attribut för kanaler som inte är aktiverade för live-kodning. Information om hur du arbetar med kanaler som är aktiverade för live-kodning finns i [Direktuppspelning med Azure Media Services för att skapa flerbitratströmmar](media-services-manage-live-encoder-enabled-channels.md).
>
>Information om rekommenderade lokala kodare finns i [Rekommenderad på lokala kodare](media-services-recommended-encoders.md).

Följande diagram representerar ett arbetsflöde för direktuppspelning som använder en lokal live-kodare för att ha rtmp-strömmar med flera bithastigheter eller fragmenterade MP4-strömmar (Smooth Streaming) som utdata.

![Live-arbetsflöde][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Vanligt scenario för livestreaming
I följande steg beskrivs uppgifter som ingår i skapandet av vanliga live-streaming-program.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal live-kodare som har en RTMP-ström med flera bithastigheter eller fragmenterad MP4(Smooth Streaming) som utdata. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824).

    Du kan också utföra det här steget när du har skapat din kanal.
2. Skapa och starta en kanal.

3. Hämta url:en för kanalingstjänsten.

    Den levande kodaren använder den intälta WEBBADRESSEN för att skicka strömmen till kanalen.
4. Hämta url:en förhandsgranskning av kanal.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program.

    När du använder Azure-portalen skapas också en tillgång genom att skapa ett program.

    När du använder .NET SDK eller REST måste du skapa en tillgång och ange att använda den här tillgången när du skapar ett program.
6. Publicera den tillgång som är associerad med programmet.   

    >[!NOTE]
    >När ditt Azure Media Services-konto skapas läggs en **standardslutpunkt** för direktuppspelning till ditt konto i tillståndet **Stoppad.** Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

7. Starta programmet när du är redo att börja strömma och arkivera.

8. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.

9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

10. Ta bort programmet (och ta eventuellt bort tillgången).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Beskrivning av en kanal och dess relaterade komponenter
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Kanalinmatningskonfigurationer (inmatning)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Inta direktuppspelningsprotokoll
Media Services stöder intag av live-flöden med hjälp av flera bithastighetsfragmenterade MP4 och multibitrat RTMP som direktuppspelningsprotokoll. När RTMP-inmatningsprotokollet väljs skapas två inmatningsslutpunkter (indata) för kanalen:

* **Primär URL:** Anger den fullständigt kvalificerade URL:en för kanalens primära RTMP-intjäningsslutpunkt.
* **Sekundär URL** (valfritt): Anger den fullständigt kvalificerade URL:en för kanalens sekundära RTMP-intiga slutpunkt.

Använd den sekundära webbadressen om du vill förbättra hållbarheten och feltoleransen för din introllningsström (samt encoder redundans och feltolerans), särskilt för följande scenarier:

- Enkelkodare dubbel-pushing till både primära och sekundära webbadresser:

    Huvudsyftet med det här scenariot är att ge mer motståndskraft mot nätverksfluktuationer och nervositet. Vissa RTMP-kodare hanterar inte nätverket frånkoppling väl. När en nätverksfrånkoppling inträffar kan en kodare stoppa kodningen och sedan inte skicka buffrade data när en återanslutning sker. Detta orsakar diskontinuiteter och dataförlust. Nätverksfrånkopplingar kan inträffa på grund av ett felaktigt nätverk eller underhåll på Azure-sidan. Primära/sekundära webbadresser minskar nätverksproblemen och tillhandahåller en kontrollerad uppgraderingsprocess. Varje gång ett schemalagt nätverks frånkoppling sker hanterar Media Services de primära och sekundära frånkopplingarna och ger en fördröjd frånkoppling mellan de två. Kodare har sedan tid att fortsätta skicka data och återansluta igen. Ordningen på frånkopplingarna kan vara slumpmässig, men det kommer alltid att finnas en fördröjning mellan primära/sekundära eller sekundära/primära webbadresser. I det här fallet är kodaren fortfarande den enda felpunkten.

- Flera kodare, där varje kodare trycker till en dedikerad punkt:

    Det här scenariot ger både kodare och intag redundans. I det här fallet skickar encoder1 till den primära URL:en och kodar2 skickar till den sekundära URL:en. När en kodare misslyckas kan den andra kodaren fortsätta att skicka data. Dataredundans kan upprätthållas eftersom Media Services inte kopplar från primära och sekundära webbadresser samtidigt. Det här scenariot förutsätter att kodare är tidssynkroniserade och tillhandahåller exakt samma data.  

- Flera kodare dubbel-pushing till både primära och sekundära webbadresser:

    I det här fallet både kodare skicka data till både primära och sekundära webbadresser. Detta ger bästa tillförlitlighet och feltolerans samt dataredundans. Det här scenariot kan tolerera både kodarfel och kopplar från, även om en kodare slutar fungera. Det förutsätter att kodare är tidssynkroniserade och ger exakt samma data.  

Information om RTMP-live-kodare finns i [Azure Media Services RTMP Support och Live Encoders](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Inta webbadresser (slutpunkter)
En kanal innehåller en indataslutpunkt (inmatnings-URL) som du anger i live-kodaren, så att kodaren kan skicka strömmar till dina kanaler.   

Du kan hämta de intjest webbadresserna när du skapar kanalen. För att du ska få dessa webbadresser behöver **Running** kanalen inte vara i körläge. När du är redo att börja skicka data till kanalen måste kanalen vara i **körläge.** När kanalen har börjat inta data kan du förhandsgranska flödet via förhandsgranskningsadressen.

Du har möjlighet att inta en fragmenterad MP4 -livestream (Smooth Streaming) via en TLS-anslutning. Om du vill börja använda över TLS kontrollerar du att den intöste URL:en uppdateras till HTTPS. För närvarande kan du inte inta RTMP via TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Nyckelbildruta intervall
När du använder en lokal live-kodare för att generera flerbitriga dataström anger nyckelruteintervallet varaktigheten för gruppen med bilder (GOP) som används av den externa kodaren. När kanalen har tagit emot den här inkommande strömmen kan du leverera din livestream till klientuppspelningsprogram i något av följande format: Smooth Streaming, Dynamic Adaptive Streaming over HTTP (DASH) och HTTP Live Streaming (HLS). När du gör live streaming, HLS är alltid paketerad dynamiskt. Som standard beräknar Media Services automatiskt HLS-segmentets förpackningsförhållande (fragment per segment) baserat på nyckelbildsintervallet som tas emot från den aktiva kodaren.

I följande tabell visas hur segmentets varaktighet beräknas:

| Nyckelbildruta intervall | HLS-segmentet förpackningsförhållande (FragmentsPerSegment) | Exempel |
| --- | --- | --- |
| Mindre än eller lika med 3 sekunder |3:1 |Om KeyFrameInterval (eller GOP) är 2 sekunder är standardförhållandet för HLS-segmentets förpackning 3 till 1. Detta skapar ett 6-sekunders HLS-segment. |
| 3 till 5 sekunder |2:1 |Om KeyFrameInterval (eller GOP) är 4 sekunder är standardförhållandet för HLS-segmentets förpackning 2 till 1. Detta skapar ett 8-sekunders HLS-segment. |
| Större än 5 sekunder |1:1 |Om KeyFrameInterval (eller GOP) är 6 sekunder är standardförhållandet för HLS-segmentets förpackning 1 till 1. Detta skapar ett 6-sekunders HLS-segment. |

Du kan ändra fragment per segment-förhållande genom att konfigurera kanalens utdata och ställa in FragmentPerSegment på ChannelOutputHls.

Du kan också ändra nyckelbildruteintervallvärdet genom att ange egenskapen KeyFrameInterval på ChannelInput. Om du uttryckligen anger KeyFrameInterval beräknas HLS-segmentets förpackningsförhållande FragmentsPerSegment via de regler som beskrivits tidigare.  

Om du uttryckligen anger både KeyFrameInterval och FragmentsPerSegment använder Media Services de värden som du anger.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som får publicera video till den här kanalen. En tillåten IP-adress kan anges som något av följande:

* En enda IP-adress (till exempel 10.0.0.1)
* Ett IP-intervall som använder en IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22)
* Ett IP-intervall som använder en IP-adress och en prickad decimalundernätmask (till exempel 10.0.0.1(255.255.252.0))

Om inga IP-adresser anges och det inte finns någon regeldefinition tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

### <a name="channel-preview"></a>Förhandsgranskning av kanal
#### <a name="preview-urls"></a>Förhandsgranska webbadresser
Kanalerna tillhandahåller en förhandsgranskningsslutpunkt (förhandsgransknings-URL) som du använder för att förhandsgranska och validera flödet innan du bearbetar och levererar vidare.

Du kan hämta förhandsgransknings-URL:en när du skapar kanalen. För att du ska få webbadressen behöver kanalen **Running** inte vara i körläge. När kanalen har börjat inta data kan du förhandsgranska flödet.

För närvarande kan förhandsgranskningsströmmen endast levereras i fragmenterat MP4-format (Smooth Streaming), oavsett den angivna indatatypen. Du kan använda smooth [streaming health monitor-spelaren](https://playready.directtaps.net/smoothstreaming/) för att testa den smidiga strömmen. Du kan också använda en spelare som finns i Azure-portalen för att visa din ström.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser har angetts tillåts en IP-adress. En tillåten IP-adress kan anges som något av följande:

* En enda IP-adress (till exempel 10.0.0.1)
* Ett IP-intervall som använder en IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22)
* Ett IP-intervall som använder en IP-adress och en prickad decimalundernätmask (till exempel 10.0.0.1(255.255.252.0))

### <a name="channel-output"></a>Kanalutgång
Information om kanalutdata finns i [avsnittet Nyckelbildsintervall.](#keyframe_interval)

### <a name="channel-managed-programs"></a>Kanalhanterade program
En kanal är associerad med program som du kan använda för att styra publicering och lagring av segment i en livestream. Kanaler hanterar program. Kanal- och programrelationen liknar traditionella medier, där en kanal har en konstant ström av innehåll och ett program är begränsat till en tidsinnöjd händelse på den kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiv fönsterlängd dikterar också det maximala antalet tid klienter kan söka tillbaka i tiden från den aktuella live-position. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång som lagrar det strömmade innehållet. En tillgång mappas till en blockblolob-behållare i Azure-lagringskontot och filerna i tillgången lagras som blobbar i den behållaren. Om du vill publicera programmet så att kunderna kan visa strömmen måste du skapa en OnDemand-positionerare för den associerade tillgången. Du kan använda den här positioneraren för att skapa en strömmande URL som du kan tillhandahålla dina klienter.

En kanal stöder upp till tre program som körs samtidigt, så att du kan skapa flera arkiv med samma inkommande ström. Du kan publicera och arkivera olika delar av en händelse efter behov. Anta till exempel att ditt affärskrav är att arkivera 6 timmar av ett program, men att bara sända de senaste 10 minuterna. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program är inställt på att arkivera sex timmar av händelsen, men programmet publiceras inte. Det andra programmet är inställt på att arkivera i 10 minuter, och detta program publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa i stället ett nytt program för varje händelse. Starta programmet när du är redo att börja strömma och arkivera. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

Om du vill ta bort arkiverat innehåll stoppar och tar du bort programmet och tar sedan bort den associerade tillgången. Det går inte att ta bort en tillgång om den används i ett program. Programmet måste tas bort först.

Även efter att du har stannat och tagit bort programmet kan användarna strömma det arkiverade innehållet som en video på begäran tills du tar bort tillgången. Om du vill behålla det arkiverade innehållet men inte ha det tillgängligt för direktuppspelning tar du bort strömningslokaliseraren.

## <a name="channel-states-and-billing"></a><a id="states"></a>Kanaltillstånd och fakturering
Möjliga värden för det aktuella tillståndet för en kanal är:

* **Stoppad**: Detta är kanalens ursprungliga tillstånd efter att den har skapats. I det här läget kan kanalegenskaperna uppdateras men direktuppspelning är inte tillåtet.
* **Start**: Kanalen startas. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet. Om ett fel inträffar återgår kanalen till **tillståndet Stoppad.**
* **Kör**: Kanalen kan bearbeta livestreamar.
* **Stoppa**: Kanalen stoppas. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.
* **Ta bort**: Kanalen tas bort. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.

Följande tabell visar hur kanaltillstånden mappas till faktureringsläget.

| Kanaltillstånd | Indikatorer för portalgränssnittet | Faktureras? |
| --- | --- | --- |
| **Start** |**Start** |Nej (tillfälligt tillstånd) |
| **Körs** |**Klar** (inga program som körs)<p><p>eller<p>**Streaming** (minst ett program som körs) |Ja |
| **Stoppas** |**Stoppas** |Nej (tillfälligt tillstånd) |
| **Stoppad** |**Stoppad** |Inga |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Dold textning och annonsinfogning
I följande tabell visas standarder som stöds för dold textning och annonsinfogning.

| Standard | Anteckningar |
| --- | --- |
| CEA-708 och MKB-608 (708/608) |CEA-708 och EIA-608 är standarder för dold textning för USA och Kanada.<p><p>För närvarande stöds textning endast om den finns i den kodade indataströmmen. Du måste använda en direkt mediakodare som kan infoga 608 eller 708-bildtexter i den kodade strömmen som skickas till Media Services. Media Services levererar innehållet med infogade bildtexter till tittarna. |
| TTML inuti .ismt (Textspår för jämn strömning) |Med dynamisk paketering av Media Services kan dina klienter strömma innehåll i något av följande format: DASH, HLS eller Smooth Streaming. Men om du intar fragmenterad MP4 (Smooth Streaming) med bildtexter inuti .ismt (Smooth Streaming textspår), kan du leverera strömmen till endast Smooth Streaming-klienter. |
| SCTE-35 |SCTE-35 är ett digitalt signalsystem som används för att cue reklam insättning. Nedströms mottagare använder signalen för att skarva reklam i strömmen för den tilldelade tiden. SCTE-35 måste skickas som ett glest spår i indataströmmen.<p><p>För närvarande är det enda dataflödesformat som stöds som innehåller annonssignaler fragmenterad MP4 (Smooth Streaming). Det enda utdataformat som stöds är också Jämn direktuppspelning. |

## <a name="considerations"></a><a id="considerations"></a>Överväganden
När du använder en lokal live-kodare för att skicka en flerbitränström till en kanal gäller följande begränsningar:

* Se till att du har tillräckligt med gratis Internet-anslutning för att skicka data till de introllade punkterna.
* Om du använder en sekundär intj.-URL krävs ytterligare bandbredd.
* Den inkommande multibitrate-strömmen kan ha högst 10 videokvalitetsnivåer (lager) och högst 5 ljudspår.
* Den högsta genomsnittliga bitrate för någon av videokvalitetsnivåer bör vara under 10 Mbps.
* Summan av de genomsnittliga bithastigheterna för alla video- och ljudströmmar bör vara under 25 Mbit/s.
* Du kan inte ändra indataprotokollet medan kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du kan inta en enda bithastighet i kanalen. Men eftersom kanalen inte bearbetar strömmen får klientprogrammen också en enda bithastighetsström. (Vi rekommenderar inte det här alternativet.)

Här är andra överväganden som rör att arbeta med kanaler och relaterade komponenter:

* Ring metoden **Återställ** på kanalen varje gång du konfigurerar om live-kodaren. Innan du återställer kanalen måste du stoppa programmet. När du har återställt kanalen startar du om programmet.

  > [!NOTE]
  > När du startar om programmet måste du associera det med en ny tillgång och skapa en ny positionerare. 
  
* En kanal kan bara stoppas när **den** är i körläge och alla program på kanalen har stoppats.
* Som standard kan du bara lägga till fem kanaler i ditt Media Services-konto. Mer information finns i [Kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du faktureras bara när din **Running** kanal är i körläge. Mer information finns i avsnittet [Kanaltillstånd och fakturering.](media-services-live-streaming-with-onprem-encoders.md#states)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="feedback"></a>Feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Rekommenderas på lokala kodare](media-services-recommended-encoders.md)

[Azure Media Services fragmenterad MP4 liv intag specifikation](../media-services-fmp4-live-ingest-overview.md)

[Översikt över Azure Media Services och vanliga scenarier](media-services-overview.md)

[Koncept för Media Services](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
