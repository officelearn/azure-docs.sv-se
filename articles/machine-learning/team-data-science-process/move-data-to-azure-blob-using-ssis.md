---
title: Flytta Blob storage-data med SSIS-anslutningsappar - Team Data Science Process
description: Lär dig hur du flyttar data till eller från Azure Blob Storage med hjälp av SQL Server Integration Services Feature Pack för Azure.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720880"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytta data till eller från Azure Blob Storage med SSIS-anslutningsappar
[SQL Server Integration Services Feature Pack för Azure](https://msdn.microsoft.com/library/mt146770.aspx) innehåller komponenter för att ansluta till Azure, överföra data mellan Azure och lokala data källor och bearbeta data som lagras i Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

När kunderna har flyttat lokala data till molnet kan de komma åt sina data från valfri Azure-tjänst för att utnyttja den fulla kraften i serien med Azure-tekniker. Data kan användas senare, till exempel i Azure Machine Learning eller i ett HDInsight-kluster.

Exempel på hur du använder dessa Azure-resurser finns i genom gången av [SQL](sql-walkthrough.md) och [HDInsight](hive-walkthrough.md) .

En beskrivning av kanoniska scenarier som använder SSIS för att utföra affärs behov som är vanliga i scenarier med hybrid data integrering finns i [göra mer med SQL Server Integration Services Feature Pack för Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogg.

> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Förutsättningar
För att utföra de uppgifter som beskrivs i den här artikeln måste du ha en Azure-prenumeration och ett Azure Storage-konto konfigurerat. Du behöver Azure Storage konto namnet och konto nyckeln för att ladda upp eller hämta data.

* Information om hur du konfigurerar en **Azure-prenumeration**finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett **lagrings konto** och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

Om du vill använda **SSIS-anslutningarna**måste du ladda ned:

* **SQL Server 2014 eller 2016 standard (eller senare)** : installera inkluderar SQL Server Integration Services.
* **Microsoft SQL Server 2014-eller 2016 Integration Services Feature Pack för Azure**: dessa anslutningar kan laddas ned från [SQL Server 2014 integrerings tjänster](https://www.microsoft.com/download/details.aspx?id=47366) och [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) -sidor.

> [!NOTE]
> SSIS installeras med SQL Server, men ingår inte i Express-version. Information om vilka program som ingår i olika versioner av SQL Server finns i [SQL Server-versioner](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Utbildningsmaterial på SSIS finns i [praktiska övningar för SSIS](https://www.microsoft.com/sql-server/training-certification)

Information om hur du kommer igång med SISS för att skapa enkla paket för extrahering, transformering och inläsning (ETL) finns i [självstudier för SSIS: skapa ett enkelt ETL-paket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Hämta NYC Taxi datauppsättningen
Exemplet som beskrivs här använder en offentligt tillgänglig data uppsättning – [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) -datauppsättningen. Datauppsättningen består av cirka 173 miljoner taxi bilar i NYC år 2013. Det finns två typer av data: resa information om data och avgiften data. Eftersom det finns en fil för varje månad har vi 24 filer, som var och en är ungefär 2 GB okomprimerad.

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
För att flytta data med hjälp av SSIS Feature Pack från lokalt till Azure Blob Storage, använder vi en instans av [**Azure Blob upload-aktiviteten**](https://msdn.microsoft.com/library/mt146776.aspx), som visas här:

![Konfigurera –--virtuell dator för datavetenskap](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Här beskrivs de parametrar som används för aktiviteten:

| Fält | Description |
| --- | --- |
| **AzureStorageConnection** |Anger en befintlig Azure Storage anslutnings hanterare eller skapar en ny som refererar till ett Azure Storage konto som pekar på var BLOB-filerna finns. |
| **BlobContainer** |Anger namnet på BLOB-behållaren som innehåller de överförda filerna som blobbar. |
| **BlobDirectory** |Anger den blob-katalogen där den överförda filen lagras som en blockblob. Blob-katalogen är en virtuell hierarkisk struktur. Om blobben som redan finns it ia ersättas. |
| **LocalDirectory** |Anger den lokala katalogen som innehåller filerna som ska laddas upp. |
| **Sökväg** |Anger ett Namnfilter för att markera filer med det angivna namnmönstret. T. ex. MySheet\*.xls\* innehåller filer som MySheet001.xls och MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Anger ett tidsfilter för intervallet. Filer som ändrades efter *TimeRangeFrom* och innan *TimeRangeTo* tas med. |

> [!NOTE]
> **AzureStorageConnection** -autentiseringsuppgifterna måste vara korrekta och **BlobContainer** måste existera innan överföringen görs.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Hämta data från Azure blob storage
Om du vill hämta data från Azure Blob Storage till lokal lagring med SSIS, använder du en instans av [hämtnings uppgiften för Azure-bloben](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Mer avancerade scenarier för Azure-SSIS
Funktionspaketet för SSIS möjliggör mer komplexa flöden som ska hanteras av paketering uppgifter tillsammans. Blob-data kan till exempel feed direkt i ett HDInsight-kluster, vars utdata kan hämtas tillbaka till en blob och sedan till den lokala lagringen. SSIS kan köra Hive och Pig-jobb på ett HDInsight-kluster med ytterligare SSIS-anslutningsappar:

* Om du vill köra ett Hive-skript på ett Azure HDInsight-kluster med SSIS använder du [Azure HDInsight Hive-aktiviteten](https://msdn.microsoft.com/library/mt146771.aspx).
* Om du vill köra ett gris-skript på ett Azure HDInsight-kluster med SSIS använder du [aktiviteten Azure HDInsight gris](https://msdn.microsoft.com/library/mt146781.aspx).

