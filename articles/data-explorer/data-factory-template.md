---
title: Använd Azure Data Factory mall för Mass kopiering från databas till Azure Datautforskaren
description: I det här avsnittet får du lära dig att använda en Azure Data Factory mall för Mass kopiering från databas till Azure Datautforskaren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 5a6aebd276ef8658da9ca763be7da5c38a9c772a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873427"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Använd Azure Data Factory mall för Mass kopiering från databas till Azure Datautforskaren

Azure Datautforskaren är en snabb, fullständigt hanterad tjänst för data analys för real tids analys av stora mängder data som strömmas från många källor, till exempel program, webbplatser och IoT-enheter. Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten Azure Data Factory för att fylla i Azure Datautforskaren-databasen med data från ditt befintliga system och spara tid när du skapar dina analys lösningar. 

[Azure Data Factory mallar](/azure/data-factory/solution-templates-introduction) är fördefinierade Azure Data Factory pipelines som gör att du snabbt kan komma igång med Data Factory och minska utvecklings tiden för att skapa data integrerings projekt. **Mass kopieringen från databasen till Azure datautforskaren** -mallen skapas med hjälp av **söknings** **-och** gransknings aktiviteter. Du kan använda mallen för att skapa många pipeliner per databas eller tabell för snabbare kopiering av data. 

> [!IMPORTANT]
> * Använd **Mass kopiering från databasen till azure datautforskaren** -mallen för att kopiera stora mängder data från databaser som SQL Server och Google BigQuery till Azure datautforskaren. 
> * Använd [kopierings verktyget](data-factory-load-data.md) för att kopiera några tabeller med små eller stora data mängder till Azure datautforskaren. 

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Ett Azure Datautforskaren-kluster och-databas](create-cluster-database-portal.md)
* [Skapa en data fabrik](data-factory-load-data.md#create-a-data-factory)
* Data källa i databas

## <a name="create-controltabledataset"></a>Skapa ControlTableDataset

**ControlTableDataset** anger vilka data som ska kopieras från källan till målet i pipelinen. Antalet rader visar det totala antalet pipelines som krävs för att kopiera data. **ControlTableDataset** ska definieras som en del av käll databasen.

Exempel på SQL Server käll tabell format:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```
    
|Egenskap  |Beskrivning  | Exempel
|---------|---------| ---------|
|Partition   |   Kopiera order | 1  |  
|SourceQuery   |   fråga som anger vilka data som ska kopieras under pipeline-körningen | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  mål tabell namn | MyAdxTable       |  

Om din **ControlTableDataset** har ett annat format kan du skapa en jämförbar **ControlTableDataset** för formatet.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Använd Mass kopiering från databas till Azure Datautforskaren-mall

1. På sidan **Låt oss komma igång** väljer du panelen **skapa pipeline från mall** , eller så väljer du Penn ikonen (fliken**författare** ) till höger > **+**  >  **pipeline från mallen** för att öppna mallgalleriet.

    ![Azure Data Factory vi sätter igång](media/data-factory-template/adf-get-started.png)

1. Välj mall **för Mass kopiering från databas till Azure datautforskaren**.
 
    ![Välj pipeline från mall](media/data-factory-template/pipeline-from-template.png)

1.  I fönstret **Mass kopiering från databas till Azure datautforskaren väljer du** befintliga data uppsättningar i list rutan. 

    * **ControlTableDataset** -länkad tjänst till kontroll tabell som anger vilka data som kopieras från källa till mål och var de ska placeras i målet. 
    * **SourceDataset** – länkad tjänst till käll databas. 
    * **AzureDataExplorerTable** – Azure datautforskaren-tabellen. Om data uppsättningen inte finns [skapar du Azure datautforskaren länkade tjänsten](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) för att lägga till data uppsättningen.
    * Välj **Använd den här mallen**.

    ![Konfigurera mall för Azure Datautforskaren Mass kopiering](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Välj ett område på arbets ytan, utanför aktiviteterna, för att få åtkomst till mallens pipeline. Välj **parametrar** för att ange parametrar för tabellen inklusive **namn** (kontroll tabellens namn) och **standardvärdet** (kolumn namn).

    ![Pipeline-parametrar](media/data-factory-template/pipeline-parameters.png)

1.  Välj lookup-aktivitet, **GetPartitionList**, om du vill visa standardinställningar. Frågan skapas automatiskt.
1.  Välj kommando aktivitet **ForEachPartition**, Välj **Inställningar**
    * **Antal batchar**: Välj tal från 1-50. Det här valet avgör antalet pipelines som körs parallellt tills **ControlTableDataset** antal rader har uppnåtts. 
    * Markera inte den **sekventiella** kryss rutan så att pipeline-batchar körs parallellt.

    ![ForEachPartition-inställningar](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > Bästa praxis är att köra många pipeliner parallellt så att data kan kopieras snabbare. Partitionera data i käll tabellen och allokera en partition per pipeline, enligt datum och tabell, för att öka effektiviteten.

1. Välj **verifiera alla** för att validera ADF-pipeline. Se **utdata för pipeline-verifiering**.

    ![Validera mall-pipeliner](media/data-factory-template/validate-template-pipelines.png)

1. Välj **Felsök**, om det behövs, och **Lägg sedan till utlösare** för att köra pipelinen.

    ![Kör pipeline](media/data-factory-template/trigger-run-of-pipeline.png)    


Nu kan du använda **Mass kopiering från databas till Azure datautforskaren** -mall för att effektivt kopiera stora mängder data från dina databaser och tabeller.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du [kopierar data till Azure datautforskaren med hjälp av Azure Data Factory](data-factory-load-data.md).

* Lär dig mer om [Azure datautforskaren-anslutningen](/azure/data-factory/connector-azure-data-explorer) i Azure Data Factory.

* Lär dig mer om [Azure datautforskaren frågor](/azure/data-explorer/web-query-data) för data frågor.






