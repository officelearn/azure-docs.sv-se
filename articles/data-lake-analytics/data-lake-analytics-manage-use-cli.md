---
title: Hantera Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet
description: Den här artikeln beskriver hur du använder Azure CLI för att hantera Data Lake Analytics-jobb, datakällor och användare.
services: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: e265a46533264bbb1d437edbfe1bbfb3306614ad
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044831"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-command-line-interface-cli"></a>Hantera Azure Data Lake Analytics med hjälp av Azure-kommandoradsgränssnittet (CLI)

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Lär dig hur du hanterar Azure Data Lake Analytics-konton, datakällor, användare och jobb med hjälp av Azure CLI. Om du vill se avsnitt om med andra verktyg klickar du på flikväljaren ovan.


**Förutsättningar**

Innan du påbörjar den här självstudien måste du ha följande resurser:

* En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* Azure CLI. Se [Installera och konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

   * Hämta och installera **förhandsversionen** [Azure CLI-verktyg](https://github.com/MicrosoftBigData/AzureDataLake/releases) för att kunna slutföra den här demon.

* Autentisera med hjälp av den `az login` kommandot och välj den prenumeration som du vill använda. Mer information om autentisering med ett arbets- eller skolkonto finns i [Anslut till en Azure-prenumeration från Azure CLI](/cli/azure/authenticate-azure-cli).

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   Du kan nu komma åt Data Lake Analytics och Data Lake Store-kommandon. Kör följande kommando för att visa Data Lake Store och Data Lake Analytics-kommandon:

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>Hantera konton

Du måste ha ett Data Lake Analytics-konto innan du kör alla Data Lake Analytics-jobb. Till skillnad från Azure HDInsight betalar du inte för en Analytics-konto när den inte körs ett jobb. Du betalar bara för den tid när den körs ett jobb.  Mer information finns i [översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Skapa konton

Kör följande kommando för att skapa ett Data Lake-konto 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>Uppdatera konton

Följande kommando uppdaterar egenskaperna för ett befintligt Data Lake Analytics-konto

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>Lista över konton

Lista över Data Lake Analytics-konton inom en specifik resursgrupp

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>Få information på ett konto

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

När du skapar en Analytics-konto, anger du ett Azure Data Lake Storage-konto för att standardkontot för lagring. Data Lake storage-kontot används för att lagra granskningsloggar för jobbet metadata och jobb. När du har skapat en Analytics-konto kan du lägga till ytterligare Data Lake Storage-konton och/eller Azure Storage-konto. 

### <a name="find-the-default-data-lake-store-account"></a>Hitta Data Lake Store-kontot av standardtyp

Du kan visa standardkontot för Data Lake Store som används genom att köra den `az dla account show` kommando. Standard-kontonamnet visas under egenskapen defaultDataLakeStoreAccount.

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

Följande kommando uppdaterar det angivna Data Lake Analytics-kontot med en ytterligare Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>Uppdatera befintlig datakälla

Så här uppdaterar en befintlig Blob storage-kontonyckel:

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>Lista över datakällor:

Visa en lista över Data Lake Store-konton:

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

Visa en lista över Blob storage-konto:

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Datakälla för data Lake Analytics-lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Ta bort datakällor:
Ta bort ett Data Lake Store-konto:

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

Ta bort en Blob storage-konto:

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>Hantera jobb
Du måste ha ett Data Lake Analytics-konto innan du kan skapa ett jobb.  Mer information finns i [hantera Data Lake Analytics-konton](#manage-accounts).

### <a name="list-jobs"></a>Lista jobb

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
> Standardprioriteten för ett jobb är 1 000 och standard graden av parallellitet för ett jobb är 1.
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>Avbryt jobb
Använd listan över kommando för att hitta jobb-id och sedan använda Avbryt om du vill avbryta jobbet.

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

## <a name="see-also"></a>Se också
* [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Kom igång med Data Lake Analytics med hjälp av Azure-portalen](data-lake-analytics-get-started-portal.md)
* [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

