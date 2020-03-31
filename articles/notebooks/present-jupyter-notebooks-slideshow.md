---
title: Presentera en Jupyter-anteckningsbok som ett bildspel i förhandsversionen av Azure Notebooks
description: Lär dig hur du konfigurerar celler för bildspelsläge i en Jupyter-anteckningsbok och presenterar sedan bildspelet med RISE-tillägget.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647126"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Kör ett bildspel för anteckningsböcker i förhandsversionen av Azure Notebooks

Azure Notebooks är förkonfigurerade med Tillägget jupyter/IPython (RISE) som gör att du kan presentera en anteckningsbok direkt som ett bildspel. I ett bildspel visas celler vanligtvis en i taget med hjälp av en teckenstorlek som är lämplig för att presentera på stora skärmar, och du kan fortfarande köra koden i stället för att växla till en separat demodator.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Följande bild visar standardvyn för anteckningsboken, där du kan se Markdown- och kodceller tillsammans:

![En anteckningsbok i standardvyn](media/slideshow/slideshow-notebook-view.png)

När du startar ett bildspel förstoras den första cellen för att fylla webbläsaren, där **X** i det övre vänstra utgår från bildspelet, **?** Längst ned till vänster visas kortkommandon och pilarna längst ned till höger navigerar mellan bilderna:

![En anteckningsbok i bildspelsläge](media/slideshow/slideshow-slide-view.png)

Att förbereda en anteckningsbok för ett bildspel innebär två primära aktiviteter:

1. Eftersom Markdown-celler återges med stora teckensnitt kanske en del innehåll inte visas i bildspelet. Du begränsar därför vanligtvis mängden text i en viss cell. en rubrik med fyra till sex rader fungerar vanligtvis bäst. Om du har mer text delar du upp informationen i flera celler.

2. Konfigurera beteendet för varje cell i bildspelet med hjälp av bildspelscellverktygsfältet. Celltyper bestämmer hur navigeringsknapparna ska vara.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomin hos ett bildspel

Om du tar en slumpmässig anteckningsbok och använder den för ett bildspel, upptäcker du vanligtvis att alla celler är röriga tillsammans, och mycket av innehållet är dolt längst ner i webbläsarfönstret. Om du vill göra en effektiv presentation måste du tilldela en bildspelstyp till varje cell med hjälp av verktygsfältet Bildspelscell:

1. Välj Bildspel i bildspel i bildspel **i verktygsfältet Cell på** > **Visa-menyn:** **View**

    ![Aktivera verktygsfältet för bildspel](media/slideshow/slideshow-view-cell-toolbar.png)

1. En listruta **för bildtyp** visas längst upp till höger i varje cell i anteckningsboken:

    ![Verktygsfältet Cellbildspel](media/slideshow/slideshow-cell-toolbar.png)

1. För varje cell väljer du en av fem typer:

    ![Bildspelstyper för celler](media/slideshow/slideshow-cell-slide-types.png)

    | Bildtyp | Beteende |
    | --- | --- |
    | - (inte inställd) | Cell visas med föregående cell, vilket ofta inte är en önskad effekt i ett bildspel. |
    | Bild | Cell är en primär bild, navigeras med hjälp av vänster och höger pilar i navigeringskontrollen. |
    | Underbild | Cellen är "under" en primär bild, navigeras till med hjälp av nedåtpilen i navigeringskontrollen. Uppåtpilen återgår till den primära bilden. Underbilder används för sekundärt material som du kan hoppa över i huvudsökvägen i en presentation, men som är lätt tillgänglig om det behövs. |
    | Fragment | Cellinnehåll visas i samband med föregående bild eller underbild när du använder nedåtnavigeringspilen (ett fragment tas bort när upppilen ska börja med). Du kan använda ett fragment med en kodcell för att få koden att visas i en bild, eller så kan du använda flera fragment för att få textpunkter att visas en efter en (se exempel i nästa avsnitt). Eftersom fragment bygger på den aktuella bilden visas inte överflödiga fragment längst ned i webbläsarfönstret. |
    | Hoppa över | Cell visas inte i bildspelet. |
    | Anteckningar | Cellen innehåller som stödanteckningar som inte visas i bildspelet. |

1. Inledningsvis är det bra att välja **Bild** för varje cell. Du kan sedan köra bildspelet och göra lämpliga justeringar.

### <a name="example-fragment-cells-for-bullet-items"></a>Exempel: fragmentceller för punktobjekt

Om du vill att punkter på en bild ska visas en efter en placerar du bildhuvudet i en Markdown-cell med **bildtypen** och placerar sedan varje punkt i en separat Markdown-cell med typen **Fragment:**

![Exempel på hur du skapar flera Markdown-celler för punktobjekt](media/slideshow/slideshow-fragments.png)

Eftersom bildspelet återger fragment med mer lodrät avstånd än när alla punkter finns i samma cell kanske du inte kan använda lika många punktobjekt.

## <a name="run-the-slideshow"></a>Kör bildspelet

1. Om du har redigerat några Markdown-celler kontrollerar du att de kan återges för att rendera html-koden, annars visas de *som* Markdown i bildspelet.

1. När du har konfigurerat **bildtypen** för varje cell markerar du den cell som bildspelet ska startas med och väljer sedan knappen **Enter/Exit RISE** i huvudverktygsfältet:

    ![Knappen Ange/avsluta RISE-bildspel i huvudverktygsfältet](media/slideshow/slideshow-start.png)

1. Om du vill navigera mellan bilder och fragment använder du vänster- och högerpilarna i navigeringskontrollen. Texten i kontrollen visar ett tal som representerar *slide.sub-slide*.

    ![Navigeringskontroll för bildspel](media/slideshow/slideshow-navigation-control.png)

1. Om du vill navigera mellan bilder och underbilder samt fragment använder du upp- och nedpilarna om de är aktiverade:

    ![Navigeringskontroller för bildspel för underbilder](media/slideshow/slideshow-navigation-control-subslide.png)

1. På en kodcell använder du uppspelningsknappen för att köra koden. utdata visas på bilden:

    ![Knappen Spela upp för att köra en kodcell](media/slideshow/slideshow-run-code-cell.png)

    ![Kodcellutdata visas i bildspelet](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Cellutdata betraktas som en del av cellen i ett bildspel. Om du kör en cell i anteckningsboks- eller bildspelsvyn visas utdata även i den andra vyn. Om du vill ta bort utdata använder du kommandot**Rensa**  > **cellström** > (för den aktuella cellen) eller **Cell** > **Alla utdata** >  **Cell****rensa** (för alla celler).

1. När du är klar med bildspelet använder du **X** för att återgå till anteckningsboksvyn.

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar och hanterar du projekt](configure-manage-azure-notebooks-projects.md)
- [Så här installerar du paket från en bärbar dator](install-packages-jupyter-notebook.md)
- [Så här: Arbeta med datafiler](work-with-project-data-files.md)
- [Så här kommer du åt åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
