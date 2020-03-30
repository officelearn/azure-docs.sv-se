---
title: Maskininlärning algoritm lathund
titleSuffix: Azure Machine Learning
description: En utskrivbar machine learning-algoritm fuskark hjälper dig att välja rätt algoritm för din prediktiva modell i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328631"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Maskininlärningsalgoritmens fuskblad för Azure Machine Learning designer

**Azure Machine Learning Algorithm Cheat Sheet** hjälper dig att välja rätt algoritm för en förutsägande analysmodell.

Azure Machine Learning har ett stort bibliotek med algoritmer från ***klassificeringen,*** ***rekommenderare system,*** ***klustring,*** ***avvikelseidentifiering,*** ***regression***och ***textanalysfamiljer.*** Var och en är utformad för att lösa en annan typ av maskininlärningsproblem.

Mer vägledning finns i [Så här väljer du algoritmer](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Ladda ner: Machine Learning Algoritm Lathund

**Ladda ner lathund här: [Machine Learning Algoritm Lathund (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning Algoritm lathund: Lär dig att välja en machine learning algoritm.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Ladda ner och skriv ut Machine Learning Algorithm Lathund i tabloidstorlek för att hålla den till hands och få hjälp med att välja en algoritm.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Hur man använder Machine Learning Algoritm Lathund

De förslag som erbjuds i denna algoritm fuska blad är ungefärliga regler-of-thumb. Vissa kan böjas, och vissa kan flagrant kränkas. Detta lathund är avsedd att föreslå en utgångspunkt. Var inte rädd för att köra en head-to-head konkurrens mellan flera algoritmer på dina data. Det finns helt enkelt inget substitut för att förstå principerna för varje algoritm och det system som genererade dina data.

Varje maskininlärningsalgoritm har sin egen stil eller induktiva bias. För ett specifikt problem kan flera algoritmer vara lämpliga och en algoritm kan passa bättre än andra. Men det är inte alltid möjligt att veta i förväg vilket som passar bäst. I fall som dessa, flera algoritmer listas tillsammans i lathunden. En lämplig strategi skulle vara att prova en algoritm, och om resultaten ännu inte är tillfredsställande, prova de andra. 

Mer information om algoritmerna i Azure Machine Learning finns i [Algoritm- och modulreferensen](algorithm-module-reference/module-reference.md).

## <a name="kinds-of-machine-learning"></a>Typer av maskininlärning

Det finns tre huvudkategorier av maskininlärning: *övervakad inlärning,* *oövervakad inlärning*och *förstärkningsinlärning*.

### <a name="supervised-learning"></a>Övervakat lärande

Vid övervakad inlärning är varje datapunkt märkt eller associerad med en kategori eller ett intressevärde. Ett exempel på en kategorisk etikett är att tilldela en bild som antingen en "katt" eller en "hund". Ett exempel på en värdeetikett är försäljningspriset som är associerat med en begagnad bil. Målet med övervakat lärande är att studera många märkta exempel som dessa, och sedan kunna göra förutsägelser om framtida datapunkter. Till exempel identifiera nya bilder med rätt djur eller tilldela korrekta försäljningspriser till andra begagnade bilar. Detta är en populär och användbar typ av maskininlärning.

### <a name="unsupervised-learning"></a>Oövervakad inlärning

Vid oövervakad inlärning har datapunkter inga associerade etiketter med dem. I stället är målet med en oövervakad inlärningsalgoritm att organisera data på något sätt eller att beskriva dess struktur. Oövervakad inlärning grupperar data i kluster, som K-medel, eller hittar olika sätt att titta på komplexa data så att det verkar enklare.

### <a name="reinforcement-learning"></a>Kunskapsförmedling

I förstärkningsinlärning får algoritmen välja en åtgärd som svar på varje datapunkt. Det är en vanlig metod inom robotteknik, där uppsättningen av sensoravläsningar vid en tidpunkt är en datapunkt, och algoritmen måste välja robotens nästa åtgärd. Det är också en naturlig passform för Internet of Things applikationer. Inlärningsalgoritmen får också en belöningssignal en kort tid senare, vilket indikerar hur bra beslutet var. Baserat på denna signal ändrar algoritmen sin strategi för att uppnå högsta belöning. 

## <a name="next-steps"></a>Nästa steg

* Se ytterligare vägledning om [Hur du väljer algoritmer](how-to-select-algorithms.md)

* [Lär dig mer om studio i Azure Machine Learning och Azure-portalen](overview-what-is-azure-ml.md).

* [Självstudiekurs: Skapa en förutsägelsemodell i Azure Machine Learning designer](tutorial-designer-automobile-price-train-score.md).

* [Lär dig mer om djupinlärning kontra maskininlärning.](concept-deep-learning-vs-machine-learning.md)
