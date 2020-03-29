---
title: Förstå tidshantering i Azure Stream Analytics
description: Lär dig hur tidshantering fungerar i Azure Stream Analytics, till exempel hur du väljer den bästa starttiden, hur du hanterar sena och tidiga händelser och tidshanteringsmått.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 367b7c2e1ce1c8b3c0dbc02003218b76096b409d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354653"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Förstå tidshantering i Azure Stream Analytics

I den här artikeln diskuterar vi hur du kan göra designval för att lösa praktiska tidshanteringsproblem i Azure Stream Analytics-tjänsten. Designbeslut för tidshantering är nära relaterade till händelsebeställningsfaktorer.

## <a name="background-time-concepts"></a>Bakgrundstidsbegrepp

Om du vill rama in diskussionen bättre ska vi definiera några bakgrundsbegrepp:

- **Händelsetid**: Den tid då den ursprungliga händelsen inträffade. Till exempel när en bil i rörelse på motorvägen närmar sig en vägtull monter.

- **Bearbetningstid**: Den tid då händelsen når bearbetningssystemet och observeras. Till exempel, när en vägtull monter sensor ser bilen och datorsystemet tar en stund att bearbeta data.

- **Vattenstämpel**: En händelsetidsmarkör som anger upp till vilken punkt händelser har ingressed till strömning processorn. Vattenstämplar låter systemet indikera tydliga framsteg vid intag av händelserna. Av strömmarnas natur slutar aldrig inkommande händelsedata, så vattenstämplar anger förloppet till en viss punkt i strömmen.

   Vattenstämpelkonceptet är viktigt. Vattenstämplar gör att Stream Analytics kan avgöra när systemet kan producera fullständiga, korrekta och repeterbara resultat som inte behöver dras tillbaka. Bearbetningen kan göras på ett garanterat sätt som är förutsägbar och repeterbar. Om en omräkning till exempel behöver göras för vissa felhanteringstillstånd är vattenstämplar säkra start- och slutpunkter.

Som ytterligare resurser i detta ämne, se Tyler Akidau's blogginlägg [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) och [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Välja den bästa starttiden

Stream Analytics ger användarna två alternativ för att välja händelsetid:

1. **Ankomsttid**  

   Ankomsttid tilldelas vid indatakällan när händelsen når källan. Du kan komma åt ankomsttid med egenskapen **EventEnqueuedUtcTime** för eventhubbar, **egenskapen IoTHub.EnqueuedTime** för IoT Hub och använda egenskapen **BlobProperties.LastModified** för blob-indata.

   Att använda ankomsttid är standardbeteendet och används bäst för dataarkiveringsscenarier, där det inte finns någon tidsmässig logik nödvändig.

2. **Programtid** (även kallad händelsetid)

   Programtid tilldelas när händelsen genereras och den är en del av händelsenyttolasten. Om du vill bearbeta händelser efter programtid använder du **tidsstämpeln efter sats** i urvalsfrågan. Om **tidsstämpeln enligt** satsen saknas bearbetas händelser efter ankomsttid.

   Det är viktigt att använda en tidsstämpel i nyttolasten när tidslogik är inblandad. På så sätt kan fördröjningar i källsystemet eller i nätverket redovisas.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Hur tiden fortskrider i Azure Stream Analytics

När du använder programtid baseras tidsprogressionen på inkommande händelser. Det är svårt för dataflödet att veta om det inte finns några händelser eller om händelser är försenade. Därför genererar Azure Stream Analytics heuristiska vattenstämplar på följande sätt för varje indatapartition:

1. När det finns någon inkommande händelse, är vattenstämpeln den största händelsetiden vi har sett hittills minus den out-of-order tolerans fönsterstorlek.

2. När det inte finns någon inkommande händelse är vattenstämpeln den aktuella uppskattade ankomsttiden (den förflutna tiden på bakom kulisserna VM som bearbetar händelserna från förra gången en indatahändelse visas plus den indatahändelsens ankomsttid) minus toleransfönstret för sen ankomst.

   Ankomsttiden kan bara beräknas eftersom den verkliga ankomsttiden genereras på indatahändelsemäklaren, till exempel eventhubbar och inte Azure Stream Analytics VM som bearbetar händelserna.

