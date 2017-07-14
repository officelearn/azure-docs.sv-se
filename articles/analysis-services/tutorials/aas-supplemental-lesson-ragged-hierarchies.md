---
title: "Kompletterande lektion i Azure Analysis Services-självstudiekurs: Ojämna hierarkier | Microsoft Docs"
description: "Beskriver hur du åtgärdar ojämna hierarkier i Azure Analysis Services-självstudiekursen."
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
ms.openlocfilehash: 0f02ff73eb126cc397312e87bde50b3ee2d6ce53
ms.contentlocale: sv-se
ms.lasthandoff: 06/03/2017

---
# Kompletterande lektion – Ojämna hierarkier
<a id="supplemental-lesson---ragged-hierarchies" class="xliff"></a>

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

I den här kompletterande lektionen löser du ett vanligt problem vid pivotering i hierarkier som innehåller tomma värden (medlemmar) på olika nivåer. Det kan till exempel förekomma i en organisation där en chef på hög nivå har både avdelningschefer och personer som inte är chefer som direkt underställda. Eller i geografiska hierarkier som består av land-region-ort, där vissa orter saknar en överordnad delstat eller provins, till exempel Washington D.C. och Vatikanstaten. När en hierarki har tomma medlemmar har den ofta olika, eller ojämna, underordnade nivåer.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Tabellmodeller på kompatibilitetsnivån 1400 har en extra **Dölj medlemmar**-egenskap för hierarkier. **Standardinställningen** utgår ifrån att det inte finns några tomma medlemmar på någon nivå. Inställningen **Dölj tomma medlemmar** exkluderar tomma medlemmar från hierarkin när den läggs till i en pivottabell eller rapport.  
  
Uppskattad tidsåtgång för den här lektionen: **20 minuter**  
  
## Krav
<a id="prerequisites" class="xliff"></a>  
Den här kompletterande lektionen ingår i en självstudiekurs om tabellmodeller. Innan du utför uppgifterna i den här kompletterande lektionen måste du ha slutfört alla föregående lektioner eller ha ett slutfört Adventure Works Internet Sales-exempelmodellprojekt. 

Om du har skapat projektet AW Internet Sales som en del i självstudiekursen innehåller inte din modell några data eller ojämna hierarkier ännu. För att slutföra den här kompletterande lektionen måste du först skapa problemet genom att lägga till ytterligare tabeller, skapa relationer, beräknade kolumner, ett mått och en ny organisationshierarki. Det tar cirka 15 minuter. Sedan tar det bara några minuter att lösa problemet.  

## Lägga till tabeller och objekt
<a id="add-tables-and-objects" class="xliff"></a>
  
### Så här lägger du till nya tabeller i din modell
<a id="to-add-new-tables-to-your-model" class="xliff"></a>
  
1.  I tabellmodellutforskaren expanderar du **Datakällor**. Sedan högerklickar du på din anslutning och väljer **Importera nya tabeller**.
  
2.  I navigatören väljer du **DimEmployee** och **FactResellerSales** och klickar sedan på **OK**.

3.  Klicka på **Importera** i frågeredigeraren

4.  Skapa följande [relationer](../tutorials/aas-lesson-4-create-relationships.md):

    | Tabell 1           | Kolumn       | Filterriktning   | Tabell 2     | Kolumn      | Active |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Standard            | DimDate     | Date        | Ja    |
    | FactResellerSales | DueDate      | Standard            | DimDate     | Date        | Nej     |
    | FactResellerSales | ShipDateKey  | Standard            | DimDate     | Date        | Nej     |
    | FactResellerSales | ProductKey   | Standard            | DimProduct  | ProductKey  | Ja    |
    | FactResellerSales | EmployeeKey  | Till båda tabellerna | DimEmployee | EmployeeKey | Ja    |

5. Skapa följande [beräknade kolumner](../tutorials/aas-lesson-5-create-calculated-columns.md) i tabellen **DimEmployee**: 

    **Sökväg** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,2)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,3)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,4)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,5)) 
    ```

6.  I tabellen **DimEmployee** skapar du en [hierarki](../tutorials/aas-lesson-9-create-hierarchies.md) med namnet **Organisation**. Lägg till följande kolumner i ordning: **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  Skapa följande [mått](../tutorials/aas-lesson-6-create-measures.md) i tabellen **FactResellerSales**:

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Använd [Analysera i Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) för att öppna Excel och automatiskt skapa en pivottabell.

9.  I **Pivottabellfält** lägger du till hierarkin **Organisation** från tabellen **DimEmployee** i **Rader** och måttet **ResellerTotalSales** från tabellen **FactResellerSales** i **Värden**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Som du ser i pivottabellen visas ojämna rader i hierarkin. Det finns flera rader med tomma medlemmar.

## Åtgärda den ojämna hierarkin genom att ange egenskapen Dölj medlemmar
<a id="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property" class="xliff"></a>

1.  I **tabellmodellutforskaren** expanderar du **Tabeller** > **DimEmployee** > **Hierarkier** > **Organisation**.

2.  I **Egenskaper** > **Dölj medlemmar** väljer du **Dölj tomma medlemmar**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Gå tillbaka till Excel och uppdatera pivottabellen. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Nu ser det betydligt bättre ut!

## Se även
<a id="see-also" class="xliff"></a>   
[Lektion 9: Skapa hierarkier](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Kompletterande lektion – Dynamisk säkerhet](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Kompletterande lektion – Detaljrader](../tutorials/aas-supplemental-lesson-detail-rows.md)  
