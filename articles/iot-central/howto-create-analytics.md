---
title: Analysera din enhetsdata i ditt program med Azure IoT Central | Microsoft Docs
description: Analysera din enhetsdata i Azure IoT Central programmet.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bf86e769aff4a9b03d5df1b1aef702814c605fa4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368094"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Hur du använder analytics för att analysera dina enhetsdata


*Den här artikeln gäller för operatörer, hjälpverktyg och administratörer.*


Microsoft Azure IoT Central innehåller omfattande analysfunktioner funktioner för att ordna stora mängder data från dina enheter. Kom igång genom att gå till **Analytics** på den vänstra navigeringsmenyn. 

  ![IoT Central navigering till analytics](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Köra frågor mot dina data

Du måste välja en **enhetsuppsättning**, lägga till en **filter** (valfritt), och välj en **tidsperiod** att komma igång. När du är klar klickar du på *visa resultat* att börja visualisera dina data.


* **Enhetsuppsättningar:** A [enhetsuppsättning](howto-use-device-sets.md) är en användardefinierad grupp av dina enheter. Till exempel Rev. alla kylskåp i Oakland eller alla 2.0 vind syfte.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filter:** du kan du lägga till filter till din söktjänst för att få fram dina data. Du kan lägga till upp till 10 filter i taget. Hitta till exempel de som har haft temperatur gå överstiger 60 grader inom alla kylskåp i Oakland. 
* **Tidsperiod:** som standard ska vi hämta data från de senaste 10 minuterna. Du kan ändra det här värdet till något av de fördefinierade tidsintervall eller välja en anpassad tidsperiod. 

 ![Analysfråga](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisera dina data

När du har frågat dina data, kommer du att kunna börja visualisera den. Du kan visa/dölj mätning av faktisk användning, ändra hur data är sammansatt, och ytterligare dela data med olika Enhetsegenskaper.  

* **Dela med:** dela data med enhetsegenskaper gör det möjligt för ytterligare gå nedåt i dina data. Du kan till exempel dela dina resultat av enhets-ID eller plats.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Mätning av faktisk användning:** du kan välja att visa/dölj upp till 10 olika telemetri objekt som rapporteras av enheterna i taget. Mätning av faktisk användning är bland annat temperatur och fuktighet. 
* **Sammansättning:** som standard vi sammanställa informationen enligt dess medelvärde, men du kan välja att ändra sammansättning data till något annat som passar dina behov. 

   ![Analytics-visualisering](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Analytics-visualisering dela efter](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagera med dina data

Det finns olika sätt som ändrar ytterligare frågeresultatet efter dina behov för visualisering. Du kan växla mellan en diagramvy och ett rutnät, Zooma in/ut, uppdatera datauppsättningen och ändra hur rader visas.

* **Visa rutnät:** ditt resultatet blir tillgängliga i tabellformat till så att du kan visa det specifika värdet för varje datapunkt. Den här vyn även uppfyller hjälpmedelsstandarder. 
* **Visa diagram:** dina resultat visas i en rad-format för att enkelt vinklar uppåtgående/nedåtgående trender och avvikelser. 

 ![Visar rutnätsvyn för analys](media\howto-create-analytics\analytics-showgrid.png)

Zoomning kan du fokusera på dina data. Om du hittar en viss tidsperiod vill du fokusera på i din resultatmängden med hjälp av din markören dra det område som du vill zooma in och använda tillgängliga kontroller för att utföra någon av följande åtgärder:
* **Zooma in:** när du har valt en tidsperiod, Zooma i aktiveras så att du kan zooma in till dina data.
* **Zooma ut:** den här kontrollen kan du zooma ut en nivå från din senaste zoomning. Till exempel om du har zoomar in till dina data tre gånger, Zooma ut tar du säkerhetskopiera ett steg i taget.
* **Zooma återställning:** när du har utfört olika nivåer av zoomning, du kan använda zoomkontrollen för återställning för att återgå till din ursprungliga resultatuppsättningen. 

 ![Utföra zoomning på dina data](media\howto-create-analytics\analytics-zoom.png)


Du kan ändra linjeformatet för att uppfylla dina behov. Du har fyra alternativ att välja mellan:
* **Rad:** består en platt linje mellan var och en av datapunkterna. 
* **Smooth:** består en kurva mellan varje punkt
* **Steg:** linjen mellan varje punkt i diagrammet skapar ett stegdiagram
* **Punktdiagram:** alla punkter ritas i diagrammet utan att ansluta dem rader. 

 ![Olika tillgängliga typer i Analytics](media\howto-create-analytics\analytics-linetypes.png)

Slutligen kan du ordna dina data på y-axeln genom att välja någon av tre lägen:

* **Liggande:** ett diagram för varje mätning stående och var och en av diagrammen har sina egna y-axeln. Liggande diagram är användbara när du har flera mätningar som valts och vill ha distinkta vy över dessa mätningar.
* **Ostaplade:** ett diagram för varje mått ritas mot en y-axeln, men värdena för y-axeln ändras baserat på det markerade måttet. Ostaplade diagram är användbara när du vill täcka över flera mått och vill se mönster över dessa åtgärder för samma tidsintervall.
* **Delade y-axeln:** alla diagram dela samma y-axeln och ändra värdena för axeln inte. Delade y-axeln diagram är användbara när du vill titta på ett enda mått vid uppdelning med delning av data.

 ![Ordna data på y-axeln med annan visualisering lägen](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar anpassade analytics för Azure IoT Central programmet är här föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Förbereda och ansluter en Node.js-program](howto-connect-nodejs.md)