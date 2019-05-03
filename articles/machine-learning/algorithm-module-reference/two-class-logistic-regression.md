---
title: 'Två Logistic Regression: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder två Logistic Regression-modulen i Azure Machine Learning-tjänsten för att skapa en logistic regression-modell som kan användas för att förutsäga resultat för två (och bara två).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029257"
---
# <a name="two-class-logistic-regression-module"></a>Två Logistic Regression-modulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en logistic regression-modell som kan användas för att förutsäga resultat för två (och bara två). 

Logistic regression är en välkänd statistiska teknik som används för modellering av många olika typer av problem. Den här algoritmen är en *övervakad inlärning* metoden.  Därför måste du ange en datauppsättning som redan innehåller resultat för att träna modellen.  

### <a name="about-logistic-regression"></a>Om logistic regression  

Logistic regression är en välkänd metod i statistik som används för att förutse sannolikheten för ett resultat och används särskilt ofta för klassificering. Algoritmen som förutsäger sannolikheten för förekomst av en händelse effektivare data till en logistisk funktion.
  
I den här modulen är algoritmen för klassificering optimerat för dichotomous eller binär variabler. Om du vill klassificera flera resultat kan du använda den [Multiclass Logistic Regression](./multiclass-logistic-regression.md) modulen.

##  <a name="how-to-configure"></a>Så här konfigurerar du  

Du måste ange en datauppsättning som innehåller en etikett eller klass kolumn för att träna modellen. Eftersom den här modulen är avsedd för två problem, måste kolumnen etikett eller klass innehålla exakt två värden. 

Till exempel etikettkolumnen kan vara [röstat] med möjliga värdena för ”Yes” eller ”No”. Eller så kan det vara [kreditrisk] med möjliga värdena för ”hög” eller ”låg”. 
  
1.  Lägg till den **Tvåklassförhöjt Logistic Regression** modulen i experimentet.  
  
2.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Om du vet hur du vill konfigurera modellen kan ange du en specifik uppsättning värden som argument.  
  
3.  För **optimering tolerans**, ange ett tröskelvärde som ska användas för att optimera modellen. Om förbättring mellan iterationer sjunker under det angivna tröskelvärdet, algoritmen anses ha konvergerat på en lösning och utbildning stoppas.  
  
4.  För **L1 regularisering vikt** och **L2 regularisering vikt**, ange ett värde för parametrarna regularisering L1 och L2. Ett annat värde än noll rekommenderas för båda.  
  
     *Regularisering* är en metod för att förhindra overfitting av penalizing modeller med extrem koefficienten värden. Regularisering fungerar genom att lägga till den särskilda avgifter som är associerad med koefficienten värden till fel hypotesen. Därför skulle straffas en korrekt modell med extrem koefficienten värden mer, men en mindre exakta modell med mer konservativ värden skulle straffas mindre.  
  
     L1 och L2 regulariseringshastighet har olika effekter och använder.  
  
    -   L1 kan tillämpas på null-optimerade modeller, vilket är användbart när du arbetar med hög-dimensionell data.  
  
    -   L2 regularisering är däremot bättre för data som inte är null-optimerad.  
  
     Den här algoritmen stöder en linjär värdekombinationen L1 och L2 regularisering: det vill säga om <code>x = L1</code> och <code>y = L2</code>, sedan <code>ax + by = c</code> definierar linjär loppet av regularisering villkoren.  
  
    > [!NOTE]
    >  Vill du veta mer om L1 och L2 regularisering? I följande artikel finns en beskrivning av hur L1 och L2 regularisering är olika och hur de påverkar modellen anslutning med kodexempel för logistic regression och modeller av neurala nätverk:  [L1 och L2 Regulariseringshastighet för Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Olika linjära kombinationer av L1 och L2 villkoren planeras för logistiska regressionsmodeller: till exempel [elastiska net regularisering](https://wikipedia.org/wiki/Elastic_net_regularization). Vi rekommenderar att du referera till dessa kombinationer för att definiera en linjär kombination som är effektivt i din modell.
      
5.  För **minnesstorleken för L BFGS**, ange mängden minne som ska användas för *L BFGS* optimering.  
  
     L-BFGS står för ”begränsat minne Broyden-Fletcher-Goldfarb-Shanno”. Det är en optimering-algoritm som används ofta för parametern kostnadsuppskattning. Den här parametern anger antalet tidigare positioner och toningar att lagra för beräkning av nästa steg.  
  
     Den här parametern för optimering begränsar mängden minne som används för att beräkna nästa steg och riktning. När du anger mindre minne är utbildning snabbare men mindre exakta.  
  
6.  För **nummer slumptal**, ange ett heltalsvärde. Definiera en seed-värdet är viktigt om du vill att resultatet ska reproducerbar över flera körningar av samma experimentet.  
  
  
8. Lägg till en taggade datauppsättning till arbetsytan för experimentet och koppla ett av de [utbildningsmoduler](module-reference.md).  
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](./train-model.md) modulen.  
  
9. Kör experimentet.  
  
## <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna vet utbildning, högerklickar du på utdata från [Träningsmodell](./train-model.md) och välj **visualisera**.   
  
+ Om du vill göra förutsägelser kring nya data, använder du den tränade modellen och nya data som indata för den [Poängmodell](./score-model.md) modulen. 


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 