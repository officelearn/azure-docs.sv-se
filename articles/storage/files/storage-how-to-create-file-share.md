---
title: Skapa en Azure-filresurs
titleSuffix: Azure Files
description: Hur du skapar en Azure-filresurs med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452876"
---
# <a name="create-a-file-share"></a>Skapa en filresurs
Du kan skapa en resurs i Azure Files med hjälp av [Azure Portal](https://portal.azure.com/), Azure Storage PowerShell-cmdletar, Azure Storage klient bibliotek eller Azure Storage REST API. I den här artikeln får du lära dig att:
- Skapa en Azure-filresurs med hjälp av Azure Portal
- Skapa en Azure-filresurs med hjälp av PowerShell
- Skapa en Azure-filresurs med hjälp av Azure CLI

## <a name="prerequisites"></a>Krav
Om du vill skapa en Azure-filresurs kan du använda ett lagrings konto som redan finns eller [skapa ett nytt Azure Storage-konto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du vill skapa en Azure-filresurs med PowerShell behöver du konto nyckeln och namnet på ditt lagrings konto. Du behöver en lagrings konto nyckel om du planerar att använda PowerShell eller CLI.

> [!NOTE]
> Om du vill skapa fil resurser som är större än 5 TiB, se [Aktivera stora fil resurser](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Skapa en filresurs via Azure-portalen

1. Gå till fönstret **lagrings konto** på Azure Portal.
    fönstret ![lagrings konto](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Välj knappen **+ fil resurs** .
    ![knappen Lägg till fil resurs](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Ange information om **namn** och **kvot**.
    ![namnet och kvoten för den nya fil resursen](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Visa den nya fil resursen.
    ![den nya fil resursen i användar gränssnittet](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Ladda upp en fil.
    ![knappen Ladda upp i navigerings fältet](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Bläddra till fil resursen och hantera dina kataloger och filer.
    ![mappvy för fil resurser](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Skapa en fil resurs via PowerShell

Hämta och installera Azure PowerShell-cmdletar. Information om installationsplatsen och installationsanvisningar finns i  [Så här installerar och konfigurerar du Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Vi rekommenderar att du hämtar och installerar eller uppgraderar till, den senaste Azure PowerShell modulen.

1. Skapa ett nytt lagringskonto.
    Ett lagrings konto är en delad pool av lagring där du kan distribuera Azure-filresurser och lagrings resurser som blobbar eller köer.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Skapa en ny fil resurs.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Fullständig information om namngivning av fil resurser och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Skapa en fil resurs via CLI

1. Ladda ned och installera Azure CLI.
    Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Skapa en anslutnings sträng till det lagrings konto där du vill skapa resursen.
    Ersätt ```<storage-account>``` och ```<resource_group>``` med lagringskontots namn och resursgruppen i följande exempel:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Skapa fil resursen.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Nästa steg

* [Ansluta och montera en fil resurs i Windows](storage-how-to-use-files-windows.md)
* [Ansluta och montera en fil resurs på Linux](storage-how-to-use-files-linux.md)
* [Ansluta och montera en fil resurs på macOS](storage-how-to-use-files-mac.md)

Mer information om Azure Files finns på följande länkar:

* [Vanliga frågor om Storage-filer](storage-files-faq.md)
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files på Linux](storage-troubleshoot-linux-file-connection-problems.md)
