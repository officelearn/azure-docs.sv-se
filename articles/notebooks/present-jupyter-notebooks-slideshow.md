---
title: Presentera en Jupyter-anteckningsbok som ett bildspel på Azure
description: Hur du konfigurerar cellerna för bildspel läge Jupyter notebook och sedan presentera bildspel med hjälp av upphov-tillägget.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: d180eaf571fa57191e3b0856020b02f05d05e344
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277599"
---
# <a name="run-a-notebook-slideshow"></a>Kör en notebook bildspel

Azure-datorer är förkonfigurerad med Jupyter/IPython bildspel tillägget (ökar) som gör att du presentera en bärbar dator direkt som ett bildspel. I ett bildspel är celler vanligtvis visas en i taget med hjälp av en teckenstorlek som är lämplig för presentera stora skärmar och du kan fortfarande köra koden i stället för att växla till en separat demo-dator.

Följande bild visar vyn standard anteckningsboken, där du kan se Markdown och kod celler allt på samma plats:

![En anteckningsbok i standardvyn](media/slideshow/slideshow-notebook-view.png)

När du startar ett bild spel förstoras den första cellen så att den fyller webbläsaren, där **X** i det övre vänstra hörnet avslutar bild spelet **?** i de nedre vänstra visar navigera kortkommandon och pilar längst ned till höger mellan bilder:

![En anteckningsbok i bildspel läge](media/slideshow/slideshow-slide-view.png)

Förbereda en bärbar dator för ett bildspel omfattar två primära aktiviteter:

1. Eftersom Markdown celler återges med stora teckensnitt, kanske en del innehåll inte visas i bildspelet. Därför begränsar du vanligtvis mängden text i en angiven cell; ett huvud med fyra till sex rader fungerar vanligtvis bäst. Om du har mer text kan du dela den informationen till flera celler.

2. Konfigurera beteendet för varje cell i bildspelet med hjälp av verktygsfältet Bildspel cell. Celltyper bestämma beteendet för navigeringsknapparna.

## <a name="the-anatomy-of-a-slideshow"></a>Hur i ett bildspel

Om du tar en slumpmässig notebook och använder det för ett bildspel, vara du normalt att alla celler är virrvarret tillsammans och mycket av innehållet döljs av längst ned i webbläsarfönstret. Om du vill göra en effektiv presentation måste, du tilldela en bildspel till cellerna med hjälp av verktygsfältet Bildspel cell:

1. I menyn **Visa** väljer du **Cell-verktygsfältet** > **bild spel**:

    ![Aktivera verktygsfältet cell bildspel](media/slideshow/slideshow-view-cell-toolbar.png)

1. En listruta för **bild typ** visas längst upp till höger i varje cell i antecknings boken:

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

1. I början är det bra att välja **bild** för varje cell. Du kan köra bildspelet och gör relevanta justeringar.

### <a name="example-fragment-cells-for-bullet-items"></a>Exempel: fragment celler för punkter

Om du vill göra punkter i en bild som visas en i taget, placerar du bild huvudet i en markdown-cell med **bild** typen och placerar sedan varje punkt i en separat markdown cell med **fragment** typen:

![Exempel på att skapa flera celler i Markdown för punkter](media/slideshow/slideshow-fragments.png)

Eftersom bildspelet renderas fragment med mer lodrätt avstånd än när alla punkter är i samma cell, kan du kanske inte använda så många punkter.

## <a name="run-the-slideshow"></a>Kör bildspelet

1. Om du har redigerat några markdown-celler, se till att köra dem för att återge deras HTML, annars visas de *som* markdown i bild spelet.

1. När du har konfigurerat **bild typen** för varje cell, markerar du den cell som du vill starta bild spelet i och väljer sedan knappen **Ange/avsluta öknings bild** i huvud verktygsfältet:

    ![/ Avslutar öka bildspel knapp i verktygsfältet](media/slideshow/slideshow-start.png)

1. Om du vill navigera mellan bilder samt fragment, med hjälp av vänster och höger pilarna i navigeringskontrollen. Texten i kontrollen visar ett tal som representerar *bild. sub-bild*.

    ![Bildspel-kontroll](media/slideshow/slideshow-navigation-control.png)

1. Om du vill navigera mellan bilder och underordnade bilder, samt fragment, Använd upp- och nedpilarna om aktiverat:

    ![Bildspel navigeringskontroller för underordnade bilder](media/slideshow/slideshow-navigation-control-subslide.png)

1. På en kodcell, använder du knappen Spela upp för att köra kod. utdata visas i bilden:

    ![Spela upp-knappen för att köra en kodcell](media/slideshow/slideshow-run-code-cell.png)

    ![Kod cell utdata visas i bildspelet](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Cell utdata anses vara en del av cell i ett bildspel. Om du kör en cell i anteckningsboken eller bildspelsvy, visas utdata i en annan vy samt. Om du vill rensa utdata använder du **cellen** > **aktuella utdata** > **Rensa** kommando (för den aktuella cellen) eller **cell** > **alla utdata** > **Rensa** (för alla celler).

1. När du är klar med bild spelet använder du **X** för att återgå till vyn Anteckningsbokslayout.

## <a name="next-steps"></a>Nästa steg

- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Gör så här: installera paket inifrån en bärbar dator](install-packages-jupyter-notebook.md)
- [Gör så här: arbeta med datafiler](work-with-project-data-files.md)
- [Så här gör du: åtkomst till data resurser](access-data-resources-jupyter-notebooks.md)
