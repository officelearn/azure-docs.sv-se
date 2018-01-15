---
title: "Azure Analysis Services självstudiekurs 7: Skapa KPI:er | Microsoft Docs"
description: "Beskriver hur du skapar KPI:er i Azure Analysis Services-självstudieprojektet."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/08/2018
ms.author: owend
ms.openlocfilehash: 8f25773541deba2a94d3adc0c9b61c1b90a90aa6
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="create-key-performance-indicators"></a>Skapa KPI:er

I den här lektionen skapar du KPI:er. KPI: er används för att mäta prestanda för ett värde som definieras av ett *basmått* mot ett *målvärde* som också definieras av ett mått eller ett absolut värde. I rapporteringsklienter kan KPI:er ge företagsanvändare ett snabbt och enkelt sätt att få en översikt över hur det går för verksamheten eller identifiera trender. Mer information finns i [KPI:er](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)
  
Uppskattad tidsåtgång för den här lektionen: **15 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna i den här lektionen måste du ha slutfört den föregående lektionen: [Lektion 6: Skapa mått](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Skapa KPI:er  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Så här skapar du en InternetCurrentQuarterSalesPerformance-KPI  
  
1.  Klicka på tabellen **FactInternetSales** i modelldesignern.  
  
2.  Klicka på en tom cell i rutnätet för mått.  
  
3.  Ange följande formel i formelfältet ovanför tabellen: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Det här måttet fungerar som basmått för KPI.  
  
4.  I rutnätet för mått högerklickar du på **InternetCurrentQuarterSalesPerformance** > **Skapa KPI**.   
  
5.  Välj **Absolut värde** i **Mål** i dialogrutan KPI (Key Performance Indicator) och skriv sedan **1.1**.  
  
7.  Skriv **1** i skjutreglagefältet till vänster (nedre) och skriv sedan **1.07** i det högra skjutreglagefältet (övre).  
  
8.  I **Välj ikonformat** väljer du ikontypen romb (röd), triangel (gul), cirkel (grön).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Observera den expanderbara etiketten **Beskrivningar** nedanför tillgängliga ikonformat. Du kan ange beskrivningar för olika KPI-element så att de blir enklare att identifiera i klientprogram.  
  
9. Klicka på **OK** för att slutföra KPI:n.  
  
    Observera ikonen bredvid måttet **InternetCurrentQuarterSalesPerformance** i rutnätet för mått. Den här ikonen anger att det här måttet är basvärdet för en KPI.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Så här skapar du en InternetCurrentQuarterMarginPerformance-KPI  
  
1.  Klicka på en tom cell i rutnätet för mått för tabellen **FactInternetSales**.  
  
2.  Ange följande formel i formelfältet ovanför tabellen:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Högerklicka på **InternetCurrentQuarterMarginPerformance** > **Skapa KPI**.  
  
4.  Välj **Absolut värde** i **Mål** i dialogrutan KPI (Key Performance Indicator) och skriv sedan **1.25**.   
  
5.  Dra i skjutreglaget till vänster (nedre) tills det står **0.8** i fältet och dra sedan i det högra skjutreglaget (övre) tills det står **1.03** i fältet.  
  
6.  I **Välj ikonformat** väljer du ikontypen romb (röd), triangel (gul), cirkel (grön). Klicka sedan på **OK**.  
  
## <a name="whats-next"></a>Nästa steg
[Lektion 8: Skapa perspektiv](../tutorials/aas-lesson-8-create-perspectives.md).
  
  
