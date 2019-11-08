---
title: Analysera enhets data i ditt Azure IoT Central-program | Microsoft Docs
description: Den här artikeln beskriver hur du analyserar enhets data i ditt Azure IoT Central-program med hjälp av frågor och visualiseringar.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827983"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Använda analyser för att analysera enhets data

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Azure IoT Central tillhandahåller omfattande analys funktioner för att kunna ta del av stora mängder data från dina enheter. Kom igång genom att gå till **Analytics** i det vänstra fönstret.

## <a name="querying-your-data"></a>Fråga dina data

Du måste välja en **enhets uppsättning**, lägga till ett **filter** (valfritt) och välja en **tids period** för att komma igång. När du är klar väljer du **Visa resultat** för att börja visualisera dina data.

* **Enhets uppsättningar:** En [enhets uppsättning](howto-use-device-sets.md) är en användardefinierad grupp av dina enheter. Till exempel alla kyl skåp i Oakland eller alla vridnings turbiner på rev 2,0.

* **Filter:** Du kan också lägga till filter i sökningen för att finslipa i dina data. Du kan lägga till upp till 10 filter i taget. I alla kyl skåp i Oakland hittar du till exempel de som har haft en temperatur på över 60 grader.
* **Tids period:** Som standard hämtar vi data från de senaste 10 minuterna. Du kan ändra det här värdet till ett av de fördefinierade tidsintervallen eller välja en anpassad tids period.

  ![Analytics-fråga](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisera dina data

När du har frågat dina data kan du börja visualisera det. Du kan visa/dölja mått, ändra hur data aggregeras och ytterligare dela data efter olika enhets egenskaper.  

* **Dela efter:** Genom att dela data efter enhets egenskaper kan du öka detalj nivån för dina data. Du kan till exempel dela upp resultaten efter enhets-ID eller plats.

* **Mått:** Du kan välja att visa/dölja upp till 10 olika telemetridata som rapporteras av enheterna i taget. Mätningar är saker som temperatur och fuktighet.

* **Sammansättning:** Som standard samlar vi in data i genomsnitt, men du kan välja att ändra data agg regeringen till något annat för att passa dina behov.

   ![Analys visualisering delas av](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagera med dina data

Du har olika sätt att ändra frågeresultaten så att de passar dina visualiserings behov. Du kan växla mellan en diagramvy och en rutnätsvy, zooma in och ut, uppdatera data uppsättningen och ändra hur rader visas.

* **Visa rutnät:** Resultaten är tillgängliga i ett tabell format så att du kan se det exakta värdet för varje data punkt. Den här vyn uppfyller även tillgänglighets standarder.
* **Visa diagram:** Resultaten visas i ett linje format för att hjälpa dig att identifiera uppåt eller nedåt i trender och avvikelser.

  ![Visar diagramvyn för analys](media/howto-create-analytics/analytics-showgrid.png)

Med zoomning kan du gå in på dina data. Om du hittar en tids period som du vill fokusera på i din resultat uppsättning kan du använda markören för att hämta området som du vill zooma in på och använda de tillgängliga kontrollerna för att utföra någon av följande åtgärder:

* **Zooma in:** När du har valt en tids period är zooma in aktiverat och du kan zooma in till dina data.
* **Zooma ut:** Med den här kontrollen kan du zooma ut en nivå från din senaste zoomning. Om du till exempel har zoomat in till dina data tre gånger tar du en titt på ett steg i taget när du zoomar ut.
* **Återställ zoom:** När du har utfört olika nivåer av zoomning kan du använda kontrollen zooma tillbaka för att återgå till den ursprungliga resultat uppsättningen.

  ![Utför zoomning på dina data](media/howto-create-analytics/analytics-zoom.png)

Du kan ändra linje formatet så att det passar dina behov. Du har fyra alternativ:

* **Rad:** En plan linje mellan var och en av data punkterna.
* **Mjukt:** En böjd linje mellan varje punkt.
* **Steg:** Linje mellan varje punkt i diagrammet är ett steg.
* **Punkt:** Alla punkter ritas i diagrammet utan linjer som ansluter dem.

  ![Olika linje typer som är tillgängliga i Analytics](media/howto-create-analytics/analytics-linetypes.png)

Slutligen kan du ordna data på Y-axeln genom att välja ett av tre lägen:

* **Staplad:** En graf för varje mått är staplad och varje graf har sin egen Y-axel. Staplade diagram är användbara när du har valt flera mått och vill ha en distinkt vy över dessa mått.
* **Unstacked:** En graf för varje mått ritas mot en Y-axel, men värdena för Y-axeln ändras baserat på det markerade måttet. Staplade diagram är användbara när du vill täcka över flera mått och vill se mönster över dessa mått för samma tidsintervall.
* **Delad Y-axel:** Alla grafer delar samma Y-axel och värdena för axeln ändras inte. Delade Y-axel-diagram är användbara när du vill titta på ett enda mått och dela upp data med Split-by.

  ![Arrangera data över y-axeln med olika visualiserings lägen](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar anpassade analyser för ditt Azure IoT Central-program så är det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Förbereda och ansluta ett Node. js-program](howto-connect-nodejs.md)