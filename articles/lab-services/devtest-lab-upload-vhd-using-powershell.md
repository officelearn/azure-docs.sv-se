---
title: Ladda upp VHD-filen till Azure DevTest Labs med hjälp av PowerShell | Microsoft Docs
description: Överför VHD-filen till övningen storage-konto med hjälp av PowerShell
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
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622787"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Överför VHD-filen till övningen storage-konto med hjälp av PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar, som används för att etablera virtuella datorer. Följande steg beskriver hur du kan ladda upp en VHD-fil till ett labb storage-konto med hjälp av PowerShell. När du har överfört din VHD-filen visas de [nästa steg avsnittet](#next-steps) visar vissa artiklar som visar hur du skapar en anpassad avbildning från överförda VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [introduktion till hanterade diskar](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

I följande steg vägleder dig genom att ladda upp en VHD-fil till Azure DevTest Labs med hjälp av PowerShell. 

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.

1. Listan över labbar, Välj önskade labbet.  

1. På den labb-bladet och välj **Configuration**. 

1. På labbet **Configuration** bladet väljer **anpassade avbildningar (VHD)**.

1. På den **anpassade avbildningar** bladet välj **+ Lägg till**. 

1. På den **anpassad avbildning** bladet väljer **VHD**.

1. På den **VHD** bladet väljer **överföra en virtuell Hårddisk med hjälp av PowerShell**.

    ![Överför virtuell Hårddisk med PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. På den **ladda upp en bild med hjälp av PowerShell** bladet kopiera genererade PowerShell-skript till en textredigerare.

1. Ändra den **LocalFilePath** -parametern för den **Add-AzureVhd** cmdlet för att peka på platsen för VHD-filen som du vill ladda upp.

1. I en PowerShell-Kommandotolken kör den **Add-AzureVhd** cmdlet (med den ändrade **LocalFilePath** parametern).

> [!WARNING] 
> 
> Processen att överföra en VHD-fil kan vara långa beroende på storleken på VHD-filen och anslutningshastigheten.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av Azure portal](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
