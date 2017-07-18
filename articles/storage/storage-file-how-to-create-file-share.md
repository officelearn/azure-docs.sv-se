---
title: "Så här skapar du en Azure-filresurs | Microsoft Docs"
description: "Så här skapar du en Azure-filresurs i Azure File Storage med hjälp av Azure Portal, PowerShell och Azure CLI."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: bfe12fbdd50e82404ff49b1e34adc03913e50905
ms.contentlocale: sv-se
ms.lasthandoff: 07/12/2017

---
# <a name="create-a-file-share-in-azure-file-storage"></a>Skapa en filresurs i Azure File Storage
Du kan skapa Azure-filresurser med [Azure Portal](https://portal.azure.com/), PowerShell-cmdlets för Azure Storage, klientbiblioteken för Azure Storage eller Azure Storage REST-API:et. I den här självstudien lär du dig:
* [Hur du skapar en Azure-filresurs med hjälp av Azure Portal](#Create file share through the Portal)
* [Hur du skapar en Azure-filresurs med hjälp av Powershell](#Create file share using PowerShell)
* [Hur du skapar en Azure-filresurs med hjälp av CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Krav
Om du vill skapa en Azure-filresurs kan du använda ett lagringskonto som redan finns eller [skapa ett nytt Azure Storage-konto](storage-create-storage-account.md). Om du vill skapa en Azure-filresurs med PowerShell så behöver du kontonyckeln och namnet på ditt lagringskonto... Du behöver din lagringskontonyckel om du avser att använda Powershell eller CLI.

## <a name="create-file-share-through-the-portal"></a>Skapa filresurs via portalen
1. **Gå till bladet Lagringskonto i Azure Portal**:    
    ![Bladet Lagringskonto](media/storage-file-how-to-create-file-share/create-file-share-portal1.png)

2. **Klicka på knappen Lägg till filresurs**:    
    ![Klicka på knappen för att lägga till filresurs](media/storage-file-how-to-create-file-share/create-file-share-portal2.png)

3. **Ange namn och kvot. Kvoten kan för närvarande maximalt vara 5TB**:    
    ![Ange ett namn och en önskad kvot för den nya filresursen](media/storage-file-how-to-create-file-share/create-file-share-portal3.png)

4. **Se den nya filresursen**: ![Se den nya filresursen](media/storage-file-how-to-create-file-share/create-file-share-portal4.png)

5. **Ladda upp en fil**: ![Ladda upp en fil](media/storage-file-how-to-create-file-share/create-file-share-portal5.png)

6. **Bläddra till filresursen och hantera dina kataloger och filer**: ![Bläddra i filresurs](media/storage-file-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Skapa filresurs via PowerShell
Innan du kan använda PowerShell laddar du ned och installerar Azure PowerShell-cmdlets. Information om installationsplatser och installationsanvisningar finns i [Installera och konfigurera Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).

> [!Note]  
> Vi rekommenderar att du laddar ned och installerar eller uppgradera till den senaste Azure PowerShell-modulen.

1. **Skapa en kontext för ditt lagringskonto och nyckel** Kontexten innehåller lagringskontots namn och kontonyckel. Anvisningar för hur du kopierar din kontonyckel från [Azure Portal](https://portal.azure.com/) finns i [Visa och kopiera åtkomstnycklar för lagring](storage-create-storage-account.md#view-and-copy-storage-access-keys).

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
    Se [Install Azure CLI 2.0](/cli/azure/install-az-cli2.md) (Installera Azure CLI 2.0) och [Get started with Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) (Kom igång med Azure CLI 2.0).

2. **Skapa en anslutningssträng för lagringskontot där du vill skapa resursen.**  
    Ersätt ```<storage-account>``` och ```<resource_group>``` med lagringskontots namn och resursgrupp i följande exempel.

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
* [Anslut och montera filresurs – Windows](storage-file-how-to-use-files-windows.md)
* [Anslut och montera filresurs – Linux](storage-how-to-use-files-linux.md)
* [Anslut och montera filresurs – macOS](storage-file-how-to-use-files-mac.md)

Mer information om Azure File Storage finns på följande länkar.

* [Vanliga frågor och svar](storage-files-faq.md)
* [Felsökning](storage-troubleshoot-file-connection-problems.md)
