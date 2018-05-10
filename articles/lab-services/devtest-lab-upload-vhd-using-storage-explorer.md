---
title: Överföra VHD-filen till Azure DevTest Labs med Microsoft Azure Lagringsutforskaren | Microsoft Docs
description: Överföra VHD-filen till labblagringskontot med Lagringsutforskaren för Microsoft Azure
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
ms.openlocfilehash: cafb3ec2da335165a3b66c18d83930008c6d72e5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Överföra VHD-filen till labblagringskontot med Lagringsutforskaren för Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Den här artikeln beskrivs hur du använder [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md) att ladda upp en VHD-fil till ett labb storage-konto. När du har överfört VHD-filen i [nästa steg avsnittet](#next-steps) innehåller vissa artiklar som illustrerar hur du skapar en anpassad avbildning från den överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns [om diskar och virtuella hårddiskar för virtuella datorer](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Stegvisa anvisningar

I följande steg beskriver hur du överför en VHD-fil på DevTest Labs med [Microsoft Azure Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Hämta och installera den senaste versionen av Microsoft Azure Lagringsutforskaren](http://www.storageexplorer.com).

1. Hämta namnet på den testmiljön storage-konto med Azure-portalen:

    1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.
    
    1. Lista över labs, Välj önskade labbet.  
    
    1. På den testmiljön bladet välj **Configuration**. 
    
    1. Om labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.
    
    1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 
    
    1. På den **anpassad bild** bladet väljer **VHD**.
    
    1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.
    
        ![Överför VHD med PowerShell][0]
    
    1. Den **ladda upp en bild med PowerShell** bladet visar ett anrop till den **Add-AzureVhd** cmdlet. Den första parametern (*mål*) innehåller namnet på lagringskontot för labbet i följande format:
    
        https://<STORAGE-ACCOUNT-Name>.BLOB.Core.Windows.NET/uploads/... 

    1. Anteckna namnet på lagringskontot eftersom den används i senare steg.
    
1. Ansluta till ett konto för Azure-prenumeration med Lagringsutforskaren.

    > [!TIP] 
    > 
    > Lagringsutforskaren stöder flera anslutningsalternativ. Det här avsnittet visar anslutning till ett lagringskonto som är associerade med din Azure-prenumeration. Om du vill se de anslutningsalternativ som stöds av Lagringsutforskaren finns i artikel [komma igång med Lagringsutforskaren](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Öppna Storage Explorer.
    
    1. I Lagringsutforskaren, Välj **Azure kontoinställningar**. 
    
        ![Inställningar för Azure-konto][1]
    
    1. Den vänstra kolumnen visar Microsoft-konton som du har loggat in till. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.
    
        ![Lägga till ett konto][2]
    
    1. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Att välja **alla prenumerationer** växlar valet av alla eller inga av de listade Azure-prenumerationerna.)
    
        ![Välja Azure-prenumerationer][3]
    
    1. I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.
    
        ![Valda Azure-prenumerationer][4]

1. Leta upp den labblagringskontot:

    1. Leta upp i den vänstra rutan i Lagringsutforskaren och expanderar du noden för Azure-prenumerationen som äger labbet.
    
    1. Expandera under den prenumeration nod **Lagringskonton**.

    1. Expandera den testmiljön storage-konto nod om du vill visa noder för **Blobbbehållare**, **filresurser**, **köer**, och **tabeller**.
    
    1. Expandera den **Blobbbehållare** nod.
    
    1. Välj överföringar blob-behållaren och visa innehållet i den högra rutan.
        
        ![Ladda upp katalogen][5]

1. Överför VHD-filen med Lagringsutforskaren:

    1. I den högra rutan i Lagringsutforskaren bör du se en lista över blobbar i den **överför** blob-behållaren i den labblagringskontot. På verktygsfältet blob editor väljer **överför** 
        
        ![Knappen för överföring][6]
    
    1. Från den **överför** nedrullningsbara menyn och väljer **Överför filer...** .
    
    1. På den **ladda upp filer** dialogrutan Välj tre punkter.
        
        ![Välj fil][8]  

    1. På den **välja filer att överföra** dialogrutan, bläddra till önskad VHD-filen, markerar du den och välj sedan **öppna**.
    
    1. När tillbaka till den **ladda upp filer** dialogrutan Ändra **Blob typen** till **Sidblob**.
    
    1. Välj **Överför**.

        ![Välj fil][9]  
    
    1. Lagringsutforskaren **aktivitetsloggen** visar statusen download (tillsammans med länkar till avbryta uppladdningen). Processen att överföra VHD-filen kan vara tidskrävande beroende på storleken på VHD-filen och anslutningshastigheten. 

        ![Överför filstatus][10]  

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
