---
title: Kopiera i bulk från en databas till Azure Datautforskaren med hjälp av Azure Data Factory-mallen
description: I den här artikeln får du lära dig att använda en Azure Data Factory mall för att kopiera i bulk från en databas till Azure Datautforskaren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293563"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Kopiera i bulk från en databas till Azure Datautforskaren med hjälp av Azure Data Factory-mallen 

Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys. Det erbjuder real tids analys av stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. 

Om du vill kopiera data från en databas i Oracle server, Netezza, Teradata eller SQL Server till Azure Datautforskaren måste du läsa in stora mängder data från flera tabeller. Normalt måste data partitioneras i varje tabell så att du kan läsa in rader med flera trådar parallellt från en enda tabell. I den här artikeln beskrivs en mall som du kan använda i dessa scenarier.

[Azure Data Factory mallar](/azure/data-factory/solution-templates-introduction) är fördefinierade Data Factory pipeliner. De här mallarna kan hjälpa dig att komma igång snabbt med Data Factory och minska utvecklings tiden för data integrerings projekt. 

Du skapar *Mass kopieringen från databasen till Azure datautforskaren* -mallen med hjälp av *Lookup* *-och* gransknings aktiviteter. För snabbare data kopiering kan du använda mallen för att skapa många pipelines per databas eller per tabell. 

> [!IMPORTANT]
> Se till att använda det verktyg som är lämpligt för den mängd data som du vill kopiera.
> * Använd *Mass kopiering från databasen till azure datautforskaren* -mallen för att kopiera stora mängder data från databaser som SQL Server och Google BigQuery till Azure datautforskaren. 
> * Använd [*verktyget Data Factory kopiera data*](data-factory-load-data.md) för att kopiera några tabeller med små eller stora data mängder till Azure datautforskaren. 

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Data Explorer-kluster och en databas](create-cluster-database-portal.md).
* [Skapa en data fabrik](data-factory-load-data.md#create-a-data-factory).
* En data källa i en databas.

## <a name="create-controltabledataset"></a>Skapa ControlTableDataset

*ControlTableDataset* anger vilka data som ska kopieras från källan till målet i pipelinen. Antalet rader anger det totala antalet pipelines som behövs för att kopiera data. Du bör definiera ControlTableDataset som en del av käll databasen.

Ett exempel på formatet för SQL Server käll tabellen visas i följande kod:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Kod elementen beskrivs i följande tabell:

|Egenskap  |Beskrivning  | Exempel
|---------|---------| ---------|
|PartitionId   |  Kopierings ordningen | 1  |  
|SourceQuery   |  Frågan som anger vilka data som ska kopieras under pipeline-körningen | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Mål tabellens namn | MyAdxTable       |  

Om din ControlTableDataset har ett annat format kan du skapa en jämförbar ControlTableDataset för formatet.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Använd mallen Mass kopiering från databas till Azure Datautforskaren

1. I fönstret **Låt oss komma igång** väljer du **skapa pipeline från mall** för att öppna fönstret **mall för mall** .

    ![Fönstret Azure Data Factory "Låt mig komma igång"](media/data-factory-template/adf-get-started.png)

1. Välj **Mass kopiering från databas till Azure datautforskaren** -mall.
 
    ![Mallen "Mass kopiering från databas till Azure Datautforskaren"](media/data-factory-template/pipeline-from-template.png)

1.  I fönstret **Mass kopiering från databas till Azure datautforskaren** , under **användarindata**, anger du dina data uppsättningar genom att göra följande: 

    a. I list rutan **ControlTableDataset** väljer du den länkade tjänsten i kontroll tabellen som anger vilka data som kopieras från källan till målet och var de ska placeras i målet. 

    b. I list rutan **SourceDataset** väljer du den länkade tjänsten till käll databasen. 

    c. Välj Azure Datautforskaren-tabellen i list rutan **AzureDataExplorerTable** . Om data uppsättningen inte finns [skapar du Azure datautforskaren länkade tjänsten](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) för att lägga till data uppsättningen.

    d. Välj **Använd den här mallen**.

    ![Fönstret "Mass kopiering från databas till Azure Datautforskaren"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Välj ett område på arbets ytan, utanför aktiviteterna, för att få åtkomst till mallens pipeline. Välj fliken **parametrar** för att ange parametrar för tabellen, inklusive **namn** (kontroll tabell namn) och **Standardvärde** (kolumn namn).

    ![Pipeline-parametrar](media/data-factory-template/pipeline-parameters.png)

1.  Under **uppslag**väljer du **GetPartitionList** för att Visa standardinställningarna. Frågan skapas automatiskt.
1.  Välj kommando aktivitet, **ForEachPartition**, Välj fliken **Inställningar** och gör sedan följande:

    a. I rutan **antal batchar** anger du en siffra mellan 1 och 50. Det här valet avgör antalet pipelines som körs parallellt tills antalet *ControlTableDataset* -rader har uppnåtts. 

    b. För att se till att pipeline-batchar körs parallellt, ska du *inte* Markera kryss rutan **sekventiell** .

    ![ForEachPartition-inställningar](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Den bästa metoden är att köra många pipeliner parallellt så att dina data kan kopieras snabbare. Du kan öka effektiviteten genom att partitionera data i käll tabellen och allokera en partition per pipeline, enligt datum och tabell.

1. Välj **verifiera alla** för att validera Azure Data Factory pipelinen och visa resultatet i fönstret **pipeline-verifiering** .

    ![Validera mall-pipeliner](media/data-factory-template/validate-template-pipelines.png)

1. Om det behövs väljer du **Felsök**och väljer sedan **Lägg till utlösare** för att köra pipelinen.

    ![Knapparna "Felsök" och "kör pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

Nu kan du använda mallen för att effektivt kopiera stora mängder data från dina databaser och tabeller.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [kopierar data till Azure datautforskaren med hjälp av Azure Data Factory](data-factory-load-data.md).
* Lär dig mer om [Azure datautforskaren-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.
* Lär dig mer om [Azure datautforskaren frågor](/azure/data-explorer/web-query-data) för data frågor.






