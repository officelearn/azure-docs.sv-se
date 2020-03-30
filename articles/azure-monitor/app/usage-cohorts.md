---
title: Användningskohorter för Azure Application Insights | Microsoft-dokument
description: Analysera olika uppsättningar eller användare, sessioner, händelser eller åtgärder som har något gemensamt
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0c552e8f3e732c49da02b2f5704fb9cf312fb3e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671094"
---
# <a name="application-insights-cohorts"></a>Programinsiktskohorter

En kohort är en uppsättning användare, sessioner, händelser eller åtgärder som har något gemensamt. I Azure Application Insights definieras kohorter av en analysfråga. I de fall där du måste analysera en viss uppsättning användare eller händelser upprepade gånger kan kohorter ge dig större flexibilitet att uttrycka exakt den uppsättning du är intresserad av.

![Fönstret Kohorter](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorter jämfört med grundläggande filter

Kohorter används på sätt som liknar filter. Men kohorternas definitioner är byggda av anpassade analysfrågor, så de är mycket mer anpassningsbara och komplexa. Till skillnad från filter kan du spara kohorter så att andra medlemmar i teamet kan återanvända dem.

Du kan definiera en kohort av användare som alla har provat en ny funktion i din app. Du kan spara den här kohorten i application insights-resursen. Det är enkelt att analysera den här sparade gruppen av specifika användare i framtiden.

> [!NOTE]
> När de har skapats är kohorter tillgängliga från verktygen Användare, Sessioner, Händelser och Användarflöden.

## <a name="example-engaged-users"></a>Exempel: Engagerade användare

Ditt team definierar en engagerad användare som alla som använder din app fem eller fler gånger under en viss månad. I det här avsnittet definierar du en kohort av dessa engagerade användare.

1. Öppna verktyget Kohorter.

2. Välj fliken **Mallgalleri.** Du ser en samling mallar för olika kohorter.

3. Välj **Engagerade användare – efter använda dagar**.

    Det finns tre parametrar för den här kohorten:
    * **Aktiviteter**, där du väljer vilka händelser och sidvisningar som ska räknas som "användning".
    * **Period**, definitionen av en månad.
    * **UsedAtLeastCustom**, antalet gånger användare behöver använda något inom en period för att räknas som engagerade.

4. Ändra **UsedAtLeastCustom** till **5+ dagar**och lämna **Period** på standardvärdet 28 dagar.

    ![Engagerade användare](./media/usage-cohorts/003.png)

    Nu representerar den här kohorten alla användar-ID:er som har skickats med en anpassad händelse eller sidvy på 5 separata dagar under de senaste 28.

5. Välj **Spara**.

   > [!TIP]
   > Ge din kohort ett namn, till exempel "Engagerade användare (5+ dagar)." Spara den i "Mina rapporter" eller "Delade rapporter", beroende på om du vill att andra som har åtkomst till den här Application Insights-resursen ska se den här kohorten.

6. Välj **Tillbaka till galleri**.

### <a name="what-can-you-do-by-using-this-cohort"></a>Vad kan du göra genom att använda den här kohorten?

Öppna verktyget Användare. I listrutan **Visa** väljer du den kohort som du skapade under **Användare som tillhör**.

Nu filtreras verktyget Användare till den här kohorten av användare:

![Fönstret Användare filtrerat till en viss kohort](./media/usage-cohorts/004.png)

Några viktiga saker att lägga märke till:

* Du kan inte skapa den här uppsättningen genom normala filter. Datumlogiken är mer avancerad.
* Du kan filtrera kohorten ytterligare med hjälp av de vanliga filtren i verktyget Användare. Så även om kohorten definieras på 28-dagarsfönster kan du fortfarande justera tidsintervallet i verktyget Användare så att det är 30, 60 eller 90 dagar.

Dessa filter stöder mer sofistikerade frågor som är omöjliga att uttrycka genom frågeverktyget. Ett exempel är _människor som varit engagerade under de senaste 28 dagarna. Hur uppförde sig samma människor under de senaste 60 dagarna?_

## <a name="example-events-cohort"></a>Exempel: Händelsekohort

Du kan också göra kohorter av händelser. I det här avsnittet definierar du en kohort av händelser och sidvisningar. Sedan ser du hur du använder dem från de andra verktygen. Den här kohorten kan definiera en uppsättning händelser som teamet anser vara _aktiv användning_ eller en uppsättning som är relaterad till en viss ny funktion.

1. Öppna verktyget Kohorter.

2. Välj fliken **Mallgalleri.** Du ser en samling mallar för olika kohorter.

3. Välj **Händelseväljare**.

    ![Skärmbild av händelseväljaren](./media/usage-cohorts/006.png)

4. I listrutan **Aktiviteter** väljer du de händelser som du vill ska vara i kohorten.

5. Spara kohorten och ge den ett namn.

## <a name="example-active-users-where-you-modify-a-query"></a>Exempel: Aktiva användare där du ändrar en fråga

De två föregående kohorterna definierades med hjälp av rullgardinsrutor. Men du kan också definiera kohorter med hjälp av analysfrågor för total flexibilitet. Skapa en kohort av användare från Storbritannien om du vill se hur du skapar en kohort av användare från Storbritannien.

![Animerad bild som går genom användning av kohorter verktyg](./media/usage-cohorts/cohorts0001.gif)

1. Öppna verktyget Kohorter, välj fliken **Mallgalleri** och välj **Kohort för tomma användare**.

    ![Tomt användare kohort](./media/usage-cohorts/001.png)

    Det finns tre avsnitt:
   * Ett Markdown-textavsnitt där du beskriver kohorten mer i detalj för andra i teamet.

   * Ett parameteravsnitt där du skapar egna parametrar, till exempel **Aktiviteter** och andra listrutor från de två föregående exemplen.

   * Ett frågeavsnitt där du definierar kohorten med hjälp av en analysfråga.

     I frågeavsnittet skriver du [en analysfråga](/azure/kusto/query). Frågan markerar den uppsättning rader som beskriver kohorten som du vill definiera. Med verktyget Kohorter läggs sedan implicit till ett "| sammanfatta med user_Id"-satsen till frågan. Dessa data förhandsgranskas under frågan i en tabell, så att du kan se till att frågan returnerar resultat.

     > [!NOTE]
     > Om frågan inte visas kan du prova att ändra storlek på avsnittet så att det blir högre och visa frågan. Den animerade GIF-gif-bilden i början av det här avsnittet illustrerar storleksändringsbeteendet.

2. Kopiera och klistra in följande text i frågeredigeraren:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Välj **Kör fråga**. Om användar-ID:t inte visas i tabellen ändras du till ett land/en region där programmet har användare.

4. Spara och namnge kohorten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

_Jag har definierat en kohort av användare från ett visst land/en viss region. När jag jämför denna kohort i verktyget Användare för att bara ställa in ett filter på det landet / regionen, ser jag olika resultat. Varför?_

Kohorter och filter är olika. Anta att du har en kohort av användare från Storbritannien (definierad som föregående exempel) och du jämför dess resultat med att ange filtret "Land eller region = Storbritannien".

* Kohortversionen visar alla händelser från användare som har skickat en eller flera händelser från Storbritannien i det aktuella tidsintervallet. Om du delar upp efter land eller region ser du troligen många länder och regioner.
* Filterversionen visar bara händelser från Storbritannien. Men om du delar efter land eller region ser du bara Storbritannien.

## <a name="learn-more"></a>Läs mer

* [Analytics-frågespråk](https://go.microsoft.com/fwlink/?linkid=856587)
* [Användare, sessioner, händelser](usage-segmentation.md)
* [Användarflöden](usage-flows.md)
* [Översikt över användning](usage-overview.md)
