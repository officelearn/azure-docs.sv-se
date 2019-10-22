---
title: 'Ange data manuellt: modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen ange data manuellt i Azure Machine Learning tjänst för att skapa en liten data uppsättning genom att ange värden. Data uppsättningen kan ha flera kolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: bd5fb636dbf79534e84a21a461f8912ceb6d9633
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693167"
---
# <a name="enter-data-manually-module"></a>Ange modulen data manuellt

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att skapa en liten data uppsättning genom att ange värden. Data uppsättningen kan ha flera kolumner.
  
Den här modulen kan vara till hjälp i scenarier som dessa:  
  
- Genererar en liten uppsättning värden för testning  
  
- Skapa en kort lista med etiketter
  
- Ange en lista med kolumn namn som ska infogas i en data uppsättning

## <a name="enter-data-manually"></a>Ange data manuellt 
  
1.  Lägg till modulen [ange data manuellt](./enter-data-manually.md) i din pipeline. Du hittar den här modulen i kategorin **data indata och utdata** i Azure Machine Learning. 
  
2.  Välj något av följande alternativ för **dataformat**. De här alternativen avgör hur de data som du anger ska tolkas. Kraven för varje format skiljer sig kraftigt, så se till att läsa närliggande ämnen.  
  
    -   **Arff**. Fil formatet för attribut-relation som används av Wekas.   
  
    -   **CSV**. Format med kommaavgränsade värden. Mer information finns i [konvertera till CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Ett format som används av Vowpal Wabbit och andra ramverk för maskin inlärning.  
  
    -   **TSV**. Format med Tabbavgränsade värden.

     Ett körnings fel uppstår om du väljer ett format och inte tillhandahåller data som uppfyller format kraven.
  
3.  Klicka i text rutan **data** för att börja ange data. Följande format kräver särskild uppmärksamhet:  
  
    - **CSV**: om du vill skapa flera kolumner, klistra in i kommaavgränsad text eller skriva flera kolumner med kommatecken mellan fälten.
  
        Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumn rubrik.  
  
        Om du avmarkerar det här alternativet används kolumn namnen, Col1, Col2 och så vidare. Du kan lägga till eller ändra kolumn namn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
    - **TSV**: om du vill skapa flera kolumner klistrar du in tabbavgränsad text eller anger flera kolumner med tabbar mellan fält.  
  
        Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumn rubrik.  
  
        Om du avmarkerar det här alternativet används kolumn namnen, Col1, Col2 och så vidare. Du kan lägga till eller ändra kolumn namn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
    -   **Arff**: klistra in i en befintlig arff-format fil. Om du skriver värden direkt ska du se till att lägga till de valfria rubrik fälten och de obligatoriska attributen i början av data. 
    
        Till exempel kan följande rader för sidhuvud och attribut läggas till i en enkel lista. Kolumn rubriken är `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Skriv eller klistra in värden med SVMLight-formatet.  
  
        Följande exempel representerar till exempel de första raderna i data uppsättningen blod donation i SVMight-format:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        När du kör modulen [ange data manuellt](./enter-data-manually.md) , konverteras dessa rader till en data uppsättning kolumner och index värden enligt följande:  
  
        |Col1|Col2|Col3|Col4|Etiketter|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Tryck på RETUR efter varje rad för att påbörja en ny rad.  
  
     **Se till att trycka på RETUR efter den sista raden.** 
     
     Om du trycker på RETUR flera gånger för att lägga till flera tomma efterföljande rader tas den sista tomma raden bort, men andra tomma rader behandlas som saknade värden.  
  
     Om du skapar rader med värden som saknas kan du alltid filtrera dem senare.  
  
5.  Högerklicka på modulen och välj **Kör valt** för att parsa data och läsa in dem i arbets ytan som en data uppsättning.  
  
     Om du vill visa data uppsättningen klickar du på utdataporten och väljer **visualisera**.  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 