Designen tjänar ytterligare två syften, förutom att generera vattenstämplar:

1. Systemet genererar resultat i tid med eller utan inkommande händelser.

   Du har kontroll över hur snabbt de vill se utdataresultaten. På sidan Händelsebeställning på sidan **Händelsebeställning i** ditt Stream Analytics-jobb kan du konfigurera inställningen **För inte ordning-händelser.** När du konfigurerar den inställningen bör du tänka på avvägningen av aktualitet med tolerans för händelser utanför ordningen i händelseströmmen.

   Toleransfönstret för sen ankomst är viktigt för att fortsätta generera vattenstämplar, även i avsaknad av inkommande händelser. Ibland kan det finnas en period där inga inkommande händelser kommer in, till exempel när en händelseinmatningsström är gles. Det problemet förvärras av användningen av flera partitioner i input händelse mäklare.

   Strömmande databehandlingssystem utan ett toleransfönster för sen ankomst kan drabbas av fördröjda utdata när indata är glesa och flera partitioner används.

2. Systembeteendet måste vara repeterbart. Repeterbarhet är en viktig egenskap hos ett databehandlingssystem för direktuppspelning.

   Vattenstämpeln härleds från ankomsttid och appliceringstid. Båda är framhärdade i händelsen mäklare, och därmed repeterbara. Om ankomsttiden måste beräknas i frånvaro av händelser, azure Stream Analytics journaler den beräknade ankomsttiden för repeterbarhet under repris i syfte att återställa fel.

Observera att när du väljer att använda **ankomsttid** som händelsetid, finns det ingen anledning att konfigurera den out-of-order tolerans och sen ankomst tolerans. Eftersom **ankomsttiden** garanterat ökar monotoniskt i kommendingshändelsemäklaren, bortser Azure Stream Analytics helt enkelt från konfigurationerna.

## <a name="late-arriving-events"></a>Sena ankomstevenemang

Per definition av toleransfönster för sent ankomst jämför Azure Stream Analytics **händelsetiden** för varje inkommande händelse med **ankomsttiden.** Om händelsetiden ligger utanför toleransfönstret kan du konfigurera systemet så att det antingen släpper händelsen eller justerar händelsens tid så att den ligger inom toleransen.

Tänk på att när vattenstämplar har genererats kan tjänsten eventuellt ta emot händelser med händelsetid som är lägre än vattenstämpeln. Du kan konfigurera tjänsten så att den antingen **släpper** dessa händelser eller **justerar** händelsens tid till vattenstämpelvärdet.

Som en del av justeringen anges händelsens **System.Timestamp** till det nya värdet, men själva **händelsetidsfältet** ändras inte. Den här justeringen är den enda situationen där en händelse **System.Timestamp** kan skilja sig från värdet i händelsetidsfältet och kan orsaka att oväntade resultat genereras.

## <a name="handling-time-variation-with-substreams"></a>Hantering av tidsvariation med underströmmar

Den heuristiska vattenstämpelgenereringsmekanismen som beskrivs här fungerar bra i de flesta fall där tiden oftast synkroniseras mellan de olika händelseavsändarena. Men i verkliga livet, särskilt i många IoT-scenarier, har systemet liten kontroll över klockan på händelseavsändare. Händelseavsändare kan vara alla typer av enheter på fältet, kanske på olika versioner av hårdvara och mjukvara.

I stället för att använda en vattenstämpel som är global för alla händelser i en indatapartition har Stream Analytics en annan mekanism som kallas underströmmar som hjälper dig. Du kan använda underströmmar i jobbet genom att skriva en jobbfråga som använder [**TIMESTAMP BY-satsen**](/stream-analytics-query/timestamp-by-azure-stream-analytics) och nyckelordet **OVER**. Om du vill ange underström anger du ett nyckelkolumnnamn efter nyckelordet **OVER,** till exempel ett `deviceid`, så att tidsprinciper tillämpas i den kolumnen. Varje subström får sin egen oberoende vattenstämpel. Den här mekanismen är användbar för att möjliggöra snabb utdatagenerering, när det handlar om stora klocksnedställningar eller nätverksförseningar bland händelseavsändare.

