---
title: 'Ange data manuellt: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Ange data manuellt i Azure Machine Learning för att skapa en liten datauppsättning genom att skriva värden. Datauppsättningen kan ha flera kolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367524"
---
# <a name="enter-data-manually-module"></a>Ange modulen Data manuellt

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd modulen **Returdata manuellt** för att skapa en liten datauppsättning genom att skriva värden. Datauppsättningen kan ha flera kolumner.
  
Den här modulen kan vara till hjälp i scenarier som:  
  
- Generera en liten uppsättning värden för testning.  
- Skapa en kort lista med etiketter.  
- Skriva en lista med kolumnnamn som ska infogas i en datauppsättning.

## <a name="create-a-dataset"></a>Skapa en datamängd 
  
1. Lägg till modulen [Returdata manuellt](./enter-data-manually.md) i pipelinen. Du hittar den här modulen i kategorin **Datainmatning och utdata** i Azure Machine Learning. 
  
1. För **DataFormat**väljer du ett av följande alternativ. De här alternativen avgör hur de data som du anger ska tolkas. Kraven för varje format skiljer sig mycket åt, så se till att läsa relaterade ämnen.  
  
   - **ARFF**: Filformat för attributrelation som används av Weka.   
   - **CSV**: Comma-separerade värden format. Mer information finns i [Konvertera till CSV](./convert-to-csv.md).    
   - **SVMLight**: Format som används av Vowpal Wabbit och andra ramverk för maskininlärning.    
   - **TSV**: Format med tabbavgränsade värden.

   Om du väljer ett format och inte tillhandahåller data som uppfyller formatspecifikationerna uppstår ett körningsfel.
  
1. Klicka i textrutan **Data** för att börja mata in data. Följande format kräver särskild uppmärksamhet:  
  
   - **CSV**: Om du vill skapa flera kolumner klistrar du in i kommaavgränsad text eller skriver flera kolumner med hjälp av kommatecken mellan fälten.
  
     Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumnrubrik.  
  
     Om du avmarkerar det här alternativet används kolumnnamnen (Col1, Col2 och så vidare). Du kan lägga till eller ändra kolumnnamn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
   - **TSV**: Om du vill skapa flera kolumner klistrar du in i tabbavgränsad text eller skriver flera kolumner med hjälp av flikar mellan fält.  
  
     Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumnrubrik.  
  
     Om du avmarkerar det här alternativet används kolumnnamnen (Col1, Col2 och så vidare). Du kan lägga till eller ändra kolumnnamn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
   - **ARFF**: Klistra in i en befintlig ARFF-formatfil. Om du skriver värden direkt måste du lägga till de valfria rubrikerna och obligatoriska attributfält i början av data. 

     Följande rubrik- och attributrader kan till exempel läggas till i en enkel lista. Kolumnrubriken skulle `SampleText`vara . Observera att strängtypen inte stöds.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Skriv eller klistra in värden med hjälp av SVMLight-formatet.  
  
     Följande exempel representerar till exempel de första raderna i datauppsättningen blodgivning i SVMLight-format:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     När du kör modulen [Returdata manuellt](./enter-data-manually.md) konverteras dessa rader till en datauppsättning med kolumner och indexvärden enligt följande:  
  
     |Col1|Col2|Col3 (på andra)|Col4 (på andra)|Etiketter|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. Välj Retur-tangenten efter varje rad om du vill starta en ny rad.      
     
   Om du väljer Ange flera gånger om du vill lägga till flera tomma efterföljande rader tas de tomma raderna bort eller trimmas.  
  
   Om du skapar rader med saknade värden kan du alltid filtrera bort dem senare.  
  
1. Anslut utdataporten till andra moduler och kör pipelinen.  
  
   Om du vill visa datauppsättningen högerklickar du på modulen och väljer **Visualisera**.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 