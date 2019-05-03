---
title: 'Ange Data manuellt: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen ange Data manuellt i Azure Machine Learning-tjänsten för att skapa en liten datamängd genom att ange värden. Datauppsättningen kan ha flera kolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028612"
---
# <a name="enter-data-manually-module"></a>Ange Data manuellt modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att skapa en liten datamängd genom att ange värden. Datauppsättningen kan ha flera kolumner.
  
Den här modulen kan vara användbart i scenarier som följande:  
  
- Generera en liten uppsättning värden för testning  
  
- Skapa en kort lista med etiketter
  
- Att skriva en lista med kolumnnamn för att infoga i en datauppsättning

## <a name="enter-data-manually"></a>Ange Data manuellt 
  
1.  Lägg till den [ange Data manuellt](./enter-data-manually.md) modulen i experimentet. Du hittar den här modulen i den **Data indata och utdata** kategori i Azure Machine Learning. 
  
2.  För **DataFormat**, väljer du något av följande alternativ. Dessa alternativ anger hur data som du anger ska parsas. Kraven för varje format skilja sig åt avsevärt, så var noga med att läsa Närliggande ämnen.  
  
    -   **ARFF**. Attributrelation filformatet används av Weka.   
  
    -   **CSV**. Kommaavgränsade värden format. Mer information finns i [konvertera till CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Ett format som används av Vowpal Wabbit och andra ramverk för maskininlärning.  
  
    -   **TSV**. Tabbavgränsade värden format.

     Om du väljer ett format och ger inte data som uppfyller specifikationerna format, inträffar ett fel under körning.
  
3.  Klicka i den **Data** textrutan för att börja föra in data. Följande format kräver särskild uppmärksamhet:  
  
    - **CSV**:  Klistra in i CSV-text för att skapa flera kolumner, eller ange flera kolumner med hjälp av kommatecken mellan fält.
  
        Om du väljer den **HasHeader** alternativet, du kan använda den första raden med värden som på kolumnens rubrik.  
  
        Om du avmarkerar det här alternativet, kolumnnamnen, Kol1, Col2 och så vidare används. Du kan lägga till eller ändra kolumner namn senare med [redigera Metadata](./edit-metadata.md).  
  
    - **TSV**: Klistra in tabbavgränsad text för att skapa flera kolumner, eller ange flera kolumner med hjälp av flikarna mellan fält.  
  
        Om du väljer den **HasHeader** alternativet, du kan använda den första raden med värden som på kolumnens rubrik.  
  
        Om du avmarkerar det här alternativet, kolumnnamnen, Kol1, Col2 och så vidare används. Du kan lägga till eller ändra kolumner namn senare med [redigera Metadata](./edit-metadata.md).  
  
    -   **ARFF**:  Klistra in en befintlig ARFF-fil. Om du skriver värden direkt, måste du lägga till valfria rubriken och obligatoriska attributfält i början av data. 
    
        Till exempel kunde följande rubrik och attributet rader läggas till en enkel lista. Kolumnrubriken skulle vara `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Skriv eller klistra in värden med hjälp av SVMLight-format.  
  
        I följande exempel representerar till exempel de första par raderna med blodgivning datauppsättningen i SVMight format:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        När du kör den [ange Data manuellt](./enter-data-manually.md) modulen, dessa rader konverteras till en datauppsättning med kolumner och index värden enligt följande:  
  
        |Kol1|Col2|Col3|Col4|Etiketter|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Tryck på RETUR efter varje rad att starta en ny rad.  
  
     **Var noga med att trycka på RETUR efter den sista raden.** 
     
     Om du trycker på RETUR flera gånger för att lägga till flera tomma efterföljande rader, den slutliga tomma raden tas bort trimmade, men andra tomma rader behandlas som värden som saknas.  
  
     Om du skapar rader med värden som saknas kan filtrera du alltid dem senare.  
  
5.  Högerklicka på modulen och välj **kör valda** parsa data och läsa in den i din arbetsyta som en datauppsättning.  
  
     Om du vill visa datauppsättningen, klicka på utdataporten och välj **visualisera**.  
## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 