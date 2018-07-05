---
title: 'Azure Analysis Services självstudiekurs 6: Skapa mått | Microsoft Docs'
description: Beskriver hur du skapar mått i Azure Analysis Services-självstudieprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6cf880cf132168d60ff49c2e7ea571cd8e89d36f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446664"
---
# <a name="create-measures"></a>Skapa mått

Under den här lektionen skapar du mått som ingår i din modell. Precis som de beräknade kolumnerna som du skapat är ett mått en beräkning som skapats med hjälp av en DAX-formel. Men till skillnad från beräknade kolumner utvärderas mått baserat på ett *filter* som en användare har valt. Ett exempel är en viss kolumn eller ett utsnitt som lagts till i fältet radetiketter i en pivottabell. Ett värde för varje cell i filtret beräknas sedan med det tillämpade måttet. Mått är kraftfulla och flexibla beräkningar som du vill ha med i nästan alla tabellmodeller för att utföra dynamiska beräkningar på numeriska data. Läs mer i [Mått](https://docs.microsoft.com/sql/analysis-services/tabular-models/measures-ssas-tabular).
  
För att skapa mått använder du *rutnätet för mått*. Varje tabell har som standard ett tomt rutnät för mått, men vanligtvis skapar du inte mått för varje tabell. Rutnätet för mått visas under en tabell i modelldesignern i datavyn. För att dölja eller visa rutnätet för mått för en tabell klickar du på menyn **Tabell** och sedan på **Visa rutnät för mått**.  
  
Du kan skapa ett mått genom att klicka på en tom cell i rutnätet för mått och sedan ange en DAX-formel i formelfältet. När du klickar på RETUR för att slutföra formeln visas måttet i cellen. Du kan även skapa mått med hjälp av en vanlig sammansatt funktion genom att klicka på en kolumn och sedan på knappen Autosumma (**∑**) i verktygsfältet. Mått som skapats med hjälp av funktionen Autosumma visas i rutnätscellen för mått direkt under kolumnen, men de kan flyttas.  
  
Under den här lektionen skapar du mått både genom att ange en DAX-formel i formelfältet och genom att använda funktionen Autosumma.  
  
Uppskattad tidsåtgång för den här lektionen: **30 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 5: Skapa beräknade kolumner](../tutorials/aas-lesson-5-create-calculated-columns.md).  
  
## <a name="create-measures"></a>Skapa mått  
  
#### <a name="to-create-a-dayscurrentquartertodate-measure-in-the-dimdate-table"></a>Så här skapar du ett DaysCurrentQuarterToDate-mått i DimDate-tabellen  
  
1.  Klicka på tabellen **DimDate** i modelldesignern.  
  
2.  Klicka på den övre vänstra tomma cellen i rutnätet för mått.  
  
3.  I formelfältet anger du följande formel:  
  
    ```
    DaysCurrentQuarterToDate:=COUNTROWS( DATESQTD( 'DimDate'[Date])) 
    ```
  
    Observera att den övre vänstra cellen nu innehåller ett måttnamn, **DaysCurrentQuarterToDate**, följt av resultatet **92**. Resultatet är inte relevant just nu eftersom inget filter har tillämpats.
    
      ![aas-lesson6-newmeasure](../tutorials/media/aas-lesson6-newmeasure.png) 
    
    Till skillnad från beräknade kolumner kan du med måttformler ange måttnamnet följt av ett kolon och sedan följt av formeluttrycket.

  
#### <a name="to-create-a-daysincurrentquarter-measure-in-the-dimdate-table"></a>Så här skapar du ett DaysInCurrentQuarter-mått i DimDate-tabellen  
  
1.  När **DimDate**-tabellen fortfarande är aktiv i modelldesignern klickar du på den tomma cellen under det mått som du skapade i rutnätet för mått.  
  
