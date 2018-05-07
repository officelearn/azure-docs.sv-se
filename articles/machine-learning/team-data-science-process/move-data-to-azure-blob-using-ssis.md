---
title: Flytta Data till eller från Azure Blob Storage med hjälp av SSIS-anslutningar | Microsoft Docs
description: Flytta Data till eller från Azure Blob Storage med hjälp av SSIS-anslutningar.
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 96a1b5fb-34d1-4b9b-8d99-2bb8289e0398
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 39feca23532264c1f4c17017f89b064af09e9c1c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Flytta data till eller från Azure Blob Storage med hjälp av SSIS-anslutningar
Den [Funktionspaketet för SQL Server Integration Services för Azure](https://msdn.microsoft.com/library/mt146770.aspx) innehåller komponenter för att ansluta till Azure, överföra data mellan Azure och lokala datakällor och bearbetning av data lagras i Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

När kunder har flyttat lokala data i molnet kan de komma åt den från alla Azure-tjänsten kan utnyttja alla fördelar med uppsättning tekniker för Azure. Den kan användas, till exempel i Azure Machine Learning eller på ett HDInsight-kluster.

Detta är vanligtvis vara det första steget för den [SQL](sql-walkthrough.md) och [HDInsight](hive-walkthrough.md) genomgång.

En beskrivning av kanoniska scenarier som använder SSIS för att utföra affärsbehov som vanligt i hybrid data integrationsscenarier finns [göra mer med SQL Server Integration Services Feature Pack för Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) blogg.

> [!NOTE]
> En fullständig introduktion till Azure blob storage finns i [grunderna i Azure Blob](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) och [Azure Blob-tjänsten](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Förutsättningar
Du måste ha en Azure-prenumeration och skapa ett Azure storage-konto för att utföra de uppgifter som beskrivs i den här artikeln. Du måste känna till din Azure storage namn och kontonyckel att överföra eller hämta data.

* Att ställa in en **Azure-prenumeration**, se [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).
* Anvisningar om hur du skapar en **lagringskonto** och för att hämta konto och viktig information, se [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

Att använda den **SSIS kopplingar**, måste du hämta:

* **SQL Server 2014 eller Standard 2016 (eller senare)**: Installera omfattar SQL Server Integration Services.
* **Microsoft SQL Server 2014 eller 2016 Integration Services Feature Pack för Azure**: dessa kan hämtas, från den [SQL Server 2014 Integration Services](http://www.microsoft.com/download/details.aspx?id=47366) och [SQL Server 2016-Integration Tjänster](https://www.microsoft.com/download/details.aspx?id=49492) sidor.

> [!NOTE]
> SSIS installeras med SQL Server, men det ingår inte i Express-version. Information om vilka program som ingår i olika utgåvor av SQL Server finns [SQL Server-versioner](http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Utbildningsmaterial på SSIS finns [händerna på utbildning för SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Information om hur du hämtar upp och körs använder SISS för att bygga enkel extrahering, transformering och laddning (ETL) paket, se [SSIS-Självstudier: skapa ett enkelt ETL-paket](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Hämta NYC Taxi dataset
Det exempel som beskrivs här använder en offentligt tillgängliga datauppsättningen--den [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset. Dataset består av om 173 miljoner taxi bilar i NYC år 2013. Det finns två typer av data: resa information data och avgiften data. Det finns en fil för varje månad, har vi 24 filer i alla som är cirka 2GB okomprimerade.

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure blob storage
Om du vill flytta data med hjälp av SSIS Funktionspaket för från lokala till Azure blob storage, vi använder en instans av den [ **Azure Blob överför uppgiften**](https://msdn.microsoft.com/library/mt146776.aspx), visas här:

![Konfigurera-data-vetenskap-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Här beskrivs de parametrar som används för aktiviteten:

| Fält | Beskrivning |
| --- | --- |
| **AzureStorageConnection** |Anger en befintlig Azure Storage Connection Manager eller skapar en ny som refererar till ett Azure storage-konto som pekar på där blob-filerna finns. |
| **BlobContainer** |Anger namnet på blob-behållaren som innehåller de överförda filerna som blobar. |
| **BlobDirectory** |Anger blob-katalogen där den överförda filen lagras som en blockblobb. Blob-katalogen är en virtuell hierarkisk struktur. Om det finns redan en blob, it ia ersättas. |
| **LocalDirectory** |Anger den lokala katalogen som innehåller filer som ska överföras. |
| **Filnamn** |Anger ett Namnfilter för att välja filer med det angivna namnmönstret. Till exempel MySheet\*.xls\* innehåller filer som MySheet001.xls och MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Anger ett tidsfilter för intervallet. Filer som modifierats efter *TimeRangeFrom* och innan *TimeRangeTo* ingår. |

> [!NOTE]
> Den **AzureStorageConnection** autentiseringsuppgifter måste vara korrekt och **BlobContainer** måste finnas innan överföringen görs.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Hämta data från Azure blob storage
Om du vill hämta data från Azure blob storage till lokal lagring med SSIS, använder du en instans av den [Azure Blob överför uppgiften](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Mer avancerade SSIS-Azure-scenarier
Funktionspaket SSIS möjliggör mer komplexa flöden som ska hanteras av paketering uppgifter tillsammans. Blob-data kan till exempel feed direkt till ett HDInsight-kluster, vars utdata gick att hämta tillbaka till en blob och lokala lagring. SSIS kan köra Hive och Pig-jobb på ett HDInsight-kluster med hjälp av ytterligare SSIS-anslutningar:

* Kör en Hive-skript i ett Azure HDInsight-kluster med SSIS med [Azure HDInsight Hive uppgiften](https://msdn.microsoft.com/library/mt146771.aspx).
* Kör en Pig-skriptet på Azure HDInsight-kluster med SSIS med [Azure HDInsight Pig aktiviteten](https://msdn.microsoft.com/library/mt146781.aspx).

