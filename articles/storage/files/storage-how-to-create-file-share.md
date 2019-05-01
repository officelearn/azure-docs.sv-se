---
title: Så här skapar du en Azure-filresurs | Microsoft Docs
description: Så här skapar du en Azure-filresurs i Azure Files med hjälp av Azure Portal, PowerShell och Azure CLI.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6440e5c2cf534ea431387bcdfed8b218919bf627
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701538"
---
# <a name="create-a-file-share-in-azure-files"></a>Skapa en filresurs i Azure Files
Du kan skapa Azure-filresurser med hjälp av  [Azure-portalen](https://portal.azure.com/), PowerShell-cmdlets för Azure Storage, klientbiblioteken för Azure Storage eller Azure Storage REST-API:et. I den här kursen lär du dig:
* Så här skapar du en Azure-filresurs med Azure-portalen
* [Så här skapar du en Azure-filresurs med Powershell](#create-file-share-through-powershell)
* [Så här skapar du en Azure-filresurs med CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill skapa en Azure-filresurs, kan du använda ett lagringskonto som redan finns eller [skapa ett nytt Azure Storage-konto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Om du vill skapa en Azure-filresurs med PowerShell behöver du kontonyckeln och namnet på ditt lagringskonto. Du behöver en lagringskontonyckel om du avser att använda Powershell eller CLI.

## <a name="create-a-file-share-through-the-azure-portal"></a>Skapa en filresurs via Azure-portalen
1. **Gå till bladet Lagringskonto i Azure-portalen**:    
    ![Bladet Lagringskonto](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Klicka på knappen Lägg till filresurs**:    
    ![Klicka på knappen för att lägga till filresurs](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Ange namn och kvot. Kvotens aktuella maxvärde är 5 TiB**:    
    ![Ange ett namn och en önskad kvot för den nya filresursen](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Visa din nya filresurs**:  ![Visa din nya filresurs](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Överför en fil**:  ![Överför en fil](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Sök i filresursen och hantera dina kataloger och filer**:  ![Sök i filresursen](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Skapa filresurs via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Innan du kan använda PowerShell laddar du ned och installerar Azure PowerShell-cmdlets. Information om installationsplatsen och installationsanvisningar finns i  [Så här installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) .

> [!Note]  
> Vi rekommenderar att du laddar ned och installerar eller uppgradera till den senaste Azure PowerShell-modulen.

1. **Skapa en kontext för ditt lagringskonto och nyckel** Kontexten innehåller lagringskontots namn och kontonyckel. Anvisningar för hur du kopierar din kontonyckel från  [Azure-portalen](https://portal.azure.com/) finns i  [Åtkomstnycklar för lagringskonto](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Skapa en ny filresurs**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Namnet på filresursen får bara innehålla gemener. Mer information om hur du namnger filresurser och filer finns i  [Namnge och referera till resurser, kataloger, filer och metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Skapa filresurs via kommandoradsgränssnittet (CLI)
1. **Om du vill förbereda för att använda kommandoradsgränssnittet (CLI) laddar du ned och installerar Azure CLI.**  
    Se  [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Skapa en anslutningssträng för lagringskontot där du vill skapa resursen.**  
    Ersätt ```<storage-account>``` och ```<resource_group>``` med lagringskontots namn och resursgruppen i följande exempel:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Skapa filresursen**
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
