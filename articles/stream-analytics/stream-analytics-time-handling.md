---
title: Förstå tid hantering i Azure Stream Analytics
description: Lär dig mer om tid hantering i Azure Stream Analytics
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 4accff7410d17e76a000b7cef957b75c65a16960
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008393"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Förstå tid hantering i Azure Stream Analytics

I den här artikeln diskuterar vi hur du ser information om designval för att lösa praktiska tid hantering av problem i Azure Stream Analytics-tjänsten. Tid hantering av design beslut är nära förknippat med Händelseordning faktorer. Mer information finns i den här relaterade artikeln: [Azure Stream Analytics händelse ordning överväganden](stream-analytics-out-of-order-and-late-events.md).

## <a name="background-time-concepts"></a>Bakgrund tid begrepp

Vi definiera vissa begrepp som bakgrund till bättre frame diskussionen:

- **Händelsetid**: Den tid när ursprungliga händelsen som inträffade. Till exempel om en glidande bil på väg närmar sig en avgift monter.

- **Bearbetningstid**: Den tidpunkt då händelsen når bearbetning av systemet och observeras. Till exempel tar när en avgift monter sensor ser bilen och datorn en stund att bearbeta data.

- **Vattenstämpel**: En händelse tid markör som visar alla händelser upp till vad peka har varit telemetriingång till strömmande processorn. Vattenstämplar kan systemet indikerar Rensa förloppet på mata in händelser. Av natur strömmar stoppas av inkommande data aldrig, så att vattenstämplar indikerar förloppet till en viss punkt i strömmen.

   Vattenstämpel konceptet är viktigt. Vattenstämplar kan Stream Analytics för att avgöra när systemet kan producera fullständiga, korrekt och repeterbara resultat som inte behöver återkallas. Bearbetningen kan göras på en garanterad sätt som är förutsägbara och repeterbara. Till exempel är räkna orden måste göras för vissa villkor för felhantering, vattenstämplar säker start- och slutpunkter.

