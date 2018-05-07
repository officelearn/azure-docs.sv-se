---
title: Hantera Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet | Microsoft Docs
description: Lär dig att hantera Data Lake Analytics-konton, datakällor, jobb och användare som använder Azure CLI
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: 9504e1fcbb0128f538cb6e959cf7586f24827836
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Hantera Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig mer om att hantera Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure CLI. Om du vill se avsnitt om med andra verktyg klickar du på flikväljaren ovan.


**Förutsättningar**

Innan du påbörjar den här självstudien måste du ha följande resurser:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Se [Installera och konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Hämta och installera **förhandsversionen** [Azure CLI-verktyg](https://github.com/MicrosoftBigData/AzureDataLake/releases) för att kunna slutföra den här demon.

* Autentisera med hjälp av den `az login` kommando och välj den prenumeration som du vill använda. Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Du kan nu komma åt Data Lake Analytics och Data Lake Store-kommandon. Kör följande kommando för att visa en lista med Data Lake Store och Data Lake Analytics-kommandon:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Hantera konton

Du måste ha ett Data Lake Analytics-konto innan du kör alla Data Lake Analytics-jobb. Till skillnad från Azure HDInsight betalar inte du för Analytics-konto när ett jobb inte körs. Du betalar bara för den tid när den körs ett jobb.  Mer information finns i [översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Skapa konton

Kör följande kommando för att skapa ett Data Lake-konto 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Uppdatera konton

Kommandot uppdaterar egenskaperna för ett befintligt Data Lake Analytics-konto

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lista över konton

Lista över Data Lake Analytics-konton inom en viss resursgrupp

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Hämta information för ett konto

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>Ta bort ett konto

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>Hantera datakällor

Data Lake Analytics stöder för närvarande följande två datakällor:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

När du skapar en Analytics-kontot, anger du ett Azure Data Lake Storage-konto för att standardkontot för lagring. Data Lake-standardlagringskontot används för att spara jobbet metadata och jobbet granskningsloggar. När du har skapat ett Analytics-konto kan du lägga till ytterligare Data Lake-lagringskontona och/eller Azure Storage-konto. 

### <a name="find-the-default-data-lake-store-account"></a>Hitta Data Lake Store-standardkontot

Du kan visa Data Lake Store-standardkontot används genom att köra den `az dla account show` kommando. Konto för standardnamnet finns under egenskapen defaultDataLakeStoreAccount.

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>Lägg till ytterligare Blob storage-konton

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> Endast Blob storage kortnamn stöds. Använd inte FQDN, till exempel ”myblob.blob.core.windows.net”.
> 

### <a name="add-additional-data-lake-store-accounts"></a>Lägga till ytterligare Data Lake Store-konton

Kommandot uppdaterar det angivna Data Lake Analytics-kontot med ett ytterligare Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Uppdatera befintlig datakälla

Så här uppdaterar en befintlig nyckel för Blob storage-konto:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista över datakällor:

Så här visar Data Lake Store-konton:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Listan Blob storage-konto:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Datakälla för data Lake Analytics-lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Ta bort datakällor:
Ta bort ett Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Ta bort ett Blob storage-konto:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Hantera jobb
Du måste ha ett Data Lake Analytics-konto innan du kan skapa ett jobb.  Mer information finns i [hantera Data Lake Analytics-konton](#manage-accounts).

### <a name="list-jobs"></a>Lista över jobb

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Datakälla för data Lake Analytics-lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Hämta jobbinformation

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>Skicka jobb

> [!NOTE]
> Standard-grad av parallellitet för ett jobb är 1 standardprioritet för ett jobb är 1000.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Avbryta jobb
Använd kommandot listan för att hitta jobb-id och sedan använda Avbryt om du vill avbryta jobbet.

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="pipelines-and-recurrences"></a>Pipelines och upprepningar

**Hämta information om pipelines och upprepningar**

Använd `az dla job pipeline`-kommandon för att visa information om pipeline för jobb som skickats tidigare.

```
az dla job pipeline list --account "<Data Lake Analytics Account Name>"

az dla job pipeline show --account "<Data Lake Analytics Account Name>" --pipeline-identity "<Pipeline ID>"
```

Använd `az dla job recurrence`-kommandon för att visa information om upprepningar för jobb som skickats tidigare.

```
az dla job recurrence list --account "<Data Lake Analytics Account Name>"

az dla job recurrence show --account "<Data Lake Analytics Account Name>" --recurrence-identity "<Recurrence ID>"
```


## <a name="use-azure-resource-manager-groups"></a>Använda Azure Resource Manager-grupper
Programmen består vanligtvis av flera komponenter, t.ex. webbapp, databas, databasserver, lagring och tredjepartstjänster. Azure Resource Manager kan du arbeta med resurserna i ditt program som en grupp som kallas en Azure-resursgrupp. Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser i programmet i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Du kan tydliggöra fakturering för din organisation genom att visa upplyfta kostnader för hela gruppen. Mer information finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Data Lake Analytics-tjänsten kan innefatta följande komponenter:

* Azure Data Lake Analytics-konto
* Nödvändiga standardkontot för lagring av Azure Data Lake
* Ytterligare Azure Data Lake Storage-konton
* Ytterligare Azure Storage-konton

Du kan skapa alla dessa komponenter under en Resource Manager-gruppen så att de blir enklare att hantera.

![Azure Data Lake Analytics-konto och lagring](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Ett Data Lake Analytics-konto och beroende storage-konton måste finnas i samma Azure-datacenter.
Resource Manager-gruppen kan dock finnas i olika datacenter.  

## <a name="see-also"></a>Se också
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

