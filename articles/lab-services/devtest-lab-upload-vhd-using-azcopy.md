---
title: Ladda upp VHD-filen till Azure DevTest Labs med hjälp av AzCopy | Microsoft Docs
description: Överför VHD-filen till övningen storage-konto med hjälp av AzCopy
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634986"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Överför VHD-filen till övningen storage-konto med hjälp av AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar, som används för att etablera virtuella datorer. Följande steg beskriver hur du kan ladda upp en VHD-fil till ett labb storage-konto med hjälp av kommandoradsverktyget azcopy. När du har överfört din VHD-filen visas de [nästa steg avsnittet](#next-steps) visar vissa artiklar som visar hur du skapar en anpassad avbildning från överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [introduktion till hanterade diskar](../virtual-machines/linux/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy är ett kommandoradsverktyg som bara Windows.

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

I följande steg vägleder dig genom att ladda upp en VHD-fil till Azure DevTest Labs med hjälp av [AzCopy](https://aka.ms/downloadazcopy). 

1. Hämta namnet på den testmiljön storage-konto med Azure portal:

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.  

1. På den labb-bladet och välj **Configuration**. 

1. På labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.

1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 

1. På den **anpassad avbildning** bladet väljer **VHD**.

1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.

    ![Överför virtuell Hårddisk med PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Den **ladda upp en bild med hjälp av PowerShell** bladet visar ett anrop till den **Add-AzureVhd** cmdlet. Den första parametern (*mål*) innehåller URI för en blob-behållare (*överför*) i följande format:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anteckna den fullständiga URI eftersom den används i senare steg.

1. Ladda upp VHD-filen med hjälp av AzCopy:
 
1. [Ladda ned och installera den senaste versionen av AzCopy](https://aka.ms/downloadazcopy).

1. Öppna ett kommandofönster och navigera till installationskatalogen för AzCopy. Du kan också kan du lägga till installationsplatsen AzCopy systemsökvägen. Som standard installeras AzCopy till följande katalog:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Med hjälp av den nyckel- och blob lagringskontobehållare URI, kör följande kommando i Kommandotolken. Den *vhdFileName* värdet måste vara inom citattecken. Processen att överföra en VHD-fil kan vara långa beroende på storleken på VHD-filen och anslutningshastigheten.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)