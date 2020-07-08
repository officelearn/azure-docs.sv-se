---
title: Presentera en Jupyter Notebook som ett bild spel i Azure Notebooks för hands version
description: Lär dig hur du konfigurerar celler för bild spels läge i en Jupyter Notebook och sedan presenterar bild spelet med ÖKNINGs tillägget.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834054"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Köra ett Notebook-bildspel i Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks är förkonfigurerat med Jupyter/IPython bild tillägg (RISE) som gör att du kan presentera en bärbar dator direkt som ett bild spel. I ett bild spel visas cellerna vanligt vis en i taget med en tecken storlek som passar för visning på stora skärmar, och du kan fortfarande köra koden i stället för att växla till en separat demo dator.

Följande bild visar standard vyn för bärbara datorer, där du kan se markdown och kod cellerna tillsammans:

![En bärbar dator i standardvyn](media/slideshow/slideshow-notebook-view.png)

När du startar ett bild spel förstoras den första cellen så att den fyller webbläsaren, där **X** i det övre vänstra hörnet avslutar bild spelet **?** i det nedre vänstra hörnet visas kortkommandon och pilarna i det nedre högra hörnet för att navigera mellan bilder:

![En bärbar dator i bild spels läge](media/slideshow/slideshow-slide-view.png)

Att förbereda en bärbar dator för ett bild spel omfattar två huvudsakliga aktiviteter:

1. Eftersom markdown-celler återges med stora teckensnitt kanske vissa innehåll inte visas i bild spelet. Det innebär vanligt vis att du begränsar mängden text i en specifik cell. ett sidhuvud med fyra till sex rader fungerar vanligt vis bäst. Om du har mer text, dela den informationen i flera celler.

2. Konfigurera funktions sättet för varje cell i bild spelet med hjälp av verktygsfältet Bild spels cell. Cell typerna bestämmer beteendet för navigerings knapparna.

## <a name="the-anatomy-of-a-slideshow"></a>Bild spelets uppbyggnad

Om du tar en slumpmässig antecknings bok och använder den för ett bild spel, ser du normalt att alla cellerna är jumbled och att mycket av innehållet är dolt längst ned i webbläsarfönstret. Om du vill göra en effektiv presentation måste du tilldela en bild spels typ till varje cell med hjälp av verktygsfältet Bild spels cell:

1. I menyn **Visa** väljer du **verktygsfältet cells-verktygsfält**  >  **Slideshow**:

    ![Aktivera verktygsfältet för cell bild spel](media/slideshow/slideshow-view-cell-toolbar.png)

1. En listruta för **bild typ** visas längst upp till höger i varje cell i antecknings boken:

    ![Verktygsfältet för cell bild spel](media/slideshow/slideshow-cell-toolbar.png)

1. Välj en av fem typer för varje cell:

    ![Cell bild spels typer](media/slideshow/slideshow-cell-slide-types.png)

    | Bildtyp | Beteende |
    | --- | --- |
    | -(har inte angetts) | Cellen visas med föregående cell, vilket ofta inte är en önskad påverkan i ett bild spel. |
    | Flytta | Cellen är en primär bild som navigeras med hjälp av den vänstra och högra pilen i navigerings kontrollen. |
    | Under bild | Cellen är "under" en primär bild som navigeras till med hjälp av nedåtpilen för navigerings kontrollen. Uppåtpilen återgår till den primära bilden. Underordnade bilder används för sekundärt material som du kan hoppa över i huvud Sök vägen för en presentation, men det är lätt att använda vid behov. |
    | Fragment | Cell innehåll visas i den föregående bildens eller under bildens kontext när du använder vänsterpilen (ett fragment tas bort när du använder uppåtpilen). Du kan använda ett fragment med en tecken cell för att se till att koden visas i en bild, eller så kan du använda flera fragment för att visa text punkter en i taget (se exemplet i nästa avsnitt). Eftersom fragment bygger på den aktuella bilden visas inte längre fragment i webbläsarfönstret. |
    | Hoppa över | Cellen visas inte i bild spelet. |
    | Obs! | Cellen innehåller som stöd anteckningar, som inte visas i bild spelet. |

1. I början är det bra att välja **bild** för varje cell. Du kan sedan köra bild spelet och göra lämpliga justeringar.

### <a name="example-fragment-cells-for-bullet-items"></a>Exempel: fragmentera celler för punkt objekt

Om du vill göra punkter i en bild som visas en i taget, placerar du bild huvudet i en markdown-cell med **bild** typen och placerar sedan varje punkt i en separat markdown cell med **fragment** typen:

![Exempel på att skapa flera markdown-celler för punkt objekt](media/slideshow/slideshow-fragments.png)

Eftersom bild spelet återger fragment med mer lodrätt avstånd än när alla punkter finns i samma cell, kanske du inte kan använda så många punkt objekt.

## <a name="run-the-slideshow"></a>Kör bild spelet

1. Om du har redigerat några markdown-celler, se till att köra dem för att återge deras HTML, annars visas de *som* markdown i bild spelet.

1. När du har konfigurerat **bild typen** för varje cell, markerar du den cell som du vill starta bild spelet i och väljer sedan knappen **Ange/avsluta öknings bild** i huvud verktygsfältet:

    ![Knappen Ange/avsluta öka bild spel i huvud verktygsfältet](media/slideshow/slideshow-start.png)

1. Använd vänster och höger pilar i navigerings kontrollen för att navigera mellan både bilder och fragment. Texten i kontrollen visar ett tal som representerar *bild. sub-bild*.

    ![Navigerings kontroll för bild spel](media/slideshow/slideshow-navigation-control.png)

1. Om du vill navigera mellan bilder och underordnade bilder, och även fragment, använder du upp-och nedpilarna, om det är aktiverat:

    ![Navigerings kontroller för bild spel för underordnade bilder](media/slideshow/slideshow-navigation-control-subslide.png)

1. Använd uppspelnings knappen i en kod cell för att köra koden. Utdata visas i bilden:

    ![Knappen Spela upp för att köra en kod cell](media/slideshow/slideshow-run-code-cell.png)

    ![Utdata från kod cellen visas i bild spelet](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Cell utdata betraktas som en del av cellen i ett bild spel. Om du kör en cell i antingen Notebook eller bildspelsvyn visas även utdata i den andra vyn. Om du vill rensa utdata använder du kommandot **cell**  >  **aktuella utdata**  >  **Rensa** (för den aktuella cellen) eller **cellen**  >  **Rensa alla utdata**  >  **Clear** (för alla celler).

1. När du är klar med bild spelet använder du **X** för att återgå till vyn Anteckningsbokslayout.

## <a name="next-steps"></a>Nästa steg

- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Gör så här: installera paket inifrån en bärbar dator](install-packages-jupyter-notebook.md)
- [Gör så här: arbeta med datafiler](work-with-project-data-files.md)
- [Så här gör du: åtkomst till data resurser](access-data-resources-jupyter-notebooks.md)
