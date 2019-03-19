---
title: Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter | Microsoft Docs
description: 'Det här avsnittet beskriver hur du konfigurerar en kanal som tar emot en direktsänd dataström med enkel bithastighet från en lokala kodare och utför sedan live encoding till ström med anpassningsbar bithastighet med Media Services. Dataströmmen kan sedan levereras till klienten uppspelning program via en eller flera-slutpunkter för direktuppspelning, med någon av följande protokoll för anpassningsbar strömning: HLS, Smooth Stream, MPEG DASH.'
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
ms.author: juliako;anilmur
ms.openlocfilehash: c168182f0b34329ed3e72e90ce86456dfbe210ca
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189860"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter

> [!NOTE]
> Startar den 12 maj 2018 livekanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) inmatningsprotokollen.

## <a name="overview"></a>Översikt
I Azure Media Services (AMS), en **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En **kanal** tar emot inkommande direktsändningar i ett av två sätt:

* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.
* En lokal livekodare skickar flera bithastigheter **RTMP** eller **Smooth Streaming** (fragmenterad MP4) till den kanal som inte är aktiverad för att utföra direktsänd kodning med AMS. Infogade strömmarna passerar genom **kanal**utan vidare bearbetning. Den här metoden anropas **direkt**. Du kan använda följande livekodare som Smooth Streaming med flera bithastigheter: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco och Elemental. I följande livekodare RTMP: Adobe Flash Media Live Encoder (FMLE), Telestream Wirecast, Haivision, Teradek och Tricaster-kodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning.
  > 
  > 

Från och med Media Services 2.10, när du skapar en kanal, kan du ange i vilken grad du vill använda för din kanal tar emot Indataströmmen och om huruvida du vill använda för kanalen för att utföra live encoding av strömmen. Du kan välja mellan två alternativ:

