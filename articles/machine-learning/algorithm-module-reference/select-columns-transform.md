---
title: 'Välj kolumn omvandling: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Transform-modulen Välj kolumner i Azure Machine Learning för att skapa en omvandling som väljer samma del av kolumner som i den aktuella data uppsättningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79455918"
---
# <a name="select-columns-transform"></a>Välja kolumntranformering

I den här artikeln beskrivs hur du använder Transform-modulen Select columns i Azure Machine Learning designer (för hands version). Syftet med modulen Välj kolumn omvandling är att säkerställa att en förutsägbar, konsekvent uppsättning kolumner används i efterföljande Machine Learning-åtgärder.

Den här modulen är användbar för uppgifter som poängsättning, som kräver vissa kolumner. Ändringar i de tillgängliga kolumnerna kan bryta pipelinen eller ändra resultatet.

Du använder transformeringen Välj kolumner för att skapa och spara en uppsättning kolumner. Använd sedan modulen [Använd omvandling](apply-transformation.md) för att tillämpa dessa val på nya data.

## <a name="how-to-use-select-columns-transform"></a>Så här använder du Välj kolumner-transformering

Det här scenariot förutsätter att du vill använda funktions val för att generera en dynamisk uppsättning kolumner som ska användas för att träna en modell. För att se till att kolumn valen är desamma för bedömnings processen använder du modulen Välj kolumner för att avbilda kolumn valen och tillämpa dem på andra platser i pipelinen.

1. Lägg till en data uppsättning i din pipeline i designern.

2. Lägg till en instans av [filtrering baserat funktions val](filter-based-feature-selection.md).

3. Anslut modulerna och konfigurera modulen för funktions val för att automatiskt hitta ett antal bästa funktioner i indata-datauppsättningen.

4. Lägg till en instans av [träna modell](train-model.md) och Använd utmatningen av [filter baserat funktions val](filter-based-feature-selection.md) som indata för träning.

    > [!IMPORTANT]
    > Eftersom funktions prioriteten baseras på värdena i kolumnen kan du inte veta i förväg vilka kolumner som kan vara tillgängliga för inmatningen till [träna modell](train-model.md).  
5. Koppla en instans av modulen Välj kolumner Transform. 

    Det här steget genererar en kolumn markering som en omvandling som kan sparas eller tillämpas på andra data uppsättningar. Det här steget ser till att kolumnerna som identifieras i funktions val sparas för andra moduler för åter användning.

6. Lägg till modulen [Poäng modell](score-model.md) . 

   *Anslut inte data uppsättningen för indata.* Lägg i stället till modulen [Använd omvandling](apply-transformation.md) och Anslut utdata från omvandling av funktions val.

   > [!IMPORTANT]
   > Du kan inte vänta på att använda [filter baserat funktions val](filter-based-feature-selection.md) för poängsättnings data uppsättningen och få samma resultat. Eftersom val av funktioner baseras på värden kan det välja en annan uppsättning kolumner, vilket skulle resultera i att åtgärden misslyckades.
7. Skicka pipelinen.

Den här processen för att spara och tillämpa en kolumn markering säkerställer att samma data schema är tillgängligt för utbildning och bedömning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
