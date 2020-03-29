---
title: Ladda upp VHD-fil till Azure DevTest Labs med PowerShell | Microsoft-dokument
description: Den här artikeln innehåller en genomgång som visar hur du laddar upp en VHD-fil till Azure DevTest Labs med PowerShell.
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
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759763"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Ladda upp VHD-fil till labbets lagringskonto med PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

I Azure DevTest Labs kan VHD-filer användas för att skapa anpassade avbildningar som används för att etablera virtuella datorer. Följande steg går igenom med PowerShell för att ladda upp en VHD-fil till ett labbs lagringskonto. När du har laddat upp vhd-filen visas några artiklar i [avsnittet Nästa steg](#next-steps) som illustrerar hur du skapar en anpassad avbildning från den uppladdade VHD-filen. Mer information om diskar och virtuella hårddiskar i Azure finns i [Introduktion till hanterade diskar](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Stegvisa instruktioner

Följande steg går igenom att ladda upp en VHD-fil till Azure DevTest Labs med PowerShell. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj önskat labb i listan över labb.  

1. På labbets blad väljer du **Konfiguration**. 

1. På **labbkonfigurationsbladet** väljer du **Anpassade avbildningar (VHD: er)**.

1. Välj **+Lägg till**på bladet **Anpassade bilder** . 

1. På det **anpassade bildbladet** väljer du **VIRTUELLD**.

1. På **VHD-bladet** väljer du **Ladda upp en virtuell hårddisk med PowerShell**.

    ![Ladda upp VHD med PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Kopiera det genererade PowerShell-skriptet till en textredigerare på **bladet Ladda upp en bild med PowerShell.**

1. Ändra parametern **LocalFilePath** för **cmdleten Add-AzureVhd** för att peka på platsen för den VHD-fil som du vill överföra.

1. Kör cmdleten **Add-AzureVhd** i PowerShell-prompten (med parametern modified **LocalFilePath).**

> [!WARNING] 
> 
> Processen att ladda upp en VHD-fil kan vara lång beroende på storleken på VHD-filen och din anslutningshastighet.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med Azure-portalen](devtest-lab-create-template.md)
- [Skapa en anpassad avbildning i Azure DevTest Labs från en VHD-fil med PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
