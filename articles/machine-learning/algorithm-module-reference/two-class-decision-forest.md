---
title: 'Beslutsskog med två: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder Beslutsskog med två modulen i Azure Machine Learning-tjänsten för att skapa en machine learning-modell som baseras på beslut skogar algoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 73b7822c56e2b07eeefdedce1bce6d410d110ebc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411483"
---
# <a name="two-class-decision-forest-module"></a>Beslutsskog med två modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en machine learning-modell som baseras på beslut skogar algoritmen.  

Beslutet skogar är snabb och övervakat ensemble modeller. Denna modul är ett bra alternativ om du vill förutsäga ett mål med högst två resultat. 

## <a name="understanding-decision-forests"></a>Förstå beslut skogar

Beslutet skog algoritmen är en ensemble learning metod som är avsedd för klassificering. Ensemble metoder baseras på den allmänna principen som i stället för att förlita dig på en enda modell, du kan få bättre resultat och en mer generaliserad modell genom att skapa flera relaterade modeller och kombinera dem på något sätt. I allmänhet ger ensemble modeller bättre täckning och noggrannhet än enkel beslutsträd. 

Det finns många sätt att skapa enskilda modeller och kombinera dem i en ensemble. Den här specifika implementering av en beslutsskog fungerar genom att skapa flera beslutsträd och sedan **röstande** om klassen populäraste utdata. Rösta är en av sådana metoder för att skapa resultat i en ensemble-modell. 

+ Många enskilda klassificering träd skapas med hjälp av hela datauppsättningen, men olika (slumpmässig vanligtvis) startpunkter. Detta skiljer sig från slumpmässiga skog metod, där de enskilda beslutsträd endast kan använda en slumpmässig del av data eller funktioner.
+ Varje trädet i skogen beslutsträdet matar ut ett icke-normaliserat frekvens histogram för etiketter. 
+ Processen för aggregering summerar dessa histogram och normaliserar resultatet för att få ”sannolikhet” för varje etikett. 
+ Träd som har hög förutsägelsen har en högre vikt i det slutliga beslutet i ensemble.

Beslutsträd har i allmänhet många fördelar för klassificering:
  
- De kan samla in icke-linjära beslut gränser.
- Du kan träna och förutsäga på stora mängder data, eftersom de är effektiv i beräkningen och minnesanvändning.
- Val av funktioner ingår i processer för utbildning och klassificering.  
- Träd kan hantera bort störande data och många funktioner.  
- De är icke-parametrisk modeller, vilket innebär att de kan hantera data med olika distributioner. 

Men enkel beslutsträd kan overfit på data och är mindre generaliserbara än trädet ensembler.

Mer information finns i [beslut skogar](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Så här konfigurerar du
  
1.  Lägg till den **Beslutsskog med två** modul till ditt experiment i Azure Machine Learning och öppna den **egenskaper** rutan i modulen. 

    Du kan hitta modulen under **Maskininlärning**. Expandera **initiera**, och sedan **klassificering**.  
  
2.  För **sampla metoden**, välja den metod som används för att skapa enskilda träd.  Du kan välja mellan **Bagging** eller **replikera**.  
  
    -   **Bagging**: Kallas även för förpackningskapacitet *bootstrap sammanställa*. I den här metoden är varje träd odlas på ett nytt stickprov som skapats av slumpmässigt sampling den ursprungliga datauppsättningen med ersättning förrän du har en datauppsättning storleken på ursprungligt.  
  
         Utdata för modellerna kombineras med *röstande*, vilket är en typ av aggregering. Varje trädet i en klassificering beslutsskog matar ut en Onormaliserad frekvens histogram för etiketter. Aggregeringen är att summera dessa histogram och normalisera för att få ”sannolikhet” för varje etikett. På så sätt kan har träd som har hög förutsägelsen en högre vikt i det slutliga beslutet i ensemble.  
  
         Mer information finns i posten för sammanställning av Bootstrap Wikipedia.  
  
    -   **Replikera**: I replikering tränas varje träd på exakt samma inkommande data. Bestämning av vilka dela predikatet används för varje trädnod är slumpmässig och träd kommer att vara olika.   
  
3.  Ange hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Om du vet hur du vill konfigurera modellen kan ange du en specifik uppsättning värden som argument.
  
4.  För **antalet beslutsträd**, skriver du det maximala antalet beslutsträd som kan skapas i ensemble. Du kan eventuellt få bättre täckning genom att skapa mer beslutsträd, men utbildningstid ökar.  
  
    > [!NOTE]
    >  Det här värdet styr också antalet träd som visas när visualisera den tränade modellen. Om du vill visa eller skriva ut ett enda träd, kan du ange värdet till 1. Endast en trädet kan dock vara produceras (trädet med den första uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
5.  För **maximalt djup för beslutsträd**, Skriv ett tal som begränsar det maximala djupet på alla beslutsträdet. Öka djupet i trädet kan öka noggrannhet på millisekunder, dess risk utbildningstid overfitting och ökad.
  
6.  För **antalet slumpmässigt delningar per nod**, anger du antalet delningar ska användas för att skapa varje nod i trädet. En *dela* innebär att funktioner i varje nivå i trädet (node) delas slumpmässigt.
  
7.  För **minsta antal prover per lövnod**, ange det minsta antalet fall som krävs för att skapa en terminal nod (löv-) i ett träd.
  
     Genom att öka det här värdet kan du öka tröskelvärdet för att skapa nya regler. Med standardvärdet 1 kan till exempel även ett enda fall orsaka en ny regel som ska skapas. Om du ökar värdet till 5, skulle träningsdata behöva innehålla minst fem ärenden som uppfyller villkor som är samma.  
  
8.  Välj den **Tillåt okända värden för kategoriska funktioner** alternativet för att skapa en grupp för okända värden i utbildning eller validering mängder. Modellen kan vara mindre exakt för kända värden, men det kan ge bättre förutsägelser för nya (okänd) värden. 

     Om du avmarkerar det här alternativet kan modellen Acceptera bara de värdena som finns i träningsdata.
  
9. Koppla en taggade datauppsättning och en av de [utbildningsmoduler](module-reference.md):  
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda den [Träningsmodell](./train-model.md) modulen.  
  
    
## <a name="results"></a>Resultat

När utbildning har slutförts:

+ Om du vill se i trädet som har skapats för varje iteration, högerklickar du på utdata från den [Träningsmodell](./train-model.md) modul och välj **visualisera**.
  
    Klicka på varje träd för att granska nedåt i delningen och se regler för varje nod.

+ Spara en ögonblicksbild av modellen genom att högerklicka på den **Trained Model** utdata och välj **Spara modell**. Sparade modellen uppdateras inte på efterföljande körningar av experimentet.

+ Om du vill använda modellen för bedömning, lägger du till den **Poängmodell** modul till ett experiment.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 