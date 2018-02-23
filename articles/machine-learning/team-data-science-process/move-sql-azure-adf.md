---
title: "Flytta data från en lokal SQL Server till SQL Azure med Azure Data Factory | Microsoft Docs"
description: "Ställ in en ADM-pipeline som composes två data migreringsaktiviteter som tillsammans flyttar data dagligen mellan databaser på lokalt och i molnet."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 05884fd39db284e268f31987e5ad7a47b9f87ebf
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory
Det här avsnittet visar hur du flyttar data från en lokal SQL Server-databas till en SQL Azure Database via Azure Blob Storage med hjälp av Azure Data Factory (ADM).

En tabell som sammanfattar olika alternativ för att flytta data till en Azure SQL Database finns [flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Introduktion: Vad är ADF och när den används för att migrera data?
Azure Data Factory är en helt hanterad molnbaserade integration datatjänst som samordnar och automatiserar flytt och transformering av data. Viktiga begrepp i ADF-modellen är pipeline. En pipeline är en logisk gruppering av aktiviteter, som definierar åtgärderna som ska utföras på de data som finns i DataSet. Länkade tjänster används för att definiera den information som behövs för Data Factory för att ansluta till dataresurser.

Med ADF, kan befintliga databearbetning tjänster sammanställas till pipeline-data som är hög tillgänglighet och hanterad i molnet. Dessa data pipelines kan schemaläggas för att mata in, förbereda, transformera, analysera och publicera data och ADF hanterar och samordnar komplexa data och beroenden för bearbetning. Lösningar kan snabbt inbyggda och distribueras i molnet, ansluta ett växande antal lokalt och molntjänster datakällor.

Överväg att använda ADF:

* När data ska migreras kontinuerligt i ett hybridscenario som har åtkomst till både lokalt och molnresurser
* När data är överförd eller måste ändras eller har affärslogik som lagts till när migreras.

ADF möjliggör schemaläggningen och övervakning av jobb med hjälp av enkla JSON-skript som hanterar flödet av data regelbundet. ADF har även andra funktioner som stöd för komplex. Mer information om ADF finns i dokumentationen på [Azure Data Factory (ADM)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Scenariot
Vi har skapat en ADM-pipeline som composes två aktiviteter för migrering av data. Tillsammans flytta data dagligen mellan en lokal SQL-databas och en Azure SQL Database i molnet. Det finns två aktiviteter:

* Kopiera data från en lokal SQL Server-databas till ett Azure Blob Storage-konto
* Kopiera data från Azure Blob Storage-konto till en Azure SQL Database.

> [!NOTE]
> Steg som visas här har anpassats från mer detaljerad genomgång som tillhandahålls av ADF-teamet: [flytta data mellan lokala källor och moln med Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) referenser till de relevanta avsnitten i avsnittet Ange när det är lämpligt.
>
>

## <a name="prereqs"></a>Förhandskrav
Den här kursen förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du kan använda ett Azure storage-konto för att lagra data i den här kursen. Om du inte har ett Azure storage-konto finns i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artikel. När du har skapat lagringskontot som du behöver hämta nyckeln konto används för åtkomst till lagringen. Se [hantera åtkomstnycklar för lagring](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Åtkomst till en **Azure SQL Database**. Om du måste skapa en Azure SQL Database, avsnittet [komma igång med Microsoft Azure SQL Database ](../../sql-database/sql-database-get-started.md) innehåller information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> Den här proceduren använder den [Azure-portalen](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Ladda upp data till din lokala SQL Server
Vi använder den [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/) att demonstrera migreringsprocessen. NYC Taxi dataset är tillgängligt, enligt beskrivningen i det inlägget på Azure-blobblagring [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Data har två filer, trip_data.csv-fil som innehåller information om kommunikation, och filen trip_far.csv, som innehåller information om avgiften betalat för varje resa. Ett exempel och en beskrivning av dessa filer finns i [NYC Taxi resor Dataset beskrivning](sql-walkthrough.md#dataset).

Du kan anpassa det förfarande som anges här till en uppsättning med dina egna data eller Följ stegen som beskrivs med NYC Taxi dataset. Överför NYC Taxi dataset till din lokala SQL Server-databas genom att följa proceduren som beskrivs i [Bulk importera Data till SQL Server-databas](sql-walkthrough.md#dbload). Dessa instruktioner är för en SQL Server på en virtuell dator i Azure, men proceduren för att ladda upp till den lokala SQL Server är samma.

## <a name="create-adf"></a> Skapa ett Azure Data Factory
Instruktioner för att skapa en ny Azure Data Factory och en resursgrupp i den [Azure-portalen](https://portal.azure.com/) tillhandahålls [skapa ett Azure Data Factory](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory). Namnge den nya instansen ADF *adfdsp* och kalla resursgruppen skapade *adfdsprg*.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Installera och konfigurera upp Data Management Gateway
Om du vill aktivera din pipelines i ett Azure data factory för att arbeta med en lokal SQL Server som du behöver lägga till den som en länkad tjänst datafabriken. Om du vill skapa en länkad tjänst för en lokal SQL Server, måste du:

* Hämta och installera Microsoft Data Management Gateway till den lokala datorn.
* Konfigurera den länkade tjänsten för lokala datakällan som ska använda gatewayen.

Data Management Gateway Serialiserar och deserializes källa och mottagare data på den dator där den finns.

Ställa in instruktioner och information om Data Management Gateway finns [flytta data mellan lokala källor och moln med Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Skapa länkade tjänster att ansluta till dataresurser
En länkad tjänst definierar den information som behövs för Azure Data Factory för att ansluta till en Dataresurs. Vi har tre resurser i det här scenariot som krävs för länkade tjänster:

1. Lokal SQLServer
2. Azure Blob Storage
3. Azure SQL-databas

Stegvisa anvisningar för att skapa länkade tjänster finns i [Skapa länkade tjänster](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Definiera och skapa tabeller för att ange hur du kommer åt datauppsättningar
Skapa tabeller som anger strukturen, plats och tillgängligheten för datauppsättningar med följande skript-baserad. JSON-filer används för att definiera tabellerna. Mer information om strukturen för de här filerna finns [datauppsättningar](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Du bör köra de `Add-AzureAccount` cmdlet innan du kör den [ny AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet för att bekräfta att rätt Azure-prenumeration har valts för Kommandokörningen. Dokumentation för denna cmdlet finns [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

JSON-baserade definitionerna i tabellerna använda följande namn:

* den **tabellnamn** i den lokala SQL server är *nyctaxi_data*
* den **behållarnamn** i Azure Blob Storage-kontot är *containername*  

Tre tabelldefinitionerna krävs för den här ADF-pipelinen:

1. [Lokal SQL-tabell](#adf-table-onprem-sql)
2. [BLOB-tabell ](#adf-table-blob-store)
3. [SQL Azure-tabellen](#adf-table-azure-sql)

> [!NOTE]
> De här procedurerna använda Azure PowerShell för att definiera och skapa ADF-aktiviteter. Men dessa uppgifter kan också utföras med hjälp av Azure-portalen. Mer information finns i [skapa datauppsättningar](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
>
>

### <a name="adf-table-onprem-sql">Lokal SQL-tabell</a>
Tabelldefinitionen för lokala SQL Server har angetts för följande JSON-fil:

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

Kolumnnamnen ingick inte. Du kan välja på kolumnnamnen underordnad genom att inkludera dem här (information den [ADF dokumentationen](../../data-factory/v1/data-factory-data-movement-activities.md) avsnittet.

Kopiera JSON-definitionen av tabellen i en fil kallad *onpremtabledef.json* filen och spara den på en känd plats (här antas vara *C:\temp\onpremtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store">BLOB-tabell </a>
Definitionen för tabellen för platsen blob finns i följande (det här mappar infogade data från lokalt till Azure blob):

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

Kopiera JSON-definitionen av tabellen i en fil kallad *bloboutputtabledef.json* filen och spara den på en känd plats (här antas vara *C:\temp\bloboutputtabledef.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sql">SQL Azure-tabellen</a>
Definitionen för tabellen för SQL Azure utdata finns i följande (det här schemat mappar data från blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
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

Kopiera JSON-definitionen av tabellen i en fil kallad *AzureSqlTable.json* filen och spara den på en känd plats (här antas vara *C:\temp\AzureSqlTable.json*). Skapa tabellen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definiera och skapa pipelinen
Ange de aktiviteter som tillhör pipelinen och skapa pipeline med följande skript-baserad. En JSON-fil används för att definiera egenskaperna pipeline.

* Skriptet förutsätter att den **pipeline namnet** är *AMLDSProcessPipeline*.
* Observera också att vi anger periodiciteten för pipeline köras dag och använder standard körningstid för jobb (12: 00 UTC).

> [!NOTE]
> Följande procedurer använda Azure PowerShell för att definiera och skapa ADF-pipeline. Men den här uppgiften kan också utföras med hjälp av Azure-portalen. Mer information finns i [skapa pipeline](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

Med tabelldefinitionerna tidigare anges pipeline-definitionen för ADF enligt följande:

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

Kopiera den här JSON-definitionen i pipeline till en fil kallad *pipelinedef.json* filen och spara den på en känd plats (här antas vara *C:\temp\pipelinedef.json*). Skapa pipelinen i ADF med följande Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Starta Pipeline
Nu kan köra pipelinen med följande kommando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Den *startdate* och *enddate* parametervärden måste ersättas med verkliga datum som ska köra pipelinen.

När pipelinen körs, bör du kunna se data som visas i behållaren som valts för blob, en fil per dag.

Observera att vi inte utnyttjas funktionerna i ADF till pipe data inkrementellt. Mer information om hur du gör detta och andra funktioner som tillhandahålls av ADF finns i [ADF dokumentationen](https://azure.microsoft.com/services/data-factory/).
