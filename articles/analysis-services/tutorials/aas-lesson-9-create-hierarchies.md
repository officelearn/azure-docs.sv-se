---
title: "Azure Analysis Services självstudiekurs 9: Skapa hierarkier | Microsoft Docs"
description: 
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: d628dc621335acf231342a6d9186079de16e85f4
ms.contentlocale: sv-se
ms.lasthandoff: 06/03/2017

---
# Lektion 9: Skapa hierarkier
<a id="lesson-9-create-hierarchies" class="xliff"></a>

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

I den här lektionen skapar du hierarkier. Hierarkier är grupper med kolumner som är ordnade i nivåer. En geografisk hierarki kan till exempel ha undernivåer för land, delstat, län och ort. Hierarkier kan visas fristående från andra kolumner i en fältlista i en rapporteringsklient, vilket gör det enklare för klientanvändare att navigera till dem och ta med dem i rapporter. Mer information finns i [Hierarkier](https://docs.microsoft.com/sql/analysis-services/tabular-models/hierarchies-ssas-tabular)
  
Du kan skapa hierarkier med modelldesignern i *diagramvyn*. Skapa och hantera hierarkier stöds inte i datavyn.  
  
Uppskattad tidsåtgång för den här lektionen: **20 minuter**  
  
## Krav
<a id="prerequisites" class="xliff"></a>  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna i den här lektionen måste du ha slutfört föregående lektion: [Lektion 8: Skapa perspektiv](../tutorials/aas-lesson-8-create-perspectives.md).  
  
## Skapa hierarkier
<a id="create-hierarchies" class="xliff"></a>  
  
#### Så här skapar du en kategorihierarki i tabellen DimProduct
<a id="to-create-a-category-hierarchy-in-the-dimproduct-table" class="xliff"></a>  
  
1.  I modelldesignern (diagramvyn) högerklickar du på **DimProduct**-tabellen > **Skapa hierarki**. En ny hierarki visas längst ned i tabellfönstret. Byt namn på hierarkin till **Kategori**.  
  
2.  Klicka på och dra kolumnen **ProductCategoryName** till den nya hierarkin **Kategori**.  
  
3.  I hierarkin **Kategori** högerklickar du på **ProductCategoryName** > **Byt namn** och skriver **Kategori**.  
  
    > [!NOTE]  
    > När du ändrar namnet på en kolumn i en hierarki ändras inte namnet på den kolumnen i tabellen. En kolumn i en hierarki är bara en representation av kolumnen i tabellen.  
  
4.  Klicka och dra kolumnen **ProductSubcategoryName** till hierarkin **Kategori**. Byt namn på den till **Underkategori**. 
  
5.  Högerklicka på kolumnen **ModelName** > **Lägg till i hierarki** och välj sedan **Kategori**. Byt namn på den till **Modell**.

6.  Lägg slutligen till **EnglishProductName** i hierarkin Kategori. Byt namn på den till **Produkt**.  

    ![aas-lesson9-category](../tutorials/media/aas-lesson9-category.png)
  
#### Så här skapar du hierarkier i tabellen DimDate
<a id="to-create-hierarchies-in-the-dimdate-table" class="xliff"></a>  
  
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
  
 ## Nästa steg
<a id="whats-next" class="xliff"></a>
[Lektion 10: Skapa partitioner](../tutorials/aas-lesson-10-create-partitions.md). 
  
  

