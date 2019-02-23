---
title: 'Azure Analysis Services självstudiekurs 10: Skapa partitioner | Microsoft Docs'
description: Beskriver hur du skapar partitioner i Azure Analysis Services-självstudieprojektet.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91d34065753ae29b14aad8580bf922b8213a7988
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738631"
---
# <a name="create-partitions"></a>Skapa partitioner

Under den här lektionen skapar du partitioner för att dela upp tabellen FactInternetSales i mindre, logiska delar som kan bearbetas (uppdateras) oberoende av andra partitioner. Som standard har alla tabeller som du tar med i din modell en partition som innehåller alla kolumner och rader i tabellen. I tabellen FactInternetSales vill vi dela upp data efter år, en partition för var och en av tabellens fem år. Varje partition kan sedan bearbetas separat. Läs mer i [Partitioner](https://docs.microsoft.com/sql/analysis-services/tabular-models/partitions-ssas-tabular). 
  
Uppskattad tidsåtgång för den här lektionen: **15 minuter**  
  
## <a name="prerequisites"></a>Förutsättningar  
Det här avsnittet ingår i självstudiekursen för tabellmodellering som bör slutföras i rätt ordning. Innan du utför uppgifterna under den här lektionen bör du ha slutfört föregående lektion: [Lektion 9: Skapa hierarkier](../tutorials/aas-lesson-9-create-hierarchies.md).  
  
## <a name="create-partitions"></a>Skapa partitioner  
  
#### <a name="to-create-partitions-in-the-factinternetsales-table"></a>Skapa ytterligare partitioner i tabellen FactInternetSales  
  
1.  Expandera **Tabeller** i tabellmodellutforskaren och högerklicka på **FactInternetSales** > **Partitioner**.  
  
2.  I partitionshanteraren klickar du på **Kopiera** och sedan ändrar du namnet till **FactInternetSales2010**.
  
    Eftersom du vill att partitionen endast ska innehålla de här raderna under en viss period så måste du ändra frågeuttrycket för år 2010.
  
4.  Klicka på **Design** för att öppna frågeredigeraren och klicka sedan på frågan **FactInternetSales2010**.

5.  I förhandsgranskningen klickar du på nedpilen i kolumnrubriken **OrderDate** och sedan klickar du på **Filter för datum/tid** > **Mellan**.

    ![aas-lesson10-query-editor](../tutorials/media/aas-lesson10-query-editor.png)

6.  I dialogrutan Filtrera rader i **visa rader där: OrderDate**, lämna **infaller efter eller lika med**, och ange sedan följande i datumfältet **1/1/2010**. Lämna operatorn **And** (Och) markerad och välj sedan **is before** (infaller innan). Ange sedan **2011-1-1** i datafältet och klicka på **OK**.

    ![aas-lesson10-filter-rows](../tutorials/media/aas-lesson10-filter-rows.png)
    
    Observera att ytterligare ett steg med namnet filtrerade rader finns i frågeredigeraren, i TILLÄMPADE STEG. Det här filtret väljer endast beställningsdatum från 2010.

8.  Klicka på **Importera**.

    Observera att frågeuttrycket i partitionshanteraren nu har ytterligare en filtrerade rader-sats.

    ![aas-lesson10-query](../tutorials/media/aas-lesson10-query.png)
  
    Åtgärdssatsen anger att den här partitionen endast ska ta med data i de rader där OrderDate är i kalenderåret 2010 i enlighet med filtrerade rader-satsen.  
  
  
#### <a name="to-create-a-partition-for-the-2011-year"></a>Så här skapar du en partition för år 2011  
  
1.  I listan över partitioner klickar du på partitionen **FactInternetSales2010** som du skapade och sedan på **Kopiera**.  Ändra partitionsnamnet till **FactInternetSales2011**. 

    Du behöver inte använda frågeredigeraren för att skapa en ny filtrerade rader-sats. Eftersom du skapade en kopia av frågan för 2010 behöver du bara göra en liten ändring i frågan för 2011.
  
2.  För att den här partitionen endast ska innehålla raderna för året 2011 ersätter du åren i filtrerade rader-satsen i **frågeuttrycket** med **2011** respektive **2012**, så här:  
  
    ```  
    let
        Source = #"SQL/localhost;AdventureWorksDW2014",
        dbo_FactInternetSales = Source{[Schema="dbo",Item="FactInternetSales"]}[Data],
        #"Removed Columns" = Table.RemoveColumns(dbo_FactInternetSales,{"OrderDateKey", "DueDateKey", "ShipDateKey"}),
        #"Filtered Rows" = Table.SelectRows(#"Removed Columns", each [OrderDate] >= #datetime(2011, 1, 1, 0, 0, 0) and [OrderDate] < #datetime(2012, 1, 1, 0, 0, 0))
    in
        #"Filtered Rows"
   
    ```  
  
#### <a name="to-create-partitions-for-2012-2013-and-2014"></a>Så här skapar du partitioner för 2012, 2013 och 2014.  
  
- Följ de tidigare stegen och skapa partitioner för 2012, 2013 och 2014 genom att ändra åren i filtrerade rader-satsen så att den endast tar med rader för det aktuella året. 
  

## <a name="delete-the-factinternetsales-partition"></a>Ta bort partitionen FactInternetSales
Nu när du har partitioner för varje år kan du ta bort partitionen FactInternetSales, vilket förhindrar överlappning när du väljer Bearbeta alla när du bearbetar partitioner.

#### <a name="to-delete-the-factinternetsales-partition"></a>Så här gör du för att ta bort partitionen FactInternetSales
-  Klicka på partitionen FactInternetSales och sedan på **Ta bort**.



## <a name="process-partitions"></a>Bearbeta partitioner  
Observera att varje ny partition du skapat inte har bearbetats, vilket visas i kolumnen **Senast bearbetad** i partitionshanteraren. När du skapar partitioner bör du köra en Bearbeta partitioner- eller Bearbeta tabell-åtgärd för att uppdatera data i dessa partitioner.  
  
#### <a name="to-process-the-factinternetsales-partitions"></a>Så här gör du för att bearbeta FactInternetSales-partitionerna  
  
1.  Stäng partitionshanteraren genom att klicka på **OK**.  
  
2.  Klicka på tabellen **FactInternetSales** och sedan på menyn **Modell** > **Bearbeta** > **Bearbeta partitioner**.  
  
3.  Kontrollera att **Läge** är inställt på **Bearbeta: Standard** i dialogrutan Bearbeta partitioner.  
  
4.  Markera kryssrutan i kolumnen **Bearbeta** för var och en av de fem partitionerna du skapade och klicka sedan på **OK**.  

    ![aas-lesson10-process-partitions](../tutorials/media/aas-lesson10-process-partitions.png)
  
    Ange det Windows-användarnamn och lösenord som du angav i lektion 2 om du uppmanas att ange autentiseringsuppgifter för personifiering.  
  
    Dialogrutan **Databearbetning** visas med information om bearbetningen av varje partition. Observera att olika antal rader överförs för varje partition. Varje partition innehåller endast de rader för året som anges i WHERE-satsen i SQL-instruktionen. När bearbetningen är klar kan du stänga dialogrutan Databearbetning.  
  
    ![aas-lesson10-process-complete](../tutorials/media/aas-lesson10-process-complete.png)
  
## <a name="whats-next"></a>Nästa steg
Gå till nästa lektion: [Lektion 11: Skapa roller](../tutorials/aas-lesson-11-create-roles.md). 
