---
title: 'Neural Network Regression: Modul Referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Neural Network Regression i Azure Machine Learning för att skapa en regressionsmodell med hjälp av en anpassningsbar algoritm för neurala nätverk..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456106"
---
# <a name="neural-network-regression-module"></a>Neural Network Regression modul

*Skapar en regressionsmodell med hjälp av en algoritm för neuralt nätverk*  
  
 Kategori: Maskininlärning / Initiera modell / Regression
  
## <a name="module-overview"></a>Modul översikt  

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en regressionsmodell med hjälp av en anpassningsbar algoritm för neurala nätverk.
  
 Även neurala nätverk är allmänt kända för användning i djupinlärning och modellering komplexa problem såsom bildigenkänning, de är lätt anpassade till regression problem. Varje klass av statistiska modeller kan kallas ett neuralt nätverk om de använder adaptiva vikter och kan approximera icke-linjära funktioner av sina ingångar. Således neurala nätverk regression är lämplig för problem där en mer traditionell regressionsmodell inte kan passa en lösning.
  
 Neural nätverksregression är en övervakad inlärningsmetod och kräver därför en *taggad datauppsättning*, som innehåller en etikettkolumn. Eftersom en regressionsmodell förutsäger ett numeriskt värde måste etikettkolumnen vara en numerisk datatyp.  
  
 Du kan träna modellen genom att tillhandahålla modellen och den taggade datauppsättningen som en indata till [Train Model](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya indataexemplen.  
  
## <a name="configure-neural-network-regression"></a>Konfigurera regression av neurala nätverk 

Neurala nätverk kan anpassas i stor utsträckning. I det här avsnittet beskrivs hur du skapar en modell med två metoder:
  
+ [Skapa en neural nätverksmodell med standardarkitekturen](#bkmk_DefaultArchitecture)  
  
    Om du accepterar standardarkitekturen för neurala nätverk använder du fönstret **Egenskaper** för att ange parametrar som styr beteendet för det neurala nätverket, till exempel antalet noder i det dolda lagret, inlärningshastigheten och normaliseringen.

    Börja här om du är ny på neurala nätverk. Modulen stöder många anpassningar, samt modelljustering, utan djup kunskap om neurala nätverk. 

+ Definiera en anpassad arkitektur för ett neuralt nätverk 

    Använd det här alternativet om du vill lägga till extra dolda lager eller helt anpassa nätverksarkitekturen, dess anslutningar och aktiveringsfunktioner.
    
    Detta alternativ är bäst om du redan är något bekant med neurala nätverk. Du använder språket Net# för att definiera nätverksarkitekturen.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Skapa en neural nätverksmodell med standardarkitekturen

1.  Lägg till modulen **Neural Network Regression** i pipelinen i designern. Du hittar den här modulen under **Machine Learning**, **Initialize**, i kategorin **Regression.** 
  
2. Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    -   **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.   

3.  I **Dold lagerspecifikation**väljer du **Fullständigt anslutet fodral**. Det här alternativet skapar en modell med hjälp av standardkaritala nätverksarkitektur, som för en neural nätverksregressionsmodell har dessa attribut:  
  
    + Nätverket har exakt ett dolt lager.
    + Utdatalagret är helt anslutet till det dolda lagret och det dolda lagret är helt anslutet till inmatningslagret.
    + Antalet noder i det dolda lagret kan anges av användaren (standardvärdet är 100).  
  
    Eftersom antalet noder i indatalagret bestäms av antalet funktioner i träningsdata, kan det i en regressionsmodell bara finnas en nod i utdatalagret.  
  
4. För **Antal dolda noder**anger du antalet dolda noder. Standardär ett dolt lager med 100 noder. (Det här alternativet är inte tillgängligt om du definierar en anpassad arkitektur med Net#.)
  
5.  För **utbildningsfrekvens**skriver du ett värde som definierar det steg som tas vid varje iteration före korrigering. Ett större värde för inlärningshastighet kan orsaka att modellen konvergerar snabbare, men den kan överskrida lokala minima.

6.  För **Antal inlärningsiterationer**anger du det maximala antalet gånger algoritmen bearbetar utbildningsärenden.


8.  För **Momentum**skriver du ett värde som ska tillämpas under inlärning som vikt på noder från tidigare iterationer.

10. Välj alternativet **Blanda exempel**om du vill ändra ordningen på ärenden mellan iterationer. Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.
  
11. För **slumptalsfrö**kan du eventuellt skriva ett värde som ska användas som frö. Det är användbart att ange ett frövärde när du vill säkerställa repeterbarhet över körningar av samma pipeline.
  
13. Anslut en träningsdatauppsättning och en av [utbildningsmodulerna:](module-reference.md) 
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du Train [Model](./train-model.md).  
  
   
14. Skicka pipelinen.  

## <a name="results"></a>Resultat

Efter träningen är klar:

- Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 