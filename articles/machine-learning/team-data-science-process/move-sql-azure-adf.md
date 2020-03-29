---
title: SQL Server-data till SQL Azure med Azure Data Factory – Team Data Science Process
description: Ställ in en ADF-pipeline som innehåller två datamigreringsaktiviteter som tillsammans flyttar data dagligen mellan databaser lokalt och i molnet.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8f696f1c6c414cd9db082e79e0f34c56156e1ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722500"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Flytta data från en lokal SQL-server till SQL Azure med Azure Data Factory

Den här artikeln visar hur du flyttar data från en lokal SQL Server-databas till en SQL Azure-databas via Azure Blob Storage med hjälp av Azure Data Factory (ADF): den här metoden är en äldre metod som stöds som har fördelarna med en replikerad mellanlagringskopia, även om [vi föreslår att du tittar på vår datamigreringssida för de senaste alternativen](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1).

En tabell som sammanfattar olika alternativ för att flytta data till en Azure SQL-databas finns i [Flytta data till en Azure SQL-databas för Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Inledning: Vad är ADF och när ska den användas för att migrera data?
Azure Data Factory är en fullständigt hanterad molnbaserad dataintegrationstjänst som dirigerar och automatiserar förflyttning och omvandling av data. Nyckelkonceptet i ADF-modellen är pipeline. En pipeline är en logisk gruppering av aktiviteter, som var och en definierar de åtgärder som ska utföras på data som finns i Datauppsättningar. Länkade tjänster används för att definiera den information som behövs för att Data Factory ska kunna ansluta till dataresurserna.

Med ADF kan befintliga databehandlingstjänster bestå i datapipeldrar som är mycket tillgängliga och hanterade i molnet. Dessa datapipelpipel kan schemaläggas för att inta, förbereda, omvandla, analysera och publicera data, och ADF hanterar och dirigerar komplexa data- och bearbetningsberoenden. Lösningar kan snabbt byggas och distribueras i molnet, vilket kopplar samman ett växande antal lokala och molnbaserade datakällor.

Överväg att använda ADF:

* när data måste migreras kontinuerligt i ett hybridscenario som får åtkomst till både lokala resurser och molnresurser
* när data behöver omvandlingar eller har affärslogik tillagd när de migreras.

ADF möjliggör schemaläggning och övervakning av jobb med hjälp av enkla JSON-skript som hanterar förflyttning av data regelbundet. ADF har också andra funktioner, till exempel stöd för komplexa åtgärder. Mer information om ADF finns i dokumentationen på [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Scenariot
Vi har skapat en ADF-pipeline som består av två datamigreringsaktiviteter. Tillsammans flyttar de data dagligen mellan en lokal SQL-databas och en Azure SQL-databas i molnet. De två aktiviteterna är:

* kopiera data från en lokal SQL Server-databas till ett Azure Blob Storage-konto
* kopiera data från Azure Blob Storage-kontot till en Azure SQL-databas.

> [!NOTE]
> Stegen som visas här har anpassats från den mer detaljerade självstudien som tillhandahålls av [ADF-teamet: Kopiera data från en lokal SQL Server-databas till Azure Blob-lagringsreferenser](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/) till relevanta avsnitt i det ämnet tillhandahålls när det är lämpligt.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Krav
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure-lagringskonto**. Du använder ett Azure-lagringskonto för att lagra data i den här självstudien. Om du inte har ett Azure-lagringskonto läser du artikeln [Skapa ett lagringskonto.](../../storage/common/storage-account-create.md) När du har skapat lagringskontot måste du hämta kontonyckeln som används för att komma åt lagringen. Se [Hantera åtkomstnycklar för lagringskonto.](../../storage/common/storage-account-keys-manage.md)
* Åtkomst till en **Azure SQL-databas**. Om du måste konfigurera en Azure SQL-databas innehåller ämnet [Komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) information om hur du etablerar en ny instans av en Azure SQL-databas.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Den här proceduren använder [Azure-portalen](https://portal.azure.com/).
>
>

## <a name="upload-the-data-to-your-on-premises-sql-server"></a><a name="upload-data"></a>Ladda upp data till din lokala SQL Server
Vi använder [NYC Taxi datauppsättning](https://chriswhong.com/open-data/foil_nyc_taxi/) för att visa migreringsprocessen. Den NYC Taxi datauppsättning är tillgänglig, som anges i det inlägget, på Azure blob storage [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Uppgifterna har två filer, trip_data.csv-filen, som innehåller reseinformation, och trip_far.csv-filen, som innehåller information om det pris som betalas för varje resa. Ett exempel och beskrivning av dessa filer finns i [NYC Taxi Trips Dataset Beskrivning](sql-walkthrough.md#dataset).

Du kan antingen anpassa proceduren som anges här till en uppsättning av dina egna data eller följa stegen enligt beskrivningen med hjälp av NYC Taxi-datauppsättningen. Om du vill överföra NYC Taxi-datauppsättningen till din lokala SQL Server-databas följer du proceduren som beskrivs i [Massimportdata till SQL Server Database](sql-walkthrough.md#dbload). Dessa instruktioner gäller för en SQL Server på en virtuell Azure-dator, men proceduren för överföring till den lokala SQL Server är densamma.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>Skapa en Azure Data Factory
Instruktionerna för att skapa en ny Azure Data Factory och en resursgrupp i [Azure-portalen](https://portal.azure.com/) tillhandahålls [Skapa en Azure Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Ge den nya ADF-instansen *adfdsp* ett namn och ge resursgruppen namnet *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installera och konfigurera Azure Data Factory Integration Runtime
Integration Runtime är en kundhanterad dataintegrationsinfrastruktur som används av Azure Data Factory för att tillhandahålla dataintegrationsfunktioner i olika nätverksmiljöer. Den här körningen kallades tidigare "Data Management Gateway".

Om du vill ställa in [följer du instruktionerna för att skapa en pipeline](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Skapa länkade tjänster för att ansluta till dataresurserna
En länkad tjänst definierar den information som behövs för Att Azure Data Factory ska kunna ansluta till en dataresurs. Vi har tre resurser i det här scenariot för vilka länkade tjänster behövs:

1. Lokalt SQL Server
2. Azure Blob Storage
3. Azure SQL Database

Steg-för-steg-proceduren för att skapa länkade tjänster finns i [Skapa länkade tjänster](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definiera och skapa tabeller för att ange hur datauppsättningarna ska komma åt
Skapa tabeller som anger datauppsättningarnas struktur, plats och tillgänglighet med följande skriptbaserade procedurer. JSON-filer används för att definiera tabellerna. Mer information om strukturen för dessa filer finns i [Datauppsättningar](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Du bör `Add-AzureAccount` köra cmdlet innan du kör cmdleten [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) för att bekräfta att rätt Azure-prenumeration har valts för kommandokörningen. Dokumentation av den här cmdleten finns i [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

De JSON-baserade definitionerna i tabellerna använder följande namn:

* **Tabellnamnet** på den lokala SQL-servern är *nyctaxi_data*
* **behållarnamnet** i Azure Blob Storage-kontot är *containernamn*

Tre tabelldefinitioner behövs för den här ADF-pipelinen:

1. [SQL lokalt tabell](#adf-table-onprem-sql)
2. [Blob-tabell](#adf-table-blob-store)
3. [SQL Azure-tabell](#adf-table-azure-sql)

> [!NOTE]
> Dessa procedurer använder Azure PowerShell för att definiera och skapa ADF-aktiviteter. Men dessa uppgifter kan också utföras med hjälp av Azure-portalen. Mer information finns i [Skapa datauppsättningar](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL lokalt tabell
Tabelldefinitionen för den lokala SQL Server anges i följande JSON-fil:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Kolumnnamnen togs inte med här. Du kan markera kolumnnamnen genom att inkludera dem här (mer information finns i dokumentationsavsnittet för [ADF.](../../data-factory/copy-activity-overview.md)

Kopiera JSON-definitionen av tabellen till en fil som kallas *onpremtabledef.json-fil* och spara den på en känd plats (här antas vara *C:\temp\onpremtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blob-tabell
Definitionen för tabellen för utdatabloloben finns i följande (detta mappar intövda data från lokalt till Azure-blob):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definitionen av tabellen till en fil som kallas *bloboutputtabledef.json-fil* och spara den på en känd plats (här antas vara *C:\temp\bloboutputtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure-tabell
Definitionen för tabellen för SQL Azure-utdata finns i följande (det här schemat mappar data som kommer från bloben):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopiera JSON-definitionen av tabellen till en fil som heter *AzureSqlTable.json-fil* och spara den på en känd plats (här antas vara *C:\temp\AzureSqlTable.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definiera och skapa pipelinen
Ange de aktiviteter som tillhör pipelinen och skapa pipelinen med följande skriptbaserade procedurer. En JSON-fil används för att definiera pipeline-egenskaperna.

* Skriptet förutsätter att **pipelinenamnet** är *AMLDSProcessPipeline*.
* Observera också att vi ställer in periodiciteten för pipelinen som ska utföras dagligen och använder standardkörningstiden för jobbet (12:00 UTC).

> [!NOTE]
> Följande procedurer använder Azure PowerShell för att definiera och skapa ADF-pipelinen. Men den här uppgiften kan också utföras med hjälp av Azure-portalen. Mer information finns i [Skapa pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Med hjälp av de tabelldefinitioner som tidigare angetts pipelinedefinitionen för ADF på följande sätt:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopiera den här JSON-definitionen av pipelinen till en fil som kallas *pipelinedef.json-fil* och spara den på en känd plats (här antas vara *C:\temp\pipelinedef.json*). Skapa pipelinen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Starta pipelinen
Pipelinen kan nu köras med följande kommando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Parametervärdena *startdatum* och *enddate* måste ersättas med de faktiska datum som du vill att pipelinen ska köras mellan.

När pipelinen körs bör du kunna se data visas i behållaren som valts för blob, en fil per dag.

Vi har inte utnyttjat de funktioner som tillhandahålls av ADF för att leda data stegvis. Mer information om hur du gör detta och andra funktioner som tillhandahålls av ADF finns i [ADF-dokumentationen](https://azure.microsoft.com/services/data-factory/).
