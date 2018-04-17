---
title: Azure Application Insights användning kohorter | Microsoft docs
description: Analysera olika uppsättningar eller användare, sessioner, händelser eller åtgärder som har något gemensamt
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights kohorter

En kommittén är en uppsättning användare, sessioner, händelser eller åtgärder som har något gemensamt. I Azure Application Insights definieras kohorter som av en Analytics-fråga. Om du märker att analysera en specifik uppsättning användare eller händelser upprepade gånger kohorter kan ge dig ännu större flexibilitet att uttrycka exakt uppsättningen som du är intresserad av.

![Kohort fönstret](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohort jämfört med enkla filter

Medan kohorter används på liknande sätt som filter, gör till att en kommittén definition bygger på egna frågor för Analytics att de kan vara anpassningsbar och mycket mer komplexa. Till skillnad från filter, kan du spara kohorter så att andra medlemmar i gruppen kan använda dem igen.

Du kan definiera en kommittén med användare som har alla försökt med en ny funktion i din app. Med den här kommittén sparas i Application Insights-resurs, gör det analysera specifika användargruppen i framtiden klickar du på direkt.

> [!NOTE]
> När du skapat är kohorter som tillgängliga från användare, sessioner, händelser och användaren flödar verktyg.

## <a name="example-engaged-users"></a>Exempel: engagerade användare

Din grupp definierar en engagerade användare som alla som använder din app fem eller fler gånger i en viss månad. Definiera en kommittén dessa engagerade användare.

1. Öppna den **kohorter** verktyget.

2. Klicka på **mallgalleriet** fliken. Här hittar du en samling mallar för olika kohorter.

3. Välj **ägnar åt användarna** – dagar används ”.

    Det finns tre parametrar för den här kommittén:
      * **Aktiviteter** som du kan välja vilka händelser och sidvyer ska räknas som ”användning”.
      * **Perioden** definitionen för en månad.
      * **UsedAtleastCustom** antalet gånger som de behöver för att använda något inom en period för att räknas som en engagerade användare.

4. Ändra **UsedAtleastCustom** ”5 + dagar” och lämna **Period** till standardvärdet 28 dagar.

    ![Bild](.\media\app-insights-usage-cohorts\003.png)

    Den här kommittén representerar nu alla användar-ID som skickades med en anpassad vy för händelsen eller sidan på fem dagar, separat under de senaste dagarna och 28.

5. Klicka på **Spara**.

   > [!TIP]
   >  Ge din kommittén ett namn som ”Engaged användare (5 + dagar)” och spara den till ”Mina rapporter” eller ”delad rapporter” beroende på om du vill att andra med åtkomst till denna program Insights-resurs för att se den här kommittén.

6. Klicka på **tillbaka till galleriet**.

### <a name="what-can-you-do-with-this-cohort"></a>Vad kan du göra med den här kommittén?

Öppna den **användare** verktyget > i den **visa** listrutan > Välj kommittén som du skapade under **användare som tillhör...**

Nu har verktyget användare filtrerats till den här kommittén av användare:

![Användare fönstret filtrerats till en viss kommittén](.\media\app-insights-usage-cohorts\004.png)

Några viktiga saker att Observera:
   * Det här är en uppsättning gick inte att du har skapat via normala filter. Mer är avancerad logik för datum.
   * Du kan filtrera den här kommittén med normal filter i verktyget användare ytterligare. Så medan kommittén definieras på 28 dagar windows justerar fortfarande tidsintervallet i verktyget användare vara 30, 60 och 90 dagar. 

På så sätt kan du ställa mer avancerade frågor som: _för personer som ägnat under de senaste 28 dagarna, de samma personerna funktionssättet för de senaste 60 dagarna?_ som annars skulle vara omöjligt att uttrycka via Frågeverktyget.

## <a name="example-events-cohort"></a>Exempel: händelser kommittén

Du kan också göra kohorter av händelser. Nu ska vi definiera en kommittén av händelser och sidvyer och se hur du använder dem från de andra verktygen. Detta kan vara praktiskt att definiera en uppsättning händelser som har teamet _active användning_, eller för att definiera en uppsättning händelser relaterade till en viss ny funktion.

1. Öppna den **kohorter** verktyget.

2. Klicka på den **mallgalleriet** fliken. Här hittar du en samling mallar för olika kohorter.

3. Välj **händelser väljaren**.

    ![Skärmbild av händelser objektväljare](.\media\app-insights-usage-cohorts\006.png)

4. I den **aktiviteter** listrutan väljer du de händelser som du vill ha kommittén

5. Spara kommittén och ge det ett namn.

## <a name="example-active-users-where-you-modify-query"></a>Exempel: aktiva användare där du kan ändra frågan

Föregående två kohort definierades med hjälp av nedrullningsbara listorna. Men vi kan också definiera kohorter med Analytics-frågor för totalt flexibilitet. Nu ska vi se hur genom att skapa en kommittén användare från Storbritannien.

![Animerad bild igenom användningen av kohorter-verktyget](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Öppna den **kohorter** verktyget > klickar du på **mallgalleriet** fliken > Välj **tomt användare kommittén**.

    ![Tomt användare kommittén](.\media\app-insights-usage-cohorts\001.png)

    Det finns tre delar:
       * Ett Markdown textavsnitt där du kan beskriva kommittén i detalj för andra i din grupp.

       * Ett avsnitt för parametrar som du kan använda för att göra dina egna parametrar som den **aktiviteter** och andra nedrullningsbara listorna i föregående två exempel.

       * En del av frågan som används för att definiera kommittén med en Analytics-fråga.

    I avsnittet frågan du [skriva en fråga Analytics](https://docs.loganalytics.io/index) som väljer vissa uppsättningen rader som beskriver kommittén som du vill definiera. Verktyget kohort implicit lägger till en ”| Sammanfatta av user_Id ”-sats i frågan. Detta förhandsgranskas nedan frågan i en tabell så att du kan kontrollera att frågan returnerar resultat.

    > [!NOTE]
    > Om du inte ser frågan och försök att ändra storlek på avsnittet om du vill göra den högre och visa frågan. Animerad GIF i början av det här avsnittet illustrerar beteendet för storleksändring.

2. Kopiera / Klistra in följande i frågeredigeraren:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Klicka på **Kör fråga**. Du bör se användar-ID som visas i tabellen. Om du inte ändra till ett land där programmet har användare.

4. Spara och namnge kommittén.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

_Jag har definierat en kommittén användare från ett visst land. När jag jämföra den här kommittén i verktyget användare att bara definiera filter för landet i verktyget användare visas olika resultat. Varför?_

Kohorter och filter är olika. Anta att du har en kommittén av användare från Storbritannien (definieras som i ovanstående exempel) och du jämföra dess resultat för att ange filtret ”land eller region = Storbritannien”.

* Kommittén version visar alla händelser från användare som har skickat minst en händelse från Storbritannien i det aktuella tidsintervallet. Om du dela efter land eller region visas antagligen många andra länder och regioner.
* Filter-versionen visas endast händelser från Storbritannien. Medan Storbritannien visas bara om du dela efter land eller region.

## <a name="learn-more"></a>Läs mer
- [Analytics-frågespråket](https://go.microsoft.com/fwlink/?linkid=856587)
- [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
- [Användarflöden](app-insights-usage-flows.md)
- [Översikt över användning](app-insights-usage-overview.md)