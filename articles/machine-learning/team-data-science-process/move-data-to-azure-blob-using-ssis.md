---
title: Flytta Blob Storage-data med SSIS-kopplingar – team data science process
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
ms.openlocfilehash: ad87272749011c81c1040825da3f3c53858a55bd
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85322864"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytta data till eller från Azure Blob Storage med SSIS-kopplingar
[SQL Server Integration Services Feature Pack för Azure](https://msdn.microsoft.com/library/mt146770.aspx) innehåller komponenter för att ansluta till Azure, överföra data mellan Azure och lokala data källor och bearbeta data som lagras i Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

När kunderna har flyttat lokala data till molnet kan de komma åt sina data från valfri Azure-tjänst för att utnyttja den fulla kraften i serien med Azure-tekniker. Data kan användas senare, till exempel i Azure Machine Learning eller i ett HDInsight-kluster.

Exempel på hur du använder dessa Azure-resurser finns i genom gången av [SQL](sql-walkthrough.md) och [HDInsight](hive-walkthrough.md) .

En beskrivning av kanoniska scenarier som använder SSIS för att utföra affärs behov som är vanliga i scenarier med hybrid data integrering finns i [göra mer med SQL Server Integration Services Feature Pack för Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) blogg.

> [!NOTE]
> En fullständig introduktion till Azure Blob Storage finns i grunderna för [Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob service](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Krav
För att utföra de uppgifter som beskrivs i den här artikeln måste du ha en Azure-prenumeration och ett Azure Storage-konto konfigurerat. Du behöver Azure Storage konto namnet och konto nyckeln för att ladda upp eller hämta data.

* Information om hur du konfigurerar en **Azure-prenumeration**finns i [den kostnads fria utvärderings perioden på en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar ett **lagrings konto** och hämtar information om konton och nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

Om du vill använda **SSIS-anslutningarna**måste du ladda ned:

* **SQL Server 2014 eller 2016 standard (eller senare)**: installera inkluderar SQL Server Integration Services.
* **Microsoft SQL Server 2014-eller 2016 Integration Services Feature Pack för Azure**: dessa anslutningar kan laddas ned från [SQL Server 2014 integrerings tjänster](https://www.microsoft.com/download/details.aspx?id=47366) och [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) -sidor.

> [!NOTE]
> SSIS installeras med SQL Server, men ingår inte i Express versionen. Information om vilka program som ingår i olika versioner av SQL Server finns i [SQL Server-versioner](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Utbildningsmaterial på SSIS finns i [praktiska övningar för SSIS](https://www.microsoft.com/sql-server/training-certification)

Information om hur du kommer igång med SISS för att skapa enkla paket för extrahering, transformering och inläsning (ETL) finns i [självstudier för SSIS: skapa ett enkelt ETL-paket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Hämta NYC taxi-datauppsättning
Exemplet som beskrivs här använder en offentligt tillgänglig data uppsättning – [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) -datauppsättningen. Data uppsättningen består av cirka 173 000 000 taxi-tillägg i NYC år 2013. Det finns två typer av data: information om rese uppgifter och pris uppgifter. Eftersom det finns en fil för varje månad har vi 24 filer, som var och en är ungefär 2 GB okomprimerad.

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob Storage
För att flytta data med hjälp av SSIS Feature Pack från lokalt till Azure Blob Storage, använder vi en instans av [**Azure Blob upload-aktiviteten**](https://msdn.microsoft.com/library/mt146776.aspx), som visas här:

![Konfigurera-data-vetenskap-VM](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

De parametrar som aktiviteten använder beskrivs här:

| Fält | Beskrivning |
| --- | --- |
| **AzureStorageConnection** |Anger en befintlig Azure Storage anslutnings hanterare eller skapar en ny som refererar till ett Azure Storage konto som pekar på var BLOB-filerna finns. |
| **BlobContainer** |Anger namnet på BLOB-behållaren som innehåller de överförda filerna som blobbar. |
| **BlobDirectory** |Anger BLOB-katalogen där den överförda filen lagras som en Block-Blob. BLOB-katalogen är en virtuell hierarkisk struktur. Om blobben redan finns, ersätts den av. |
| **LocalDirectory** |Anger den lokala katalog som innehåller de filer som ska överföras. |
| **Sökväg** |Anger ett namn filter för att välja filer med angivet namn mönster. Till exempel innehåller bladet \* . xls \* filer som MySheet001.xls och MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Anger ett intervall filter. Filer som ändrades efter *TimeRangeFrom* och innan *TimeRangeTo* tas med. |

> [!NOTE]
> **AzureStorageConnection** -autentiseringsuppgifterna måste vara korrekta och **BlobContainer** måste existera innan överföringen görs.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Hämta data från Azure Blob Storage
Om du vill hämta data från Azure Blob Storage till lokal lagring med SSIS, använder du en instans av [hämtnings uppgiften för Azure-bloben](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Mer avancerade SSIS – Azure-scenarier
SSIS Feature Pack gör det möjligt för mer komplexa flöden att hanteras genom att paketera uppgifter tillsammans. BLOB-data kan till exempel matas direkt till ett HDInsight-kluster, vars utdata kunde hämtas tillbaka till en blob och sedan till lokal lagring. SSIS kan köra Hive-och gris-jobb i ett HDInsight-kluster med hjälp av ytterligare SSIS-anslutningar:

* Om du vill köra ett Hive-skript på ett Azure HDInsight-kluster med SSIS använder du [Azure HDInsight Hive-aktiviteten](https://msdn.microsoft.com/library/mt146771.aspx).
* Om du vill köra ett gris-skript på ett Azure HDInsight-kluster med SSIS använder du [aktiviteten Azure HDInsight gris](https://msdn.microsoft.com/library/mt146781.aspx).

