---
title: 'Kompletterande lektion i Azure Analysis Services-självstudiekurs: Ojämna hierarkier | Microsoft Docs'
description: Beskriver hur du åtgärdar ojämna hierarkier i Azure Analysis Services-självstudiekursen.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f5bd679c11139b7ac439247df81a7adb0b02b0fe
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426343"
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Kompletterande lektion – Ojämna hierarkier

I den här kompletterande lektionen löser du ett vanligt problem vid pivotering i hierarkier som innehåller tomma värden (medlemmar) på olika nivåer. Det kan till exempel förekomma i en organisation där en chef på hög nivå har både avdelningschefer och personer som inte är chefer som direkt underställda. Eller i geografiska hierarkier som består av land-region-ort, där vissa orter saknar en överordnad delstat eller provins, till exempel Washington D.C. och Vatikanstaten. När en hierarki har tomma medlemmar har den ofta olika, eller ojämna, underordnade nivåer.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Tabellmodeller på kompatibilitetsnivån 1400 har en extra **Dölj medlemmar**-egenskap för hierarkier. **Standardinställningen** utgår ifrån att det inte finns några tomma medlemmar på någon nivå. Inställningen **Dölj tomma medlemmar** exkluderar tomma medlemmar från hierarkin när den läggs till i en pivottabell eller rapport.  
  
Uppskattad tidsåtgång för den här lektionen: **20 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Den här kompletterande lektionen ingår i en självstudiekurs om tabellmodeller. Innan du utför uppgifterna i den här kompletterande lektionen måste du ha slutfört alla föregående lektioner eller ha ett slutfört Adventure Works Internet Sales-exempelmodellprojekt. 

Om du har skapat projektet AW Internet Sales som en del i självstudiekursen innehåller inte din modell några data eller ojämna hierarkier ännu. För att slutföra den här kompletterande lektionen måste du först skapa problemet genom att lägga till ytterligare tabeller, skapa relationer, beräknade kolumner, ett mått och en ny organisationshierarki. Det tar cirka 15 minuter. Sedan tar det bara några minuter att lösa problemet.  

## <a name="add-tables-and-objects"></a>Lägga till tabeller och objekt
  
### <a name="to-add-new-tables-to-your-model"></a>Så här lägger du till nya tabeller i din modell
  
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
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],2,1)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],3,1)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],4,1)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],5,1)) 
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

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Åtgärda den ojämna hierarkin genom att ange egenskapen Dölj medlemmar

1.  I **tabellmodellutforskaren** expanderar du **Tabeller** > **DimEmployee** > **Hierarkier** > **Organisation**.

2.  I **Egenskaper** > **Dölj medlemmar** väljer du **Dölj tomma medlemmar**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  Gå tillbaka till Excel och uppdatera pivottabellen. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Nu ser det betydligt bättre ut!

## <a name="see-also"></a>Se även   
[Lektion 9: Skapa hierarkier](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Kompletterande lektion – Dynamisk säkerhet](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Kompletterande lektion – Detaljrader](../tutorials/aas-supplemental-lesson-detail-rows.md)  
