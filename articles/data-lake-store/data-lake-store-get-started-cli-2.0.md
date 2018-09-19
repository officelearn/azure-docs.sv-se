---
title: Använd Azures kommandoradsgränssnitt 2.0-gränssnittet för att komma igång med Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använd Azure plattformsoberoende kommandoradsgränssnitt 2.0 för att skapa ett Data Lake Storage Gen1-konto och utföra grundläggande åtgärder
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: b75fc3a96ce4e46fbd9efb03aa2ea9c2c4aaa15c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124787"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-cli-20"></a>Kom igång med Azure Data Lake Storage Gen1 med Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Portalen](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig mer om att använda Azure CLI 2.0 för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta datafiler, ta bort ditt konto, osv. Läs mer om Data Lake Storage Gen1 [översikt av Data Lake Storage Gen1](data-lake-store-overview.md).

Azure CLI 2.0 är Azures nya kommandoradsmiljö för att hantera Azure-resurser. Den kan användas i Mac OS, Linux och Windows. Mer information finns i [Översikt över Azure CLI 2.0](https://docs.microsoft.com/cli/azure). Du kan också titta på den [Azure Data Lake Storage Gen1 CLI 2.0-referensen](https://docs.microsoft.com/cli/azure/dls) för en fullständig lista över kommandon och syntax.


## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar den här artikeln måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI 2.0** – Anvisningar finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="authentication"></a>Autentisering

Den här artikeln använder en enklare metod för autentisering med Data Lake Storage Gen1 där du loggar in som en användare av slutanvändaren. Åtkomstnivå till Data Lake Storage Gen1 kontot och filsystemet styrs av åtkomstnivån för den inloggade användaren. Men det finns andra sätt att autentisera med Data Lake Storage Gen1 som är **slutanvändarautentisering** eller **tjänst-till-tjänst-autentisering**. Instruktioner och mer information om hur du autentiserar finns i [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md) eller [Tjänst-till-tjänst-autentisering](data-lake-store-authenticate-using-active-directory.md).


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

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Skapa ett konto för Azure Data Lake Storage Gen1

1. Skapa en ny resursgrupp. I följande kommando, anger du de parametervärden som du vill använda. Om platsnamnet innehåller blanksteg måste du placera det inom citattecken. Till exempel "USA, östra 2". 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Skapa Gen1 för Data Lake Storage-konto.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Skapa mappar i ett Data Lake Storage Gen1-konto

Du kan skapa mappar under Azure Data Lake Storage Gen1 kontot för att hantera och lagra data. Använd följande kommando för att skapa en mapp med namnet **mynewfolder** i roten på Data Lake Storage Gen1-konto.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametern `--folder` gör att kommandot skapar en mapp. Om den här parametern inte finns skapar kommandot en tom fil med namnet mynewfolder i roten på Data Lake Storage Gen1-konto.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Ladda upp data till ett Data Lake Storage Gen1-konto

Du kan överföra data till Data Lake Storage Gen1 direkt på rotnivå eller till en mapp som du har skapat i kontot. Fragmenten nedan visar hur du laddar upp exempeldata till mappen (**mynewfolder**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den i en lokal katalog på datorn, till exempel C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> För målet måste du ange den fullständiga sökvägen, inklusive filnamnet.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Listfiler i ett Data Lake Storage Gen1-konto

Använd följande kommando för att lista filer i ett Data Lake Storage Gen1-konto.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
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

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Byt namn på, hämta och ta bort data från ett Data Lake Storage Gen1 

* **Byt namn på en fil** med hjälp av följande kommando:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Hämta en fil** med hjälp av följande kommando. Kontrollera att den målsökväg som du anger redan finns.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Kommandot skapar målmappen om den inte finns.
    > 
    >

* **Ta bort en fil** med hjälp av följande kommando:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Om du vill ta bort mappen **mynewfolder** och filen **vehicle1_09142014_copy.csv** i samma kommando använder du parametern --recurse

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Arbeta med behörigheter och ACL: er för ett Data Lake Storage Gen1-konto

I det här avsnittet lär du dig hur du hanterar ACL:er och behörigheter med hjälp av Azure CLI 2.0. Detaljerad information om hur ACL: er implementeras i Azure Data Lake Storage Gen1, se [åtkomstkontroll i Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Om du vill uppdatera ägaren till en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill uppdatera behörigheterna för en fil/mapp** använder du följande kommando:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Om du vill hämta åtkomstkontrollistorna för en viss sökväg** använder du följande kommando:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
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
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Om du vill ta bort en post för en åtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Om du vill ta bort en hel standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Om du vill ta bort en hel åtkomstkontrollista som inte är en standardåtkomstkontrollista** använder du följande kommando:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1-konto
Använd följande kommando för att ta bort ett Data Lake Storage Gen1-konto.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg
* [Använda Azure Data Lake Storage Gen1 för stordatakrav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
