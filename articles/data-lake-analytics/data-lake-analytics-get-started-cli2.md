---
title: "Kom igång med Azure Data Lake Analytics med hjälp av Azure CLI 2.0 | Microsoft Docs"
description: "Lär dig hur du skapar ett Data Lake Analytics-konto med hjälp av Azure-kommandoradsgränssnittet 2.0, hur du skapar ett Data Lake Analytics-jobb med hjälp av U-SQL och hur du skickar jobbet. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 109460cecc4e11c729203af97c9bf1c22b90e61a
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20-preview"></a>Självstudier: Kom igång med Azure Data Lake Analytics med hjälp av Azure CLI 2.0 (förhandsversion)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lär dig hur du använder Azure CLI 2.0 för att skapa Azure Data Lake Analytics-konton, definiera Data Lake Analytics-jobb i [U-SQL](data-lake-analytics-u-sql-get-started.md) och skicka jobb till Data Lake Analytics-konton. Mer information om Data Lake Analytics finns i [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).

I de här självstudierna utvecklar du ett jobb som läser en fil med tabbavgränsade värden (TVS) och konverterar den till en fil med kommaavgränsade värden (CSV). Använd rullgardinsmenyn överst i det här avsnittet om du vill gå igenom samma självstudier med andra verktyg.

## <a name="prerequisites"></a>Krav
Innan du börjar den här självstudiekursen behöver du följande:

* **en Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. Se [Installera och konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* **Aktivera Data Lake Store/Analytics CLI 2.0, förhandsversion**. Data Lake Store och Data Lake Analytics CLI 2.0 är fortfarande förhandsversioner. Kör följande kommandon för att aktivera båda:

    ```azurecli
    az component update --add dls
    az component update --add dla 
    ```

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in till din Azure-prenumeration:

```azurecli
az login
```

Du behöver öppna en webbadress och ange en autentiseringskod.  Därefter kan du följa anvisningarna för att ange dina uppgifter.

När du har loggat in visas dina prenumerationer.

Använda en viss prenumeration:

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Skapa ett Data Lake Analytics-konto
Du måste ha ett Data Lake Analytics-konto innan du kan köra några jobb. Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande objekt:

* **Azure-resursgrupp**. Ett Data Lake Analytics-konto måste skapas i en Azure-resursgrupp. Med [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) kan du arbeta med resurserna i ditt program som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i programmet i en enda, samordnad åtgärd.  
  
    Listar de befintliga resursgrupperna under din prenumeration:
  
    ```azurecli
    az group list 
    ```

    Om du vill skapa en ny resursgrupp:
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Data Lake Analytics-kontonamn**. Varje Data Lake Analytics-konto har ett namn.
* **Plats**. Använd ett av de Azure-datacenter som stöder Data Lake Analytics.
* **Data Lake Store-standardkonto**: Varje Data Lake Analytics-konto har ett Data Lake Store-standardkonto.
  
    Om du vill skapa en lista över befintliga Data Lake Store-konton:

    ```azurecli
    az dls account list
    ```
  
    Skapa ett nytt Data Lake Store-konto:
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Om du vill skapa ett Data Lake Analytics-konto, måste du ange följande syntax:

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

När du har skapat ett konto kan du använda följande kommandon för att visa en lista över konton och visa kontoinformation:

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Ladda upp data till Data Lake Store
I den här självstudien bearbetar du vissa sökloggar.  Sökloggen kan lagras i Data Lake Store eller Azure Blob-lagring. 

Azure Portal innehåller ett användargränssnitt för att kopiera vissa exempeldatafiler till Data Lake Store-standardkontot, bland annat en sökloggfil. Se [Förbereda källdata](data-lake-analytics-get-started-portal.md#prepare-source-data) för att ladda upp data till Data Lake Store-standardkontot.

Om du vill ladda upp filer med hjälp av CLI 2.0, använder du följande kommando:

```azurecli
az dls file upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls file list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Data Lake Analytics kan också använda Azure Blob-lagring.  Information om att ladda upp data till Azure Blob-lagring finns i [Använda Azure CLI med Azure Storage](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Skicka Data Lake Analytics-jobb
Data Lake Analytics-jobb skrivs på U-SQL-språket. Läs mer om U-SQL i [Kom igång med U-SQL-språket](data-lake-analytics-u-sql-get-started.md) och [Referens för U-SQL-språket](http://go.microsoft.com/fwlink/?LinkId=691348).

**Skapa ett Data Lake Analytics-jobbskript**

Skapa en textfil med följande U-SQL-skript och spara filen på din arbetsstation:
  
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
  
U-SQL-skriptet läser källdatafilen med hjälp av **Extractors.Tsv()** och skapar sedan en CSV-fil med hjälp av **Outputters.Csv()**. 

Ändra inte de två sökvägarna om du inte har kopierat filen till en annan plats.  Data Lake Analytics skapar utdatamappen om den inte finns.

Det är enklare att använda relativa sökvägar för filer lagrade i Data Lake Store-standardkonton. Du kan också använda absoluta sökvägar.  Exempel:

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Du måste använda absoluta sökvägar för att få åtkomst till filer i länkade Storage-konton.  Syntaxen för filer som lagras i ett länkat Azure Storage-konto är:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Åtkomstbehörighet till Azure Blob-behållare med offentliga blobbar eller offentliga behållare stöds inte.      
  > 
  > 

**Skicka jobb**

Använd följande syntax för att skicka ett jobb.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Exempel:

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Visa en lista över jobb och visa jobbinformation**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Avbryta jobb**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Hämta jobbresultat

När jobbet har slutförts kan du använda följande kommandon för att visa och ladda ned filer med utdata:

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Exempel:

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Nästa steg

* Klicka på flikväljarna överst på sidan om du vill se samma självstudier med andra verktyg.
* För att visa Data Lake Analytics CLI 2.0-referensdokumentet, se [Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla).
* För att visa Data Lake Store CLI 2.0-referensdokumentet, se [Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls).
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Information om att utveckla U-SQL-program finns i [Utveckla U-SQL-skript med hjälp av Data Lake-verktyg för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Information om hanteringsuppgifter finns i [Hantera Azure Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-manage-use-portal.md).
* Om du vill få en översikt över Data Lake Analytics, se [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md).