Som ytterligare resurser om det här ämnet finns Tyler Akidau blogginläggen [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) och [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Välja bästa starttiden

Stream Analytics ger användarna två alternativ för att välja händelsetid:

1. **Ankomsttid**  

   Ankomsttid tilldelas vid Indatakällan när händelsen når källan. Du kan komma åt ankomsttid med hjälp av den **EventEnqueuedUtcTime** -egenskapen för Event Hubs indata **IoTHub.EnqueuedTime** egenskapen för IoT Hub och använda den **BlobProperties.LastModified**  -egenskapen för blob-indata.

   Med hjälp av ankomsttid är standardbeteendet och bäst för dataarkivering scenarier där det finns inga temporal logik som behövs.

2. **Programmet tid** (som även kallas Händelsetid)

   Tid för programmet tilldelas när händelsen genereras och det är en del av händelsenyttolast. För att bearbeta händelser efter tid för program, använder den **tidsstämpel av** -satsen i select-frågan. Om den **tidsstämpel av** satsen saknas, händelser bearbetas av ankomsttid.

   Det är viktigt att du använder en tidsstämpel i nyttolasten när temporal logik ingår. På så sätt kan fördröjningar i källsystemet eller i nätverket kan redovisas.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hur tid allt är klart i Azure Stream Analytics

När du använder programmet tid, baseras i stora drag processen för tid på inkommande händelser. Det är svårt för system du behöver veta om det finns inga händelser, eller om händelser fördröjs för strömbearbetning. Därför genererar Azure Stream Analytics heuristisk vattenstämplar på följande sätt för varje inkommande partition:

1. När det finns några inkommande händelsen, är vattenstämpeln största tidpunkt för händelsen som vi har sett hittills minus fönsterstorlek out ordning tolerans.

2. När det finns ingen inkommande händelse, är vattenstämpeln i den aktuella beräknad ankomst tiden (den förflutna tiden på bakgrunden VM bearbetning av händelser från förra gången en inkommande händelsen återkommer flera plus som indata händelsens ankomsttid) minus fönstret tolerans sent ankomst.

   Ankomsttiden kan beräknas, eftersom den verkliga ankomsttiden genereras på den inkommande asynkron meddelandekön för händelser, t.ex Event Hubs, och inte Azure Stream Analytics VM bearbetning av händelser.

Designen har två ytterligare syften, förutom att generera vattenstämplar:

1. Systemet genererar resultat i tid med eller utan inkommande händelser.

   Du har kontroll över hur lägliga vill se resultatet. I Azure-portalen på den **Händelseordning** sidan för ditt Stream Analytics-jobb kan du konfigurera den **oordnade händelser** inställningen. När du konfigurerar den här inställningen kan du överväga att få till följd att punktligheten för incidenter med tolerans på out ordning händelser i händelseströmmen.

   Fönstret tolerans sent ankomst är viktigt att hålla genererar vattenstämplar, även i frånvaron av inkommande händelser. Ibland kan finnas det en viss där inga inkommande händelser levereras i, till exempel när en händelse indataström är null-optimerad. Det problemet förvärras med hjälp av flera partitioner i den inkommande händelsekoordinator.

   Strömning databearbetning system utan ett sent ankomst tolerans fönster kan drabbas av fördröjda utdata när indata är null-optimerade och flera partitioner som används.

2. Systembeteendet måste vara repeterbara. Repeterbarhet är en viktig egenskap för en strömmande system för databearbetning.

   Vattenstämpeln härleds från ankomsttid och tid för programmet. Båda är beständiga i asynkron meddelandekö för händelser och därmed repeterbara. I fallet har ankomsttiden uppskattas om händelser, Azure Stream Analytics journaler uppskattade ankomsttid för repeterbarhet under repetitionsattacker för återställning efter fel.

Observera att när du väljer att använda **ankomsttid** som händelsens tidpunkt, det finns inget behov att konfigurera ut ordning tolerans och sen ankomst tolerans. Eftersom **ankomsttid** kommer att monotont ökar i den inkommande asynkron meddelandekön för händelser, Azure Stream Analytics bara skiljer konfigurationerna.

## <a name="late-arriving-events"></a>Dessa data anländer händelser

Per definition av sent ankomst tolerans fönstret för varje inkommande händelse Azure Stream Analytics jämför den **händelsetid** med den **ankomsttid**; om tidpunkt för händelsen är utanför fönstret tolerans du Konfigurera systemet att ta bort händelsen eller justera händelsetid vara inom toleransen.

Överväg att när vattenstämplar har genererats tjänsten kan eventuellt ta emot händelser med lägre än vattenstämpeln tidpunkt för händelsen. Du kan konfigurera tjänsten för att antingen **släppa** dessa händelser eller **justera** händelsetid till värdet för vattenstämpeln.

Som en del av justering, händelsens **System.Timestamp** är inställt på det nya värdet, men **händelsetid** själva fältet ändras inte. Den här justeringen är bara om en händelse **System.Timestamp** kan skilja sig från värdet i fältet händelse tid och kan orsaka oväntade resultat som ska genereras.

## <a name="handling-time-variation-with-substreams"></a>Hantering av tid variation med underströmmar

Mekanismen heuristisk vattenstämpel generation som beskrivs här fungerar bra i de flesta fall där tiden huvudsakligen har synkroniserats mellan olika händelseavsändare. I verkligheten har särskilt i många IoT-scenarier systemet dock lite kontroll över klockan på händelseavsändare. Händelseavsändare som kan vara alla typer av enheter i fältet kanske i olika versioner av maskinvara och programvara.

Istället för att använda en vattenstämpel som är globala för alla händelser i en indatapartitionen, har Stream Analytics en annan mekanism som heter underströmmar som hjälper dig att. Du kan använda underströmmar i jobbet genom att skriva en jobbfråga som använder den [ **TIMESTAMP BY** ](/stream-analytics-query/timestamp-by-azure-stream-analytics) -satsen och nyckelordet **över**. Om du vill ange underström, anger du ett namn för nyckelkolumn efter den **över** nyckelord, till exempel en `deviceid`, så att tiden principer tillämpas efter den kolumnen. Varje underström hämtar sin egen oberoende vattenstämpel. Den här mekanismen är användbar för att tillåta tid utdata-generering, hanterar stora när systemklockan jämföra eller network fördröjningar mellan händelseavsändare.

Underströmmar är en unik lösning som tillhandahålls av Azure Stream Analytics och erbjuds inte av andra strömmande system för databearbetning. Stream Analytics gäller fönstret tolerans sent ankomst inkommande händelser när underströmmar används. Standardinställningen (5 sekunder) är sannolikt för liten för enheter med olika tidsstämplar. Vi rekommenderar att du börjar med 5 minuter och göra justeringar enligt användningsmönster enheten klockan skeva.

## <a name="early-arriving-events"></a>Tidiga dessa data anländer händelser

Du kanske har märkt en annan begrepp som kallas tidig ankomst fönstret som ser ut som motsatsen till sent ankomst tolerans fönster. Det här fönstret vara högst 5 minuter och har en annan funktion från sent ankomst något.

Eftersom Azure Stream Analytics garanterar alltid genereras hela resultatet, kan du bara ange **jobbet starttiden** som utdata vänder för jobbet, inte indatatid. Jobbets starttid krävs så att fönstret slutförd bearbetas, inte bara från mitten av fönstret.

Stream Analytics hämtar sedan starttiden i frågan-specifikationen. Eftersom inkommande händelsekoordinator endast indexeras av ankomsttid, men har systemet att översätta starttiden för händelsen att ankomsttid. Systemet kan börja bearbeta händelser från den tidpunkten i den inkommande händelsekoordinator. Översättningen är enkelt med tidig dessa data anländer fönstret gränsen. Den startar händelsetid minus fönstret 5 minuter tidig dessa data anländer. Den här beräkningen innebär också att systemet släpper alla händelser som visas med tidpunkt för händelsen 5 minuter större än ankomsttid.

Det här konceptet används för att se till att bearbetningen är upprepningsbara oavsett var du börjar att mata ut från. Utan sådan funktion skulle det inte vara möjligt att garantera repeterbarhet, som många andra strömmande system gör anspråk på de gör.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Sidoeffekter tid toleranser för Händelseordning

Stream Analytics-jobb har flera **Händelseordning** alternativ. Två kan konfigureras i Azure portal: den **oordnade händelser** inställningen (out ordning tolerans) och **händelser som kommer sent** inställningen (sent ankomst tolerans). Den **tidig ankomst** tolerans är fast och kan inte ändras. Principerna tid används av Stream Analytics för att ge starkt garantier. De här inställningarna har emellertid vissa ibland oväntade konsekvenser:

1. Av misstag skicka händelser som är för tidigt.

   Tidiga händelser bör inte vara normalt utdata. Det är möjligt att tidiga händelser skickas till utdata om avsändarens klockan körs för snabb dock. Alla händelser för tidig dessa data anländer ignoreras så att du inte kommer se någon av dem från utdata.

2. Skicka gamla händelser till Event Hubs som ska bearbetas av Azure Stream Analytics.

   Medan gamla händelser kan verka ofarliga på först på grund av att sen ankomst toleransen, kan du ta bort gamla händelser. Om händelserna är för gammal den **System.Timestamp** värdet ändras under händelsepåfyllning. På grund av det här beteendet är för närvarande Azure Stream Analytics mer lämpligt för scenarier, i stället för bearbetning av historiska händelser scenarier för händelsebearbetning i nära realtid. Du kan ange den **händelser som kommer sent** till det högsta värdet (20 dagar) att undvika problemet i vissa fall.

3. Utdata verkar vara fördröjd.

   Den första vattenstämpeln ska genereras vid den beräknade tiden: den **maximala händelsetid** systemet har observerats hittills minus fönsterstorlek out ordning tolerans. Som standard konfigureras out ordning toleransen till noll (00 minuter och 00 sekunder). När du ställer in den i ett senare, noll tidsvärde det direktuppspelade jobbet första utdata är fördröjd med det aktuella värdet på tid (eller längre) på grund av den första gången, vattenstämpel, som beräknas.

4. Indata är null-optimerad.

   Om det finns inga indata i en viss partition, vattenstämpel tid beräknas som den **ankomsttid** minus fönstret tolerans sent ankomst. Därför om indatahändelser är ovanliga och sparse-filer, kan utdata fördröjas av den mängden tid. Standard **händelser som kommer sent** värdet är 5 sekunder. Du bör förvänta dig att se viss fördröjning när du skickar inmatningshändelser en i taget, till exempel. Fördröjningen kan få sämre, när du ställer in **händelser som kommer sent** fönstret till ett stort värde.

5. **System.Timestamp** värde skiljer sig från tiden i den **händelsetid** fält.

   Som tidigare nämnts, justeras händelsetid av out ordning tolerans eller sent ankomst tolerans för windows. Den **System.Timestamp** värdet för händelsen justeras, men inte den **händelsetid** fält.

## <a name="metrics-to-observe"></a>Mått för att se

Du kan se ett antal Händelseordning tid tolerans effekterna via [Stream Analytics-jobbet mått](stream-analytics-monitoring.md). Följande gäller:

|Mått  | Beskrivning  |
|---------|---------|
| **Out ordning händelser** | Anger hur många händelser som tagits emot fel ordning, som antingen släpptes eller får en justerade tidsstämpel. Det här måttet påverkas direkt av konfigurationen av den **oordnade händelser** på den **Händelseordning** sidan på jobbet i Azure-portalen. |
| **Sena Indatahändelser** | Anger antalet händelser som kommer sent från källan. Det här måttet innefattar händelser som har tagits bort eller har haft tidsstämpel justerades. Det här måttet påverkas direkt av konfigurationen av den **händelser som kommer sent** i den **Händelseordning** sidan på jobbet i Azure-portalen. |
| **Tidiga Indatahändelser** | Anger hur många händelser som tidigt från källan som antingen tagits bort eller så har ställts in tidsstämpel om de befinner sig utanför tidigt 5 minuter. |
| **Fördröjning för vattenstämpel** | Anger fördröjningen för strömningsuppgift för databearbetning. Se mer information finns i avsnittet nedan.|

## <a name="watermark-delay-details"></a>Information om fördröjning av vattenstämpel

Den **vattenstämpel fördröjning** mått beräknas som klocktid av processnoden minus största vattenstämpeln den har sett hittills. Mer information finns i den [vattenstämpel fördröjning blogginlägget](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Det kan finnas flera orsaker till detta värde är större än 0 under normal drift:

1. Inbyggd bearbetningsfördröjning i pipeline för dataströmning. Den här fördröjningen är normalt nominell.

2. Fönstret ut ordning tolerans introducerade fördröjning, eftersom vattenstämpel begränsas av storleken på fönstret tolerans.

3. Fönstret sent ankomst introducerades fördröjning, eftersom vattenstämpel minskas med storleken fönstret tolerans.

4. Klockan förskjuta för noden bearbetning genererar måttet.

Det finns ett antal andra resursbegränsningar som kan orsaka att sakta ned strömmande pipelinen. Vattenstämpel fördröjning mått kan öka på grund av:

1. Det finns inte tillräckligt med bearbetningsresurser i Stream Analytics för att hantera mängden inkommande händelser. Om du vill skala upp resurser, se [förstå och justera Direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md).

2. Inte tillräckligt dataflöde i de inkommande asynkrona meddelandeköer för händelser, så att de har begränsats. Möjliga lösningar finns i [automatiskt skalar upp Azure Event Hubs-dataflödesenheter](../event-hubs/event-hubs-auto-inflate.md).

3. Utdatamottagarna har inte etablerats med tillräckligt med kapacitet så att de har begränsats. Möjliga lösningar varierar mycket beroende på smak för utdata-tjänst som används.

## <a name="output-event-frequency"></a>Frekvens för utdata-händelse

Azure Stream Analytics använder vattenstämpel förloppet som endast utlösare för att producera utdata-händelser. Eftersom vattenstämpeln hämtas från indata, är det upprepningsbara under återställning efter fel och även i användarinitierad ombearbetning.

När du använder [fönsteraggregeringar](stream-analytics-window-functions.md), tjänsten endast producerar utdata i slutet av windows. I vissa fall kan användarna vill se partiella aggregeringar som genereras från windows. Partiell aggregeringar stöds inte för närvarande i Azure Stream Analytics.

I andra direktuppspelade lösningar kan utdatahändelserna materialiseras vid olika tidpunkter för utlösare, beroende på externa omständigheterna. Det är möjligt i vissa lösningar att utdata-händelser för en given tidpunkt fönstret par genereras flera gånger. Eftersom indatavärdena förfinade bli aggregerade resultatet mer exakta. Händelser kan vara speculated på första och ändrade över tid. Till exempel när en viss enhet är frånkopplad från nätverket, kan ett beräknat värde användas av ett system. Senare på ansluts samma enhet till nätverket. Den faktiska händelsedata kan sedan inkluderas i Indataströmmen. Utdataresultat från att bearbeta den tidsperioden ger mer exakta utdata.

## <a name="illustrated-example-of-watermarks"></a>Illustrerade exempel på vattenstämplar

Följande bilder visar hur vattenstämplar utvecklas under olika omständigheter.

Den här tabellen visas exempeldata som är i diagrammet nedan. Observera att tidpunkt för händelsen och ankomsttiden variera ibland matchar och ibland inte.

| Händelsetid | Ankomsttid | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

I den här bilden används följande toleranser:

- Tidig ankomst windows är 5 minuter
- Sent dessa data anländer fönstret är 5 minuter
- Ändra ordning är 2 minuter

1. Bild av vattenstämpel går via de här händelserna:

   ![Bild av Azure Stream Analytics-vattenstämpel](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Viktiga processer som visas i föregående bild:

   1. Den första händelsen (device1) och andra händelsen (device2) har justerats gånger och bearbetas utan justeringar. Vattenstämpel utvecklas på varje händelse.

   2. När den tredje händelsen (device1) bearbetas föregår ankomsttid (12:11) händelsetid (12:17). Händelsen anlänt tidigt, 6 minuter så att händelsen har släppts på grund av 5 minuter tidig ankomst toleransen.

      Vattenstämpeln fortsätter inte i det här fallet en tidig händelse.

   3. Den fjärde händelsen (device3) och femte händelse (device1) har justerats gånger och bearbetas utan justering. Vattenstämpel utvecklas på varje händelse.

   4. När händelsen sjätte (device3) bearbetas är ankomsttid (12:17) och tidpunkt för händelsen (12:12) under vattenstämpel-nivå. Tidpunkt för händelsen justeras till water mark nivå (12:17).

   5. När händelsen nionde (device3) bearbetas är ankomsttid (12:27) 6 minuter före händelsetid (12:21). Sen ankomst principen tillämpas. Tidpunkt för händelsen är justerade (12:22), vilket är över vattenstämpel (12:21) så ingen ytterligare justering tillämpas.

2. Andra illustration av vattenstämpel fortsätter utan en tidig ankomst princip:

   ![Azure Stream Analytics ingen tidig princip vattenstämpel bild](media/stream-analytics-time-handling/watermark-graph-2.png)

   I det här exemplet används inga tidig ankomst-principen. Avvikande händelser som kommer tidigt öka vattenstämpeln avsevärt. Lägg märke till den tredje händelsen (deviceId1 vid tid 12:11) har inte släpps i det här scenariot och vattenstämpeln upphöjt till 12:15. Fjärde tidpunkt för händelsen är justerade vidarebefordra 7 minuter (12:08 till 12:15) som ett resultat.

3. I den sista bilden används underströmmar (över DeviceId). Flera vattenstämplar spåras, en per stream. Det finns färre händelser med sina justeras som ett resultat.

   ![Azure Stream Analytics substreams vattenstämpel bild](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Stream Analytics händelse ordning överväganden](stream-analytics-out-of-order-and-late-events.md)
- [Mätvärden för Stream Analytics-jobb](stream-analytics-monitoring.md)
