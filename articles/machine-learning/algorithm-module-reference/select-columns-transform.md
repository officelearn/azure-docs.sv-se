---
title: 'Välj kolumntransformering: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Välj kolumner omvandla i Azure Machine Learning för att skapa en omvandling som väljer samma delmängd av kolumner som i den angivna datauppsättningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455918"
---
# <a name="select-columns-transform"></a>Välja kolumntranformering

I den här artikeln beskrivs hur du använder modulen Välj kolumntransformering i Azure Machine Learning designer (förhandsversion). Syftet med modulen Välj kolumner omforma är att säkerställa att en förutsägbar, konsekvent uppsättning kolumner används i nedströms maskininlärningsåtgärder.

Den här modulen är användbar för uppgifter som bedömning, som kräver specifika kolumner. Ändringar i de tillgängliga kolumnerna kan bryta pipelinen eller ändra resultatet.

Du kan använda Markera kolumner omformning för att skapa och spara en uppsättning kolumner. Använd sedan modulen [Använd omformning](apply-transformation.md) för att tillämpa dessa val på nya data.

## <a name="how-to-use-select-columns-transform"></a>Så här använder du Omforma markera kolumner

Det här scenariot förutsätter att du vill använda funktionsval för att generera en dynamisk uppsättning kolumner som ska användas för att träna en modell. Om du vill vara säkra på att kolumnvalen är desamma för bedömningsprocessen använder du modulen Välj kolumner för att samla in kolumnvalen och använda dem någon annanstans på pipelinen.

1. Lägg till en indatauppsättning i pipelinen i designern.

2. Lägg till en förekomst av [filterbaserad funktionsval](filter-based-feature-selection.md).

3. Anslut modulerna och konfigurera funktionsvalsmodulen för att automatiskt hitta ett antal de bästa funktionerna i indatauppsättningen.

4. Lägg till en instans av [Tågmodell](train-model.md) och använd utdata från [filterbaserat funktionsval](filter-based-feature-selection.md) som indata för träning.

    > [!IMPORTANT]
    > Eftersom funktionsbetydelse baseras på värdena i kolumnen kan du inte i förväg veta vilka kolumner som kan vara tillgängliga för indata till [Tågmodell](train-model.md).  
5. Bifoga en förekomst av modulen Markera kolumner. 

    Det här steget genererar en kolumnval som en omvandling som kan sparas eller tillämpas på andra datauppsättningar. Det här steget säkerställer att kolumnerna som identifieras i funktionsvalet sparas för andra moduler att återanvända.

6. Lägg till modulen [Poängmodell.](score-model.md) 

   *Anslut inte indatauppsättningen.* Lägg i stället till modulen [Använd omformning](apply-transformation.md) och anslut utdata för funktionsvalsomformningen.

   > [!IMPORTANT]
   > Du kan inte förvänta dig att använda [filterbaserad funktionsval](filter-based-feature-selection.md) på bedömningsdatauppsättningen och få samma resultat. Eftersom funktionsval baseras på värden kan det välja en annan uppsättning kolumner, vilket skulle leda till att bedömningsåtgärden misslyckas.
7. Skicka pipelinen.

Den här processen att spara och sedan använda en kolumnval säkerställer att samma dataschema är tillgängligt för utbildning och poängsättning.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
