---
title: 'Lägg till kolumner: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Lägg till kolumner i Azure Machine Learning för att sammanfoga två data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 55981279cb1902424d1a0f77af097dc379d7222f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493960"
---
# <a name="add-columns-module"></a>Lägg till kolumner-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att sammanfoga två data uppsättningar. Du kan kombinera alla kolumner från de två data uppsättningarna som du anger som indata för att skapa en enskild data uppsättning. Om du behöver sammanfoga fler än två data uppsättningar använder du flera instanser av **Lägg till kolumner**.



## <a name="how-to-configure-add-columns"></a>Så här konfigurerar du Lägg till kolumner
1. Lägg till modulen **Lägg till kolumner** i din pipeline.

2. Anslut de två data uppsättningarna som du vill sammanfoga. Om du vill kombinera fler än två data uppsättningar kan du kedja samman flera kombinationer av **Lägg till kolumner**.

    - Det går att kombinera två kolumner som har ett annat antal rader. Data uppsättningen för utdata fylls med värden som saknas för varje rad i den mindre käll kolumnen.

    - Du kan inte välja enskilda kolumner som ska läggas till. Alla kolumner från varje data uppsättning sammanfogas när du använder **Lägg till kolumner**. Om du bara vill lägga till en delmängd av kolumnerna använder du Välj kolumner i data uppsättning för att skapa en data uppsättning med de kolumner som du vill använda.

3. Köra en pipeline.

### <a name="results"></a>Resultat
När pipelinen har körts:

- Om du vill se de första raderna i den nya data uppsättningen högerklickar du på utdata från **Lägg till kolumner** och väljer visualisera.

Antalet kolumner i den nya data mängden motsvarar summan av kolumnerna i båda data uppsättningarna.

Om det finns två kolumner med samma namn i indata-datauppsättningarna läggs ett numeriskt suffix till i kolumnens namn. Om det till exempel finns två instanser av en kolumn med namnet TargetOutcome skulle den vänstra kolumnen byta namn till TargetOutcome_1 och den högra kolumnen får namnet TargetOutcome_2.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 