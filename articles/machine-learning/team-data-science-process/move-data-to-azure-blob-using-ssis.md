---
title: Flytta Blob-lagringsdata med SSIS-kopplingar - Team Data Science Process
description: Lär dig hur du flyttar data till eller från Azure Blob Storage med SQL Server Integration Services Feature Pack för Azure.
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
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720880"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytta data till eller från Azure Blob Storage med SSIS-kopplingar
[SQL Server Integration Services Feature Pack för Azure](https://msdn.microsoft.com/library/mt146770.aspx) tillhandahåller komponenter för att ansluta till Azure, överföra data mellan Azure och lokala datakällor och bearbeta data som lagras i Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

När kunderna har flyttat lokala data till molnet kan de komma åt sina data från valfri Azure-tjänst för att utnyttja den fulla kraften i azure-teknikpaketet. Data kan sedan användas, till exempel i Azure Machine Learning eller i ett HDInsight-kluster.

Exempel för att använda dessa [Azure-resurser](sql-walkthrough.md) finns i SQL- och [HDInsight-genomgångarna.](hive-walkthrough.md)

En diskussion om kanoniska scenarier som använder SSIS för att utföra affärsbehov som är vanliga i hybriddataintegrationsscenarier finns i [Göra mer med SQL Server Integration Services Feature Pack för Azure-blogg.](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx)

> [!NOTE]
> En fullständig introduktion till Azure blob-lagring finns i [Azure Blob Basics](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Krav
För att kunna utföra de uppgifter som beskrivs i den här artikeln måste du ha en Azure-prenumeration och ett Azure Storage-konto konfigurerat. Du behöver Azure Storage-kontonamnet och kontonyckeln för att ladda upp eller ladda ned data.

* Information om hur du konfigurerar en **Azure-prenumeration**finns i [Kostnadsfri en månads provperiod](https://azure.microsoft.com/pricing/free-trial/).
* Instruktioner om hur du skapar ett **lagringskonto** och för att hämta konto- och nyckelinformation finns i [Om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

Om du vill använda **SSIS-kopplingarna**måste du hämta:

* **SQL Server 2014 eller 2016 Standard (eller högre)**: Installera inkluderar SQL Server Integration Services.
* **Microsoft SQL Server 2014 eller 2016 Integration Services Feature Pack för Azure:** Dessa kopplingar kan hämtas från [sidorna SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) och SQL Server [2016 Integration Services.](https://www.microsoft.com/download/details.aspx?id=49492)

> [!NOTE]
> SSIS installeras med SQL Server, men ingår inte i Express-versionen. Information om vilka program som ingår i olika versioner av SQL Server finns i [SQL Server Editions](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

För utbildningsmaterial på SSIS, se [Hands On Training för SSIS](https://www.microsoft.com/sql-server/training-certification)

Information om hur du kommer igång med SISS för att skapa enkla utvinnings-, omvandlings- och belastningspaket (ETL) finns i [SSIS Tutorial: Skapa ett enkelt ETL-paket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Ladda ner NYC Taxi dataset
I exemplet som beskrivs här används en offentligt tillgänglig datauppsättning – NEW Taxi Trips-datauppsättningen. [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) Datauppsättningen består av cirka 173 miljoner taxiresor i NYC år 2013. Det finns två typer av data: reseinformationsdata och biljettdata. Eftersom det finns en fil för varje månad har vi 24 filer, som var och en är ca 2 GB okomprimerad.

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure-bloblagring
Om du vill flytta data med SSIS-funktionspaketet från lokalt till Azure-bloblagring använder vi en instans av [**Azure Blob Upload Task**](https://msdn.microsoft.com/library/mt146776.aspx), som visas här:

![konfigurera-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De parametrar som aktiviteten använder beskrivs här:

| Field | Beskrivning |
| --- | --- |
| **AzureStorageAnslutning** |Anger en befintlig Azure Storage Connection Manager eller skapar en ny som refererar till ett Azure Storage-konto som pekar på var blob-filerna finns. |
| **BlobContainer** |Anger namnet på blob-behållaren som innehåller de uppladdade filerna som blobbar. |
| **BlobDirectory** |Anger blob-katalogen där den uppladdade filen lagras som en blockblob. Blob-katalogen är en virtuell hierarkisk struktur. Om blobben redan finns, ersätts den. |
| **Lokalkatalog** |Anger den lokala katalogen som innehåller de filer som ska överföras. |
| **Filnamn** |Anger ett namnfilter för att markera filer med det angivna namnmönstret. MySheet\*.xls\* innehåller till exempel filer som MySheet001.xls och MySheetABC.xlsx |
| **Tidsaviseringfrån/tidsarrangemangtill** |Anger ett tidsintervallfilter. Filer som ändrats efter *TimeRangeFrom* och före *TimeRangeTo* inkluderas. |

> [!NOTE]
> **AzureStorageConnection-autentiseringsuppgifterna** måste vara korrekta och **BlobContainer** måste finnas innan överföringen görs.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Hämta data från Azure blob storage
Om du vill hämta data från Azure blob storage till lokal lagring med SSIS använder du en instans av [Azure Blob Download Task](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Mer avancerade SSIS-Azure-scenarier
SSIS-funktionspaketet gör det möjligt att hantera mer komplexa flöden med förpackningsuppgifter tillsammans. Blob-data kan till exempel matas direkt till ett HDInsight-kluster, vars utdata kan hämtas tillbaka till en blob och sedan till lokal lagring. SSIS kan köra Hive- och Pig-jobb på ett HDInsight-kluster med hjälp av ytterligare SSIS-kopplingar:

* Om du vill köra ett Hive-skript i ett Azure HDInsight-kluster med SSIS använder du [Azure HDInsight Hive Task](https://msdn.microsoft.com/library/mt146771.aspx).
* Om du vill köra ett Pig-skript i ett Azure HDInsight-kluster med SSIS använder du [Azure HDInsight Pig Task](https://msdn.microsoft.com/library/mt146781.aspx).