2.  I formelfältet anger du följande formel:  
  
    ```
    DaysInCurrentQuarter:=COUNTROWS( DATESBETWEEN( 'DimDate'[Date], STARTOFQUARTER( LASTDATE('DimDate'[Date])), ENDOFQUARTER('DimDate'[Date])))
    ```
  
    När du skapar ett jämförelseförhållande mellan en ofullständig period och den föregående perioden. Formeln måste beräkna andelen av perioden som har förflutit och jämföra den med samma andel i den föregående period. I detta fall ger [DaysCurrentQuarterToDate]/[DaysInCurrentQuarter] andelen förfluten tid i den aktuella perioden.  
  
#### <a name="to-create-an-internetdistinctcountsalesorder-measure-in-the-factinternetsales-table"></a>Så här skapar du ett InternetDistinctCountSalesOrder-mått i tabellen FactInternetSales  
  
1.  Klicka på tabellen **FactInternetSales**.   
  
2.  Klicka på kolumnrubriken **SalesOrderNumber**.  
  
3.  I verktygsfältet klickar du på nedpilen bredvid knappen Autosumma (**∑**) och väljer sedan **DistinctCount**.  
  
    Funktionen Autosumma skapar automatiskt ett mått för den markerade kolumnen med det vanliga sammansatta måttet DistinctCount.  
    
       ![aas-lesson6-newmeasure2](../tutorials/media/aas-lesson6-newmeasure2.png)
  
4.  Klicka på det nya måttet i rutnätet för mått och klicka sedan på fönstret **Egenskaper** och byt namn på måttet i **Måttnamn** till **InternetDistinctCountSalesOrder**. 
 
  
#### <a name="to-create-additional-measures-in-the-factinternetsales-table"></a>Skapa ytterligare mått i tabellen FactInternetSales  
  
1.  Skapa och namnge följande åtgärder med hjälp av funktionen Autosumma:  

    |Kolumn|Måttnamn|Autosumma (∑)|Formel|  
    |----------------|----------|-----------------|-----------|  
    |SalesOrderLineNumber|InternetOrderLinesCount|Antal|=COUNTA([SalesOrderLineNumber])|  
    |OrderQuantity|InternetTotalUnits|Summa|=SUM([OrderQuantity])|  
    |DiscountAmount|InternetTotalDiscountAmount|Summa|=SUM([DiscountAmount])|  
    |TotalProductCost|InternetTotalProductCost|Summa|=SUM([TotalProductCost])|  
    |SalesAmount|InternetTotalSales|Summa|=SUM([SalesAmount])|  
    |Marginal|InternetTotalMargin|Summa|=SUM([Margin])|  
    |TaxAmt|InternetTotalTaxAmt|Summa|=SUM([TaxAmt])|  
    |Frakt|InternetTotalFreight|Summa|=SUM([Freight])|  
  
2.  Genom att klicka på en tom cell i rutnätet för mått och sedan använda formelfältet skapar du följande anpassade mått i den här ordningen:  
  
      ```
      InternetPreviousQuarterMargin:=CALCULATE([InternetTotalMargin],PREVIOUSQUARTER('DimDate'[Date]))
      ```
      
      ```
      InternetCurrentQuarterMargin:=TOTALQTD([InternetTotalMargin],'DimDate'[Date])
      ```
  
      ```
      InternetPreviousQuarterMarginProportionToQTD:=[InternetPreviousQuarterMargin]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
      ```
      InternetPreviousQuarterSales:=CALCULATE([InternetTotalSales],PREVIOUSQUARTER('DimDate'[Date]))
      ```
  
      ```
      InternetCurrentQuarterSales:=TOTALQTD([InternetTotalSales],'DimDate'[Date])
      ```
      
      ```
      InternetPreviousQuarterSalesProportionToQTD:=[InternetPreviousQuarterSales]*([DaysCurrentQuarterToDate]/[DaysInCurrentQuarter])
      ```
  
Mått som skapats för tabellen FactInternetSales kan användas för att analysera viktiga ekonomiska data som försäljning, kostnader och vinstmarginaler för objekt som definieras av filtret som valts av användaren.  
  
## <a name="whats-next"></a>Nästa steg
[Lektion 7: Skapa KPI:er](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  

  