Underströmmar är en unik lösning som tillhandahålls av Azure Stream Analytics och erbjuds inte av andra databehandlingssystem för direktuppspelning. Stream Analytics tillämpar toleransfönstret för sen ankomst på inkommande händelser när underströmmar används. Standardinställningen (5 sekunder) är sannolikt för liten för enheter med divergerande tidsstämplar. Vi rekommenderar att du börjar med 5 minuter och gör justeringar enligt deras enhet klocka skeva mönster.

## <a name="early-arriving-events"></a>Tidiga ankomstevenemang

Du kanske har märkt ett annat koncept som kallas tidig ankomst fönster, som ser ut som motsatsen till sen ankomst tolerans fönster. Detta fönster är fast på 5 minuter, och tjänar ett annat syfte än sen ankomst en.

Eftersom Azure Stream Analytics garanterar att det alltid genererar fullständiga resultat kan du bara ange **jobbstarttid** som jobbets första utdatatid, inte inmatningstiden. Starttiden för jobbet krävs så att hela fönstret bearbetas, inte bara från mitten av fönstret.

Stream Analytics hämtar sedan starttiden från frågespecifikationen. Men eftersom indatahändelsemäklare bara indexeras efter ankomsttid måste systemet översätta starthändelsetiden till ankomsttid. Systemet kan börja bearbeta händelser från den punkten i indatahändelsemäklaren. Med den tidiga ankomstfönstergränsen är översättningen enkel. Det är startdatum minus 5 minuter tidigt ankomst fönster. Den här beräkningen innebär också att systemet släpper alla händelser som ses med händelsetid 5 minuter större än ankomsttiden.

Detta koncept används för att säkerställa att bearbetningen kan upprepas oavsett var du börjar mata ut från. Utan en sådan mekanism skulle det inte vara möjligt att garantera repeterbarhet, vilket många andra streamingsystem hävdar att de gör.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Biverkningar av händelsebeställningstidstoleranser

Stream Analytics-jobb har flera alternativ **för händelsebeställning.** Två kan konfigureras i Azure-portalen: **händelser i oordning** (oriktiga tolerans) och inställningen **Händelser som kommer sent** (tolerans för sen ankomst). Toleransen **för tidig ankomst** är fast och kan inte justeras. Dessa tidsprinciper används av Stream Analytics för att ge starka garantier. Dessa inställningar har dock vissa ibland oväntade konsekvenser:

1. Oavsiktligt skicka händelser som är för tidigt.

   Tidiga händelser bör inte matas ut normalt. Det är möjligt att tidiga händelser skickas till utdata om avsändarens klocka går för fort ändå. Alla tidiga ankomsthändelser släpps, så du kommer inte att se någon av dem från utdata.

2. Skicka gamla händelser till eventhubbar som ska bearbetas av Azure Stream Analytics.

   Medan gamla händelser kan verka ofarliga i början, på grund av tillämpningen av den sena ankomsttoleransen, kan de gamla händelserna släppas. Om händelserna är för gamla ändras **systemstämpelvärdet** vid händelseintag. På grund av det här beteendet är Azure Stream Analytics för närvarande mer lämpad för scenarier för händelsebearbetning i nära realtid, i stället för historiska händelsebearbetningsscenarier. Du kan ställa in **att händelser som kommer sent** till största möjliga värde (20 dagar) ska kunna undvika detta i vissa fall.

3. Resultaten verkar vara försenade.

   Den första vattenstämpeln genereras vid den beräknade tiden: den **maximala händelsetiden** som systemet har observerat hittills, minus toleransfönstrets storlek utanför ordningen. Som standard är toleransen för oordning konfigurerad till noll (00 minuter och 00 sekunder). När du ställer in det på ett högre, icke-noll tidsvärde, försenas strömningsjobbets första utdata med det tidsvärdet (eller större) på grund av den första vattenstämpeltiden som beräknas.

