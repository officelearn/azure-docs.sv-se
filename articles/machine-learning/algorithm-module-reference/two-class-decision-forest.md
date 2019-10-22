---
title: 'Besluts skog med två klasser: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder besluts skogen i två klasser i Azure Machine Learning service för att skapa en Machine Learning-modell som baseras på algoritmen för besluts skogar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 243f1774069f048d0e8a1ce11e3ac42e4e73f58b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693623"
---
# <a name="two-class-decision-forest-module"></a>Modul för besluts skog i två klass

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för besluts skogar.  

Besluts skogar är snabba, övervakade Ensemble-modeller. Den här modulen är ett bra alternativ om du vill förutsäga ett mål med högst två resultat. 

## <a name="understanding-decision-forests"></a>Förstå besluts skogar

Detta besluts skogs algoritm är en ensemble-utbildnings metod som är avsedd för klassificerings uppgifter. Ensemble-metoder baseras på den allmänna principen att i stället för att förlita dig på en enskild modell kan du få bättre resultat och en mer generaliserad modell genom att skapa flera relaterade modeller och kombinera dem på ett visst sätt. Vanligt vis ger Ensemble-modeller bättre täckning och exakthet än enskilda besluts träd. 

Det finns många sätt att skapa enskilda modeller och kombinera dem i en ensemble. Den här specifika implementeringen av en besluts skog fungerar genom att skapa flera besluts träd och sedan **rösta** på den mest populära utmatnings klassen. Röstning är en av de bättre kända metoderna för att skapa resultat i en ensemble-modell. 

+ Många enskilda klassificerings träd skapas med hela data uppsättningen, men olika (vanligt vis slumpmässiga) Start punkter. Detta skiljer sig från den slumpmässiga skogs metoden, där de enskilda besluts träden bara kan använda viss slumpmässig del av data eller funktioner.
+ Varje träd i trädet besluts skog matar ut ett icke-normaliserat frekvens histogram med etiketter. 
+ Agg regerings processen summerar dessa histogram och normaliserar resultatet för att få "sannolikhet" för varje etikett. 
+ De träd som har hög förutsägelse säkerhet får en större vikt i det slutliga beslutet av ensemblen.

Besluts träd i allmänhet har många fördelar för klassificerings aktiviteter:
  
- De kan fånga icke-linjära besluts gränser.
- Du kan träna och förutse massor av data eftersom de är effektiva i beräknings-och minnes användning.
- Val av funktioner är integrerat i inlärnings-och klassificerings processerna.  
- Träd kan hantera data för störningar och många funktioner.  
- De är icke-parameter modeller, vilket innebär att de kan hantera data med varierande distributioner. 

Enkla besluts träd kan dock overfit på data och är mindre generalizable än för träd-ensembler.

Mer information finns i [besluts skogar](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Så här konfigurerar du
  
1.  Lägg till modulen **besluts skog i två klasser** till din pipeline i Azure Machine Learning och öppna rutan **Egenskaper** för modulen. 

    Du kan hitta modulen under **Machine Learning**. Expandera **initiera**och sedan **klassificering**.  
  
2.  För **metoden**för att sampla om väljer du den metod som används för att skapa enskilda träd.  Du kan välja mellan **bagage** eller **Replikera**.  
  
    -   **Bagage**: bagage kallas även *Start agg regering*. I den här metoden odlas varje träd på ett nytt exempel som skapats genom att slumpmässigt sampla den ursprungliga data uppsättningen med ersättning tills du har en data uppsättning som är den ursprungliga storleken.  
  
         Modellernas utdata kombineras med *röstning*, vilket är en form av agg regering. Varje träd i en klassificering besluts skog utvärderar ett normaliserat frekvens histogram med etiketter. Aggregation är att summera dessa histogram och normalisera för att få "sannolikhet" för varje etikett. På det här sättet kommer de träd som har hög förutsägelse tillförlitlighet att ha en större vikt i det slutliga beslutet av ensemblen.  
  
         Mer information finns i Wikipedia-posten för start agg regering.  
  
    -   **Replikera**: i replikering tränas varje träd på exakt samma indata. Bestämning av vilket delat predikat som används för varje trädnod förblir slumpmässigt och träden är olika.   
  
3.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.
  
4.  För **antal besluts träd**anger du det maximala antalet besluts träd som kan skapas i ensemblen. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.  
  
    > [!NOTE]
    >  Det här värdet styr också antalet träd som visas vid visualisering av den tränade modellen. Om du vill se eller skriva ut ett enda träd kan du ange värdet till 1. Men det går bara att skapa ett träd (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
5.  Ange ett tal för att begränsa det maximala djupet för besluts träden för att begränsa det maximala djupet för **besluts träd**. Att öka djupet i trädet kan öka precisionen på risken för viss överanpassning och ökad inlärnings tid.
  
6.  För **antalet slumpmässiga delningar per nod**anger du antalet delningar som ska användas när du skapar varje nod i trädet. En *delning* innebär att funktionerna i varje nivå i trädet (noden) är slumpmässigt uppdelade.
  
7.  För **minsta antal sampel per lövnod**anger du det minsta antal fall som krävs för att skapa en terminalsession (löv) i ett träd.
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.  
  
8.  Välj alternativet **Tillåt okända värden för kategoriska-funktioner** om du vill skapa en grupp för okända värden i träningen eller verifierings uppsättningarna. Modellen kan vara mindre exakt för kända värden, men den kan ge bättre förutsägelser för nya (okända) värden. 

     Om du avmarkerar det här alternativet kan modellen bara acceptera de värden som finns i tränings data.
  
9. Bifoga en etikettad data uppsättning och en av [inlärnings modulerna](module-reference.md):  
  
    -   Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](./train-model.md) .  
  
    
## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se trädet som skapades på varje iteration högerklickar du på utdata från modulen [träna modell](./train-model.md) och väljer **visualisera**.
  
    Klicka på varje träd för att öka detalj nivån i delningarna och se reglerna för varje nod.

+ Om du vill spara en ögonblicks bild av modellen högerklickar du på den **tränade modellens** utdata och väljer **Spara modell**. Den sparade modellen uppdateras inte vid efterföljande körningar av pipelinen.

+ Om du vill använda modellen för poängsättning lägger du till modulen **Poäng modell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 