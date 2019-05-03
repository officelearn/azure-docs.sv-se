---
title: 'Normalisera Data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen normalisera Data i Azure Machine Learning-tjänsten för att omvandla en datauppsättning via *normalisering*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029452"
---
# <a name="normalize-data-module"></a>Normalisera datamodulen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att transformera en datauppsättning via *normalisering*.

Normalisering är en teknik som används ofta som en del av förberedelse av data för machine learning. Målet med normalisering är att ändra värdena för numeriska kolumner i datauppsättningen för att använda en gemensam skala utan snedvridande skillnader i intervallen för värden eller att förlora information. Normalisering krävs också för vissa algoritmer för att modellera data korrekt.

Anta exempelvis att ditt indatauppsättningen innehåller en kolumn med värden mellan 0 och 1 och en annan kolumn med värden som sträcker sig från 10 000 till 100 000. Bra skillnad i den *skala* för tal kan orsaka problem när du försöker kombinera värdena som funktioner under modellering.

*Normalisering* undviker de här problemen genom att skapa nya värden som upprätthåller allmän distribution och förhållanden i källdata, samtidigt som värden i en skala som tillämpas på alla numeriska kolumner som används i modellen.

Den här modulen erbjuder flera alternativ för hur du transformerar numeriska data:

- Du kan ändra alla värden till 0 – 1 skala eller transformerar värdena som dem som: e percentilen rankar i stället för absoluta värden.
- Du kan använda normalisering på en eller flera kolumner i samma datauppsättning.
- Om du vill upprepa experimentet eller använda samma normalisering steg för andra data kan du spara steg som en normalisering transformering och tillämpa den på andra datauppsättningar som har samma schema.

> [!WARNING]
> Vissa algoritmer kräver att data anges normaliseras innan träna en modell. Andra algoritmer utföra sina egna data skalning eller normalisering. När du väljer en machine learning-algoritm som ska användas för att skapa en förutsägbar modell, bör du granska datakrav av algoritmen innan du använder normalisering på träningsdata.

##  <a name="configure-normalize-data"></a>Konfigurera normalisera Data

Du kan använda bara en normalisering av metoderna i taget med den här modulen. Därför används samma normalisering metod på alla kolumner som du väljer. Om du vill använda olika normalisering metoder, använder du en andra instans av **normalisera Data**.

1. Lägg till den **normalisera Data** modulen i experimentet. Du kan hitta modulen i Azure Machine Learning, under **Dataomvandling**i den **skalbarhet och minska** kategori.

2. Ansluta en datauppsättning som innehåller minst en kolumn med tal.

3. Använd kolumnväljaren för att välja de numeriska kolumnerna att normalisera. Om du inte väljer enskilda kolumner, som standard **alla** numerisk typkolumner i inkommande ingår och samma normaliseringsprocessen tillämpas på alla valda kolumner. 

    Detta kan leda till onormalt resultat om du lägger till numeriska kolumner som inte bör anges normaliseras! Kontrollera alltid kolumnerna noggrant.

    Om inga numeriska kolumner identifieras måste du kontrollera kolumnmetadata för att kontrollera att datatypen för kolumnen är en numerisk typ som stöds.

    > [!TIP]
    > För att säkerställa att kolumnerna i en viss typ har angetts som indata, försök att använda den [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) modulen innan **normalisera Data**.

4. **Använd 0 för konstant kolumner när alternativet är markerat**:  Välj det här alternativet när en numerisk kolumn innehåller ett fast värde. Detta säkerställer att sådana kolumner inte används i normalisering åtgärder.

5. Från den **omvandling metoden** listrutan väljer du en enda matematisk funktion ska tillämpas på alla valda kolumner. 
  
    - **Zscore**: Konverterar alla värden till ett z-värde.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med z&#45;poäng](media/module/aml-normalization-z-score.png)
  
      Mean- och standard avvikelse beräknas separat för varje kolumn. Populationens standardavvikelse används.
  
    - **MinMax**: Min – max normaliserare ändrar linjärt skalan alla funktioner till intervallet [0,1].
    
      Skalas om till intervallet [0,1] görs genom att ändra värdena för varje funktion så att det minimala värdet är 0 och sedan dividera med det nya värdet för maximal (vilket är skillnaden mellan de ursprungliga värdena för maximal och minimal).
      
      Värdena i kolumnen omvandlas med hjälp av följande formel:  
  
      ![normalisering med det minsta värdet&#45;max funktionen](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logistic**: Värdena i kolumnen omvandlas med hjälp av följande formel:

      ![formeln för normalisering av logistic funktion](media/module/aml-normalization-logistic.png "AML_normalization logistic")  
  
    - **LogNormal**: Det här alternativet konverterar alla värden till en lognormala skala.
  
      Värdena i kolumnen omvandlas med hjälp av följande formel:
  
      ![formeln log&#45;normalfördelning](media/module/aml-normalization-lognormal.png "AML_normalization lognormala")
    
      Här följer μg och σmax parametrarna för distribution, beräknad empiriskt från data som maximalt sannolikheten beräkningar för varje kolumn separat.  
  
    - **TanH**: Alla värden som konverteras till en hyperboliska tangens.
    
      Värdena i kolumnen omvandlas med hjälp av följande formel:
    
      ![normalisering med hjälp av funktionen tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Kör experimentet eller dubbelklicka på den **normalisera Data** modul och välj **kör valda**. 

## <a name="results"></a>Resultat

Den **normalisera Data** modulen genererar två utdata:

- Om du vill visa transformerade värden, högerklicka på modulen, Välj **omvandlade datauppsättningen**, och klicka på **visualisera**.

    Som standard omvandlas värden på plats. Om du vill jämföra transformerade värden till de ursprungliga värdena kan du använda den [Lägg till kolumner](./add-columns.md) modulen att kombinera datauppsättningarna och visa kolumner sida vid sida.

- Om du vill spara omvandlingen så att du kan använda samma metod som normalisering för en annan liknande datauppsättningar, högerklicka på modulen, Välj **Transformation-funktion**, och klicka på **Spara som transformeringen**.

    Du kan sedan läsa in sparade omvandlingar från den **omvandlar** i det vänstra navigeringsfönstret och tillämpa den på en datauppsättning med samma schema med hjälp av [. / gäller omvandling](apply-transformation.md).  


## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 