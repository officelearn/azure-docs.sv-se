---
title: Analysera din enhetsdata i ditt program med Azure IoT Central | Microsoft Docs
description: Analysera din enhetsdata i Azure IoT Central programmet.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 83225af7cb2cbdff4a264693e30b2a813f6ec9c7
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214937"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Hur du använder analytics för att analysera dina enhetsdata

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Azure IoT Central innehåller omfattande analysfunktioner funktioner för att ordna stora mängder data från dina enheter. Kom igång genom att gå till **Analytics** på den vänstra navigeringsmenyn.

## <a name="querying-your-data"></a>Köra frågor mot dina data

Du måste välja en **enhetsuppsättning**, lägga till en **filter** (valfritt), och välj en **tidsperiod** att komma igång. När du är klar klickar du på *visa resultat* att börja visualisera dina data.

* **Enhetsuppsättningar:** En [enhetsuppsättning](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) är en användardefinierad grupp av dina enheter. Till exempel Rev. alla kylskåp i Oakland eller alla 2.0 vind syfte.

* **Filter:** Du kan du lägga till filter till din söktjänst för att få fram dina data. Du kan lägga till upp till 10 filter i taget. Hitta till exempel de som har haft temperatur gå överstiger 60 grader inom alla kylskåp i Oakland.
* **Tidsperiod:** Som standard ska vi hämta data från de senaste 10 minuterna. Du kan ändra det här värdet till något av de fördefinierade tidsintervall eller välja en anpassad tidsperiod.

 ![Analytics-fråga](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisera dina data

När du har frågat dina data, kommer du att kunna börja visualisera den. Du kan visa/dölj mätning av faktisk användning, ändra hur data är sammansatt, och ytterligare dela data med olika Enhetsegenskaper.  

* **Dela efter:** Dela data med enhetsegenskaper kan du ytterligare gå nedåt i dina data. Du kan till exempel dela dina resultat av enhets-ID eller plats.

* **Mätning av faktisk användning:** Du kan välja att visa/dölj upp till 10 olika telemetri objekt som rapporteras av enheterna i taget. Mätning av faktisk användning är bland annat temperatur och fuktighet.

* **Sammansättning:** Som standard vi sammanställa informationen enligt dess medelvärde, men du kan välja att ändra sammansättning data till något annat som passar dina behov.

   ![Analytics-visualisering dela efter](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagera med dina data

Du har olika sätt att ändra frågeresultatet efter dina behov för visualisering. Du kan växla mellan en diagramvy och ett rutnät, Zooma in och ut, uppdatera datauppsättningen och ändra hur rader visas.

* **Visa rutnät för:** Resultaten är tillgängliga i tabellformat, så att du kan visa det specifika värdet för varje datapunkt. Den här vyn även uppfyller hjälpmedelsstandarder.
* **Visa diagram:** Dina resultat visas i ett linjersformat för att identifiera uppåt eller nedåt trender och avvikelser.

 ![Visar rutnätsvyn för analys](media/howto-create-analytics-experimental/analytics-showgrid.png)

Zooma låter dig till startsidan i på dina data. Om du hittar en tidsperiod som du vill fokusera på i din resultatuppsättning, använder du markören hämta det område som du vill zooma in och använda tillgängliga kontroller för att utföra någon av följande åtgärder:

* **Zooma in:** När du har valt en tidsperiod, Zooma in är aktiverat och gör att du kan zooma in till dina data.
* **Zooma ut:** Den här kontrollen kan du zooma ut en nivå från din senaste zoomning. Till exempel om du har zoomning säkerhetskopiera i dina data tre gånger, Zooma ut tar du ett steg i taget.
* **Zooma återställning:** När du har utfört olika nivåer av zoomning, kan du använda zoomkontrollen för återställning för att återgå till din ursprungliga resultatuppsättningen.

 ![Utföra zoomning på dina data](media/howto-create-analytics-experimental/analytics-zoom.png)

Du kan ändra linjeformatet för att uppfylla dina behov. Det finns fyra alternativ:

* **Rad:** En platt linje mellan var och en av datapunkterna.
* **Smidig:** En kurva mellan varje punkt.
* **Steg:** Rad mellan varje punkt i diagrammet är ett steg.
* **Punktdiagram:** Alla punkter ritas utan rader ansluta dem i diagrammet.

 ![Olika tillgängliga typer i Analytics](media/howto-create-analytics-experimental/analytics-linetypes.png)

Slutligen kan du ordna dina data på y-axeln genom att välja ett av tre lägen:

* **Liggande:** Ett diagram för varje mätning är stående och var och en av diagrammen har sina egna y-axeln. Liggande diagram är användbara när du har flera mätningar som valts och vill ha distinkta vy över dessa mätningar.
* **Ostaplade:** Ett diagram för varje mått ritas mot en y-axeln, men värdena för y-axeln ändras baserat på det markerade måttet. Ostaplade diagram är användbara när du vill täcka över flera mått och vill se mönster över dessa åtgärder för samma tidsintervall.
* **Delade y-axeln:** Alla diagram delar samma y-axeln och ändra värdena för axeln inte. Delade y-axeln diagram är användbara när du vill titta på ett enda mått vid uppdelning med delning av data.

 ![Ordna data på y-axeln med annan visualisering lägen](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar anpassade analytics för Azure IoT Central programmet är här föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Förbereda och ansluter en Node.js-program](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)