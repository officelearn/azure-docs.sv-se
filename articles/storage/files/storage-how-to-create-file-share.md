---
title: Så här skapar du en Azure-filresurs | Microsoft Docs
description: Så här skapar du en Azure-filresurs i Azure Files med hjälp av Azure Portal, PowerShell och Azure CLI.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.component: files
ms.openlocfilehash: ec952aa26d7bc6b185b425700080a4f474564b76
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955817"
---
# <a name="create-a-file-share-in-azure-files"></a>Skapa en filresurs i Azure Files
Du kan skapa Azure-filresurser med hjälp av [Azure Portal](https://portal.azure.com/), PowerShell-cmdlets för Azure Storage, klientbiblioteken för Azure Storage eller Azure Storage REST-API:et. I den här kursen lär du dig:
* [Så här skapar du en Azure-filresurs med Azure-portalen](#create-file-share-through-the-azure-portal)
* [Så här skapar du en Azure-filresurs med Powershell](#create-file-share-through-powershell)
* [Så här skapar du en Azure-filresurs med CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill skapa en Azure-filresurs, kan du använda ett lagringskonto som redan finns eller [skapa ett nytt Azure Storage-konto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du vill skapa en Azure-filresurs med PowerShell behöver du kontonyckeln och namnet på ditt lagringskonto. Du behöver din lagringskontonyckel om du avser att använda Powershell eller CLI.

## <a name="create-file-share-through-the-azure-portal"></a>Skapa filresurs via Azure-portalen
1. **Gå till bladet Lagringskonto i Azure-portalen**:    
    ![Bladet Lagringskonto](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Klicka på knappen Lägg till filresurs**:    
    ![Klicka på knappen för att lägga till filresurs](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Ange namn och kvot. Kvoten kan för närvarande maximalt vara 5 TiB**:    
    ![Ange ett namn och en önskad kvot för den nya filresursen](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Se den nya filresursen**: ![Se den nya filresursen](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Ladda upp en fil**: ![Ladda upp en fil](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Bläddra till filresursen och hantera dina kataloger och filer**: ![Bläddra i filresurs](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Skapa filresurs via PowerShell
Innan du kan använda PowerShell laddar du ned och installerar Azure PowerShell-cmdlets. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).

> [!Note]  
> Vi rekommenderar att du laddar ned och installerar eller uppgradera till den senaste Azure PowerShell-modulen.

1. **Skapa en kontext för ditt lagringskonto och nyckel** Kontexten innehåller lagringskontots namn och kontonyckel. Anvisningar för hur du kopierar din kontonyckel från [Azure-portalen](https://portal.azure.com/) finns i [Åtkomstnycklar för lagringskonto](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Skapa en ny filresurs**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Skapa filresurs via kommandoradsgränssnittet (CLI)
1. **Om du vill förbereda för att använda ett kommandoradsgränssnitt (CLI) laddar du ned Azure CLI och installerar det.**  
    Se [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Skapa en anslutningssträng för lagringskontot där du vill skapa resursen.**  
    Ersätt ```<storage-account>``` och ```<resource_group>``` med lagringskontots namn och resursgrupp i följande exempel:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Skapa en filresurs**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Nästa steg
* [Anslut och montera filresurs – Windows](storage-how-to-use-files-windows.md)
* [Anslut och montera filresurs – Linux](../storage-how-to-use-files-linux.md)
* [Anslut och montera filresurs – macOS](storage-how-to-use-files-mac.md)

Mer information om Azure Files finns på följande länkar.

* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning i Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Felsökning i Linux](storage-troubleshoot-linux-file-connection-problems.md)   
