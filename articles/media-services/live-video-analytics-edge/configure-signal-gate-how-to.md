---
title: Konfigurera en signal grind för händelse-baserad videoinspelning – Azure
description: Den här artikeln innehåller rikt linjer för hur du konfigurerar en signal-grind i ett medie diagram.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380240"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurera en signal grind för händelse-baserad videoinspelning

I ett medie diagram kan du med en [Signals grind processor](media-graph-concept.md#signal-gate-processor) kan du vidarebefordra media från en nod till en annan när porten utlöses av en händelse. När den utlöses öppnas porten och den tillåter medie flödet genom en angiven varaktighet. Om det inte finns några händelser att utlösa porten, stängs porten och mediet slutar att flöda. Signal grind processorn kan användas för händelsebaserade videoinspelningar.
I den här artikeln får du lära dig mer om hur du konfigurerar en signal grind processor.

## <a name="suggested-pre-reading"></a>Föreslagen för läsning
-   [Mediegraf](media-graph-concept.md)
-   [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)


## <a name="problem"></a>Problem
Användaren kanske vill starta en viss tid före eller efter att gaten utlöstes av en händelse. Användaren känner till acceptabel latens i systemet, så användaren vill ange svars tiden för signal grind processorn. Användaren vill ange den kortaste och den längsta längden för registreringen kan vara oavsett hur många nya händelser som tas emot.
 
### <a name="use-case-scenario"></a>Användningsfallsscenario
Anta att du vill spela in video varje gång den första dörren i din byggnad öppnas. Du vill att **X** sekunderna innan dörren öppnas i inspelningen. Du vill att inspelningen ska senast minst **Y** sekunder om dörren inte öppnas igen. Du vill att inspelningen ska vara sist i de flesta **Z** sekunder, om dörren har öppnats upprepade gånger. Du vet att dörren har en fördröjning på **K** sekunder och vill minska risken för händelser som ignoreras ("sena ankomster"), så du vill att minst **K** sekunder ska ta emot händelserna.


## <a name="solution"></a>Lösning

**_Ändrar signal grind processor parametrar_* _

En signal grind processor har kon figurer ATS med 4 parametrar:
- _ *aktiverings utvärderings period**
- **förskjutning av aktiverings signal**
- **minsta aktiverings period**
- **maximalt aktiverings fönster**. 

När signalen om Signals grind utlöses förblir den öppen för den minsta aktiverings tiden. Aktiverings händelsen börjar vid tidsstämpeln för den tidigaste händelsen, plus förskjutningen för aktiverings signalen. Om signal porten processor utlöses igen, medan den är öppen, kommer timern att vara öppen för minst den minsta aktiverings tiden. Signal grind processorn kommer aldrig att vara öppen längre än den maximala aktiverings tiden. En händelse **(händelse 1)** som inträffar före en annan händelse **(händelse 2)** , baserat på mediets tidsstämplar, är underkastad att ignoreras, om systemet lags och **händelse 1** kommer till signal porten processor efter **händelse 2**. Om **händelse 1** inte anländer mellan ankomsten av **händelse 2** och **aktiverings utvärderings perioden** , ignoreras **händelse 1** och skickas inte via signal grind processorn. Korrelations-ID: n anges för varje händelse. Dessa ID: n anges från den inledande händelsen och är sekventiella för varje följande händelse.

> [!IMPORTANT]
> Medie tiden baseras på tidsstämpeln för mediet när en händelse inträffade i mediet. Sekvensen av händelser som anländer till signal porten kanske inte återspeglar sekvensen av händelser som anländer i medie tiden.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parametrar: (baserat på när händelser anländer i fysisk tid till signal porten)

* **minimumActivationTime (kortaste varaktighet för en inspelning)** = det minsta antal sekunder som signal grind processorn förblir öppen när den har Aktiver ATS för att ta emot nya händelser, om den inte avbryts av **maximumActivationTime**
* **maximumActivationTime (längsta möjliga varaktighet för en inspelning)** = det maximala antalet sekunder från den inledande händelsen att signal grind processorn förblir öppen när den har Aktiver ATS för att ta emot nya händelser, oavsett vilka händelser som tas emot
* **activationSignalOffset** = antalet sekunder mellan aktivering av Signals grind processorn och när videoinspelningen börjar, vanligt vis det här värdet är negativt för att starta inspelningen innan händelsen utlöses
* **activationEvaluationWindow** = antalet sekunder som startar från den inledande utlösaren, i vilken en händelse som inträffat före den inledande händelsen, i medie tid, måste komma till signal grind processorn innan den ignoreras och betraktas som "sent ankomst"

> [!NOTE]
> En sent införsel är en händelse som anländer när aktiverings utvärderings perioden har gått, men den här händelsen infördes före den inledande händelsen i medie tid.

### <a name="limits-of-parameters"></a>Parameter gränser

* **activationEvaluationWindow: 0 sekunder till 10 sekunder**

* **activationSignalOffset:-1 minut till 1 minut**

* **minimumActivationTime: 1 sekund till 1 timme**

* **maximumActivationTime: 1 sekund till 1 timme**


Baserat på användnings fallet skulle parametrarna anges på följande sätt:

* **activationEvaluationWindow = K s**
* **activationSignalOffset =-X s**
* **minimumActivationWindow = Y SEK**
* **maximumActivationWindow = ö s**


Här är ett exempel på vad avsnittet signal-processor-noden vill ha i en Media Graph-topologi för följande parameter värden:
* **activationEvaluationWindow = 1 sekund**
* **activationSignalOffset =-5 sekunder**
* **minimumActivationTime = 20 sekunder**
* **maximumActivationTime = 40 sekunder**

> [!IMPORTANT]
> [Varaktighets formatet i ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) förväntas för varje parameter värde. 
**T: PT1S = 1 sekund**


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Nu ska vi ta en titt på hur denna Signals grind processor konfiguration fungerar i olika registrerings scenarier.


**1 händelse från 1 källa ( *Normal aktivering* )**

En signal grind processor som tar emot en händelse skulle resultera i en inspelning som börjar "aktivering av signal förskjutning" (5) sekunder innan händelsen anlände till porten. Resten av inspelningen är "minsta aktiverings tid" (20) sekunder sedan inga andra händelser anländer innan den minsta aktiverings tiden har slutförts för att utlösa om porten.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Normal aktivering":::

* Varaktighet för inspelning =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**2 händelser från 1 källa ( *Återaktiverad aktivering* )**

En signal grind processor som tar emot två händelser resulterar i en inspelning som börjar "aktivering av signal förskjutning" (fem) sekunder innan den första händelsen anlände till porten. Den andra händelsen anländer fem sekunder efter den första händelsen, som är före "minsta aktiverings tid" (20) sekunder från den första händelsen som slutförs, och därför utlöses grinden för att vara öppen. Resten av inspelningen är "minsta aktiverings tid" (20) sekunder lång, eftersom inga andra händelser anländer innan den minsta aktiverings tiden från den andra händelsen har slutförts för att utlösa porten igen.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Återaktiverad aktivering":::

* Varaktighet för registrering =-offset + (ankomst av den första händelsen vid händelse införsel) + minimumActivationTime


**N händelser från 1 källa ( *maximal aktivering* )**

En signal grind processor som tar emot N-händelser resulterar i en inspelning som börjar "aktivering av signal förskjutning" (5) sekunder innan den första händelsen anlände till porten. När varje händelse infaller före slutförandet av "minsta aktiverings tid" (20) sekunder från föregående händelse, skulle porten kontinuerligt återkallas och förbli öppen tills "maximal aktiverings tid" (40) sekunder efter den första händelsen, där porten skulle stängas och inte längre acceptera nya händelser.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Maximal aktivering":::
 
* Varaktighet för inspelning =-offset + maximumActivationTime

> [!IMPORTANT]
> Diagram förutsätter att varje händelse kommer till samma instans i fysiska-och medie tid. (Inga sent ankomst)

## <a name="next-steps"></a>Nästa steg

### <a name="try-it-out"></a>Prova

[Själv studie kurs om Event-baserad video inspelning](event-based-video-recording-tutorial.md)

Använd den event-baserade videoinspelnings guiden för att redigera [topology.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), ändra parametrarna för signalgateProcessor-noden och följ resten av självstudien. Granska videoinspelningarna och analysera parametrarna.



