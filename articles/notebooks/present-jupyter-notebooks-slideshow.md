---
title: Presentera en Jupyter Notebook som ett bild spel i Azure Notebooks för hands version
description: Lär dig hur du konfigurerar celler för bild spels läge i en Jupyter Notebook och sedan presenterar bild spelet med ÖKNINGs tillägget.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647126"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Köra ett Notebook-bildspel i Azure Notebooks för hands version

Azure-datorer är förkonfigurerad med Jupyter/IPython bildspel tillägget (ökar) som gör att du presentera en bärbar dator direkt som ett bildspel. I ett bildspel är celler vanligtvis visas en i taget med hjälp av en teckenstorlek som är lämplig för presentera stora skärmar och du kan fortfarande köra koden i stället för att växla till en separat demo-dator.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Följande bild visar vyn standard anteckningsboken, där du kan se Markdown och kod celler allt på samma plats:

![En anteckningsbok i standardvyn](media/slideshow/slideshow-notebook-view.png)

När du startar ett bildspel, den första cellen förstoras så att den fyller webbläsaren, där den **X** avslutas bildspel, i det övre vänstra hörnet **?** i de nedre vänstra visar navigera kortkommandon och pilar längst ned till höger mellan bilder:

![En anteckningsbok i bildspel läge](media/slideshow/slideshow-slide-view.png)

Förbereda en bärbar dator för ett bildspel omfattar två primära aktiviteter:

1. Eftersom Markdown celler återges med stora teckensnitt, kanske en del innehåll inte visas i bildspelet. Därför begränsar du vanligtvis mängden text i en angiven cell; ett huvud med fyra till sex rader fungerar vanligtvis bäst. Om du har mer text kan du dela den informationen till flera celler.

2. Konfigurera beteendet för varje cell i bildspelet med hjälp av verktygsfältet Bildspel cell. Celltyper bestämma beteendet för navigeringsknapparna.

## <a name="the-anatomy-of-a-slideshow"></a>Hur i ett bildspel

Om du tar en slumpmässig notebook och använder det för ett bildspel, vara du normalt att alla celler är virrvarret tillsammans och mycket av innehållet döljs av längst ned i webbläsarfönstret. Om du vill göra en effektiv presentation måste, du tilldela en bildspel till cellerna med hjälp av verktygsfältet Bildspel cell:

1. På den **visa** menyn och välj **Cell verktygsfältet** > **bildspel**:

    ![Aktivera verktygsfältet cell bildspel](media/slideshow/slideshow-view-cell-toolbar.png)

1. En **dra typ** listrutan visas längst upp till höger i varje cell i anteckningsboken:

    ![Cell bildspel verktygsfältet](media/slideshow/slideshow-cell-toolbar.png)

1. För varje cell, väljer du en av fem typer:

    ![Cell bildspel typer](media/slideshow/slideshow-cell-slide-types.png)

    | Bildtyp | Beteende |
    | --- | --- |
    | -(ej angivet) | Cell visas med föregående cell, vilket ofta är inte en önskad effekt i ett bildspel. |
    | Bild | Cellen är en primär bild, navigerat med hjälp av vänster och höger pilarna i navigeringskontrollen. |
    | Underordnade bild | Cellen är en primär bild, navigerat till med nedpilen i navigeringskontrollen ”under”. På uppåt-pilen återgår till den primära bilden. Underordnade bilder som används för sekundär material som du kan hoppa över den huvudsakliga sökvägen till en presentation, men är tillgängligt om det behövs. |
    | Fragment | Innehållet i cellen visas i samband med föregående bild eller underordnade bild när nedåtpilen navigering (ett fragment tas bort när du använder på uppåt-pilen). Du kan använda ett fragment med en kodcell för att göra den kod som visas i en bild eller du kan använda flera fragment textformatering punkter visas en i taget (se exemplet i nästa avsnitt). Eftersom fragment bygger på den aktuella bilden, är överskjutande fragment inte synlig av längst ned i webbläsarfönstret. |
    | Hoppa över | Cell visas inte i bildspelet. |
    | Anteckningar | Cellen innehåller som stödanteckningar, inte visas i bildspelet. |

1. Inledningsvis är det bra att välja **dra** för varje cell. Du kan köra bildspelet och gör relevanta justeringar.

### <a name="example-fragment-cells-for-bullet-items"></a>Exempel: fragment celler för punkter

Att göra punkter i en bild visas en ett, steg rubriken bild i en Markdown-cell med den **bild** Skriv och placera varje punkt i en separat Markdown cell med den **Fragment** typ:

![Exempel på att skapa flera celler i Markdown för punkter](media/slideshow/slideshow-fragments.png)

Eftersom bildspelet renderas fragment med mer lodrätt avstånd än när alla punkter är i samma cell, kan du kanske inte använda så många punkter.

## <a name="run-the-slideshow"></a>Kör bildspelet

1. Om du har redigerat Markdown celler, se till att köra dem för att återge HTML, annars visas de *som* Markdown i bildspelet.

1. När du har konfigurerat den **dra typ** för varje cell, väljer du cellen som du vill starta bildspelet och välj sedan den **RETUR/Avsluts öka bildspel** i verktygsfältet huvudsakliga:

    ![/ Avslutar öka bildspel knapp i verktygsfältet](media/slideshow/slideshow-start.png)

1. Om du vill navigera mellan bilder samt fragment, med hjälp av vänster och höger pilarna i navigeringskontrollen. Texten i kontrollen visar ett tal som representerar *slide.sub bild*.

    ![Bildspel-kontroll](media/slideshow/slideshow-navigation-control.png)

1. Om du vill navigera mellan bilder och underordnade bilder, samt fragment, Använd upp- och nedpilarna om aktiverat:

    ![Bildspel navigeringskontroller för underordnade bilder](media/slideshow/slideshow-navigation-control-subslide.png)

1. På en kodcell, använder du knappen Spela upp för att köra kod. utdata visas i bilden:

    ![Spela upp-knappen för att köra en kodcell](media/slideshow/slideshow-run-code-cell.png)

    ![Kod cell utdata visas i bildspelet](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Cell utdata anses vara en del av cell i ett bildspel. Om du kör en cell i anteckningsboken eller bildspelsvy, visas utdata i en annan vy samt. Om du vill ta bort utdata, använda den **Cell** > **aktuella utdata** > **Rensa** kommandot (för den aktuella cellen) eller **Cell**  >  **Alla utdata** > **Rensa** (för alla celler).

1. När du är klar med bildspelet kan använda den **X** att återgå till anteckningsboken vyn.

## <a name="next-steps"></a>Nästa steg

- [Så här: konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Så här: Installera paket från inom en anteckningsbok](install-packages-jupyter-notebook.md)
- [Så här: arbeta med filer](work-with-project-data-files.md)
- [Så här: åtkomst till dataresurser](access-data-resources-jupyter-notebooks.md)
