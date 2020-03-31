---
title: 'Normalisera data: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Normalisera data i Azure Machine Learning för att omvandla en datauppsättning genom *normalisering*..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: a740c81aa165e221bca19987c7b3c62da56b0402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477535"
---
# <a name="normalize-data-module"></a>Normalisera datamodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att omvandla en datauppsättning genom *normalisering*.

Normalisering är en teknik som ofta används som en del av dataförberedelser för maskininlärning. Målet med normalisering är att ändra värdena för numeriska kolumner i datauppsättningen så att de använder en gemensam skala, utan att förvränga skillnaderna i värdeintervall eller förlora information. Normalisering krävs också för att vissa algoritmer ska kunna modellera data korrekt.

Anta till exempel att indatauppsättningen innehåller en kolumn med värden från 0 till 1 och en annan kolumn med värden från 10 000 till 100 000. Den stora skillnaden i *storleken* på talen kan orsaka problem när du försöker kombinera värdena som funktioner under modellering.

*Normalisering* undviker dessa problem genom att skapa nya värden som upprätthåller den allmänna fördelningen och nyckeltalen i källdata, samtidigt som värdena hålls inom en skala som tillämpas i alla numeriska kolumner som används i modellen.

Den här modulen erbjuder flera alternativ för att omvandla numeriska data:

- Du kan ändra alla värden till en skala 0–1 eller omvandla värdena genom att representera dem som percentilranking i stället för absoluta värden.
- Du kan använda normalisering på en enskild kolumn eller på flera kolumner i samma datauppsättning.
- Om du behöver upprepa pipelinen eller använda samma normaliseringssteg på andra data kan du spara stegen som en normaliseringstransformering och använda den på andra datauppsättningar som har samma schema.

> [!WARNING]
> Vissa algoritmer kräver att data normaliseras innan du tränar en modell. Andra algoritmer utför sin egen dataskalning eller normalisering. När du väljer en maskininlärningsalgoritm som du vill använda för att skapa en prediktiv modell ska du därför se till att granska datakraven för algoritmen innan du tillämpar normalisering på träningsdata.

##  <a name="configure-normalize-data"></a>Konfigurera normalisera data

Du kan bara använda en normaliseringsmetod i taget med den här modulen. Därför används samma normaliseringsmetod på alla kolumner som du väljer. Om du vill använda olika normaliseringsmetoder använder du en andra instans av **Normalisera data**.

1. Lägg till modulen **Normalisera data** i pipelinen. Du hittar modulen I Azure Machine Learning, under **Data Transformation**, i kategorin Skala **och minska.**

2. Anslut en datauppsättning som innehåller minst en kolumn med alla tal.

3. Använd kolumnväljaren för att välja de numeriska kolumnerna som ska normaliseras. Om du inte väljer enskilda kolumner inkluderas som standard **alla** numeriska typkolumner i indata och samma normaliseringsprocess tillämpas på alla markerade kolumner. 

    Detta kan leda till konstiga resultat om du inkluderar numeriska kolumner som inte bör normaliseras! Kontrollera alltid kolumnerna noggrant.

    Om inga numeriska kolumner identifieras kontrollerar du kolumnmetadata för att kontrollera att datatypen för kolumnen är en numerisk typ som stöds.

    > [!TIP]
    > Om du vill vara säkra på att kolumner av en viss typ tillhandahålls som indata kan du prova att använda modulen [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) innan **du normaliserar data**.

4. **Använd 0 för konstantkolumner när markerad:** Välj det här alternativet när en numerisk kolumn innehåller ett enda oföränderliga värde. Detta säkerställer att sådana kolumner inte används i normaliseringsåtgärder.

5. Välj en enda matematisk funktion som ska tillämpas på alla markerade kolumner i listrutan **Omformningsmetod.** 
  
    - **Zscore:** Konverterar alla värden till en z-poäng.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med z&#45;poäng](media/module/aml-normalization-z-score.png)
  
      Medel- och standardavvikelse beräknas för varje kolumn separat. Populationens standardavvikelse används.
  
    - **MinMax**: Min-max normalizer skalar linjärt om varje funktion till intervallet [0,1].
    
      Omskalning till intervallet [0,1] görs genom att flytta värdena för varje funktion så att det minimala värdet är 0 och sedan dividera med det nya maximala värdet (vilket är skillnaden mellan de ursprungliga maximalvärdena och minimala värden).
      
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med hjälp av min&#45;max-funktionen](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistik**: Värdena i kolumnen omvandlas med hjälp av följande formel:

      ![formel för normalisering genom logistisk funktion](media/module/aml-normalization-logistic.png "AML_normalization-logistik")  
  
    - **LogNormal**: Det här alternativet konverterar alla värden till en lognormal skala.
  
      Värdena i kolumnen omvandlas med hjälp av följande formel:
  
      ![formellogg&#45;normal fördelning](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Här μ och σ är parametrarna för fördelningen, beräknad empiriskt från data som högsta sannolikhetuppskattningar, för varje kolumn separat.  
  
    - **TanH**: Alla värden konverteras till en hyperbolisk tangent.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:
    
      ![normalisering med hjälp av tanh-funktionen](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Skicka pipelinen eller dubbelklicka på modulen **Normalisera data** och välj **Kör markerad**. 

## <a name="results"></a>Resultat

Modulen **Normalisera data** genererar två utdata:

- Om du vill visa de omformade värdena högerklickar du på modulen och väljer **Visualisera**.

    Som standard omvandlas värden på plats. Om du vill jämföra de omformade värdena med de ursprungliga värdena använder du modulen [Lägg till kolumner](./add-columns.md) för att kombinera datauppsättningarna igen och visa kolumnerna sida vid sida.

- Om du vill spara omformningen så att du kan använda samma normaliseringsmetod på en annan datauppsättning markerar du modulen och väljer **Registrera datauppsättning** under fliken **Utdata** på den högra panelen.

    Du kan sedan läsa in de sparade omformningar från gruppen Transformer i det vänstra **navigeringsfönstret** och använda den på en datauppsättning med samma schema med hjälp av [Använd omformning](apply-transformation.md).  


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 