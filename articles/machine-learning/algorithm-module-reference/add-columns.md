---
title: 'Lägg till kolumner: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Lägg till kolumner i Azure Machine Learning-tjänsten för att sammanfoga två data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 630f8f7d4561ed6e7e9f943f4b3b123daeee4d67
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129026"
---
# <a name="add-columns-module"></a>Lägg till kolumner-modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att sammanfoga två data uppsättningar. Du kan kombinera alla kolumner från de två data uppsättningarna som du anger som indata för att skapa en enskild data uppsättning. Om du behöver sammanfoga fler än två data uppsättningar använder du flera instanser av **Lägg till kolumner**.



## <a name="how-to-configure-add-columns"></a>Så här konfigurerar du Lägg till kolumner
1. Lägg till modulen **Lägg till kolumner** i experimentet.

2. Anslut de två data uppsättningarna som du vill sammanfoga. Om du vill kombinera fler än två data uppsättningar kan du kedja samman flera kombinationer av **Lägg till kolumner**.

    - Det går att kombinera två kolumner som har ett annat antal rader. Data uppsättningen för utdata fylls med värden som saknas för varje rad i den mindre käll kolumnen.

    - Du kan inte välja enskilda kolumner som ska läggas till. Alla kolumner från varje data uppsättning sammanfogas när du använder **Lägg till kolumner**. Om du bara vill lägga till en delmängd av kolumnerna använder du Välj kolumner i data uppsättning för att skapa en data uppsättning med de kolumner som du vill använda.

3. Kör experimentet.

### <a name="results"></a>Resultat
När experimentet har körts:

- Om du vill se de första raderna i den nya data uppsättningen högerklickar du på utdata från **Lägg till kolumner** och väljer visualisera.

Antalet kolumner i den nya data mängden motsvarar summan av kolumnerna i båda data uppsättningarna.

Om det finns två kolumner med samma namn i indata-datauppsättningarna läggs ett numeriskt suffix till i kolumnens namn. Om det till exempel finns två instanser av en kolumn med namnet TargetOutcome skulle den vänstra kolumnen byta namn till TargetOutcome_1 och den högra kolumnen får namnet TargetOutcome_2.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 