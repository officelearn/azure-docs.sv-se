---
title: 'Lägg till kolumner: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Lägg till kolumner i Azure Machine Learning för att sammanfoga två datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456786"
---
# <a name="add-columns-module"></a>Modulen Lägg till kolumner

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att sammanfoga två datauppsättningar. Du kombinerar alla kolumner från de två datauppsättningar som du anger som indata för att skapa en enda datauppsättning. Om du behöver sammanfoga fler än två datauppsättningar använder du flera instanser av **Lägg till kolumner**.



## <a name="how-to-configure-add-columns"></a>Konfigurera Lägga till kolumner
1. Lägg till modulen **Lägg till kolumner** i pipelinen.

2. Anslut de två datauppsättningar som du vill sammanfoga. Om du vill kombinera fler än två datauppsättningar kan du kedja ihop flera kombinationer av **Lägg till kolumner**.

    - Det är möjligt att kombinera två kolumner som har ett annat antal rader. Utdatauppsättningen ärvad med saknade värden för varje rad i den mindre källkolumnen.

    - Du kan inte välja enskilda kolumner att lägga till. Alla kolumner från varje datauppsättning sammanfogas när du använder **Lägg till kolumner**. Om du bara vill lägga till en delmängd av kolumnerna använder du därför Markera kolumner i datauppsättning för att skapa en datauppsättning med de kolumner du vill använda.

3. Skicka pipelinen.

### <a name="results"></a>Resultat
När pipelinen har körts:

- Om du vill visa de första raderna i den nya datauppsättningen högerklickar du på modulen **Lägg till kolumner** och väljer Visualisera. Eller Välj modul och växla till fliken **Utdata** på den högra panelen klickar du på histogramikonen i **portutgångarna** för att visualisera resultatet.

Antalet kolumner i den nya datauppsättningen är lika med summan av kolumnerna för båda indatauppsättningarna.

Om det finns två kolumner med samma namn i indatauppsättningarna läggs ett numeriskt suffix till i namnet på kolumnen. Om det till exempel finns två förekomster av en kolumn med namnet TargetOutcome, döps den vänstra kolumnen om TargetOutcome_1 och den högra kolumnen döps om TargetOutcome_2.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 