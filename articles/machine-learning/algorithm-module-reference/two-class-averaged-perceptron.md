---
title: 'Beslutsskog Regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Tvåklassförhöjt i genomsnitt Perceptron i Azure Machine Learning-tjänsten för att skapa en machine learning-modell som baseras på genomsnittlig perceptron algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029242"
---
# <a name="two-class-averaged-perceptron-module"></a>Tvåklassförhöjt i genomsnitt Perceptron modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en machine learning-modell som baseras på genomsnittlig perceptron-algoritmen.  
  
Den här klassificeringen algoritmen är en metod för övervakad inlärning och kräver en *taggade datauppsättning*, som innehåller en kolumn för etiketten. Du kan träna modellen genom att ange modellen och taggade datauppsättningen som indata till [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya inkommande exempel.  

### <a name="about-averaged-perceptron-models"></a>Genomsnittlig perceptron modeller

Den *perceptron-metod i genomsnitt* är en tidig och enkel version av ett neurala nätverk. Den här metoden kan klassificeras indata till flera möjliga utdata utifrån en linjär funktion och sedan kombineras med en uppsättning vikterna som härleds från funktionen vektor – därför namnet ”perceptron”.

Enklare perceptron modeller passar bra för learning linjärt kan särskiljas mönster, medan neurala nätverk (särskilt djupa neurala nätverk) kan utforma mer komplexa klassgränser. Men perceptrons är snabbare och eftersom de bearbetar fall seriellt perceptrons kan användas med kontinuerlig utbildning.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Så här konfigurerar du två genomsnitt Perceptron

1.  Lägg till den **Tvåklassförhöjt i genomsnitt Perceptron** modulen i experimentet.  

2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.
  
3.  För **Learning rate**, ange ett värde för den *learning rate*. Inlärningsfrekvensen värdena kontroll storleken på det steg som används i stokastisk brantaste lutningsmetoden varje gång modellen testas och korrigerade.
  
     Genom att minska frekvensen storlek testa du modellen oftare, med risken att du kan hämta fastnat i en lokal platå. Genom att göra steget större, kan du samlar in snabbare, dess risk överskridande SANT minimi.
  
4.  För **maximalt antal iterationer**, ange hur många gånger du vill att algoritmen för att undersöka träningsdata.  
  
     Stoppa tidigt ofta ger bättre generalisering. Öka antalet iterationer förbättrar montering, dess risk overfitting.
  
5.  För **nummer slumptal**kan du också ange ett heltalsvärde som används som startvärdet. Med hjälp av ett startvärde rekommenderas om du vill kontrollera att reproducerbarhet av experimentet över kan köras.  
  
1.  Anslut en datauppsättning för träning och en av modulerna som utbildning:
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](train-model.md) modulen.

## <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna vet utbildning, högerklickar du på utdata från [Träningsmodell](./train-model.md).


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 