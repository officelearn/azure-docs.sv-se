---
title: Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet | Microsoft Docs
description: 'Det här avsnittet beskriver hur du ställer in en kanal som tar emot en direktsänd dataström med enkel bithastighet från en lokala kodare och utför sedan live encoding till ström med anpassningsbar bithastighet med Media Services. Dataströmmen kan sedan levereras till klienten uppspelning program via en eller flera Strömningsslutpunkter, med någon av följande protokoll för anpassningsbar strömning: HLS, Smooth Stream, MPEG DASH.'
services: media-services
documentationcenter: ''
author: anilmur
manager: cfowler
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: juliako;anilmur
ms.openlocfilehash: c4d5533c443d27afa56471ce048efc5a375f6780
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter

> [!NOTE]
> Startar den 12 maj 2018 live-kanaler kommer inte längre stöd för RTP/MPEG-2-transportström infogningsprotokollet. Migrera från RTP/MPEG-2 till RTMP eller fragmenterad MP4 (Smooth Streaming) infognings-protokoll.

## <a name="overview"></a>Översikt
I Azure Media Services (AMS), en **kanal** representerar en pipeline för bearbetning av liveströmmat innehåll. En **kanal** tar emot live indata i ett av två sätt:

* En lokal livekodare skickar en dataström med enkel bithastighet till den kanal som är aktiverad för att utföra live encoding med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.
* En lokal livekodare skickar flera bithastigheter **RTMP** eller **Smooth Streaming** (fragmenterad MP4) till den kanal som inte är aktiverad för att utföra live encoding med AMS. Infogade strömmarna passerar genom **kanal**s utan vidare bearbetning. Den här metoden anropas **direkt**. Du kan använda de följande livekodare som skickar Smooth Streaming i flera bithastigheter: MediaExcel, Ateme, anta kommunikation, Envivio, Cisco och Elemental. Följande livekodare skickar RTMP: Adobe Flash Media Live-kodare (FMLE), Telestream Wirecast, Haivision, Teradek och Tricaster kodare.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.
  
  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveströmning.
  > 
  > 

Från och med Media Services 2.10-versionen när du skapar en kanal kan ange du i hur du vill använda för din kanal tar emot Indataströmmen och om huruvida du vill använda att utföra live encoding av strömmen kanalen. Du kan välja mellan två alternativ:

