---
title: Livestreaming med Azure Media Services för att skapa flerbitrateströmmar | Microsoft-dokument
description: I det här avsnittet beskrivs hur du konfigurerar en kanal som tar emot en enda bitrate livestream från en lokal kodare och sedan utför live-kodning till adaptiv bithastighetsström med Media Services.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 6210d6ee4877c6ba84178340cf0a6610e402da31
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641106"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter

> [!NOTE]
> Från och med den 12 maj 2018 stöder livekanalerna inte längre rtp/MPEG-2-transportströmmens introllningsprotokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmenterade MP4-protokoll (Smooth Streaming).

## <a name="overview"></a>Översikt
I Azure Media Services (AMS) representerar en **kanal** en pipeline för bearbetning av direktuppspelning av innehåll. En **kanal** tar emot live-indataströmmar på ett av två sätt:

* En lokal live-kodare skickar en enbitrate-ström till kanalen som är aktiverad för att utföra live-kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (Fragmenterad MP4). Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.
* En lokal live-kodare skickar en multibitrate **RTMP** eller **Smooth Streaming** (Fragmented MP4) till kanalen som inte är aktiverad för att utföra live-kodning med AMS. De intövda strömmarna passerar genom **Kanal**erna utan ytterligare bearbetning. Den här metoden kallas **genomströmning**. Du kan använda följande live-kodare som matar ut multibitrate Smooth Streaming: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. Följande live-kodare matar ut RTMP: [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek-kodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Att använda en genomströmningsmetod är det mest ekonomiska sättet att göra livestreaming.
  > 
  > 

Från och med mediatjänsterna 2.10-versionen kan du när du skapar en kanal ange på vilket sätt du vill att kanalen ska ta emot indataströmmen och om du vill att kanalen ska utföra livekodning av din ström eller inte. Du kan välja mellan två alternativ:

* **Ingen** – Ange det här värdet om du planerar att använda en lokal live-kodare som ska mata ut flerbitrerad ström (en direktström). I det här fallet gick den inkommande strömmen vidare till utdata utan kodning. Detta är beteendet hos en kanal innan 2.10 släpps.  Mer detaljerad information om hur du arbetar med kanaler av den här typen finns i [Direktuppspelning med lokala kodare som skapar flerbitratströmmar](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda din enda bithastighetsstream till flerbitrateström. Tänk på att det finns en faktureringseffekt för live-kodning och du bör komma ihåg att om du lämnar en direkt kodningskanal i tillståndet "Kör" medför faktureringsavgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när ditt livestreamingevenemang är klart för att undvika extra timkostnader.

> [!NOTE]
> I det här avsnittet beskrivs attribut för kanaler som är**Standard** aktiverade för live-kodning ( standardkodningstyp). Information om hur du arbetar med kanaler som inte är aktiverade för live-kodning finns i [Direktuppspelning med lokala kodare som skapar flerbitratströmmar](media-services-live-streaming-with-onprem-encoders.md).
> 
> Se till att granska avsnittet [Överväganden.](media-services-manage-live-encoder-enabled-channels.md#Considerations)
> 
> 

## <a name="billing-implications"></a>Konsekvenser för fakturering
En direktkodningskanal börjar faktureras så fort det är tillståndsövergångar till "Körs" via API:et.   Du kan också visa tillståndet i Azure-portalen eller ihttps://aka.ms/amse)Azure Media Services Explorer-verktyget ( .

Följande tabell visar hur kanaltillstånd mappas till faktureringstillstånd i API- och Azure-portalen. Tillstånden skiljer sig något mellan API och Portal UX. Så snart en kanal är i tillståndet "Kör" via API:et, eller i tillståndet "Klar" eller "Streaming" i Azure-portalen, är faktureringen aktiv.
Om du vill stoppa kanalen från att fakturera dig vidare måste du stoppa kanalen via API:et eller i Azure-portalen.
Du är ansvarig för att stoppa dina kanaler när du är klar med direktkodningskanalen.  Om du inte stoppar en kodningskanal kommer faktureringen att fortsätta.

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanallägen och hur de mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden inkluderar:

* **Stoppad**. Det här är kanalens ursprungliga tillstånd efter att den har skapats (såvida inte autostart har valts i portalen.) Ingen fakturering sker i det här tillståndet. I det här läget kan kanalegenskaperna uppdateras men direktuppspelning är inte tillåtet.
* **Start**. Kanalen startas. Ingen fakturering sker i det här tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta livestreamar. Det är nu faktureringsanvändning. Du måste stoppa kanalen för att förhindra ytterligare fakturering. 
* **Stoppa**. Kanalen stoppas. Ingen fakturering sker i det här tillfälliga tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.
* **Ta bort**. Kanalen tas bort. Ingen fakturering sker i det här tillfälliga tillståndet. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläget. 

| Kanaltillstånd | Indikatorer för portalgränssnitt | Är det fakturering? |
| --- | --- | --- |
| Startar |Startar |Nej (tillfälligt tillstånd) |
| Körs |Klar (inga program som körs)<br/>eller<br/>Streaming (minst ett program som körs) |JA |
| Stoppas |Stoppas |Nej (tillfälligt tillstånd) |
| Stoppad |Stoppad |Inga |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisk avstängning för oanvända kanaler
Från och med den 25 januari 2016 har Media Services distribuerat en uppdatering som automatiskt stoppar en kanal (med live-kodning aktiverad) efter att den har körts i oanvänt tillstånd under en längre tid. Detta gäller kanaler som inte har några aktiva program och som inte har fått något indatabidragsflöde under en längre tid.

Tröskeln för en outnyttjad period är nominellt 12 timmar, men kan komma att ändras.

## <a name="live-encoding-workflow"></a>Arbetsflöde för direktkodning
Följande diagram representerar ett arbetsflöde för direktuppspelning där en kanal tar emot en enda bithastighetsström i något av följande protokoll: RTMP eller Smooth Streaming; Den kodar sedan strömmen till en multibitrate-ström. 

![Live-arbetsflöde][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Vanligt scenario för direktuppspelning
Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar.
>
> Det finns en faktureringseffekt för live-kodning och du bör komma ihåg att om du lämnar en direktkodningskanal i tillståndet "Köra" debiteras faktureringsavgifter varje timme. Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när ditt livestreamingevenemang är klart för att undvika extra timkostnader. 

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal live-kodare som kan mata ut en **enda** bithastighetsström i något av följande protokoll: RTMP eller Smooth Streaming. 

    Det här steget kan också utföras när du har skapat din kanal.
2. Skapa och starta en kanal. 
3. Hämta kanalens infognings-URL. 

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL. 

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program. 

    När du använder Azure-portalen skapar du också en tillgång när du skapar ett program. 

    När du använder .NET SDK eller REST måste du skapa en tillgång och ange att använda den här tillgången när du skapar ett program. 
6. Publicera den tillgång som är associerad med programmet.   

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**. 

7. Starta programmet när du är redo att påbörja strömning och arkivering.
8. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
10. Ta bort programmet (och ta eventuellt bort tillgången).   

> [!NOTE]
> Det är mycket viktigt att inte glömma att stoppa en Live Encoding Channel. Tänk på att det finns en faktureringseffekt per timme för live-kodning och du bör komma ihåg att om du lämnar en direktkodningskanal i tillståndet "Kör" medför faktureringsavgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när ditt livestreamingevenemang är klart för att undvika extra timkostnader. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Kanalens inmatningskonfigurationer (inmatning)
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Inta direktuppspelningsprotokoll
Om **kodartypen** är inställd på **Standard**är giltiga alternativ:

* En enda bithastighet **RTMP**
* En liten bithastighet **fragmenterad MP4** (jämn strömning)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP med enkel bithastighet
Överväganden:

* Den inkommande strömmen kan inte innehålla video med flera bitar
* Videoströmmen ska ha en genomsnittlig bithastighet under 15 Mbit/s
* Ljudströmmen bör ha en genomsnittlig bithastighet under 1 Mbit/s
* Följande är de codec-enheter som stöds:
* MPEG-4 AVC / H.264 Video
* Baslinje, Huvud, Hög profil (8-bitars 4:2:0)
* Hög 10-profil (10-bitars 4:2:0)
* Hög 422-profil (10-bitars 4:2:2)
* MPEG-2 AAC-LC-ljud
* Mono, Stereo, Surround (5.1, 7.1)
* 44,1 kHz samplingsfrekvens
* MPEG-2 stil ADTS-förpackning
* Rekommenderade kodare inkluderar:
* [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md)
* Live-kodare för Flash Media

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
Typiskt användningsfall:

Använd lokala live-kodare från leverantörer som Elemental Technologies, Ericsson, Ateme, Envivio för att skicka indataströmmen via det öppna internet till ett närliggande Azure-datacenter.

Överväganden:

Samma som för [enstaka bithastighet RTMP](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Andra överväganden
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Maximal upplösning för den inkommande videoströmmen är 1920x1080, och högst 60 fält/sekund om den flätas samman, eller 30 bildrutor/sekund om den är progressiv.

### <a name="ingest-urls-endpoints"></a>Inta webbadresser (slutpunkter)
En kanal innehåller en indataslutpunkt (inmatnings-URL) som du anger i live-kodaren, så att kodaren kan skicka strömmar till dina kanaler.

Du kan hämta de intjest webbadresserna när du har skapat en kanal. För att hämta dessa webbadresser behöver kanalen **Running** inte vara i körläge. När du är redo att börja skicka data till kanalen måste **den** vara i körläge. När kanalen börjar inta data kan du förhandsgranska flödet via förhandsgranskningsadressen.

Du har möjlighet att inta fragmenterad MP4 (Smooth Streaming) live stream via en TLS-anslutning. Om du vill börja använda över TLS kontrollerar du att den intöste URL:en uppdateras till HTTPS. För närvarande stöder AMS inte TLS med anpassade domäner.  

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som får publicera video till den här kanalen. Tillåtna IP-adresser kan anges som antingen en enda IP-adress (t.ex. "10.0.0.1"), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22"), eller ett IP-intervall med en IP-adress och en prickad decimalundernätmask (till exempel "10.0.0.1(255.255.252.0)").

Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

## <a name="channel-preview"></a>Förhandsgranskning av kanal
### <a name="preview-urls"></a>Förhandsgranska webbadresser
Kanalerna tillhandahåller en förhandsgranskningsslutpunkt (förhandsgransknings-URL) som du använder för att förhandsgranska och validera flödet innan du bearbetar och levererar vidare.

Du kan hämta förhandsgransknings-URL:en när du skapar kanalen. För att få webbadressen behöver kanalen inte **Running** vara i körläge.

När kanalen börjar inta data kan du förhandsgranska din ström.

> [!NOTE]
> För närvarande kan förhandsgranskningsströmmen endast levereras i fragmenterat MP4-format (Smooth Streaming) oavsett den angivna indatatypen.  Du kan använda en spelare som finns i Azure-portalen för att visa din ström.
> 
> 

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser har angetts kommer någon IP-adress att tillåtas. Tillåtna IP-adresser kan anges som antingen en enda IP-adress (t.ex. "10.0.0.1"), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22"), eller ett IP-intervall med en IP-adress och en prickad decimalundernätmask (till exempel "10.0.0.1(255.255.252.0)").

## <a name="live-encoding-settings"></a>Inställningar för livekodning
I det här avsnittet beskrivs hur inställningarna för live-kodaren i kanalen kan justeras när **kodningstypen** för en kanal är inställd på **Standard**.

> [!NOTE]
> Ditt bidragsflöde kan bara innehålla ett enda ljudspår – intag av flera ljudspår stöds för närvarande inte. När du gör live-kodning med [lokala live-kodningar](media-services-live-streaming-with-onprem-encoders.md)kan du skicka en bidragsfeed i Smooth Streaming-protokollet som innehåller flera ljudspår.
> 
> 

### <a name="ad-marker-source"></a>Annonsmarkörkälla
Du kan ange källan för annonsmarkörsignaler. Standardvärdet är **Api**, vilket anger att den aktiva kodaren i kanalen ska lyssna på ett asynkront **AD Marker API**.

### <a name="cea-708-closed-captions"></a>CEA 708 Undertexter
En valfri flagga som talar om för den aktiva kodaren att ignorera alla CEA 708-bildtexter som är inbäddade i den inkommande videon. När flaggan är inställd på false (standard), kommer kodaren att identifiera och åter infoga CEA 708-data i utdatavideoströmmarna.

#### <a name="index"></a>Index
Vi rekommenderar att du skickar in ett enda program transportström (SPTS). Om indataströmmen innehåller flera program tolkar live-kodaren i kanaltabellen (PMT) i indata, identifierar de indata som har ett flödestypnamn för MPEG-2 AAC ADTS eller AC-3 System-A eller AC-3 System-B eller MPEG-2 Private PES eller MPEG-1 Audio eller MPEG-2 Audio och ordnar dem i den ordning som anges i PMT. Det nollbaserade indexet används sedan för att hämta n-posten i det arrangemanget.

#### <a name="language"></a>Språk
Ljudströmmens språkidentifierare överensstämmer med ISO 639-2, till exempel ENG. Om det inte finns, är standard UND (odefinierad).

### <a name="system-preset"></a><a id="preset"></a>Systemförinställning
Anger den förinställning som ska användas av strömgivaren i den här kanalen. För närvarande är det enda tillåtna värdet **Default720p** (standard).

**Default720p** kodar videon till följande 6 lager.

#### <a name="output-video-stream"></a>Utdatavideoström

| Bitrate | Bredd | Höjd | MaxFPS (maxfps) | Profil | Namn på utdataström |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hög |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hög |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hög |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hög |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hög |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Utdataljudström

Ljudet kodas till stereo AAC-LC med 128 kbps, samplingsfrekvens på 48 kHz.

## <a name="signaling-advertisements"></a>Signalering Annonser
När kanalen har aktiverat Direktkodning har du en komponent i pipelinen som bearbetar video och kan ändra den. Du kan signalera för kanalen att infoga griffeltavlor och /eller annonser i den utgående adaptiva bitrate-strömmen. Skiffer är fortfarande bilder som du kan använda för att täcka upp indata live-feed i vissa fall (till exempel under en kommersiell paus). Reklamsignaler, är tidssynkronerade signaler som du bäddar in i den utgående strömmen för att tala om för videospelaren att vidta särskilda åtgärder – till exempel för att byta till en annons vid lämplig tidpunkt. Se den här [bloggen](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) för en översikt över SCTE-35 signalmekanism som används för detta ändamål. Nedan är ett typiskt scenario som du kan implementera i din live-händelse.

1. Be tittarna få en pre-event-bild innan evenemanget startar.
2. Låt tittarna få en post-event-bild när evenemanget är.
3. Låt tittarna få en FELHÄNDELSEbild om det uppstår problem under evenemanget (till exempel strömavbrott på arenan).
4. Skicka en AD-BREAK-bild för att dölja livehändelseflödet under en kommersiell paus.

Följande är de egenskaper som du kan ställa in när du signalerar annonser. 

### <a name="duration"></a>Varaktighet
Varaktigheten, i sekunder, av den kommersiella pausen. Detta måste vara ett plusvärde som inte är noll för att kunna starta den kommersiella pausen. När en kommersiell paus pågår och varaktigheten är inställd på noll med CueId som matchar den pågående kommersiella rasten, avbryts den pausen.

### <a name="cueid"></a>CueId (på andra)
Ett unikt ID för den kommersiella pausen, som ska användas av nedströmsprogram för att vidta lämpliga åtgärder. Måste vara ett positivt heltal. Du kan ange det här värdet på ett slumpmässigt positivt heltal eller använda ett uppströmssystem för att spåra cue-ID:erna. Se till att normalisera eventuella ID:er till positiva heltal innan du skickar via API:et.

### <a name="show-slate"></a>Visa skiffer
Valfri. Signalerar live-kodaren för att växla till [standardskifferbilden](media-services-manage-live-encoder-enabled-channels.md#default_slate) under en kommersiell paus och dölja den inkommande videofeeden. Ljudet är också avstängt under skiffer. Standard är **falskt**. 

Den bild som används är den som anges via standardvärdes-ID-egenskapen för skiffertillgångar när kanalen skapades. Skiffern kommer att sträckas ut för att passa visningsbildens storlek. 

## <a name="insert-slate--images"></a>Infoga skifferbilder
Den levande kodaren i kanalen kan signaleras för att växla till en skifferbild. Det kan också signaleras att avsluta en pågående skiffer. 

Den levande kodaren kan konfigureras för att växla till en skifferbild och dölja den inkommande videosignalen i vissa situationer , till exempel under en annonspaus. Om en sådan griffeltavla inte är konfigurerad maskeras inte indatavideo under annonspausen.

### <a name="duration"></a>Varaktighet
Längden på skiffer i sekunder. Detta måste vara ett plusvärde som inte är noll för att skiffern ska kunna startas. Om det finns en pågående skiffer, och en varaktighet på noll anges, då den pågående skiffer kommer att avslutas.

### <a name="insert-slate-on-ad-marker"></a>Infoga griffeltavla på annonsmarkör
När den här inställningen är true konfigureras live-kodaren så att en skifferbild infogas under en annonsbrytning. Standardvärdet är True. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Tillgångs-ID för standardskiffer

Valfri. Anger tillgångs-ID för Medietjänsttillgången som innehåller skifferbilden. Standard är null. 


> [!NOTE] 
> Innan kanalen skapas bör skifferbilden med följande begränsningar laddas upp som en dedikerad tillgång (inga andra filer ska finnas i den här tillgången). Den här bilden används endast när den levande kodaren infogar en griffeltavla på grund av en annonspaus, eller uttryckligen har signalerats för att infoga en griffeltavla. Det finns för närvarande inget alternativ för att använda en anpassad bild när live-kodaren går in i ett sådant tillstånd för att gå förlorad för insignalen. Du kan rösta på den här funktionen [här](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Högst 1920x1080 i upplösning.
* Högst 3 Mbyte i storlek.
* Filnamnet måste ha ett *.jpg-tillägg.
* Bilden måste överföras till en tillgång som den enda AssetFile i tillgången och denna AssetFile ska markeras som den primära filen. Tillgången kan inte lagringskrypteras.

Om **standard-griffeltavlans tillgångs-ID** inte har angetts och **en griffeltavla på annonsmarkören** är **true**används en standardavbildning för Azure Media Services för att dölja indatavideoströmmen. Ljudet är också avstängt under skiffer. 

## <a name="channels-programs"></a>Kanalens program
En kanal är associerad till program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Kanal- och programrelationen är mycket lik traditionella medier där en kanal har en konstant ström av innehåll och ett program är begränsat till en tidsinnöjd händelse på den kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiv fönsterlängd dikterar också det maximala antalet tid klienter kan söka tillbaka i tiden från den aktuella live-position. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång som lagrar det strömmade innehållet. En tillgång mappas till en blockblolobbehållare i Azure Storage-kontot och filerna i tillgången lagras som blobbar i den behållaren. Om du vill publicera programmet så att kunderna kan visa strömmen måste du skapa en OnDemand-positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal stöder upp till tre program som körs samtidigt så att du kan skapa flera arkiv med samma inkommande ström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta i stället ett nytt program för varje händelse enligt beskrivningen i avsnittet Programmering av livestreamingprogram.

Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort programmet och tar sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först. 

Även efter att du har stoppat och tagit bort programmet skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge som du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Få en miniatyrförhandsvisning av en live-feed
När Live Encoding är aktiverat kan du nu få en förhandsgranskning av liveflödet när den når kanalen. Detta kan vara ett värdefullt verktyg för att kontrollera om ditt liveflöde faktiskt når kanalen. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Kanallägen och hur tillstånd mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden inkluderar:

* **Stoppad**. Detta är det ursprungliga tillståndet för kanalen efter dess tillkomst. I det här läget kan kanalegenskaperna uppdateras men direktuppspelning är inte tillåtet.
* **Start**. Kanalen startas. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta livestreamar.
* **Stoppa**. Kanalen stoppas. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.
* **Ta bort**. Kanalen tas bort. Inga uppdateringar eller direktuppspelning tillåts under det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläget. 

| Kanaltillstånd | Indikatorer för portalgränssnitt | Faktureras? |
| --- | --- | --- |
| Startar |Startar |Nej (tillfälligt tillstånd) |
| Körs |Klar (inga program som körs)<br/>eller<br/>Streaming (minst ett program som körs) |Ja |
| Stoppas |Stoppas |Nej (tillfälligt tillstånd) |
| Stoppad |Stoppad |Inga |

> [!NOTE]
> För närvarande är kanalstartgenomsnittet ca 2 minuter, men ibland kan det ta upp till 20 + minuter. Kanalåterställning kan ta upp till 5 minuter.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Överväganden
* När en **kanal** av standardkodningstyp drabbas av förlust av indatakälla/bidragsflöde kompenserar den för den genom att ersätta källvideo/ljud med en felskiffer och tystnad. Kanalen kommer att fortsätta att avge en skiffer tills indata-/bidragsflödet återupptas. Vi rekommenderar att en direkt kanal inte lämnas i ett sådant tillstånd längre än 2 timmar. Utöver den punkten är beteendet för kanalen vid återanslutning av indata inte garanterat, inte heller dess beteende som svar på ett Återställningskommando. Du måste stoppa kanalen, ta bort den och skapa en ny.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Ring metoden **Återställ** på kanalen varje gång du konfigurerar om live-kodaren. Innan du återställer kanalen måste du stoppa programmet. När du har återställt kanalen startar du om programmet.
* En kanal kan bara stoppas när den är i körläge och alla program på kanalen har stoppats.
* Som standard kan du bara lägga till fem kanaler i ditt Media Services-konto. Detta är en mjuk kvot för alla nya konton. Mer information finns i [Kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du faktureras bara när kanalen **Running** är i körläge. Mer information finns i [det här](media-services-manage-live-encoder-enabled-channels.md#states) avsnittet.
* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. 
* Se till att ha den slutpunkt för direktuppspelning som du vill strömma innehåll från i läget **Kör.**
* Kodning förinställning använder begreppet "max bildhastighet" på 30 fps. Så om ingången är 60fps/59.94i, är indataramar tappade / de-sammanflätade till 30/29.97 fps. Om indata är 50fps/50i, är indataramar tappade / de-sammanflätade till 25 fps. Om ingången är 25 fps, är produktionen kvar på 25 fps.
* Glöm inte att stoppa dina kanaler när du är klar. Om du inte gör det fortsätter faktureringen.

## <a name="known-issues"></a>Kända problem
* Kanalstarttiden har förbättrats till i genomsnitt 2 minuter, men i tider av ökad efterfrågan kan det fortfarande ta upp till 20+ minuter.
* Skiffer bilder bör överensstämma med de begränsningar som beskrivs [här](media-services-manage-live-encoder-enabled-channels.md#default_slate). Om du försöker skapa en kanal med en standardskiffer som är större än 1920x1080, kommer begäran så småningom att fela.
* En gång till.... glöm inte att stoppa dina kanaler när du är klar streaming. Om du inte gör det fortsätter faktureringen.

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna en supportbiljett genom att navigera till [Ny supportförfrågan](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Leverera händelser via strömning live med Azure Media Services](media-services-overview.md)

[Skapa kanaler som utför livekodning från en singe bitrate till adaptiv bithastighetsström med Portal](media-services-portal-creating-live-encoder-enabled-channel.md)

[Skapa kanaler som utför livekodning från en singe bitrate till adaptiv bithastighetsström med .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Hantera kanaler med REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Begrepp för medietjänster](media-services-concepts.md)

[Fragmenterad MP4 Live-specifikation för Azure Media Services](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

