---
title: 'Regression neuralt nätverk: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Neurala nätverk Regression i Azure Machine Learning-tjänsten för att skapa en regressionsmodell som använder en algoritm för anpassningsbara neuralt nätverk...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029317"
---
# <a name="neural-network-regression-module"></a>Neurala nätverk Regression modul

*Skapar en regressionsmodell som använder en algoritm för neurala nätverk*  
  
 Kategori: Machine Learning / initiera modell / Regression
  
## <a name="module-overview"></a>Modul-översikt  

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en regressionsmodell som använder en algoritm för anpassningsbara neuralt nätverk.
  
 Även om neurala nätverk är välkända för djupinlärning och modellering av komplexa problem, till exempel bildigenkänning, är de enkelt anpassade regression problem. Alla klasser av statistiska modeller kan kallas ett neurala nätverk om de använder anpassningsbar vikterna och kan göra en uppskattning av icke-linjära funktioner i sina indata. Neuralt nätverk regression är därför lämpligt problem där en mer traditionell regressionsmodell inte får plats i en lösning.
  
 Neuralt nätverk regression är en metod för övervakad inlärning och därför kräver ett *taggade datauppsättning*, som innehåller en kolumn för etiketten. Eftersom en regressionsmodell som förutsäger ett numeriskt värde vara en numerisk datatyp i etikettkolumnen.  
  
 Du kan träna modellen genom att ange modellen och taggade datauppsättningen som indata till [träna modell](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya inkommande exempel.  
  
## <a name="configure-neural-network-regression"></a>Konfigurera Neuralt nätverk Regression 

Neurala nätverk kan anpassas stor utsträckning. Det här avsnittet beskriver hur du skapar en modell med två metoder:
  
+ [Skapa en modell för neurala nätverk med hjälp av standard-arkitekturen](#bkmk_DefaultArchitecture)  
  
    Om du godkänner standard neuralt nätverksarkitektur, använda den **egenskaper** fönstret för att ange parametrar som styr beteendet för neurala nätverk, till exempel antalet noder i det dolda lagret och inlärningsfrekvensen normalisering.

    Börja här om du är nybörjare på neurala nätverk. Modulen har stöd för många anpassningar, samt modellen justering utan djupa kunskaper om neurala nätverk. 

+ Definiera en anpassad arkitektur för ett neurala nätverk 

    Använd det här alternativet om du vill lägga till extra dolda lager eller helt anpassa nätverksfunktioner arkitektur, dess anslutningar och aktivering.
    
    Det här alternativet är bäst om du redan är något van neurala nätverk. Du kan använda Net #-språket för att definiera nätverksarkitekturen.  

##  <a name="bkmk_DefaultArchitecture"></a> Skapa en modell för neurala nätverk med hjälp av standard-arkitekturen
  
1.  Lägg till den **Neurala nätverk Regression** modulen i experimentet i gränssnittet. Du hittar den här modulen under **Maskininlärning**, **initiera**i den **Regression** kategori. 
  
2. Visa hur du vill att modellen ska tränas genom att ange den **skapande trainer läge** alternativet.  
  
    -   **Enkel parametern**: Välj det här alternativet om du redan vet hur du vill konfigurera modellen.  

3.  I **dolda lager-specifikationen**väljer **anslutas fullständigt fallet**. Skapar en modell med neuralt nätverksarkitektur, som för en regressionsmodell för neuralt nätverk har dessa attribut:  
  
    + Nätverket har exakt ett dolt lager.
    + Utdata-lagret är anslutet till det dolda lagret och det dolda lagret anslutas fullständigt till inkommande lagret.
    + Antalet noder i det dolda lagret kan ställas in av användaren (standardvärdet är 100).  
  
    Eftersom antalet noder i det inkommande lagret bestäms av hur många av funktionerna i träningsdata, i en regressionsmodell kan det finnas endast en nod i utdata-lagret.  
  
4. För **antalet dolda noder**, anger du antalet dolda noder. Standardvärdet är ett dolt lager med 100 noder. (Det här alternativet är inte tillgängligt om du definierar en anpassad arkitektur med hjälp av Net #.)
  
5.  För **inlärningsfrekvensen**, ange ett värde som definierar de steg som togs vid varje iteration innan korrigering. Ett större värde för learning hastighet kan orsaka modellen att Konvergera snabbare, men det kan överskridande lokala minimi.

6.  För **antalet learning iterationer**, ange det maximala antalet gånger som algoritmen som bearbetar träningsfall.

7.  För ** inledande learning viktas diameter, ange ett värde som avgör vikterna nod i början av processen learning.

8.  För **dynamiska**, ange ett värde som ska användas under learning som en vikt på noder från föregående iterationer.

10. Välj alternativet **blanda exempel**, för att ändra ordningen på fall mellan iterationer. Om du avmarkerar det här alternativet kan bearbetas fall i exakt samma ordning varje gång du kör experimentet.
  
11. För **nummer slumptal**, kan du ange ett värde som ska användas som startvärde. Ange ett startvärde är värdet användbart när du vill säkerställa repeterbarhet över körningar av samma experimentet.
  
13. Ansluta en datauppsättning för träning och en av de [utbildningsmoduler](module-reference.md): 
  
    -   Om du ställer in **skapande trainer läge** till **enda Parameter**, använda [Träningsmodell](./train-model.md).  
  
   
14. Kör experimentet.  

## <a name="results"></a>Resultat

När utbildning har slutförts:

+ För att visa en sammanfattning av modellens parametrar, tillsammans med funktionen vikterna som registrerats från utbildnings- och andra parametrar för det neurala nätverket, högerklicka på utdata från [Träningsmodell](./train-model.md), och välj **visualisera**.  

+ Spara en ögonblicksbild av den tränade modellen genom att högerklicka på den **Trained model** utdata och välj **Spara som Trained Model**. Den här modellen uppdateras inte på efterföljande körningar av samma experimentet.


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 