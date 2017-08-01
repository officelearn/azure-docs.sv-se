---
title: "Azure Analysis Services självstudiekurs 11: Skapa roller | Microsoft Docs"
description: "Beskriver hur du skapar roller i Azure Analysis Services-självstudieprojektet."
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
ms.openlocfilehash: 085a36edd2a0e80123ac8754b438bceadfa6c0e9
ms.contentlocale: sv-se
ms.lasthandoff: 06/03/2017

---
# <a name="lesson-11-create-roles"></a>Lektion 11: Skapa roller

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Under den här lektionen skapar du roller. Roller skyddar modelldatabasobjekt och data genom att begränsa åtkomsten till endast de användare som är rollmedlemmar. Varje roll definieras med en enskild behörighet: Ingen, Läsa, Läsa och bearbeta, Bearbeta eller Administratör. Roller kan definieras vid modellredigering med hjälp av rollhanteraren. När en modell har distribuerats kan du hantera roller med SQL Server Management Studio (SSMS). Mer information finns i [Roller](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Du behöver inte skapa roller för att slutföra den här självstudiekursen. Som standard har det konto som du är inloggad med administratörsbehörighet på modellen. Men för att andra användare i din organisation ska kunna se modellen med en rapporteringsklient måste du skapa minst en roll med läsbehörighet och lägga till dessa användare som medlemmar i rollen.  
  
Du kan skapa tre roller:  
  
-   **Säljchef** – Den här rollen kan innefatta användare i organisationen som du vill ska ha läsbehörighet till alla modellobjekt och data.  
  
-   **Försäljningsanalytiker, USA** – Den här rollen kan innefatta användare i organisationen som endast ska ha behörighet att visa data som rör försäljning i USA. För den här rollen använder du en DAX-formel för att definiera ett *radfilter* som begränsar medlemmarna till att endast kunna visa data för USA.  
  
-   **Administratör** – Den här rollen kan innefatta användare som du vill ska ha administratörsbehörighet, vilket ger dem obegränsad åtkomst och behörighet att utföra administrativa uppgifter för modelldatabasen.  
  
Eftersom Windows-användarkonton och -gruppkonton i din organisation är unika kan du lägga till konton från din organisation till medlemmar. Men i den här självstudiekursen kan du lämna medlemsfälten tomma om du vill. Du får testa effekten av varje roll senare i lektion 12: Analysera i Excel.  
  
Uppskattad tidsåtgång för den här lektionen: **15 minuter**  
  
## <a name="prerequisites"></a>Krav  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 10: Skapa partitioner](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Skapa roller  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Så här skapar du användarrollen Säljchef  
  
1.  Högerklicka på **Roller** > **Roller** i tabellmodellutforskaren.  
  
2.  Klicka på **Ny** i rollhanteraren.  
  
3.  Klicka på den nya rollen och ändra namnet på rollen till **Säljchef** i kolumnen **Namn**.  
  
4.  Klicka på listrutan i kolumnen **Behörigheter** och välj sedan behörigheten **Läsa**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Valfritt: Klicka på fliken **Medlemmar** och sedan på **Lägg till**. I dialogrutan **Välj användare eller grupper** anger du de Windows-användare eller grupper från din organisation som du vill ska ingå i rollen.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Så här skapar du användarrollen Försäljningsanalytiker, USA  
  
1.  Klicka på **Ny** i rollhanteraren.    
  
2.  Byt namn på rollen till **Försäljningsanalytiker, USA**.  
  
3.  Ge rollen **läsbehörighet**.  
  
4.  Klicka på fliken Radfilter och skriv sedan följande formel i kolumnen DAX-filter, endast för tabellen **DimGeography**:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    En radfilterformel måste matcha ett booleskt värde (SANT/FALSKT). Med den här formeln anger du att endast rader med land-/regionskoden ”US” visas för användaren.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Valfritt: Klicka på fliken **Medlemmar** och sedan på **Lägg till**. I dialogrutan **Välj användare eller grupper** anger du de Windows-användare eller grupper från din organisation som du vill ska ingå i rollen.  
  
#### <a name="to-create-an-administrator-user-role"></a>Så här skapar du användarrollen Administratör  
  
1.  Klicka på **Ny**.  
  
2.  Byt namn på rollen till **Administratör**.  
  
3.  Ge den här rollen **administratörsbehörighet**.  
  
4.  Valfritt: Klicka på fliken **Medlemmar** och sedan på **Lägg till**. I dialogrutan **Välj användare eller grupper** anger du de Windows-användare eller grupper från din organisation som du vill ska ingå i rollen. 
  
  
## <a name="whats-next"></a>Nästa steg
[Lektion 12: Analysera i Excel](../tutorials/aas-lesson-12-analyze-in-excel.md).

  
  

