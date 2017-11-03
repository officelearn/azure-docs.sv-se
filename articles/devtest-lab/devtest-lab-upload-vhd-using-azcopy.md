---
title: "Överföra VHD-filen till Azure DevTest Labs med hjälp av AzCopy | Microsoft Docs"
description: "Överföra VHD-filen till labblagringskontot med hjälp av AzCopy"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: a4f43354740d9f17570932b0b9c753f46d67dc33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>Överföra VHD-filen till labblagringskontot med hjälp av AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg beskriver hur du använder kommandoradsverktyget azcopy för att ladda upp en VHD-fil till ett labb storage-konto. När du har överfört VHD-filen i [nästa steg avsnittet](#next-steps) innehåller vissa artiklar som illustrerar hur du skapar en anpassad avbildning från den överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns [om diskar och virtuella hårddiskar för virtuella datorer](../virtual-machines/linux/about-disks-and-vhds.md)

> [!NOTE] 
>  
> AzCopy är ett kommandoradsverktyg som endast för Windows.

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

I följande steg beskriver hur du överför en VHD-fil till Azure DevTest Labs med [AzCopy](http://aka.ms/downloadazcopy). 

1. Hämta namnet på den testmiljön storage-konto med Azure-portalen:

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Fler tjänster** och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. På den testmiljön bladet välj **Configuration**. 

1. Om labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.

1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 

1. På den **anpassad bild** bladet väljer **VHD**.

1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.

    ![Överför VHD med PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Den **ladda upp en bild med PowerShell** bladet visar ett anrop till den **Add-AzureVhd** cmdlet. Den första parametern (*mål*) innehåller URI för en blob-behållare (*överför*) i följande format:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Anteckna den fullständiga URI eftersom den används i senare steg.

1. Överför VHD-filen med hjälp av AzCopy:
 
1. [Hämta och installera den senaste versionen av AzCopy](http://aka.ms/downloadazcopy).

1. Öppna ett kommandofönster och gå till installationskatalogen för AzCopy. Alternativt kan du lägga till installationsplatsen AzCopy systemsökvägen. Som standard installeras AzCopy till följande katalog:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Med den kontot nyckel och blob lagringsbehållaren URI, kör följande kommando i Kommandotolken. Den *vhdFileName* värdet måste vara inom citattecken. Processen att överföra VHD-filen kan vara tidskrävande beroende på storleken på VHD-filen och anslutningshastigheten.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)