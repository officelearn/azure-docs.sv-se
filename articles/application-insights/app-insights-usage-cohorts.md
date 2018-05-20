---
title: Azure Application Insights användning kohorter | Microsoft Docs
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
ms.openlocfilehash: f8d566f552c86f749b914ffed70512209ad76ab7
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights kohorter

En kommittén är en uppsättning användare, sessioner, händelser eller åtgärder som har något gemensamt. I Azure Application Insights definieras kohorter som av en analytics-fråga. I fall där du behöver analysera en specifik uppsättning användare eller händelser upprepade gånger kohorter kan ge dig större flexibilitet att uttrycka exakt uppsättningen som du är intresserad av.

![Kohort fönstret](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohort jämfört med enkla filter

Kohorter används på sätt som liknar filter. Men kohorter definitioner skapas från anpassade analytics frågor, så att de är mycket anpassningsbara och komplexa. Till skillnad från filter, kan du spara kohorter så att andra medlemmar i gruppen kan använda dem igen.

Du kan definiera en kommittén med användare som har alla försökt med en ny funktion i din app. Du kan spara den här kommittén i Application Insights-resurs. Det är lätt att analysera sparade gruppen för specifika användare i framtiden.

> [!NOTE]
> När de skapas, är kohorter som tillgängliga från användare, sessioner, händelser och användaren flödar verktyg.

## <a name="example-engaged-users"></a>Exempel: Engagerade användare

Din grupp definierar en engagerade användare som alla som använder din app fem eller fler gånger i en viss månad. I det här avsnittet kan du definiera en kommittén dessa engagerade användare.

1. Öppna verktyget kohorter.

2. Välj den **mallgalleriet** fliken. Du kan se en samling mallar för olika kohorter.

3. Välj **engagerade användare--dagar används**.

    Det finns tre parametrar för den här kommittén:
    * **Aktiviteter**, där du väljer vilka händelser och sidvyer räknas som ”användning”.
    * **Perioden**, definitionen för en månad.
    * **UsedAtleastCustom**, antalet gånger som användare behöver använda något inom en period räkna när ägnar åt.

4. Ändra **UsedAtleastCustom** till **5 + dagar**, och lämna **Period** standard 28 dagar.

    ![Engagerade användare](.\media\app-insights-usage-cohorts\003.png)

    Nu den här kommittén representerar alla användar-ID som skickas med en anpassad vy för händelsen eller sidan 5 avgränsa dagar bakåt i tiden 28.

5. Välj **Spara**.

   > [!TIP]
   >  Ge din kommittén ett namn som ”Engaged användare (5 + dagar)”. Spara den till ”Mina rapporter” eller ”delat rapporter”, beroende på om du vill att andra personer som har åtkomst till denna Application Insights-resurs för att se den här kommittén.

6. Välj **tillbaka till galleriet**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Vad kan du göra genom att använda den här kommittén?

Öppna verktyget användare. I den **visa** listrutan väljer du kommittén som du skapade under **användare som tillhör**.

Nu har verktyget användare filtrerats till den här kommittén av användare:

![Användare fönstret filtrerats till en viss kommittén](.\media\app-insights-usage-cohorts\004.png)

Några viktiga saker att Observera:
* Du kan inte skapa den här uppsättningen genom normal filter. Mer är avancerad logik för datum.
* Du kan filtrera den här kommittén ytterligare med hjälp av normal filter i verktyget användare. Så även om kommittén definieras på 28 dagar windows justerar fortfarande tidsintervallet i verktyget användare vara 30, 60 och 90 dagar.

Dessa filter har stöd för mer avancerade frågor som är omöjligt att uttrycka via Frågeverktyget. Ett exempel är _personer som ägnat under de senaste 28 dagarna. Hur fungerar personer samma under de senaste 60 dagarna?_

## <a name="example-events-cohort"></a>Exempel: Händelser kommittén

Du kan också göra kohorter av händelser. I det här avsnittet kan du definiera en kommittén av händelser och sidvyer. Sedan kan du se hur du använder dem från andra verktyg. Den här kommittén kan definiera en uppsättning händelser som har teamet _active användning_ eller en uppsättning relaterade till en viss ny funktion.

1. Öppna verktyget kohorter.

2. Välj den **mallgalleriet** fliken. Du ser en samling mallar för olika kohorter.

3. Välj **händelser väljaren**.

    ![Skärmbild av händelser objektväljare](.\media\app-insights-usage-cohorts\006.png)

4. I den **aktiviteter** listrutan väljer du de händelser som du vill ska ingå i kommittén.

5. Spara kommittén och ge det ett namn.

## <a name="example-active-users-where-you-modify-a-query"></a>Exempel: Aktiva användare om du ändrar en fråga

Föregående två kohort definierades med hjälp av listrutorna. Men du kan också definiera kohorter med analytics-frågor för totalt flexibilitet. Se hur, skapa en kommittén användare från Storbritannien.

![Animerad bild igenom användningen av kohorter-verktyget](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Öppna verktyget kohorter, Välj den **mallgalleriet** fliken och markera **tomt användare kommittén**.

    ![Tomt användare kommittén](.\media\app-insights-usage-cohorts\001.png)

    Det finns tre delar:
    * Ett Markdown textavsnitt, där du beskriva kommittén i detalj för andra i din grupp.

    * Ett avsnitt med parametrar, där du kan göra dina egna parametrar som **aktiviteter** och andra listrutorna i föregående två exempel.

    * Ett fråga avsnitt, där du kan definiera kommittén med hjälp av en analytics-fråga.

    I avsnittet frågan du [skriva en fråga analytics](https://docs.loganalytics.io/index). Frågan väljer vissa uppsättningen rader som beskriver kommittén som du vill definiera. Verktyget kohort implicit lägger till en ”| Sammanfatta av user_Id ”-sats i frågan. Dessa data förhandsgranskas nedan frågan i en tabell, så du kan kontrollera att frågan returnerar resultat.

    > [!NOTE]
    > Om du inte ser frågan och försök att ändra storlek på avsnittet om du vill göra den högre och visa frågan. Animerad GIF i början av det här avsnittet illustrerar beteendet för storleksändring.

2. Kopiera och klistra in följande text i frågeredigeraren:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Välj **Kör fråga**. Om du inte ser användar-ID som visas i tabellen, ändra till ett land där programmet har användare.

4. Spara och namnge kommittén.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

_Jag har definierat en kommittén användare från ett visst land. När jag jämföra den här kommittén i verktyget användare att bara ange ett filter på det landet, visas olika resultat. Varför?_

Kohorter och filter är olika. Anta att du har en kommittén av användare från Storbritannien (definieras som i föregående exempel), och du jämföra dess resultat för att ange filtret ”land eller region = Storbritannien”.

* Kommittén version visar alla händelser från användare som skickade en eller flera händelser från Storbritannien i det aktuella tidsintervallet. Om du dela efter land eller region se du antagligen många andra länder och regioner.
* Filter-versionen visas endast händelser från Storbritannien. Men om du dela efter land eller region visas enbart Storbritannien.

## <a name="learn-more"></a>Läs mer
- [Analytics-frågespråket](https://go.microsoft.com/fwlink/?linkid=856587)
- [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
- [Användaren flöden](app-insights-usage-flows.md)
- [Översikt över användning](app-insights-usage-overview.md)