4. Ingångarna är glesa.

   När det inte finns någon ingång i en viss partition beräknas vattenstämpeltiden som **ankomsttid** minus toleransfönstret för sen ankomst. Om indatahändelser är ovanliga och glesa kan utdata därför fördröjas med den tiden. Standardvärdet **Händelser som kommer sent** är 5 sekunder. Du bör förvänta dig att se en viss fördröjning när du skickar indatahändelser en i taget, till exempel. Förseningarna kan förvärras när du ställer in **händelser som kommer sent** fönster till ett stort värde.

5. **System.Timestamp-värdet** skiljer sig från tiden i **händelsetidsfältet.**

   Som tidigare beskrivits justerar systemet händelsetiden med de out-of-order tolerans eller sen ankomst tolerans fönster. **Händelsens system-tidsstämpelvärde** justeras, men inte **händelsetidsfältet.**

## <a name="metrics-to-observe"></a>Mått att observera

Du kan observera ett antal av händelsebeställningstidstoleranseffekterna via [Dataanalys-jobbmått](stream-analytics-monitoring.md). Följande mått är relevanta:

|Mått  | Beskrivning  |
|---------|---------|
| **Händelser utanför ordningen** | Anger antalet inkomna händelser som togs emot i oordning, som antingen har tappats eller fått en justerad tidsstämpel. Det här måttet påverkas direkt av konfigurationen av inställningen **För inte ordning-händelser** på sidan **Händelsebeställning** på jobbet i Azure-portalen. |
| **Sena indatahändelser** | Anger antalet händelser som kommer sent från källan. Det här måttet innehåller händelser som har tagits bort eller som har fått sin tidsstämpel justerad. Det här måttet påverkas direkt av konfigurationen av de **händelser som kommer sent** in på sidan **Händelsebeställning** på jobbet i Azure-portalen. |
| **Tidiga inmatningshändelser** | Anger antalet händelser som anländer tidigt från källan som antingen har tagits bort, eller så har deras tidsstämpel justerats om de är längre än 5 minuter för tidigt. |
| **Fördröjning av vattenstämpel** | Anger fördröjningen för databearbetningsjobbet för direktuppspelning. Se mer information i följande avsnitt.|

## <a name="watermark-delay-details"></a>Information om fördröjning av vattenstämpel

