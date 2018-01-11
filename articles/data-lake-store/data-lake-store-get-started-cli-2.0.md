---
title: "Komma igång med Azure Data Lake Store med hjälp av Azures kommandoradsgränssnitt 2.0 | Microsoft Docs"
description: "Använda Azures plattformsoberoende kommandoradsgränssnitt 2.0 för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 4ffa0f4a-1cca-46ac-803d-1fc8538c685b
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 431c974401c201a76b6d20de9837e44374716417
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli-20"></a>Komma igång med Azure Data Lake Store med hjälp av Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Lär dig hur du använder Azure CLI 2.0 för att skapa ett Azure Data Lake Store-konto och hur du utför grundläggande åtgärder, till exempel hur du skapar mappar, laddar upp och hämtar filer, tar bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Data Lake Store](data-lake-store-overview.md).

Azure CLI 2.0 är Azures nya kommandoradsmiljö för att hantera Azure-resurser. Den kan användas i Mac OS, Linux och Windows. Mer information finns i [Översikt över Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview). En fullständig lista över kommandon och syntax finns i [Azure Data Lake Store CLI 2.0-referensen](https://docs.microsoft.com/cli/azure/dls).


## <a name="prerequisites"></a>Krav
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI 2.0** – Anvisningar finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="authentication"></a>Autentisering

Den här artikeln använder en enklare metod för autentisering med Data Lake Store där du loggar in som en användare av slutanvändaren. Åtkomstnivå till Data Lake Store-kontot och filsystemet styrs av åtkomstnivån för den inloggade användaren. Det finns dock olika sätt att autentisera med Data Lake Store: **slutanvändarens autentisering** eller **serviceautentisering**. Instruktioner och mer information om hur du autentiserar finns i [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md) eller [Tjänst-till-tjänst-autentisering](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Logga in till din Azure-prenumeration.

    ```azurecli
    az login
    ```

    Du får en kod som du ska använda i nästa steg. Använd en webbläsare för att öppna sidan https://aka.ms/devicelogin och ange koden för autentisering. Du uppmanas att logga in med dina autentiseringsuppgifter.

2. När du loggar in visar fönstret alla Azure-prenumerationer som är kopplade till ditt konto. Välj en specifik prenumeration med hjälp av följande kommando.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Skapa ett Azure Data Lake Store-konto

1. Skapa en ny resursgrupp. I följande kommando, anger du de parametervärden som du vill använda. Om platsnamnet innehåller blanksteg måste du placera det inom citattecken. Till exempel "USA, östra 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Skapa Data Lake Store-kontot.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Skapa mappar i ett Data Lake Store-konto

Du kan skapa mappar under Azure Data Lake Store-kontot för att hantera och lagra data. Använd följande kommando för att skapa en mapp med namnet **mynewfolder** i roten för Data Lake Store.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> Parametern `--folder` gör att kommandot skapar en mapp. Om den här parametern utelämnas skapar kommandot en tom fil med namnet mynewfolder i roten för Data Lake Store-kontot.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Ladda upp data till ett Data Lake Store-konto

Du kan ladda upp data till Data Lake Store direkt på rotnivå eller till en mapp som du har skapat i kontot. Fragmenten nedan visar hur du laddar upp exempeldata till mappen (**mynewfolder**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den i en lokal katalog på datorn, till exempel C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> För målet måste du ange den fullständiga sökvägen, inklusive filnamnet.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Visa en lista över filer i ett Data Lake Store-konto

Använd följande kommando för att visa filer i ett Data Lake Store-konto.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

Resultatet av detta ska se ut ungefär så här:

    [
        {
            "accessTime": 1491323529542,
            "aclBit": false,
            "blockSize": 268435456,
            "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "length": 1589881,
            "modificationTime": 1491323531638,
            "msExpirationTime": 0,
            "name": "mynewfolder/vehicle1_09142014.csv",
            "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
            "pathSuffix": "vehicle1_09142014.csv",
            "permission": "770",
            "replication": 1,
            "type": "FILE"
        }
    ]

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Byt namn på, ladda ned och ta bort data från ett Data Lake Store-konto 

* **Byt namn på en fil** med hjälp av följande kommando:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Hämta en fil** med hjälp av följande kommando. Kontrollera att den målsökväg som du anger redan finns.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Kommandot skapar målmappen om den inte finns.
    > 
    >

* **Ta bort en fil** med hjälp av följande kommando:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Om du vill ta bort mappen **mynewfolder** och filen **vehicle1_09142014_copy.csv** i samma kommando använder du parametern --recurse

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Arbeta med behörigheter och ACL:er för ett Data Lake Store-konto

I det här avsnittet lär du dig hur du hanterar ACL:er och behörigheter med hjälp av Azure CLI 2.0. Detaljerad information om hur ACL:er implementeras i Azure Data Lake Store finns i [Åtkomstkontroll i Azure Data Lake Store](data-lake-store-access-control.md).

* **Om du vill uppdatera ägaren till en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill uppdatera behörigheterna för en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Om du vill hämta åtkomstkontrollistorna för en viss sökväg** använder du följande kommando:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    Resultatet bör likna följande:

        {
            "entries": [
            "user::rwx",
            "group::rwx",
            "other::---"
          ],
          "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
          "permission": "770",
          "stickyBit": false
        }

* **Om du vill ange en post för en åtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Om du vill ta bort en post för en åtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill ta bort en hel standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Om du vill ta bort en hel åtkomstkontrollista som inte är en standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Ta bort ett Data Lake Store-konto
Använd följande kommando för att ta bort ett Data Lake Store-konto.

```azurecli
az dls account delete --account mydatalakestore
```

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg
* [Använd Azure Data Lake Store för stordatakrav](data-lake-store-data-scenarios.md) 
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
