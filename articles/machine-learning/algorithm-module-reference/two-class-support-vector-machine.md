---
title: 'Dubbelriktad Vector-dator: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för **dubbelriktad Vector** i Azure Machine Learning för att skapa en modell som baseras på algoritmen för att stödja Vector-datorer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: ca47bed45f8d726010dde40a936da6f93eb0b140
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76315022"
---
# <a name="two-class-support-vector-machine-module"></a>Dubbelriktad Vector Machine-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att skapa en modell som baseras på algoritmen för att stödja Vector-datorer. 

Support Vector Machines (SVMs) är en väl genomsökd klass med övervakade inlärnings metoder. Den här specifika implementeringen är anpassad till förutsägelse av två möjliga resultat, baserat på antingen kontinuerliga eller kategoriska variabler.

När du har definierat modell parametrarna tränar du modellen med hjälp av modulen utbildning och tillhandahåller en *Taggad data uppsättning* som innehåller en etikett eller en resultat kolumn.

## <a name="about-support-vector-machines"></a>Om support Vector-datorer

Support vektor datorer är bland de flesta av Machine Learning-algoritmer och SVM-modeller har använts i många program, från informations hämtning till text och bild klassificering. SVMs kan användas för både klassificerings-och Regressions uppgifter.

Den här SVM-modellen är en övervakad utbildnings modell som kräver etiketterade data. I övnings processen analyserar algoritmen indata och identifierar mönster i ett flerdimensionellt funktions utrymme som kallas för den här *planen*.  Alla exempel på indata visas som punkter i det här utrymmet och mappas till utdata-kategorier på ett sådant sätt att kategorierna är indelade som breda och rensar en lucka som är möjlig.

För förutsägelse tilldelar SVM-algoritmen nya exempel till en kategori eller en annan, och mappar dem till samma utrymme. 

## <a name="how-to-configure"></a>Så här konfigurerar du 

För den här modell typen rekommenderar vi att du normaliserar data uppsättningen innan du använder den för att träna klassificeraren.
  
1.  Lägg till modulen för **dubbelriktad Vector-** modul i din pipeline.  
  
2.  Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: om du vet hur du vill konfigurera modellen kan du ange en viss uppsättning värden som argument.  

3.  För **antal iterationer**anger du ett tal som anger antalet iterationer som används när modellen skapas.  
  
     Den här parametern kan användas för att styra kompromisser mellan inlärnings hastigheten och noggrannhet.  
  
4.  För **lambda**anger du ett värde som ska användas som vikt för L1-regulariseringshastigheten.  
  
     Den här regulariseringshastigheten-koefficienten kan användas för att finjustera modellen. Större värden bestraffa mer komplexa modeller.  
  
5.  Välj alternativet, **normalisera funktioner**om du vill normalisera funktioner innan du tränar.
  
     Om du använder normalisering före träning centreras data punkter på medelvärdet och skalas för att ha en enhet av standard avvikelsen.
  
6.  Välj alternativet och **projicera till enhets sfären**för att normalisera koefficienter.
  
     Projekt värden till enhets utrymme innebär att data punkter centreras på 0 och skalas för att ha en enhet av standard avvikelsen.
  
7.  Skriv ett heltals värde som ska användas som ett Seed-värde för att säkerställa reproducerbarhet i körningar i **slumptal**.  Annars används ett värde för system klockan som ett Seed, vilket kan resultera i något annorlunda resultat mellan körningar.
  
9. Anslut en etikettad data uppsättning och en av [modulerna för utbildning](module-reference.md):
  
    -   Om du ställer in **skapa utbildare** för **en parameter**använder du modulen [träna modell](train-model.md) .
  
10. Köra en pipeline.

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för poängsättning lägger du till modulen **Poäng modell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 