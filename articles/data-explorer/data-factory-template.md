---
title: Kopiera i grupp från en databas till Azure Data Explorer med hjälp av Azure Data Factory-mallen
description: I den här artikeln lär du dig att använda en Azure Data Factory-mall för att kopiera i mass från en databas till Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293563"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Kopiera i grupp från en databas till Azure Data Explorer med hjälp av Azure Data Factory-mallen 

Azure Data Explorer är en snabb, fullständigt hanterad dataanalystjänst. Det erbjuder realtidsanalys på stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. 

Om du vill kopiera data från en databas i Oracle Server, Netezza, Teradata eller SQL Server till Azure Data Explorer måste du läsa in enorma mängder data från flera tabeller. Vanligtvis måste data partitioneras i varje tabell så att du kan läsa in rader med flera trådar parallellt från en enda tabell. I den här artikeln beskrivs en mall som ska användas i dessa scenarier.

[Azure Data Factory-mallar](/azure/data-factory/solution-templates-introduction) är fördefinierade Data Factory-pipelines. Dessa mallar kan hjälpa dig att komma igång snabbt med Data Factory och minska utvecklingstiden för dataintegrationsprojekt. 

Du skapar *masskopian från databas till Azure Data Explorer-mall* med hjälp av aktiviteter för *uppslag* och *för eÃ¶s.* För snabbare datakopiering kan du använda mallen för att skapa många pipelines per databas eller per tabell. 

> [!IMPORTANT]
> Var noga med att använda det verktyg som är lämpligt för den mängd data som du vill kopiera.
> * Använd *masskopiering från databas till Azure Data Explorer-mall* för att kopiera stora mängder data från databaser som SQL-server och Google BigQuery till Azure Data Explorer. 
> * Använd [*datafabrikskopieringsdataverktyget*](data-factory-load-data.md) för att kopiera några tabeller med små eller måttliga mängder data till Azure Data Explorer. 

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md).
* [Skapa en datafabrik](data-factory-load-data.md#create-a-data-factory).
* En datakälla i en databas.

## <a name="create-controltabledataset"></a>Skapa ControlTableDataset

*ControlTableDataset* anger vilka data som ska kopieras från källan till målet i pipelinen. Antalet rader anger det totala antalet pipelines som behövs för att kopiera data. Du bör definiera ControlTableDataset som en del av källdatabasen.

Ett exempel på SQL Server-källtabellformatet visas i följande kod:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Kodelementen beskrivs i följande tabell:

|Egenskap  |Beskrivning  | Exempel
|---------|---------| ---------|
|Partitionid   |  Kopieringsordningen | 1  |  
|SourceQuery (Källa)   |  Frågan som anger vilka data som ska kopieras under pipelinekörningen | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName (ADXTableName)  |  Måltabellens namn | MyAdxTable       |  

Om ControlTableDataset är i ett annat format skapar du en jämförbar ControlTableDataset för ditt format.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Använda masskopian från databas till Azure Data Explorer-mall

1. I fönstret **Kom igång** väljer du Skapa pipeline **från mall** för att öppna **mallgallerifönstret.**

    ![Fönstret Azure Data Factory "Nu kommer igång"](media/data-factory-template/adf-get-started.png)

1. Välj **masskopiering från databas till Azure Data** Explorer-mall.
 
    ![Mallen "Masskopiering från databas till Azure Data Explorer"](media/data-factory-template/pipeline-from-template.png)

1.  I fönstret **Masskopia från databas till Azure Data Explorer** anger du datauppsättningar under **Användarindata**genom att göra följande: 

    a. I listrutan **ControlTableDataset** väljer du den länkade tjänsten till kontrolltabellen som anger vilka data som kopieras från källan till målet och var de placeras i målet. 

    b. Välj den länkade tjänsten till källdatabasen i listrutan **SourceDataset.** 

    c. Välj tabellen Azure Data Explorer i listrutan **AzureDataExplorerTable.** Om datauppsättningen inte finns [skapar du den länkade Azure Data Explorer-tjänsten](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) för att lägga till datauppsättningen.

    d. Välj **Använd den här mallen**.

    ![Fönstret Masskopia från databas till Azure Data Explorer](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Välj ett område på arbetsytan, utanför aktiviteterna, för att komma åt mallpipelinen. Välj fliken **Parametrar** om du vill ange parametrarna för tabellen, inklusive **Namn** (kontrolltabellnamn) och **Standardvärde** (kolumnnamn).

    ![Pipeline-parametrar](media/data-factory-template/pipeline-parameters.png)

1.  Under **Uppslag**väljer du **GetPartitionList** om du vill visa standardinställningarna. Frågan skapas automatiskt.
1.  Välj kommandoaktivitet, **ForEachPartition**, välj fliken **Inställningar** och gör sedan följande:

    a. Ange ett tal från 1 till 50 i rutan **Batchräkning.** Den här markeringen bestämmer antalet pipelines som körs parallellt tills antalet *ControlTableDataset-rader* har nåtts. 

    b. Markera *inte* kryssrutan **Sekventiell** för att säkerställa att pipelinebatcharna körs parallellt.

    ![Inställningar för FörEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Det bästa är att köra många pipelines parallellt så att dina data kan kopieras snabbare. För att öka effektiviteten, partitionera data i källtabellen och allokera en partition per pipeline, enligt datum och tabell.

1. Välj **Validera alla** för att validera Azure Data Factory-pipelinen och visa sedan resultatet i fönstret Utdata för **pipelineverifiering.**

    ![Validera mallpipelledningar](media/data-factory-template/validate-template-pipelines.png)

1. Om det behövs väljer du **Felsök**och väljer sedan **Lägg till utlösare** för att köra pipelinen.

    ![Knapparna "Debug" och "Kör pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

Du kan nu använda mallen för att effektivt kopiera stora mängder data från databaser och tabeller.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [kopierar data till Azure Data Explorer med hjälp av Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om [Azure Data Explorer-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.
* Lär dig mer om [Azure Data Explorer-frågor](/azure/data-explorer/web-query-data) för datafrågor.






