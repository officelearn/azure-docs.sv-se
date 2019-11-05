---
title: 'Neurala nätverks regression: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder neurala Network regression-modulen i Azure Machine Learning för att skapa en Regressions modell med en anpassningsbar neurala-nätverks algoritm.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d5cc0f06e9fb95894df30af1322d47337f51e314
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466004"
---
# <a name="neural-network-regression-module"></a>Neurala Network regression-modul

*Skapar en Regressions modell med en neurala*  
  
 Kategori: Machine Learning/initiera modell/regression
  
## <a name="module-overview"></a>Översikt över modul  

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att skapa en Regressions modell med en anpassningsbar neurala-nätverks algoritm.
  
 Även om neurala-nätverk är allmänt kända för användning i djup inlärning och modellering av komplexa problem, till exempel bild igenkänning, är de lätt att anpassa till Regressions problem. En klass med statistiska modeller kan kallas ett neurala nätverk om de använder anpassningsbara vikter och kan uppskatta icke-linjära funktioner i sina indata. Därför är neurala Network regression lämplig för problem där en mer traditionell Regressions modell inte kan passa en lösning.
  
 Neurala Network regression är en övervakad inlärnings metod och kräver därför en *Taggad data uppsättning*som innehåller en etikett kolumn. Eftersom en Regressions modell förutsäger ett numeriskt värde måste etikett kolumnen vara en numerisk datatyp.  
  
 Du kan träna modellen genom att tillhandahålla modellen och den taggade data uppsättningen som indata för att [träna modellen](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya ingångs exemplen.  
  
## <a name="configure-neural-network-regression"></a>Konfigurera neurala Network regression 

Neurala-nätverk kan anpassas i stor utsträckning. I det här avsnittet beskrivs hur du skapar en modell med hjälp av två metoder:
  
+ [Skapa en neurala-nätverks modell med standard arkitekturen](#bkmk_DefaultArchitecture)  
  
    Om du godkänner standard nätverks arkitekturen för neurala kan du använda fönstret **Egenskaper** för att ange parametrar som styr beteendet för neurala-nätverket, till exempel antalet noder i det dolda lagret, inlärnings takt och normalisering.

    Börja här om du är nybörjare på neurala-nätverk. Modulen stöder många anpassningar, samt modell justering, utan djupgående kunskaper om neurala-nätverk. 

+ Definiera en anpassad arkitektur för ett neurala-nätverk 

    Använd det här alternativet om du vill lägga till extra dolda lager eller helt anpassa nätverks arkitekturen, dess anslutningar och aktiverings funktioner.
    
    Det här alternativet är bäst om du redan är bekant med neurala-nätverk. Du använder språket net # för att definiera nätverks arkitekturen.  

##  <a name="bkmk_DefaultArchitecture"></a>Skapa en neurala-nätverks modell med standard arkitekturen

1.  Lägg till **neurala Network regression** -modulen i din pipeline i designern. Du kan hitta den här modulen under **Machine Learning**, **initiera**i **Regressions** kategorin. 
  
2. Ange hur du vill att modellen ska tränas genom att ställa in alternativet **skapa utbildare läge** .  
  
    -   **Enda parameter**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.  

3.  I **specifikationen för dolt lager**väljer du **fullständigt anslutet ärende**. Med det här alternativet skapas en modell som använder standard nätverks arkitekturen neurala, som för en neurala-nätverks Regressions modell, som har följande attribut:  
  
    + Nätverket har exakt ett dolt lager.
    + Utmatnings lagret är fullständigt anslutet till det dolda lagret och det dolda lagret är fullständigt anslutet till inmatnings lagret.
    + Antalet noder i det dolda lagret kan ställas in av användaren (Standardvärdet är 100).  
  
    Eftersom antalet noder i inmatnings lagret bestäms av antalet funktioner i tränings data, kan det i en Regressions modell bara finnas en nod i output-lagret.  
  
4. Ange antalet dolda noder för **antalet dolda noder**. Standardvärdet är ett dolt lager med 100 noder. (Det här alternativet är inte tillgängligt om du definierar en anpassad arkitektur med net #.)
  
5.  För **inlärnings hastighet**anger du ett värde som definierar det steg som tas vid varje iteration, före korrigeringen. Ett större värde för inlärnings frekvensen kan göra att modellen konvergerar snabbare, men den kan överskotta lokala minimi.

6.  I **antal inlärnings iterationer**anger du det maximala antalet gånger som algoritmen bearbetar inlärnings fallen.

7.  För * * den inledande frekvensen för inlärnings viktningen anger du ett värde som avgör den aktuella nodens vikt i början av inlärnings processen.

8.  Ange ett **värde som ska**användas under inlärningen som en vikt på noder från föregående iterationer.

10. Välj alternativet, **blanda exempel**för att ändra ordningen på fall mellan iterationer. Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.
  
11. Du kan ange ett värde som ska användas som startvärdet för det **slumpmässiga antalet frön**. Att ange ett Seed-värde är användbart när du vill säkerställa repeterbarhet i flera körningar av samma pipeline.
  
13. Anslut en data uppsättning för utbildning och en av [inlärnings modulerna](module-reference.md): 
  
    -   Om du ställer in **skapa utbildare** till **en parameter**använder du [träna modell](./train-model.md).  
  
   
14. Köra en pipeline.  

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill se en översikt över modellens parametrar, tillsammans med funktions vikterna från träningen och andra parametrar för neurala-nätverket, högerklickar du på utdata från [träna modell](./train-model.md)och väljer **visualisera**.  

+ Om du vill spara en ögonblicks bild av den tränade modellen högerklickar du på den **tränade modellens** utdata och väljer **Spara som utbildad modell**. Den här modellen uppdateras inte vid efterföljande körningar av samma pipeline.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 