* **Ingen** – ange det här värdet om du planerar att använda en lokal livekodare som kommer utdata med flera bithastigheter stream (en direkt stream). I det här fallet den inkommande dataströmmen skickas vidare till utdata utan kodning. Det här är beteendet för en kanal innan 2,10 versionen.  Mer detaljerad information om hur du arbetar med kanaler för den här typen finns i [direktsänd strömning med lokala kodare som skapar strömmar med flera bithastigheter](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda din direktsänd dataström med enkel bithastighet till en dataström med multibithastighet. Tänk på att det finns en fakturering inverkan för live encoding och du bör komma ihåg att lämna en livekanal med kodning i tillståndet ”körs” debiteras debiterade avgifterna.  Vi rekommenderar att du omedelbart stoppa dina kanaler som körs när din liveströmmat evenemang är klar att undvika extra debitering.

> [!NOTE]
> Det här avsnittet beskrivs attribut kanaler som är aktiverade för att utföra live encoding (**Standard** kodningstyp). Information om hur du arbetar med kanaler som inte är aktiverade för att utföra live encoding finns i [direktsänd strömning med lokala kodare som skapar strömmar med flera bithastigheter](media-services-live-streaming-with-onprem-encoders.md).
> 
> Se till att granska den [överväganden](media-services-manage-live-encoder-enabled-channels.md#Considerations) avsnittet.
> 
> 

## <a name="billing-implications"></a>Fakturering effekter
En livekanal med kodning börjar fakturering som ändrar status till ”körs” är det via API: et.   Du kan också visa tillståndet i Azure portal eller i Azure Media Services Explorer-verktyget (https://aka.ms/amse).

I följande tabell visar hur kanaltillstånd mappas till fakturering tillstånd i API: et och Azure-portalen. Tillstånd kan variera mellan API och -portalen UX. När en kanal är i tillståndet ”körs” via API: et eller statusen ”klar” eller ”strömning” i Azure-portalen, aktiveras fakturering.
Du måste stoppa kanalen via API: et eller i Azure portal om du vill stoppa kanalen från fakturering du ytterligare.
Du ansvarar för att stoppa dina kanaler när du är klar med live encoding kanalen.  Det gick inte att stoppa en kanal för kodning leder fortsatt fakturering.

### <a id="states"></a>Kanaltillstånd och hur de mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppad**. Det här är starttillståndet för kanalen när den har skapandet (såvida inte autostart har valts i portalen.) Ingen debitering sker i det här tillståndet. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**. Kanalen startas. Ingen debitering sker i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar. Det nu Debiterad användning. Du måste stoppa kanalen för att förhindra att ytterligare fakturering. 
* **Stoppa**. Kanalen stoppas. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Tar bort**. Kanalen tas bort. Ingen debitering sker i den här tillfälligt tillstånd. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläge. 

| Kanaltillstånd | Portalgränssnittsindikatorer | Är det fakturering? |
| --- | --- | --- |
| Startar |Startar |Nej (övergångsläge) |
| Körs |Klart (inga program körs)<br/>eller<br/>Strömmar (minst ett program körs) |JA |
| Stoppas |Stoppas |Nej (övergångsläge) |
| Stoppad |Stoppad |Nej |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisk avstängning av för oanvända kanaler
Från och med den 25 januari 2016 Media Services distribueras en uppdatering som stoppas automatiskt en kanal (med direktsänd kodning aktiverat) när den har körts i ett tillstånd som inte används under en lång period. Detta gäller för kanaler som har inga aktiva program och som inte har fått ett inkommande bidrag feed under en längre tid.

Tröskeln under en oanvända är praktiskt taget 12 timmar, men kan ändras.

## <a name="live-encoding-workflow"></a>Live Encoding-arbetsflöde
Följande diagram representerar en live arbetsflöde där en kanal tar emot en enda bithastighet i något av följande protokoll: RTMP eller Smooth Streaming; den kodar sedan strömma till en dataström med multibithastighet. 

![Live-arbetsflöde][live-overview]

## <a id="scenario"></a>Vanligt Scenario för liveuppspelning
Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived@microsoft.com om du behöver köra en kanal under en längre tidsperiod. Det finns en fakturering inverkan för live encoding och du bör komma ihåg att lämna en livekanal med kodning i tillståndet ”körs” debiteras per timme debiterade avgifterna.  Vi rekommenderar att du omedelbart stoppa dina kanaler som körs när din liveströmmat evenemang är klar att undvika extra debitering. 

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som kan mata ut en **enda** bithastighet i något av följande protokoll: RTMP eller Smooth Streaming. 

    Det här steget kan också utföras när du har skapat din kanal.
2. Skapa och starta en kanal. 
3. Hämta kanalens infognings-URL. 

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL. 

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program. 

    När du använder Azure portal, skapar skapa ett program även en tillgång. 

    När du använder .NET SDK eller REST måste du skapa en tillgång och ange om du vill använda den här tillgången när du skapar ett Program. 
6. Publicera tillgången som är associerade med programmet.   

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**. 

7. Starta programmet när du är redo att påbörja strömning och arkivering.
8. Som alternativ kan livekodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
10. Ta bort programmet (och ta eventuellt bort tillgången).   

> [!NOTE]
> Det är viktigt att du inte glömmer att stoppa en livekanal med kodning. Tänk på att det finns ett timpris för fakturering inverkan för direktsänd kodning och du bör komma ihåg att lämna en livekanal med kodning i tillståndet ”körs” debiteras debiterade avgifterna.  Vi rekommenderar att du omedelbart stoppa dina kanaler som körs när din liveströmmat evenemang är klar att undvika extra debitering. 
> 
> 

## <a id="channel"></a>Kanalen användarens indata (hämta) konfigurationer
### <a id="Ingest_Protocols"></a>Mata in strömmande protokoll
Om den **typ av kodare** är inställd på **Standard**, giltiga alternativ är:

* Enkel bithastighet **RTMP**
* Enkel bithastighet **fragmenterad MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>RTMP med enkel bithastighet
Överväganden:

* Den inkommande dataströmmen får inte innehålla flera bithastigheter video
* Videoströmmen bör ha ett genomsnittligt bithastighet under 15 Mbit/s
* Ljudströmmen bör ha ett genomsnittligt bithastighet nedan 1 Mbit/s
* Nedan följer koder som stöds:
* MPEG-4 AVC / H.264 Video
* Baslinje, Main, hög profil (8-bitars 4:2:0)
* Hög 10-profil (10-bitars 4:2:0)
* Hög 422-profil (10-bitars 4:2:2)
* MPEG-2 AAC-LC ljud
* Mono, Stereo, omge (5.1, 7.1)
* 44,1 samplingsfrekvensen
* MPEG-2-style ADTS paketering
* Rekommenderade kodare är:
* Telestream Wirecast
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
Vanligt användningsfall:

Använd en lokal livekodare från leverantörer som Elemental tekniker, Ericsson, Ateme, Envivio skickar Indataströmmen via det öppna internet i närheten Azure-datacenter.

Överväganden:

Samma som för [RTMP med enkel bithastighet](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Annat att tänka på
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Maximal upplösning för inkommande videoströmmen är 1920 x 1080, och högst 60 fält per sekund om sammanflätad eller 30 bilder per sekund om progressiva.

### <a name="ingest-urls-endpoints"></a>Mata in URL: er (slutpunkter)
En kanal som innehåller en slutpunkt för indata (infognings-URL) att du anger i live-kodare så kodaren kan skicka dataströmmar till dina kanaler.

Du kan hämta URL: er för inmatning när du skapar en kanal. Kanalen har för att få dessa URL: er kan inte finnas i den **kör** tillstånd. När du är redo att börja skicka data till kanalen kan det finnas i den **kör** tillstånd. När kanalen startar mata in data, kan du förhandsgranska dataströmmen via förhandsgransknings-URL.

Du har en möjlighet att mata in fragmenterad MP4 (Smooth Streaming) live stream via en SSL-anslutning. För att mata in via SSL, se till att uppdatera URL: en inmatning till HTTPS. För närvarande stöder AMS inte SSL med anpassade domäner.  

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som får publicera video i den här kanalen. Tillåtna IP kan adresser anges som en enda IP-adress (t.ex. ”10.0.0.1”), ett IP-intervall med IP-adress och en CIDR-nätmask (exempelvis 10.0.0.1/22) eller en IP-intervall med en IP-adress och en undernätsmask med punktavgränsad decimalform (till exempel , ' 10.0.0.1(255.255.252.0)').

Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

## <a name="channel-preview"></a>Förhandsgranskning av kanal
### <a name="preview-urls"></a>Granska URL: erna
Kanaler ger förhandsgranskningsslutpunkten (förhandsgransknings-URL) som används för att förhandsgranska och validera dataströmmen inför vidare behandling och leverans.

Du kan hämta förhandsgransknings-URL när du skapar kanalen. Kanalen har URL: en får inte finnas i den **kör** tillstånd.

När kanalen startar mata in data, kan du förhandsgranska dataströmmen.

> [!NOTE]
> Förhandsgranska dataströmmen kan för närvarande bara levereras i fragmenterad MP4 (Smooth Streaming) format oavsett angivna Indatatyp.  Du kan använda en spelare i Azure-portalen för att visa din dataström.
> 
> 

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som ska kunna ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser anges får alla IP-adresser. Tillåtna IP kan adresser anges som en enda IP-adress (t.ex. ”10.0.0.1”), ett IP-intervall med IP-adress och en CIDR-nätmask (exempelvis 10.0.0.1/22) eller en IP-intervall med en IP-adress och en undernätsmask med punktavgränsad decimalform (till exempel , ' 10.0.0.1(255.255.252.0)').

## <a name="live-encoding-settings"></a>Live encoding-inställningar
Det här avsnittet beskrivs hur du inställningarna för livekodaren i kanalen kan justeras, när den **kodningstypen** för en kanal är inställd på **Standard**.

> [!NOTE]
> Ditt bidrag flöde får bara innehålla en enda ljudspår – inmatning av flera ljudspår stöds inte för tillfället. Vid direktsänd kodning med [lokala live kodar](media-services-live-streaming-with-onprem-encoders.md), kan du skicka ett bidrag feed i Smooth Streaming-protokollet som innehåller flera ljudspår.
> 
> 

### <a name="ad-marker-source"></a>AD markör-källa
Du kan ange källan för annonsmarkörssignaler. Standardvärdet är **Api**, vilket betyder att livekodaren i kanalen bör lyssna på en asynkron **Annonsmarkörs-API**.

### <a name="cea-708-closed-captions"></a>CEA-708-undertexter
En valfri flagga som anger livekodaren att ignorera CEA-708 undertexter data inbäddade i inkommande videon. När flaggan anges till FALSKT (standard), vilken kodare som identifierar och sätt tillbaka CEA-708-data i video utdataströmmar.

#### <a name="index"></a>Index
Vi rekommenderar att skicka i ett enda program transportströmmen (SPTS). Om Indataströmmen innehåller flera program kan livekodaren i kanalen Parsar programmet kartan tabell (betalning) i angivna indata, identifierar indata som har en stream-typnamnet för MPEG-2 AAC ADTS eller AC-3 System-A eller AC-3 System-B eller MPEG-2 privata PES eller MPEG-1 Ljud- eller MPEG-2-ljud och ordnas i den ordning som anges i bet. Det nollbaserade indexet används sedan för att hämta n: e-post i den ordningen.

#### <a name="language"></a>Språk
Språk-ID för ljudström, som överensstämmer med ISO 639-2, till exempel ENG. Om den inte finns är standard i UND (odefinierat).

### <a id="preset"></a>Förinställning för systemet
Anger förinställningen som ska användas av livekodaren i den här kanalen. Det enda tillåtna värdet är för närvarande **Default720p** (standard).

Observera att om du behöver anpassade förinställningar, bör du kontakta amslived@microsoft.com.

**Default720p** kommer koda videon till följande 6 lagren.

#### <a name="output-video-stream"></a>Utdata Video Stream

| Bithastighet | Bredd | Höjd | MaxFPS | Profil | Utdatanamnet för Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Hög |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Hög |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Hög |Video_512x288_850kbps |
| 550 |384 |216 |30 |Hög |Video_384x216_550kbps |
| 200 |340 |192 |30 |Hög |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Utdata ljud Stream

Ljud kodas till stereo AAC-LC på 128 kbit/s, samplingsfrekvensen 48 kHz.

## <a name="signaling-advertisements"></a>Annonser-signalering
När din kanal har Live Encoding aktiverad, du har en komponent i din pipeline som är bearbetningen video och kan ändra den. Du kan signalera kanalen att infoga pekdatorer och/eller reklam i den utgående dataströmmen med anpassningsbar bithastighet. Bakgrundsbilder är stillbilder som du kan använda för att täcka den inkommande direktsändningen i vissa fall (exempelvis under en reklampaus). Annonserar signaler är tiden synkroniserad signaler du bädda in i den utgående dataströmmen som talar om videospelaren vidta några särskilda åtgärder – till exempel för att växla till ett meddelande vid rätt tidpunkt. Se den här [blogg](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) en översikt över SCTE 35 signaling mekanismen som används för detta ändamål. Nedan visas ett typiskt scenario kan du implementera i live-händelsen.

1. Har användarna få en bild som före HÄNDELSEN innan händelsen startar.
2. Har användarna få en bild som efter när händelsen slutar.
3. Har användarna får en FELHÄNDELSE avbildning om det finns ett problem när händelser (till exempel strömavbrott i arenan).
4. Skicka en AD-BREAK-avbildning för att dölja direktsändningen feed under en reklampaus.

Följande är de egenskaper som du kan ange när signalering annonser. 

### <a name="duration"></a>Varaktighet
Varaktighet i sekunder, för reklampaus. Detta måste vara ett positivt värde inte är noll för att starta reklampaus. När en reklampaus pågår och varaktigheten nollställs med CueId matchar pågående reklampaus, och sedan den break har avbrutits.

### <a name="cueid"></a>CueId
Ett unikt ID för reklampaus, som ska användas av nedströms program för att vidta lämpliga åtgärder. Måste vara ett positivt heltal. Du kan ange ett värde och ett slumpmässigt positivt heltal eller använda en överordnad system för att spåra stack-ID: N. Kontrollera att normalisera eventuella ID: N till positiva heltal innan du skickar via API: et.

### <a name="show-slate"></a>Visa bakgrundsbild
Valfri. Signalerar livekodaren att växla till den [standard bakgrundsbild](media-services-manage-live-encoder-enabled-channels.md#default_slate) avbildningen under en reklampaus och dölja den inkommande Videoupptagning. Ljudet är också avstängt under bakgrundsbild. Standardvärdet är **FALSKT**. 

Bilden används är den som angetts via standard bakgrundsbild tillgången Id-egenskapen vid tidpunkten för skapa kanaler. Mallen kommer att sträckas ut för att passa visningsstorlek för avbildningen. 

## <a name="insert-slate--images"></a>Infoga bakgrundsbild bilder
Livekodaren i kanalen kan signaleras att växla till en bakgrundsbild. Det kan också signaleras att avsluta en pågående bakgrundsbild. 

Live Encoding kan konfigureras för att växla till en bakgrundsbild och dölja inkommande video signalen i vissa situationer – exempelvis under en ad-break. Om en sådan bakgrundsbild inte är konfigurerad maskeras inte indatavideon under den ad-break.

### <a name="duration"></a>Varaktighet
Varaktighet för mallen i sekunder. Detta måste vara ett positivt värde inte är noll för att starta mallen. Om det finns en pågående bakgrundsbild och en varaktighet på noll har angetts, avslutas den pågående bakgrundsbild.

### <a name="insert-slate-on-ad-marker"></a>Infoga bakgrundsbild vid reklammarkör
När värdet är true, den här inställningen konfigurerar livekodaren att infoga en bakgrundsbild under en ad-break. Standardvärdet är sant. 

### <a id="default_slate"></a>Standard bakgrundsbild tillgångs-Id

Valfri. Anger tillgångs-Id för Media Services tillgång som innehåller bakgrundsbild. Standardvärdet är null. 


> [!NOTE] 
> Innan du skapar kanalen ska bakgrundsbild med följande begränsningar överföras som en dedikerad tillgång (inga andra filer bör finnas i den här tillgången). Den här bilden används endast när live Encoding infoga en bakgrundsbild på grund av en ad-break, eller har uttryckligen signalerat att infoga en bakgrundsbild. Det finns för närvarande inget alternativ för att använda en anpassad avbildning när livekodaren kommer sådana ett inkommande signal förlorat läge. Du kan rösta på den här funktionen [här](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Högst 1 920 x 1 080 i lösningen.
* Högst 3 MB i storlek.
* Filnamnet måste ha filtillägget *.jpg.
* Avbildningen måste överföras till en tillgång som den enda AssetFile eftersom tillgången och den här AssetFile bör markeras som den primära filen. Tillgången kan inte vara krypterad.

Om den **standard reservera tillgångs-Id** inte anges och **Infoga bakgrundsbild vid reklammarkör** är inställd på **SANT**, en standard-avbildning för Azure Media Services används för att dölja indatavideon Stream. Ljudet är också avstängt under bakgrundsbild. 

## <a name="channels-programs"></a>Kanalens program
En kanal är associerad till program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och Program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiveringsfönstret avgör också det maximala antalet klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program som är associerad med en tillgång som lagrar strömmas innehållet. En tillgång är mappad till en block blob-behållare i Azure Storage-kontot och filerna i tillgången lagras som blobar i behållaren. Om du vill publicera programmet så att dina kunder kan visa dataströmmen måste du skapa en OnDemand-positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera Arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta ett nytt program för varje händelse som beskrivs i avsnittet Programming Live Streaming program istället.

Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort programmet och tar sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först. 

Även efter att du har stoppat och tagit bort programmet skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge som du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Hämta en miniatyr av en live-feed
När Live Encoding är aktiverad, nu får du en förhandsgranskning av live flödet när den når kanalen. Detta kan vara ett värdefullt verktyg för att kontrollera om ditt live-flöde faktiskt når kanalen. 

## <a id="states"></a>Kanaltillstånd och hur tillstånd mappas till faktureringsläget
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppad**. Det här är starttillståndet för kanalen när den har skapats. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**. Kanalen startas. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar.
* **Stoppa**. Kanalen stoppas. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Tar bort**. Kanalen tas bort. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläge. 

| Kanaltillstånd | Portalgränssnittsindikatorer | Fakturerad? |
| --- | --- | --- |
| Startar |Startar |Nej (övergångsläge) |
| Körs |Klart (inga program körs)<br/>eller<br/>Strömmar (minst ett program körs) |Ja |
| Stoppas |Stoppas |Nej (övergångsläge) |
| Stoppad |Stoppad |Nej |

> [!NOTE]
> För närvarande kanal start medelvärdet är cirka 2 minuter, men ibland kan ta upp till 20 + minuter. Antalet återställningar av kanal kan ta upp till 5 minuter.
> 
> 

## <a id="Considerations"></a>Att tänka på
* När en kanal för **Standard** kodningstyp påträffar en förlust av inkommande källa/bidrag feed, kompenseras det den genom att ersätta det källa/ljudet med en bakgrundsbild för fel och åsidosatt inaktivitet. Kanalen kommer att fortsätta att skapa en bakgrundsbild tills indata/bidrag feed återupptar. Vi rekommenderar att en livekanal inte lämnas i ett sådant tillstånd under längre tid än 2 timmar. Beteendet för kanalen återupprättas indata är inte säkert förbi den punkten, är varken sitt beteende som svar på ett kommando för återställning. Du måste stoppa kanalen, ta bort det och skapa en ny.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Varje gång du konfigurera om livekodaren anropa den **återställa** metoden på kanalen. Du måste stoppa programmet innan du återställer kanalen. Starta om programmet när du har återställt kanalen.
* En kanal kan stoppas endast när den har statusen körs och alla program i kanalen har stoppats.
* Som standard kan du bara lägga till 5 kanaler till Media Services-kontot. Det här är en icke-begränsande kvot på alla nya konton. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du debiteras endast när din kanal finns i den **kör** tillstånd. Mer information finns i [detta](media-services-manage-live-encoder-enabled-channels.md#states) avsnittet.
* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived@microsoft.com om du behöver köra en kanal under en längre tidsperiod.
* Se till att ha den strömningsslutpunkt från vilken du vill spela upp innehåll i den **kör** tillstånd.
* Förinställningen för kodningen använder begreppet ”max bildfrekvens” av 30 bilder per sekund. Om indata är 60fps / 59.94i, antal för inkommande paket är bort/programvara-interlaced till 30/29,97 fps. Om indata är 50fps/50i, är antal för inkommande paket bort/programvara-interlaced till 25 bildrutor per sekund. Om indata är 25 bildrutor förblir utdata 25 bildrutor per sekund.
* Glöm inte att stoppa din kanaler när du är klar. Om du inte, fakturering kommer att fortsätta.

## <a name="known-issues"></a>Kända problem
* Kanalen starttider har förbättrats till ett medelvärde för 2 minuter, men ibland med ökad efterfrågan kan fortfarande ta upp till 20 + minuter.
* Statiska bilder ska följa begränsningar beskrivs [här](media-services-manage-live-encoder-enabled-channels.md#default_slate). Om du försöker skapa en kanal med en standard bakgrundsbild som är större än 1920 x 1080, kommer så småningom begäran avbryts.
* Återigen... Glöm inte att stoppa din kanaler när du är klar direktuppspelning. Om du inte, fakturering kommer att fortsätta.

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Leverera Liveuppspelningshändelser med Azure Media Services](media-services-overview.md)

[Skapa kanaler som utför live encoding från en enskilt bithastighet till ström med anpassningsbar bithastighet med portalen](media-services-portal-creating-live-encoder-enabled-channel.md)

[Skapa kanaler som utför live encoding från en enskilt bithastighet till ström med anpassningsbar bithastighet med .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Hantera kanaler med REST API](https://docs.microsoft.com/rest/api/media/operations/channel)

[Media Services-begrepp](media-services-concepts.md)

[Specifikation för Azure Media Services fragmenterad MP4 Live-inmatning](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

