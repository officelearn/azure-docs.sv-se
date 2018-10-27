---
title: Azure Application Insights användning kohorter | Microsoft Docs
description: Analysera olika uppsättningar eller användare, sessioner, händelser eller åtgärder som har något gemensamt
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 35737b91d8ef0b58223f51530f56408df1c7eb8e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138800"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorter

En kohort är en uppsättning användare, sessioner, händelser eller åtgärder som har något gemensamt. I Azure Application Insights definieras kohorter av en analytics-fråga. I fall där du behöver analysera en specifik uppsättning användare eller händelser upprepade gånger kohorter kan du större flexibilitet att uttrycka exakt den uppsättning som du är intresserad av.

![Kohorter fönstret](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorter jämfört med grundläggande filter

Kohorter används på ett sätt som liknar filter. Men kohorter definitioner skapas från anpassade analytics-frågor, så att de är anpassningsbart och mycket mer komplexa. Till skillnad från filter, kan du spara kohorter så att andra medlemmar i ditt team kan återanvända dem.

Du kan definiera en kohort för användare som har alla försökt en ny funktion i din app. Du kan spara den här kohorten i Application Insights-resursen. Det är enkelt att analysera den här sparade grupp med specifika användare i framtiden.

> [!NOTE]
> När de skapas, är kohorter tillgängliga från användare, sessioner, händelser och Användarflöden verktyg.

## <a name="example-engaged-users"></a>Exempel: Engagerade användare

Ditt team definierar en engagerade användare som alla som använder din app fem eller fler gånger under en månad. I det här avsnittet definierar du en kohort dessa engagerade användare.

1. Öppna verktyget kohorter.

2. Välj den **mallgalleriet** fliken. Du kan se en samling mallar för olika kohorter.

3. Välj **engagerade användare – dagar använt**.

    Det finns tre parametrar för den här kohorten:
    * **Aktiviteter**, där du väljer vilka händelser och sidvisningar räknas som ”användning”.
    * **Period**, definitionen av en månad.
    * **UsedAtleastCustom**, antalet gånger som användare måste du använda något inom en period räkna när engagerade.

4. Ändra **UsedAtleastCustom** till **5 + dagar**, och lämna **Period** på standardvärdet 28 dagar.

    ![Engagerade användare](.\media\app-insights-usage-cohorts\003.png)

    Nu den här kohorten representerar alla användar-ID som skickas med valfri anpassad händelse eller Sidvisning på 5 avgränsa dagar tidigare 28.

5. Välj **Spara**.

   > [!TIP]
   >  Ge din kohort ett namn som ”Engaged användare (5 + dagar)”. Spara den till ”Mina rapporter” eller ”delade rapporter”, beroende på om du vill att andra personer som har åtkomst till den här Application Insights-resursen för att se den här kohorten.

6. Välj **tillbaka till galleriet**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Vad kan du göra med hjälp av den här kohorten?

Öppna verktyget användare. I den **visa** listrutan väljer du den kohort som du skapade under **användare som tillhör**.

Nu filtreras användarverktyget till den här användarkohort:

![Användare-fönstret som filtrerats till en viss kohort](.\media\app-insights-usage-cohorts\004.png)

Några viktiga saker att Observera:
* Du kan inte skapa den här uppsättningen via normal filter. Logiken som datum är mer avancerad.
* Du kan filtrera den här kohorten ytterligare med hjälp av de normala filter i verktyget användare. Så även om kohorten har definierats i 28-dagars windows kan kan du fortfarande justera tidsintervallet i verktyget användare vara 30, 60 eller 90 dagar.

Dessa filter har stöd för mer avancerade frågor som är omöjligt att uttrycka via Frågeverktyget. Ett exempel är _personer som ägnat under de senaste 28 dagarna. Hur fungerar personer samma under de senaste 60 dagarna?_

## <a name="example-events-cohort"></a>Exempel: Kohort för händelser

Du kan också göra kohorter av händelser. I det här avsnittet definierar du en kohort för händelser och sidvisningar. Sedan kan du se hur du använder dem från andra verktyg. Den här kohorten kan definiera en uppsättning händelser som ditt team betraktar _active användning_ eller en uppsättning relaterade till vissa en ny funktion.

1. Öppna verktyget kohorter.

2. Välj den **mallgalleriet** fliken. Du ser en samling mallar för olika kohorter.

3. Välj **händelser väljare**.

    ![Skärmbild av händelser väljare](.\media\app-insights-usage-cohorts\006.png)

4. I den **aktiviteter** listrutan väljer du de händelser som du vill ska vara i kohorten.

5. Spara kohorten och ge den ett namn.

## <a name="example-active-users-where-you-modify-a-query"></a>Exempel: Aktiva användare där du har ändrat en fråga

Föregående två kohorter definierades med hjälp av listrutorna. Men du kan också definiera kohorter med analytics-frågor för total flexibilitet. Se hur du skapar en användarkohort från Storbritannien.

![Animerade avbildning walking med hjälp av verktyget kohorter](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. Öppna verktyget kohorter, Välj den **mallgalleriet** och sedan **kohort för tomma användare**.

    ![Kohort för tomma användare](.\media\app-insights-usage-cohorts\001.png)

    Det finns tre avsnitt:
    * Ett Markdown textavsnitt, där du beskriver kohort i detalj för andra i din grupp.

    * Ett avsnitt med parametrar, där du kan göra dina egna parametrar som **aktiviteter** och andra listrutorna i föregående två exempel.

    * Ett fråga avsnitt, där du kan definiera kohorten med hjälp av en analytics-fråga.

    I avsnittet frågan du [skriva en analytics-fråga](/azure/kusto/query). Frågan väljer vissa uppsättningen rader som beskriver den kohort som du vill definiera. Verktyget kohorter implicit lägger till en ”| summera efter user_Id ”-sats i frågan. Dessa data förhandsgranskas nedan frågan i en tabell, så kontrollera att frågan returnerar resultat.

    > [!NOTE]
    > Om du inte ser frågan kan du prova att ändra storlek i avsnittet om du vill göra den högre och visa frågan. Animerad GIF i början av det här avsnittet visar vad storleksändring.

2. Kopiera och klistra in följande text i frågeredigeraren:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Välj **Kör fråga**. Om du inte ser användar-ID som visas i tabellen, ändra till ett land där ditt program har användare.

4. Spara och namnge kohorten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

_Jag har definierat en kohort användare från ett visst land. När jag jämföra den här kohorten i verktyget användare för att bara definiera filter för landet i fråga, visas olika resultat. Varför?_

Kohorter och filter är olika. Anta att du har en kohort användare från Storbritannien (definieras som i föregående exempel), och du jämför resultaten till att ange filtret ”land = Förenade kungariket”.

* Kohort-versionen innehåller alla händelser från användare som skickade en eller flera händelser från Storbritannien i det aktuella tidsintervallet. Om du dela upp efter land eller region, ser du antagligen många länder och regioner till.
* Filter-versionen visas endast händelser från Storbritannien. Men om du dela upp efter land eller region, visas endast Storbritannien.

## <a name="learn-more"></a>Läs mer
- [Analytics-frågespråket](https://go.microsoft.com/fwlink/?linkid=856587)
- [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
- [Användarflöden](app-insights-usage-flows.md)
- [Användningsöversikten](app-insights-usage-overview.md)