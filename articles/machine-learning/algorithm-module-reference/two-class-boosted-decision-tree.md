---
title: 'Besluts träd med två klasser: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen för besluts träd med två klasser i Azure Machine Learning service för att skapa en maskin inlärnings modell som baseras på algoritmen för besluts fattare.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 5795dc994872ac5da37a1226524dddd35aa35126
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692634"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modul för besluts träd med två klasser

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en Machine Learning-modell som baseras på algoritmen för besluts träd. 

Ett utökat besluts träd är en ensemble-utbildnings metod där det andra trädet korrigerar fel i det första trädet, och det tredje trädet korrigeras för felen i de första och andra träden och så vidare.  Förutsägelser baseras på hela ensemblen för träd som utgör förutsägelsen.
  
När rätt konfigurerade besluts träd är korrekt konfigurerade är de enkla metoder som du kan använda för att få bästa prestanda på en mängd olika Machine Learning-uppgifter. De är dock också en av de mer minnes intensiva eleverna och den aktuella implementeringen innehåller allt i minnet. Därför kanske en bättre besluts träd modell inte kan bearbeta de stora data mängderna som vissa linjära lärare kan hantera.

## <a name="how-to-configure"></a>Så här konfigurerar du

Den här modulen skapar en modell som inte är tränad. Eftersom klassificeringen är en övervakad inlärnings metod behöver du en *Taggad data uppsättning* som innehåller en etikett kolumn med ett värde för alla rader för att träna modellen.

Du kan träna den här typen av modell med hjälp av [träna modell](././train-model.md). 

1.  I Azure Machine Learning lägger du till modulen för **besluts träd med högre** till din pipeline.
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .
  
    + **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.
  
  
3.  För **maximalt antal löv per träd**, anger du det maximala antalet terminalservrar (löv) som kan skapas i alla träd.
  
     Genom att öka det här värdet kan du öka storleken på trädet och få bättre precision vid överanpassning och längre inlärnings tid.
  
4.  För **minsta antal exempel per lövnod**, anger du antalet fall som krävs för att skapa en terminalserversession (löv) i ett träd.  
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel, med standardvärdet 1, kan ett enda ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste tränings data innehålla minst fem fall som uppfyller samma villkor.
  
5.  För **inlärnings hastighet**anger du ett tal mellan 0 och 1 som definierar steg storleken under inlärningen.  
  
     Inlärnings frekvensen avgör hur snabbt eller långsamt en elev konvergerar på den optimala lösningen. Om steg storleken är för stor kan du överskrida den optimala lösningen. Om steg storleken är för liten tar inlärningen längre tid att konvergera i den bästa lösningen.
  
6.  Ange det totala antalet besluts träd som ska skapas i ensemblen för **antalet skapade träd**. Genom att skapa fler besluts träd kan du eventuellt få bättre täckning, men inlärnings tiden ökar.
  
     Det här värdet styr också antalet träd som visas vid visualisering av den tränade modellen. Om du vill se eller skriva ut ett enda träd ställer du in värdet på 1. Men när du gör det skapas endast ett träd (trädet med den inledande uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
7.  För **slumpmässigt antal frön**kan du ange ett icke-negativt heltal som ska användas som det slumpmässiga startvärdet. Genom att ange ett utsäde säkerställer du reproducerbarhet i körningar som har samma data och parametrar.  
  
     Det slumpmässiga startvärdet anges som standard till 0, vilket innebär att det första startvärdet hämtas från system klockan.  Efterföljande körningar med hjälp av ett slumpmässigt Seed kan ha olika resultat.
  

9. Träna modellen.
  
    + Om du ställer in **skapa utbildare** för en **parameter**ansluter du en taggad data uppsättning och modulen [träna modell](./train-model.md) .  
  
   
## <a name="results"></a>Resultat

När modell träningen är klar högerklickar du på resultatet av [träna modell](./train-model.md) för att visa resultaten:

+ Om du vill se trädet som skapades på varje iteration väljer du **visualisera**. 
+ Klicka på varje träd för att öka detalj nivån i delningarna och se reglerna för varje nod.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 