* **Ingen** – ange det här värdet om du planerar att använda en lokal livekodare som kommer att skrivas ut flera bithastigheter dataström (en direkt stream). I det här fallet den inkommande dataströmmen som skickats till utdata utan kodning. Det här är beteendet för en kanal före 2,10 versionen.  Mer detaljerad information om att arbeta med kanaler av den här typen finns [direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda dina direktsänd dataström med enkel bithastighet till dataström med multibithastighet. Tänk på att det finns en fakturering påverkan för live encoding och ska du komma ihåg att lämna en live encoding kanal i tillståndet ”körs” kommer avgifter faktureringsinformation.  Vi rekommenderar att du stoppar du omedelbart dina kanaler som körs när din direktsänd strömning händelse är klar att undvika extra timvis avgifter.

> [!NOTE]
> Det här avsnittet beskrivs attributen för kanaler som är aktiverade för att utföra live encoding (**Standard** kodningstypen). Information om att arbeta med kanaler som inte är aktiverade för att utföra live encoding finns [direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).
> 
> Se till att granska den [överväganden](media-services-manage-live-encoder-enabled-channels.md#Considerations) avsnitt.
> 
> 

## <a name="billing-implications"></a>Konsekvenser för fakturering
En kanal för live encoding börjar fakturering så snart den är tillståndsövergångar till ”körs” via API: et.   Du kan också visa status i Azure-portalen eller i verktyget Azure Media Services Explorer (http://aka.ms/amse).

I följande tabell visas hur kanal tillstånd mappas till fakturering tillstånd i API och Azure-portalen. Observera att tillstånden är något annorlunda mellan API och portalen UX. När en kanal är i tillståndet ”körs” via API: et eller statusen ”klar” eller ”Streaming” i Azure-portalen, fakturering kommer att vara aktiv.
Du måste stoppa kanalen via API: et eller i Azure-portalen för att stoppa kanalen från fakturering du ytterligare.
Du ansvarar för att stoppa dina kanaler när du är klar med live encoding kanalen.  Det gick inte att stoppa en kodning kanal leder fortsatt fakturering.

### <a id="states"></a>Kanal tillstånd och hur de mappas till fakturerings-läge
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppats**. Inledningsvis i kanalen är efter skapades (om inte autostart har valts i portalen.) Ingen fakturering inträffar i det här tillståndet. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Starta**. Kanalen startas. Ingen fakturering inträffar i det här tillståndet. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar. Det nu fakturering användning. Du måste stoppa kanalen för att förhindra att ytterligare fakturering. 
* **Stoppa**. Kanalen stoppas. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Om du tar bort**. Kanalen tas bort. Ingen fakturering inträffar denna status. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläge. 

| Kanaltillstånd | Portalgränssnittsindikatorer | Är det fakturering? |
| --- | --- | --- |
| Startar |Startar |Nej (övergångsläge) |
| Körs |Klart (inga program körs)<br/>eller<br/>Strömmar (minst ett program körs) |JA |
| Stoppas |Stoppas |Nej (övergångsläge) |
| Stoppad |Stoppad |Nej |

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisk avstängning av för oanvända kanaler
Från och med 25 januari 2016 Media Services distribuerat en uppdatering som automatiskt stoppar en kanal (med live encoding aktiverat) när den har körts i ett tillstånd som inte används under lång tid. Detta gäller kanaler som inte har något active och som inte har fått ett inkommande bidrag flödet för en längre tid.

Tröskelvärdet för en oanvända tid är praktiskt taget 12 timmar, men kan ändras.

## <a name="live-encoding-workflow"></a>Live Encoding-arbetsflöde
Följande diagram representerar en live-streaming arbetsflöde där en kanal som tar emot en dataström med enkel bithastighet i något av följande protokoll: RTMP eller Smooth Streaming; sedan kodar den dataström som en dataström med multibithastighet. 

![Live-arbetsflöde][live-overview]

## <a id="scenario"></a>Vanligt Scenario för direktsänd strömning
Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived@microsoft.com om du behöver köra en kanal under längre tid. Tänk på att det finns en fakturering påverkan för live encoding och ska du komma ihåg att lämna en live encoding kanal i tillståndet ”körs” kommer avgifter per timme faktureringsinformation.  Vi rekommenderar att du stoppar du omedelbart dina kanaler som körs när din direktsänd strömning händelse är klar att undvika extra timvis avgifter. 
> 
> 

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som kan mata ut en **enda** bithastighet i något av följande protokoll: RTMP eller Smooth Streaming. 
   
    Det här steget kan också utföras när du har skapat din kanal.
2. Skapa och starta en kanal. 
3. Hämta kanalens infognings-URL. 
   
    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL. 
   
    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program. 
   
    Det skapas även en tillgång till när du använder Azure-portalen för att skapa ett program. 
   
    När du använder .NET SDK eller REST måste du skapa en tillgång och ange om du vill använda den här tillgången när du skapar ett Program. 
6. Publicera tillgången som hör till programmet.   
   
    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**. 
    
7. Starta programmet när du är redo att påbörja strömning och arkivering.
8. Som alternativ kan livekodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
10. Ta bort programmet (och ta eventuellt bort tillgången).   

> [!NOTE]
> Det är mycket viktigt att inte glömma att stoppa en kanal för Live-kodning. Tänk på att det finns en varje timme fakturering påverkan för live encoding och ska du komma ihåg att lämna en live encoding kanal i tillståndet ”körs” kommer avgifter faktureringsinformation.  Vi rekommenderar att du stoppar du omedelbart dina kanaler som körs när din direktsänd strömning händelse är klar att undvika extra timvis avgifter. 
> 
> 

## <a id="channel"></a>Kanal input (infognings) konfigurationer
### <a id="Ingest_Protocols"></a>Strömmande infogningsprotokollet
Om den **kodare typen** är inställd på **Standard**, giltiga alternativ är:

* Enkel bithastighet **RTMP**
* Enkel bithastighet **fragmenterad MP4** (Smooth Streaming)

#### <a id="single_bitrate_RTMP"></a>RTMP med enkel bithastighet
Att tänka på:

* Den inkommande dataströmmen får inte innehålla flera bithastigheter video
* Video-ström ska ha en genomsnittlig bithastighet under 15 Mbit/s
* Ljudströmmen ska ha en genomsnittlig bithastighet nedan 1 Mbit/s
* Följande är koder som stöds:
* MPEG-4 AVC / H.264 Video
* Baslinje, Main, hög profil (8-bitars 4:2:0)
* Hög 10 profil (10 bitar 4:2:0)
* Hög 422 profil (10 bitar 4:2:2)
* MPEG-2 AAC-LC ljud
* Mono, Stereo Surround (5.1, 7.1)
* 44,1 samplingsfrekvensen
* MPEG-2-style ADTS paketering
* Rekommenderade kodare inkluderar:
* Telestream Wirecast
* Flash Media Livekodare

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
Vanligt användningsfall:

Använd lokal livekodare från leverantörer som grundämne tekniker, Ericsson, Ateme, Envivio skicka Indataströmmen via öppna internet till en närliggande Azure-datacenter.

Att tänka på:

Samma som för [RTMP med enkel bithastighet](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Andra överväganden
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Maximal upplösning för de inkommande video-strömmen är 1 920 x 1 080 och högst 60 fält per sekund om sammanflätad eller 30 bildrutor per sekund om progressiv.

### <a name="ingest-urls-endpoints"></a>Infognings-URL: er (slutpunkter)
En kanal som tillhandahåller en slutpunkt för indata (infognings-URL) som du anger i livekodaren, så kan push-kodaren dataströmmar till din kanaler.

När du skapar en kanal kan du få infognings-URL: er. För att få dessa URL: er, kanalen behöver inte finnas i den **kör** tillstånd. När du är redo att börja skicka data till kanalen, det måste vara i den **kör** tillstånd. När kanalen startar mata in data, kan du förhandsgranska dataströmmen via förhandsgransknings-URL.

Du har ett alternativ för vill föra in fragmenterad MP4 (Smooth Streaming) direktsänd dataström via en SSL-anslutning. För att mata in via SSL, se till att uppdatera infognings-URL: en till HTTPS. Observera att för närvarande AMS inte stöder SSL med anpassade domäner.  

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan ange IP-adresser som får publicera video i den här kanalen. Tillåtna IP-adresser kan anges som antingen en enskild IP-adress (t.ex. ”10.0.0.1”), ett IP-intervall med en IP-adress och en CIDR-undernätsmask (t.ex. ”10.0.0.1/22”) eller ett IP-intervall med en IP-adress och en CIDR-undernätsmask med punktavgränsad decimalform (t.ex. '10.0.0.1(255.255.252.0)').

Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

## <a name="channel-preview"></a>Förhandsgranskning av kanal
### <a name="preview-urls"></a>URL: er för förhandsgranskning
Kanaler ange förhandsgranskningsslutpunkten (förhandsgranskning URL) som används för att förhandsgranska och verifiera strömmen innan ytterligare bearbetning och leverans.

När du skapar kanalen kan du få förhandsgransknings-URL. Om du vill hämta Webbadress kanalen behöver inte finnas i den **kör** tillstånd.

När kanalen startar mata in data, kan du förhandsgranska dataströmmen.

> [!NOTE]
> Förhandsgranska dataströmmen kan för närvarande bara levereras i fragmenterad MP4 (Smooth Streaming) format oavsett angivna Indatatyp. Du kan använda den [ http://smf.cloudapp.net/healthmonitor ](http://smf.cloudapp.net/healthmonitor) player att testa Smooth Stream. Du kan också använda en spelare som finns i Azure-portalen för att visa din dataström.
> 
> 

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan ange IP-adresser som tillåts att ansluta till förhandsgranskningsslutpunkten. Om inga IP-adresser har angetts någon IP-adress ska tillåtas. Tillåtna IP-adresser kan anges som antingen en enskild IP-adress (t.ex. ”10.0.0.1”), ett IP-intervall med en IP-adress och en CIDR-undernätsmask (t.ex. ”10.0.0.1/22”) eller ett IP-intervall med en IP-adress och en CIDR-undernätsmask med punktavgränsad decimalform (t.ex. ”10.0.0.1(255.255.252.0)”).

## <a name="live-encoding-settings"></a>Live kodningsinställningar
Det här avsnittet beskrivs hur inställningarna för livekodaren i kanalen kan justeras, när den **kodning typen** för en kanal är inställd på **Standard**.

> [!NOTE]
> Ditt bidrag feed kan endast innehålla en enda ljud spåra – vill föra in flera ljud spår stöds inte för närvarande. När du gör live encoding med [lokalt live kodar](media-services-live-streaming-with-onprem-encoders.md), kan du skicka ett bidrag feed i Smooth Streaming-protokollet som innehåller flera ljud spår.
> 
> 

### <a name="ad-marker-source"></a>Markör AD-källa
Du kan ange källan för annonsmarkörssignaler. Standardvärdet är **Api**, vilket betyder att livekodaren i kanalen bör lyssna på en asynkron **Annonsmarkörs-API**.

### <a name="cea-708-closed-captions"></a>CEA 708 textning
En valfri flagga som anger att livekodaren till ignorerar CEA 708 bildtexter data som är inbäddad i inkommande videon. När flaggan anges till FALSKT (standard), kodaren identifierar och sätt tillbaka CEA 708 data i video utdataströmmar.

#### <a name="index"></a>Index
Det rekommenderas att skicka i ett enda program transportström (SPTS). Om Indataströmmen innehåller flera program, livekodaren i kanalen Parsar programmet kartan tabellen (betalning) i indata, identifierar indata som har en dataström typnamn MPEG-2 AAC ADTS eller AC-3 System-A AC-3 System-B eller MPEG-2 privata PES MPEG-1 ljud eller ljud MPEG-2 och ordnar dem i den ordning som anges i bet Det nollbaserade indexet används sedan för att hämta n: e-post i den ordningen.

#### <a name="language"></a>Språk
Språk-ID för ljudström, som överensstämmer med ISO 639-2, till exempel ENG. Om den inte finns är standard och (Odefinierad).

### <a id="preset"></a>System förinställda
Anger förinställningen som ska användas av livekodaren i den här kanalen. Det enda tillåtna värdet är för närvarande **Default720p** (standard).

Observera att om du behöver anpassade förinställningar, bör du kontakta amslived@microsoft.com.

**Default720p** kommer koda videon till följande 7 lager.

#### <a name="output-video-stream"></a>Video utdataströmmen
| Bithastighet | Bredd | Höjd | MaxFPS | Profil | Namn på utdata Stream |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Hög |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Huvud |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Huvud |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Huvud |Video_512x288_850kbps |
| 550 |384 |216 |30 |Huvud |Video_384x216_550kbps |
| 350 |340 |192 |30 |Baslinje |Video_340x192_350kbps |
| 200 |340 |192 |30 |Baslinje |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Utdataströmmen för ljud
Ljud kodas till stereo AAC LC 64 kbit/s, samplingsfrekvensen 44,1 kHz.

## <a name="signaling-advertisements"></a>Annonser-signalering
När kanalen har Live Encoding aktiverat, du har en komponent i pipeline som är bearbetningen video och kan ändra den. Du kan skicka en signal för kanalen Infoga pekdatorer och/eller annonser i den utgående strömmen med anpassningsbar bithastighet. Pekdatorer är fortfarande bilder som du kan använda för att täcka in inkommande live feeden i vissa fall (till exempel under en kommersiell paus). Annonserar signaler är tiden har synkroniserats signalerar du bädda in i den utgående dataströmmen som talar om videospelare vidta åtgärder för särskilda – exempelvis för att växla till ett meddelande vid rätt tidpunkt. Se den här [blogg](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) en översikt över SCTE 35 signaling mekanismen som används för detta ändamål. Nedan visas ett vanligt scenario som du kan implementera i din direktsända händelse.

1. Har din visningsprogram hämta en avbildning av före HÄNDELSEN innan händelsen startar.
2. Har din visningsprogram hämta en avbildning efter HÄNDELSEN när händelsen slutar.
3. Har din visningsprogram får en FELHÄNDELSE avbildning om det finns ett problem när händelser (till exempel strömavbrott på arenan).
4. Skicka ett AD-BREAK-bild att dölja direktsänd händelse under en kommersiell break-feed.

Följande är de egenskaper som du kan ange när signalering annonser. 

### <a name="duration"></a>Varaktighet
Varaktighet i sekunder, för kommersiell break. Detta måste vara ett positivt värde inte är noll för att kunna börja kommersiella break. När en kommersiell break pågår och varaktigheten har angetts till noll med CueId matchar kommersiella break pågående sedan att break har avbrutits.

### <a name="cueid"></a>CueId
Ett unikt ID för kommersiell break som ska användas av nedströms program för att vidta lämpliga åtgärder. Måste vara ett positivt heltal. Du kan det här värdet till ett slumpmässigt positivt heltal eller använda en överordnad system för att spåra stack-ID: N. Kontrollera att normalisera eventuella ID: n till positiva heltal innan du skickar via API.

### <a name="show-slate"></a>Visa mallen
Valfri. Signalerar att växla till live Encoding den [standard mallen](media-services-manage-live-encoder-enabled-channels.md#default_slate) avbildningen under kommersiella pausen och dölja inkommande Videoupptagning. Ljudet är också avstängt under mallen. Standardvärdet är **FALSKT**. 

Bilden som används är den som angetts via en standard tillgången Id-egenskap vid tidpunkten för att skapa kanalen. Mallen ska anpassas så att de passar bildstorleken visas. 

## <a name="insert-slate--images"></a>Infoga mallen bilder
Livekodaren i kanalen kan signaleras att växla till en bild. Det kan också få signal om att avbryta en pågående mallen. 

Livekodaren kan konfigureras för att växla till en avbildning och dölja inkommande video signal i vissa situationer – till exempel under ett ad-break. Om en sådan mallen inte har konfigurerats maskeras inte indatavideo under den ad-break.

### <a name="duration"></a>Varaktighet
Varaktighet för mallen i sekunder. Detta måste vara ett positivt värde inte är noll för att starta mallen. Om det finns en pågående mallen och en varaktighet på noll har angetts, avslutas den pågående mallen.

### <a name="insert-slate-on-ad-marker"></a>Infoga bakgrundsbild vid reklammarkör
När du inställd på Sant för den här inställningen konfigurerar livekodaren att infoga en bild under en ad-break. Standardvärdet är true. 

### <a id="default_slate"></a>Id som standard en tillgång

Valfri. Anger tillgången Id för Media Services tillgång som innehåller en bild. Standardvärdet är null. 


>[!NOTE] 
>Innan du skapar kanalen, ska en bild med följande begränsningar skickas som en dedikerad tillgång (inga andra filer ska vara i tillgången). Den här avbildningen används endast när livekodaren infogar en mallen på grund av en ad-break eller har uttryckligen signalerat att infoga en mallen. Livekodaren kan också gå i en läge under vissa felvillkor – till exempel om den inkommande signalen går förlorad. Det finns inget alternativ för att använda en anpassad avbildning när livekodaren når sådana insignal förlorade tillståndet. För den här funktionen kan du rösta [här](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).


* Högst 1 920 x 1 080 i resolution.
* Högst 3 MB i storlek.
* Filnamnet måste ha filnamnstillägget *.jpg.
* Bilden måste laddas upp till en tillgång som endast AssetFile i att tillgången och den här AssetFile markeras som den primära filen. Tillgången kan inte vara krypterade.

Om den **standard reservera tillgångsnummer** har inte angetts, och **Infoga mallen på ad-markör** är inställd på **true**, en standardbild Azure Media Services används för att dölja video Indataströmmen. Ljudet är också avstängt under mallen. 

## <a name="channels-programs"></a>Kanalens program
En kanal är associerad till program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och Program liknar traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program som är associerad med en tillgång som lagrar direktuppspelat innehåll. En tillgång som är mappad till en block-blob-behållare i Azure Storage-konto och lagras filerna i tillgången som blobbar i behållaren. Om du vill publicera programmet så att kunderna kan visa dataströmmen måste du skapa en positionerare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera Arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta ett nytt program för varje händelse som beskrivs i avsnittet Programming Live Streaming program istället.

Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort programmet och tar sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först. 

Även efter att du har stoppat och tagit bort programmet skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge som du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Hämta en miniatyr av en live-feed
När Live Encoding aktiveras kan nu du få en förhandsgranskning av feeden live när den når kanalen. Detta kan vara ett ovärderligt verktyg för att kontrollera om din live feed faktiskt når kanalen. 

## <a id="states"></a>Kanal tillstånd och hur tillstånd mappas till fakturerings-läge
Det aktuella tillståndet för en kanal. Möjliga värden omfattar:

* **Stoppats**. Det här är starttillståndet för kanalen när den har skapats. I det här tillståndet kan kanalegenskaperna uppdateras, men strömning är inte tillåtet.
* **Starta**. Kanalen startas. Inga uppdateringar eller strömning tillåts i det här tillståndet. Om ett fel inträffar återgår kanalen till tillståndet Stoppad.
* **Kör**. Kanalen kan bearbeta direktsända strömmar.
* **Stoppa**. Kanalen stoppas. Inga uppdateringar eller strömning tillåts i det här tillståndet.
* **Om du tar bort**. Kanalen tas bort. Inga uppdateringar eller strömning tillåts i det här tillståndet.

Följande tabell visar hur kanaltillstånd mappas till faktureringsläge. 

| Kanaltillstånd | Portalgränssnittsindikatorer | Fakturerad? |
| --- | --- | --- |
| Startar |Startar |Nej (övergångsläge) |
| Körs |Klart (inga program körs)<br/>eller<br/>Strömmar (minst ett program körs) |Ja |
| Stoppas |Stoppas |Nej (övergångsläge) |
| Stoppad |Stoppad |Nej |

> [!NOTE]
> För närvarande kanal start medelvärdet är ungefär 2 minuter, men ibland kan ta upp till 20 + minuter. Återställer kanal kan ta upp till 5 minuter.
> 
> 

## <a id="Considerations"></a>Överväganden
* När en kanal för **Standard** kodningstypen påträffar en förlust av inkommande källa/bidrag feed, den kompenserar den genom att ersätta det källa ljudet och med en fel mallen och tystnad. Kanalen kommer att fortsätta att generera en mallen tills indata/bidrag feed återupptar. Vi rekommenderar att en levande kanal inte lämnas i sådant tillstånd under längre tid än 2 timmar. Beteendet för kanal på inkommande återanslutning kan inte garanteras förbi den punkten, är varken sitt beteende som svar på kommandot återställning. Du måste stoppa kanalen, ta bort den och skapa en ny.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Varje gång du konfigurera om livekodaren anropa den **återställa** metod på kanalen. Du måste stoppa programmet innan du återställer kanalen. Starta om programmet när du har återställt kanalen.
* En kanal kan stoppas endast när det är i tillståndet körs, och alla program i kanalen har stoppats.
* Som standard kan du bara lägga till 5 kanaler Media Services-kontot. Det här är en icke-begränsande kvot för alla nya konton. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du debiteras endast när din kanal är i den **kör** tillstånd. Mer information finns i [detta](media-services-manage-live-encoder-enabled-channels.md#states) avsnitt.
* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived@microsoft.com om du behöver köra en kanal under en längre tidsperiod.
* Se till att ha den strömningsslutpunkt från vilken du vill strömma innehåll i den **kör** tillstånd.
* Kodning förinställningen använder begreppet ”max bildfrekvens” på 30 fps. Om indata är 60fps 59.97i, inkommande ramarna är bort/de-interlaced till 30/29,97 fps. Om indata är 50fps/50i, är inkommande ramarna bort/de-interlaced till 25 bildrutor per sekund. Om indata är 25 bildrutor förblir utdata 25 bildrutor per sekund.
* Glöm inte att stoppa din kanaler när du är klar. Om du inte gör fortsätter fakturering.

## <a name="known-issues"></a>Kända problem
* Kanal tid att starta förbättrats genomsnitt 2 minuter, men ibland med ökad efterfrågan kan fortfarande ta upp till 20 + minuter.
* En bilder bör följa begränsningar som beskrivs [här](media-services-manage-live-encoder-enabled-channels.md#default_slate). Om du försöker skapa en kanal med en standard-blad som är större än 1 920 x 1 080, begäran kommer så småningom fel ut.
* Återigen... Glöm inte att stoppa din kanaler när du är klar strömning. Om du inte gör fortsätter fakturering.

## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Leverera händelser via strömning Live med Azure Media Services](media-services-overview.md)

[Skapa kanaler som utför live encoding från ett enskilt bithastighet till ström med anpassningsbar bithastighet med portalen](media-services-portal-creating-live-encoder-enabled-channel.md)

[Skapa kanaler som utför live encoding från ett enskilt bithastighet till ström med anpassningsbar bithastighet med .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Hantera kanaler med REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
 
[Media Services-begrepp](media-services-concepts.md)

[Azure Media Services fragmenterad MP4 Live infognings-specifikationen](media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png

