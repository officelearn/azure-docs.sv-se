---
title: 'Lägg till kolumner: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att använda modulen Lägg till kolumner i Azure Machine Learning-tjänsten för att sammanfoga två datauppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029362"
---
# <a name="add-columns-module"></a>Lägg till kolumner modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Du kan använda den här modulen för att sammanfoga två datauppsättningar. Du kan kombinera alla kolumner från två datauppsättningar som du anger som indata för att skapa en enskild datauppsättning. Om du vill sammanfoga mer än två datauppsättningar kan använda flera instanser av **Lägg till kolumner**.



## <a name="how-to-configure-add-columns"></a>Så här konfigurerar du lägga till kolumner
1. Lägg till den **Lägg till kolumner** modulen i experimentet.

2. Ansluta de två datauppsättningar som du vill sammanfoga. Om du vill kombinera fler än två datauppsättningar, du kan länka samman flera kombinationer av **Lägg till kolumner**.

    - Det är möjligt att kombinera två kolumner som har olika antal rader. Datauppsättningen för utdata fylls ut med saknade värden för varje rad i mindre källkolumnen.

    - Du kan inte välja enskilda kolumner att lägga till. Alla kolumner från varje datauppsättning sammanfogas när du använder **Lägg till kolumner**. Därför, om du vill lägga till en delmängd av kolumnerna använda Välj kolumner i datauppsättning för att skapa en datauppsättning med de kolumner du vill.

3. Kör experimentet.

### <a name="results"></a>Resultat
När experimentet har körts:

- Om du vill se de första raderna i den nya datauppsättningen, högerklickar du på utdata från **Lägg till kolumner** och välj visualisera.

Antalet kolumner i den nya datauppsättningen är lika med summan av kolumnerna i båda indatauppsättningar.

Om det finns två kolumner med samma namn i indatauppsättningar, läggs ett numeriskt suffix till namnet på kolumnen. Till exempel om det finns två instanser av en kolumn med namnet TargetOutcome, den vänstra kolumnen skulle ändras TargetOutcome_1 och den högra kolumnen att byta namn på TargetOutcome_2.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 