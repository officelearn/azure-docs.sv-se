---
title: "Överföra VHD-filen till Azure DevTest Labs med hjälp av PowerShell | Microsoft Docs"
description: "Överföra VHD-filen till labblagringskontot med hjälp av PowerShell"
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
ms.openlocfilehash: 3c43ef77b8fa10cd6dbd726968264f32f7a3dd0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Överföra VHD-filen till labblagringskontot med hjälp av PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg beskriver hur du använder PowerShell för att ladda upp en VHD-fil till ett labb storage-konto. När du har överfört VHD-filen i [nästa steg avsnittet](#next-steps) innehåller vissa artiklar som illustrerar hur du skapar en anpassad avbildning från den överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns [om diskar och virtuella hårddiskar för virtuella datorer](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg beskriver hur du överför en VHD-fil till Azure DevTest Labs med hjälp av PowerShell. 

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Fler tjänster** och välj sedan **DevTest Labs** från listan.

1. Lista över labs, Välj önskade labbet.  

1. På den testmiljön bladet välj **Configuration**. 

1. Om labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.

1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 

1. På den **anpassad bild** bladet väljer **VHD**.

1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.

    ![Överför VHD med PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. På den **ladda upp en bild med PowerShell** bladet kopiera genererade PowerShell-skript till en textredigerare.

1. Ändra den **LocalFilePath** parameter för den **Add-AzureVhd** cmdlet så att den pekar till platsen för VHD-filen som du vill överföra.

1. I PowerShell-Kommandotolken kör den **Add-AzureVhd** cmdlet (med det ändrade **LocalFilePath** parametern).

> [!WARNING] 
> 
> Processen att överföra VHD-filen kan vara tidskrävande beroende på storleken på VHD-filen och anslutningshastigheten.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
