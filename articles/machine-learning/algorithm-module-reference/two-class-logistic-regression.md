---
title: 'Logistik regression i två klasser: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder en logistik Regressions modell med två klasser i Azure Machine Learning service för att skapa en logistik Regressions modell som kan användas för att förutsäga två (och bara två) resultat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 7020bf529fa635d74959a9dac071aa6e2b134c5b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692612"
---
# <a name="two-class-logistic-regression-module"></a>Logistik Regressions modul i två klasser

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en logistik Regressions modell som kan användas för att förutsäga två (och endast två) resultat. 

Logistisk regression är en välkänd statistisk teknik som används för att modellera många typer av problem. Den här algoritmen är en *övervakad inlärnings* metod.  Därför måste du ange en data uppsättning som redan innehåller resultatet för att träna modellen.  

### <a name="about-logistic-regression"></a>Om Logistisk regression  

Logistisk regression är en välkänd metod i statistik som används för att förutsäga sannolikheten för ett resultat och är särskilt populär för klassificerings uppgifter. Algoritmen förutsäger sannolikheten för förekomst av en händelse genom att anpassa data till en logistik funktion.
  
I den här modulen är klassificerings algoritmen optimerad för dichotomous eller binära variabler. Om du behöver klassificera flera resultat använder du [logistik Regressions modulen multiklass](./multiclass-logistic-regression.md) .

##  <a name="how-to-configure"></a>Så här konfigurerar du  

För att träna den här modellen måste du ange en data uppsättning som innehåller en etikett eller klass kolumn. Eftersom den här modulen är avsedd för två klass problem måste etiketten eller klass kolumnen innehålla exakt två värden. 

Etikett kolumnen kan till exempel vara [röstat] med möjliga värden "Yes" eller "No". Eller så kan det vara [kredit risk], med möjliga värden "hög" eller "låg". 
  
1.  Lägg till en **logistik Regressions-modul med två klasser** i din pipeline.  
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.  
  
3.  För **optimerings tolerans**anger du ett tröskelvärde som ska användas när modellen optimeras. Om förbättringen mellan iterationer sjunker under det angivna tröskelvärdet anses algoritmen ha konvergerat till en lösning och träningen stoppas.  
  
4.  För **L1-regulariseringshastigheten vikt** och **L2-regulariseringshastigheten**anger du ett värde som ska användas för regulariseringshastigheten-parametrarna L1 och L2. Ett värde som inte är noll rekommenderas för båda.  
  
     *Regulariseringshastigheten* är en metod för att förhindra övermontering genom att motverka modeller med extrema koefficient värden. Regulariseringshastigheten fungerar genom att lägga till den påföljd som är kopplad till koefficienterna till felet i hypotesen. Därför skulle en korrekt modell med extrema koefficienter bli mer, men en mindre exakt modell med fler restriktiva värden skulle bli mindre.  
  
     L1-och L2-regulariseringshastigheten har olika effekter och användnings områden.  
  
    -   L1 kan användas för sparse-modeller, vilket är användbart när du arbetar med avancerade data.  
  
    -   L2-regulariseringshastigheten är däremot bättre för data som inte är sparse.  
  
     Den här algoritmen stöder en linjär kombination av L1-och L2-regulariseringshastigheten värden: det vill säga om <code>x = L1</code> och <code>y = L2</code>, definierar <code>ax + by = c</code> det linjära omfånget för regulariseringshastigheten-villkoren.  
  
    > [!NOTE]
    >  Vill du veta mer om L1-och L2-regulariseringshastigheten? Följande artikel innehåller en beskrivning av hur L1-och L2-regulariseringshastigheten skiljer sig från varandra och hur de påverkar modell anpassning, med kod exempel för logistik regression och neurala nätverks modeller: [L1-och L2-regulariseringshastigheten för Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Olika linjära kombinationer av L1-och L2-villkor har gjorts för logistik Regressions modeller: till exempel [elastiska net-regulariseringshastigheten](https://wikipedia.org/wiki/Elastic_net_regularization). Vi rekommenderar att du refererar till dessa kombinationer för att definiera en linjär kombination som är effektiv i din modell.
      
5.  För **minnes storlek för l-BFGS**anger du hur mycket minne som ska användas för att optimera *l-BFGS* .  
  
     L-BFGS står för "begränsat minne Broyden-Fletcher-Goldfarb-Shanno". Det är en optimerings algoritm som är populär för parameter uppskattning. Den här parametern anger antalet tidigare positioner och toningar som ska lagras för att beräkna nästa steg.  
  
     Den här optimerings parametern begränsar mängden minne som används för att beräkna nästa steg och riktning. När du anger mindre minne är träningen snabbare men mindre exakt.  
  
6.  Ange ett heltals värde för **slumpmässig numrering av utsäde**. Det är viktigt att definiera ett Seed-värde om du vill att resultatet ska vara reproducerbart över flera körningar av samma pipeline.  
  
  
8. Lägg till en taggad data uppsättning i pipelinen och Anslut en av [inlärnings modulerna](module-reference.md).  
  
    -   Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](./train-model.md) .  
  
9. Köra en pipeline.  
  
## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se en översikt över modellens parametrar, tillsammans med funktions vikterna från träningen, högerklickar du på utdata från [träna modell](./train-model.md) och väljer **visualisera**.   
  
+ Om du vill göra förutsägelser för nya data använder du den tränade modellen och nya data som indata till modulen [Poäng modell](./score-model.md) . 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 