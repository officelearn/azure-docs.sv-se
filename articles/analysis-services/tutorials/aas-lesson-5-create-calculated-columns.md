---
title: 'Azure Analysis Services självstudiekurs 5: Skapa beräknade kolumner | Microsoft Docs'
description: Beskriver hur du skapar beräknade kolumner i Azure Analysis Services-självstudieprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2d92edd0bda15ecd57a5a29375adae3a51c661c2
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49428138"
---
# <a name="create-calculated-columns"></a>Skapa beräknade kolumner

Under den här lektionen skapar du data i modellen genom att lägga till beräknade kolumner. Du kan skapa beräknade kolumner (som anpassade kolumner) när du använder Hämta Data med hjälp av frågeredigeraren, eller senare i modelldesignern, på samma sätt som du gör här. Läs mer i [Skapa beräknade kolumner](https://docs.microsoft.com/sql/analysis-services/tabular-models/ssas-calculated-columns).
  
Du kan skapa fem nya beräknade kolumner i tre olika tabeller. Stegen för att göra detta är något annorlunda för varje uppgift, vilket visar att det finns flera sätt att skapa kolumner, byta namn på dem och placera dem på olika platser i en tabell.  

Det är även den här lektionen där du först använder dataanalysuttryck (DAX). DAX är ett särskilt språk för att skapa mycket anpassningsbara formeluttryck för tabellmodeller. I den här självstudien använder du DAX för att skapa beräknade kolumner, mått och filter för roller. Läs mer i [DAX in tabular models](https://docs.microsoft.com/sql/analysis-services/tabular-models/understanding-dax-in-tabular-models-ssas-tabular) (DAX i tabellmodeller). 
  
Uppskattad tidsåtgång för den här lektionen: **15 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 4: Skapa relationer](../tutorials/aas-lesson-4-create-relationships.md). 
  
## <a name="create-calculated-columns"></a>Skapa beräknade kolumner  
  
#### <a name="create-a-monthcalendar-calculated-column-in-the-dimdate-table"></a>Skapa en beräknad MonthCalendar-kolumn i DimDate-tabellen  
  
1.  Klicka på menyn **Modell** > **Modellvy** > **Datavy**.  
  
    Beräknade kolumner kan endast skapas med hjälp av modelldesignern i datavyn.  
  
2.  Klicka på tabellen **DimDate** i modelldesignern (flik).  
  
3.  Högerklicka på kolumnrubriken **CalendarQuarter** och sedan på **Infoga kolumn**.  
  
    En ny kolumn med namnet **Beräknad kolumn 1** infogas till vänster om kolumnen **Kalenderkvartal**.  
  
4.  Ange följande DAX-formel i formelfältet ovanför tabellen: AutoComplete hjälper dig att ange de fullständiga namnen på kolumnerna och tabellerna och visar en lista över de funktioner som är tillgängliga.  
  
    ```  
    =RIGHT(" " & FORMAT([MonthNumberOfYear],"#0"), 2) & " - " & [EnglishMonthName]  
    ``` 
  
    Värden fylls sedan i för alla rader i den beräknade kolumnen. Om du bläddrar ner i tabellen ser du att rader kan ha olika värden för den här kolumnen baserat på data i varje rad.    
  
5.  Byt namn på den här kolumnen till **MonthCalendar**. 

    ![aas-lesson5-newcolumn](../tutorials/media/aas-lesson5-newcolumn.png) 
  
Den beräknade MonthCalendar-kolumnen innehåller ett sorterbart namn för Månad.  
  
#### <a name="create-a-dayofweek-calculated-column-in-the-dimdate-table"></a>Skapa en beräknad DayOfWeek-kolumn i DimDate-tabellen  
  
1.  När **DimDate**-tabellen fortfarande är aktiv klickar du på menyn **Kolumn** och sedan på **Lägg till kolumn**.  
  
2.  I formelfältet anger du följande formel:  
    
    ```
    =RIGHT(" " & FORMAT([DayNumberOfWeek],"#0"), 2) & " - " & [EnglishDayNameOfWeek]  
    ```
    
    Tryck på RETUR när du är klar med att skapa formeln. Den nya kolumnen läggs till längst till höger i tabellen.  
  
3.  Byt namn på kolumnen till **DayOfWeek**.  
  
4.  Klicka på kolumnrubriken och dra sedan kolumnen mellan kolumnen **EnglishDayNameOfWeek** och kolumnen **DayNumberOfMonth**.  
  
    > [!TIP]  
    > Genom att flytta kolumner i tabellen gör du det enklare att navigera.  
  
Den beräknade DayOfWeek-kolumnen innehåller ett sorterbart namn för veckodagen.  
  
#### <a name="create-a-productsubcategoryname-calculated-column-in-the-dimproduct-table"></a>Skapa en beräknad ProductSubcategoryName-kolumn i tabellen DimProduct  
  
  
1.  I tabellen **DimProduct** bläddrar du längst till höger i tabellen. Observera att kolumnen längst till höger heter **Lägg till kolumn** (kursiv) och klicka på kolumnens rubrik.  
  
2.  I formelfältet anger du följande formel:  
    
    ```
    =RELATED('DimProductSubcategory'[EnglishProductSubcategoryName])  
    ```
  
3.  Byt namn på kolumnen till **ProductSubcategoryName**.  
  
Den beräknade kolumnen ProductSubcategoryName används för att skapa en hierarki i tabellen DimProduct som innehåller data från kolumnen EnglishProductSubcategoryName i tabellen DimProductSubcategory. Hierarkier kan inte finnas i mer än en tabell. Du kan skapa hierarkier senare i lektion 9.  
  
#### <a name="create-a-productcategoryname-calculated-column-in-the-dimproduct-table"></a>Skapa en beräknad ProductCategoryName-kolumn i tabellen DimProduct  
  
1.  När **DimProduct**-tabellen fortfarande är aktiv klickar du på menyn **Kolumn** och sedan på **Lägg till kolumn**.  
  
2.  I formelfältet anger du följande formel:  
  
    ```
    =RELATED('DimProductCategory'[EnglishProductCategoryName]) 
    ```
    
3.  Byt namn på kolumnen till **ProductCategoryName**.  
  
Den beräknade kolumnen ProductCategoryName används för att skapa en hierarki i tabellen DimProduct som innehåller data från kolumnen EnglishProductCategoryName i tabellen DimProductCategory. Hierarkier kan inte finnas i mer än en tabell.  
  
#### <a name="create-a-margin-calculated-column-in-the-factinternetsales-table"></a>Skapa en beräknad kolumn för marginaler i tabellen FactInternetSales  
  
1.  Välj tabellen **FactInternetSales** i modelldesignern.  
  
2.  Skapa en ny beräknad kolumn mellan kolumnen **SalesAmount** och kolumnen **TaxAmt**.  
  
3.  I formelfältet anger du följande formel:  
  
    ```
    =[SalesAmount]-[TotalProductCost]
    ``` 

4.  Byt namn på kolumnen till **Marginal**.  
 
      ![aas-lesson5-newmargin](../tutorials/media/aas-lesson5-newmargin.png)
      
    Den beräknade marginal-kolumnen används för att analysera vinstmarginaler för varje försäljning.  
  
## <a name="whats-next"></a>Nästa steg
[Lektion 6: Skapa mått](../tutorials/aas-lesson-6-create-measures.md).
  
  
  
