---
title: Ladda upp VHD-filen till Azure DevTest Labs med Microsoft Azure Storage Explorer | Microsoft Docs
description: Överför VHD-filen till övningen storage-konto med Microsoft Azure Storage Explorer
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
ms.openlocfilehash: 3f3b20ac21c0a871d3baad50098700b7cf3b3b9c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235402"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Överför VHD-filen till övningen storage-konto med Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar, som används för att etablera virtuella datorer. Den här artikeln beskrivs hur du använder [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att överföra en VHD-fil till ett labb storage-konto. När du har överfört din VHD-filen visas de [nästa steg avsnittet](#next-steps) visar vissa artiklar som visar hur du skapar en anpassad avbildning från överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [om diskar och virtuella hårddiskar för virtuella datorer](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

I följande steg vägleder dig genom att ladda upp en VHD-fil till labb med hjälp av [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Ladda ned och installera den senaste versionen av Microsoft Azure Storage Explorer](http://www.storageexplorer.com).

1. Hämta namnet på den testmiljön storage-konto med Azure portal:

    1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
    
    1. Listan över labbar, Välj önskade labbet.  
    
    1. På den labb-bladet och välj **Configuration**. 
    
    1. På labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.
    
    1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 
    
    1. På den **anpassad avbildning** bladet väljer **VHD**.
    
    1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.
    
        ![Överför virtuell Hårddisk med PowerShell][0]
    
    1. Den **ladda upp en bild med hjälp av PowerShell** bladet visar ett anrop till den **Add-AzureVhd** cmdlet. Den första parametern (*mål*) innehåller namnet på lagringskontot för labb i följande format:
    
        https://<STORAGE-ACCOUNT-Name>.BLOB.Core.Windows.NET/uploads/... 

    1. Anteckna namnet på lagringskontot eftersom den används i senare steg.
    
1. Anslut till en Azure-prenumeration-konto med Storage Explorer.

    > [!TIP] 
    > 
    > Lagringsutforskaren stöder flera anslutningsalternativ. Det här avsnittet illustrerar ansluter till ett lagringskonto som är associerade med din Azure-prenumeration. Om du vill se de anslutningsalternativ som stöds av Storage Explorer finns i artikeln [komma igång med Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Öppna Storage Explorer.
    
    1. I Storage Explorer väljer **Azure kontoinställningar**. 
    
        ![Inställningar för Azure-konto][1]
    
    1. Microsoft-konton som du har loggat in till visas i den vänstra rutan. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.
    
        ![Lägga till ett konto][2]
    
    1. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Att välja **alla prenumerationer** växlar du mellan valet av alla eller inga av de angivna Azure-prenumerationerna.)
    
        ![Välja Azure-prenumerationer][3]
    
    1. I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.
    
        ![Valda Azure-prenumerationer][4]

1. Leta upp den testmiljön storage-konto:

    1. Leta upp i den vänstra rutan i Lagringsutforskaren och expanderar du noden för Azure-prenumerationen som äger labbet.
    
    1. Expandera under prenumerationens noden **Lagringskonton**.

    1. Expandera den testmiljön konto lagringsnod för att visa noder för **Blobbehållare**, **filresurser**, **köer**, och **tabeller**.
    
    1. Expandera den **Blobbehållare** noden.
    
    1. Välj blob-behållaren överföringar för att visa innehållet i den högra rutan.
        
        ![Överföra katalogen][5]

1. Ladda upp VHD-filen med Storage Explorer:

    1. I den högra rutan i Lagringsutforskaren bör du se en lista över blobarna i den **överför** blob-behållare för den testmiljön storage-konto. På verktygsfältet blob editor väljer **ladda upp** 
        
        ![Knappen för överföring][6]
    
    1. Från den **överför** nedrullningsbara menyn och välj **ladda upp filer...** .
    
    1. På den **ladda upp filer** dialogrutan, Välj ellipserna.
        
        ![Välj fil][8]  

    1. På den **Välj filer att ladda upp** dialogrutan, bläddra till önskad VHD-filen, markera den och därefter **öppna**.
    
    1. När du tillbaka till den **ladda upp filer** dialogrutan Ändra **typ av Blob** till **Sidblob**.
    
    1. Välj **Överför**.

        ![Välj fil][9]  
    
    1. Lagringsutforskaren **aktivitetsloggen** visar hämtningsstatus (tillsammans med länkar till överföringen). Processen att överföra en VHD-fil kan vara långa beroende på storleken på VHD-filen och anslutningshastigheten. 

        ![Uppladdningsfilen status][10]  

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
