---
title: Ladda upp VHD-fil till Azure DevTest Labs med Lagringsutforskaren
description: Ladda upp VHD-fil till labbets lagringskonto med Microsoft Azure Storage Explorer
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170375"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Ladda upp VHD-fil till labbets lagringskonto med Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Den här artikeln visar hur du använder [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) för att överföra en VHD-fil till ett labbs lagringskonto. När du har laddat upp vhd-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [Introduktion till hanterade diskar](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg går igenom att ladda upp en VHD-fil till DevTest Labs med [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Hämta och installera den senaste versionen av Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Hämta namnet på labbets lagringskonto med Azure-portalen:

    1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
    
    1. Välj önskat labb i listan över labb.  
    
    1. På labbets blad väljer du **Konfiguration**. 
    
    1. På **labbkonfigurationsbladet** väljer du **Anpassade avbildningar (VHD: er)**.
    
    1. Välj **+Lägg till**på bladet **Anpassade bilder** . 
    
    1. På det **anpassade bildbladet** väljer du **VIRTUELLD**.
    
    1. På **VHD-bladet** väljer du **Ladda upp en virtuell hårddisk med PowerShell**.
    
        ![Ladda upp VHD med PowerShell][0]
    
    1. **Ladda upp en bild med PowerShell-bladet** visar ett anrop till cmdleten **Add-AzureVhd.** Den första parametern (*Mål*) innehåller labbets lagringskontonamn i följande format:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Anteckna lagringskontonamnet som det används i senare steg.
    
1. Anslut till ett Azure-prenumerationskonto med Storage Explorer.

    > [!TIP] 
    > 
    > Storage Explorer stöder flera anslutningsalternativ. Det här avsnittet illustrerar anslutning till ett lagringskonto som är kopplat till din Azure-prenumeration. Information om vilka andra anslutningsalternativ som stöds av Storage Explorer finns i artikeln [Komma igång med Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Öppna Storage Explorer.
    
    1. Välj **Azure-kontoinställningar**i Storage Explorer . 
    
        ![Inställningar för Azure-konto][1]
    
    1. I den vänstra rutan visas de Microsoft-konton som du har loggat in på. Om du vill ansluta till ett annat konto väljer du **Lägg till ett konto** och följer dialogrutorna för att logga in med ett Microsoft-konto som är kopplat till minst en aktiv Azure-prenumeration.
    
        ![Lägga till ett konto][2]
    
    1. När du har loggat in med ett Microsoft-konto fylls den vänstra rutan i med de Azure-prenumerationer som är kopplade till kontot. Välj de Azure-prenumerationer som du vill arbeta med och välj sedan **Använd**. (Om du väljer **Alla prenumerationer** växlar du valet av alla eller inga av de listade Azure-prenumerationerna.)
    
        ![Välja Azure-prenumerationer][3]
    
    1. I den vänstra rutan visas lagringskontona som är kopplade till de valda Azure-prenumerationerna.
    
        ![Valda Azure-prenumerationer][4]

1. Leta reda på labbets lagringskonto:

    1. Leta reda på och expandera noden för Azure-prenumerationen som äger labbet i den vänstra rutan Lagringsutforskaren.
    
    1. Expandera **Lagringskonton**under prenumerationens nod .

    1. Expandera labbets lagringskontonod för att visa noder för **Blob Containers,** **Filresurser,** **Köer**och **Tabeller**.
    
    1. Expandera noden **Blob Containers.**
    
    1. Markera den uppladdningsbumpbehållare för att visa innehållet i den högra rutan.
        
        ![Ladda upp katalog][5]

1. Ladda upp VHD-filen med Storage Explorer:

    1. I den högra fönstret **Lagringsutforskaren** bör du se en lista över blobbar i blob-behållaren för överför överföringar för labbets lagringskonto. Välj **Ladda upp** i verktygsfältet blobredigerare 
        
        ![Knappen för överföring][6]
    
    1. Välj Ladda **upp** filer på den nedrullningsbara menyn Ladda **upp...**.
    
    1. Välj ellipsen i dialogrutan **Ladda upp filer.**
        
        ![Välj fil][8]  

    1. Bläddra till önskad VHD-fil i dialogrutan **Välj filer som ska överföras,** bläddra till önskad VHD-fil, markera den och välj sedan **Öppna**.
    
    1. När du returnerar till dialogrutan **Ladda upp filer** ändrar du **Blob-typ** till **Sidblob**.
    
    1. Välj **Överför**.

        ![Välj fil][9]  
    
    1. I fönstret **Aktivitetslogg** för Storage Explorer visas hämtningsstatusen (tillsammans med länkar för att avbryta överföringen). Processen att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och din anslutningshastighet. 

        ![Status för upload-file][10]  

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med Azure-portalen](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

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