Mätvärden **för vattenstämpelfördröjning** beräknas som väggklockatiden för bearbetningsnoden minus den största vattenstämpeln som den har sett hittills. Mer information finns i [blogginlägget om vattenstämpelfördröjning](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Det kan finnas flera orsaker till att det här måttvärdet är större än 0 under normal drift:

1. Inbyggd bearbetningsfördröjning av strömningspipelinen. Normalt är denna fördröjning nominell.

2. Toleransfönstret i oordning introducerades fördröjning, eftersom vattenstämpeln minskas med toleransfönstrets storlek.

3. Det sena ankomstfönstret introducerade fördröjning, eftersom vattenstämpeln minskas med storleken på toleransfönstret.

4. Klocksnedställning för den bearbetningsnod som genererar måttet.

Det finns ett antal andra resursbegränsningar som kan orsaka att pipelinen för direktuppspelning saktar ned. Mätvärden för vattenstämpelfördröjning kan öka på grund av:

1. Det finns inte tillräckligt med bearbetningsresurser i Stream Analytics för att hantera volymen av indatahändelser. Om du vill skala upp resurser finns i [Förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md).

2. Inte tillräckligt med dataflöde i indatahändelsemäklare, så de begränsas. Möjliga lösningar finns i [Skala automatiskt upp Azure Event Hubs-dataflödesenheter](../event-hubs/event-hubs-auto-inflate.md).

3. Utdatamottagare är inte etablerade med tillräcklig kapacitet, så de begränsas. De möjliga lösningarna varierar kraftigt beroende på smaken av produktionen tjänst som används.

## <a name="output-event-frequency"></a>Händelsefrekvens för utdata

Azure Stream Analytics använder vattenstämpelstatus som den enda utlösaren för att skapa utdatahändelser. Eftersom vattenstämpeln härleds från indata kan den upprepas vid felåterställning och även i användarinitierad upparbetning.

När du använder [fönsterade aggregat](stream-analytics-window-functions.md)producerar tjänsten endast utdata i slutet av fönstren. I vissa fall kanske användare vill se partiella aggregat som genereras från fönstren. Partiella aggregat stöds inte för närvarande i Azure Stream Analytics.

I andra strömningslösningar kan utdatahändelser materialiseras vid olika triggerpunkter, beroende på yttre omständigheter. Det är möjligt i vissa lösningar att utdatahändelserna för ett visst tidsfönster kan genereras flera gånger. När indatavärdena förfinas blir de sammanlagda resultaten mer exakta. Händelser kan spekuleras i början, och revideras över tiden. När en viss enhet till exempel är offline från nätverket kan ett uppskattat värde användas av ett system. Senare kommer samma enhet online till nätverket. Då kan de faktiska händelsedata inkluderas i indataströmmen. Utdata från bearbetningen av tidsfönstret ger mer exakta utdata.

## <a name="illustrated-example-of-watermarks"></a>Illustrerat exempel på vattenstämplar

Följande bilder illustrerar hur vattenstämplar fortskrider under olika omständigheter.

I den här tabellen visas exempeldata som visas nedan. Observera att händelsetiden och ankomsttiden varierar, ibland matchning och ibland inte.

| Händelsetid | Ankomsttid | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | enhet3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | enhet3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | enhet3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | enhet3

I den här bilden används följande toleranser:

- Fönster för tidig ankomst är 5 minuter
- Sent ankomstfönster är 5 minuter
- Beställningsfönstret är 2 minuter

1. Illustration av vattenstämpel som fortskrider genom dessa händelser:

   ![Vattenstämpelillustration för Azure Stream Analytics](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Noterbara processer illustreras i föregående grafik:

   1. Den första händelsen (enhet1) och den andra händelsen (device2) har justerat tider och bearbetas utan justeringar. Vattenstämpeln fortskrider för varje händelse.

   2. När den tredje händelsen (enhet1) bearbetas föregår ankomsttiden (12:11) händelsetiden (12:17). Evenemanget kom 6 minuter för tidigt, så händelsen släpps på grund av 5-minuters tidig ankomst tolerans.

      Vattenstämpeln går inte vidare i det här fallet med en tidig händelse.

   3. Den fjärde händelsen (device3) och femte händelsen (device1) har justerat tider och bearbetas utan justering. Vattenstämpeln fortskrider för varje händelse.

   4. När den sjätte händelsen (device3) bearbetas ligger ankomsttiden (12:17) och händelsetiden (12:12) under vattenstämpelnivån. Händelsetiden justeras till vattenmarkeringsnivån (12:17).

   5. När den tolfte händelsen (device3) bearbetas är ankomsttiden (12:27) 6 minuter före händelsetiden (12:21). Principen för sen ankomst tillämpas. Händelsetiden justeras (12:22), som ligger ovanför vattenstämpeln (12:21) så ingen ytterligare justering tillämpas.

2. Andra illustrationen av vattenstämpeln fortskrider utan en politik för tidig ankomst:

   ![Azure Stream Analytics ingen tidig princip vattenstämpel illustration](media/stream-analytics-time-handling/watermark-graph-2.png)

   I det här exemplet tillämpas ingen princip för tidig ankomst. Avvikare händelser som anländer tidigt höja vattenstämpeln betydligt. Observera att den tredje händelsen (deviceId1 vid tiden 12:11) inte tas bort i det här scenariot och vattenstämpeln höjs till 12:15. Den fjärde händelsetiden justeras framåt 7 minuter (12:08 till 12:15) som följd.

3. I den slutliga bilden används underströmmar (OVER the DeviceId). Flera vattenstämplar spåras, en per ström. Det finns färre händelser med sina tider justeras som ett resultat.

   ![Azure Stream Analytics-delströmmar vattenstämpelillustration](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Nästa steg

- [Azure Stream Analytics händelseorderöverväganden](stream-analytics-out-of-order-and-late-events.md)
- [Sök efter jobbmått för Stream Analytics](stream-analytics-monitoring.md)
