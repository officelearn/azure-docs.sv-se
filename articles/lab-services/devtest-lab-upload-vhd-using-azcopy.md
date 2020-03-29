---
title: Ladda upp VHD-fil till Azure DevTest Labs med AzCopy | Microsoft-dokument
description: Den här artikeln innehåller en genomgång för att använda kommandoradsverktyget AzCopy för att överföra en VHD-fil till ett labbs lagringskonto i Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757430"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Ladda upp VHD-fil till labbets lagringskonto med AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg går igenom med kommandoradsverktyget AzCopy för att överföra en VHD-fil till ett labbs lagringskonto. När du har laddat upp vhd-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [Introduktion till hanterade diskar](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy är ett kommandoradsverktyg endast för Windows.

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg går igenom att ladda upp en VHD-fil till Azure DevTest Labs med [AzCopy](https://aka.ms/downloadazcopy). 

1. Hämta namnet på labbets lagringskonto med Azure-portalen:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj önskat labb i listan över labb.  

1. På labbets blad väljer du **Konfiguration**. 

1. På **labbkonfigurationsbladet** väljer du **Anpassade avbildningar (VHD: er)**.

1. Välj **+Lägg till**på bladet **Anpassade bilder** . 

1. På det **anpassade bildbladet** väljer du **VIRTUELLD**.

1. På **VHD-bladet** väljer du **Ladda upp en virtuell hårddisk med PowerShell**.

    ![Ladda upp VHD med PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **Ladda upp en bild med PowerShell-bladet** visar ett anrop till cmdleten **Add-AzureVhd.** Den första parametern (*Mål*) innehåller URI för en blob-behållare *(uppladdningar)* i följande format:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anteckna hela URI som det används i senare steg.

1. Ladda upp VHD-filen med AzCopy:
 
1. [Ladda ner och installera den senaste versionen av AzCopy](https://aka.ms/downloadazcopy).

1. Öppna ett kommandofönster och navigera till installationskatalogen för AzCopy. Du kan också lägga till installationsplatsen för AzCopy i systemsökvägen. Som standard installeras AzCopy i följande katalog:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Med hjälp av lagringskontonyckeln och blob-behållar-URI kör du följande kommando i kommandotolken. *Värdet vhdFileName* måste anges inom citationstecken. Processen att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och din anslutningshastighet.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med Azure-portalen](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)