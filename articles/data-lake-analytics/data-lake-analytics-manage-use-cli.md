---
title: Hantera Azure Data Lake Analytics med Azure CLI
description: I den här artikeln beskrivs hur du använder Azure CLI för att hantera Data Lake Analytics-jobb, datakällor & användare.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 69a48952ef273acb8cf7eb0ec5968e12b962b622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454371"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Hantera Azure Data Lake Analytics med hjälp av AZURE Command-line Interface (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med Hjälp av Azure CLI. Om du vill se hanteringsämnen med andra verktyg klickar du på fliken välj ovan.


**Krav**

Innan du påbörjar den här självstudien måste du ha följande resurser:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Se [Installera och konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Hämta och installera **förhandsversionen** [Azure CLI-verktyg](https://github.com/MicrosoftBigData/AzureDataLake/releases) för att kunna slutföra den här demon.

* Autentisera med `az login` kommandot och välj den prenumeration som du vill använda. Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Nu kan du komma åt kommandona DataSjöanalys och DataSjö store. Kör följande kommando för att lista kommandona DataSjölagring och DataSjöanalys:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Hantera konton

Innan du kör några DataSjöanalysjobb måste du ha ett DataSjöanalyskonto. Till skillnad från Azure HDInsight betalar du inte för ett Analytics-konto när det inte kör ett jobb. Du betalar bara för den tid då det körs ett jobb.  Mer information finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Skapa konton

Kör följande kommando för att skapa ett DataSjö-konto, 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Uppdatera konton

Följande kommando uppdaterar egenskaperna för ett befintligt DataSjöanalyskonto

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lista över konton

Lista DataSjöanalyskonton inom en viss resursgrupp

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Få information om ett konto

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Ta bort ett konto

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Hantera datakällor

DataSjöanalys stöder för närvarande följande två datakällor:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure-lagring](../storage/common/storage-introduction.md)

När du skapar ett Analytics-konto måste du ange att ett Azure Data Lake Storage-konto ska vara standardlagringskontot. Standardkontot för lagring av DataSjö används för att lagra jobbmetadata och jobbgranskningsloggar. När du har skapat ett Analytics-konto kan du lägga till ytterligare DataSjölagringskonton och/eller Azure Storage-konto. 

### <a name="find-the-default-data-lake-store-account"></a>Hitta standardkontot för DataSjölagring

Du kan visa standardkontot för Datasjölagring som används genom att köra `az dla account show` kommandot. Standardkontonamnet visas under egenskapen defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Lägga till ytterligare Blob-lagringskonton

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Endast korta Blob-lagringsnamn stöds. Använd inte FQDN, till exempel "myblob.blob.core.windows.net".
> 

### <a name="add-additional-data-lake-store-accounts"></a>Lägga till ytterligare datasjölagringskonton

Följande kommando uppdaterar det angivna DataSjöanalyskontot med ytterligare ett DataSjölagringskonto:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Uppdatera befintlig datakälla

Så här uppdaterar du en befintlig Blob-lagringskontonyckel:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista datakällor:

Så här listar du datasjölagringskontona:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Så här listar du Blob-lagringskontot:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Datakälla för datakälla för datasjöanalys i datasjöanalys](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Ta bort datakällor:
Så här tar du bort ett DataSjölagringskonto:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Så här tar du bort ett Blob-lagringskonto:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Hantera jobb
Du måste ha ett DataSjöanalyskonto innan du kan skapa ett jobb.  Mer information finns i [Hantera DataSjöanalyskonton](#manage-accounts).

### <a name="list-jobs"></a>Lista jobb

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Datakälla för datakälla för datasjöanalys i datasjöanalys](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Få jobbinformation

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Skicka jobb

> [!NOTE]
> Standardprioriteten för ett jobb är 1000 och standardgraden för parallellitet för ett jobb är 1.
> 
>    ```azurecli
>    az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
>    ```

### <a name="cancel-jobs"></a>Avbryta jobb
Använd kommandot List för att hitta jobb-ID:et och använd sedan avbryt för att avbryta jobbet.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines och upprepningar

**Hämta information om pipelines och upprepningar**

Använd `az dla job pipeline`-kommandon för att visa information om pipeline för jobb som skickats tidigare.

```azurecli
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Använd `az dla job recurrence`-kommandon för att visa information om upprepningar för jobb som skickats tidigare.

```azurecli
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```

## <a name="see-also"></a>Se även
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med Azure-portal](data-lake-analytics-manage-use-portal.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

