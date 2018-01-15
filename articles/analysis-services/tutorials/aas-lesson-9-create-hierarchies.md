---
title: "Azure Analysis Services självstudiekurs 9: Skapa hierarkier | Microsoft Docs"
description: 
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
ms.openlocfilehash: 041096b1a93fdc671939b6d6715a7836d1977e3c
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="create-hierarchies"></a>Skapa hierarkier

I den här lektionen skapar du hierarkier. Hierarkier är grupper med kolumner som är ordnade i nivåer. En geografisk hierarki kan till exempel ha undernivåer för land, delstat, län och ort. Hierarkier kan visas fristående från andra kolumner i en fältlista i en rapporteringsklient, vilket gör det enklare för klientanvändare att navigera till dem och ta med dem i rapporter. Mer information finns i [Hierarkier](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Du kan skapa hierarkier med modelldesignern i *diagramvyn*. Skapa och hantera hierarkier stöds inte i datavyn.  
  
Uppskattad tidsåtgång för den här lektionen: **20 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna i den här lektionen måste du ha slutfört föregående lektion: [Lektion 8: Skapa perspektiv](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## <a name="create-hierarchies"></a>Skapa hierarkier  
  
#### <a name="to-create-a-category-hierarchy-in-the-dimproduct-table"></a>Så här skapar du en kategorihierarki i tabellen DimProduct  
  
1.  I modelldesignern (diagramvyn) högerklickar du på **DimProduct**-tabellen > **Skapa hierarki**. En ny hierarki visas längst ned i tabellfönstret. Byt namn på hierarkin till **Kategori**.  
  
2.  Klicka på och dra kolumnen **ProductCategoryName** till den nya hierarkin **Kategori**.  
  
3.  I hierarkin **Kategori** högerklickar du på **ProductCategoryName** > **Byt namn** och skriver **Kategori**.  
  
    > [!NOTE]  
    > När du ändrar namnet på en kolumn i en hierarki ändras inte namnet på den kolumnen i tabellen. En kolumn i en hierarki är bara en representation av kolumnen i tabellen.  
  
4.  Klicka och dra kolumnen **ProductSubcategoryName** till hierarkin **Kategori**. Byt namn på den till **Underkategori**. 
  
5.  Högerklicka på kolumnen **ModelName** > **Lägg till i hierarki** och välj sedan **Kategori**. Byt namn på den till **Modell**.

6.  Lägg slutligen till **EnglishProductName** i hierarkin Kategori. Byt namn på den till **Produkt**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### <a name="to-create-hierarchies-in-the-dimdate-table"></a>Så här skapar du hierarkier i tabellen DimDate  
  
1.  Skapa en hierarki med namnet **Kalender** i tabellen **DimDate**.  
  
3.  Lägg till följande kolumner i ordning:

    *  CalendarYear
    *  CalendarSemester
    *  CalendarQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
    
4.  Skapa en**Fiscal**-hierarki i tabellen **DimDate**. Ta med följande kolumner i ordning:  
  
    *  FiscalYear
    *  FiscalSemester
    *  FiscalQuarter
    *  MonthCalendar
    *  DayNumberOfMonth
  
5.  Skapa slutligen en **ProductionCalendar**-hierarki i tabellen **DimDate**. Ta med följande kolumner i ordning:  
    *  CalendarYear
    *  WeekNumberOfYear
    *  DayNumberOfWeek
  
 ## <a name="whats-next"></a>Nästa steg
[Lektion 10: Skapa partitioner](../tutorials/aas-lesson-10-create-partitions.md). 
  
  
