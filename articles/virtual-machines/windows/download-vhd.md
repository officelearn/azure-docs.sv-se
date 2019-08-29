---
title: Hämta en Windows-VHD från Azure | Microsoft Docs
description: Ladda ned en virtuell Windows-hårddisk med Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c1c09382102045dd248b6771d8d0ea1ef090b6eb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079622"
---
# <a name="download-a-windows-vhd-from-azure"></a>Hämta en Windows-VHD från Azure

I den här artikeln får du lära dig hur du hämtar en Windows virtuell hård disk fil (VHD) från Azure med hjälp av Azure Portal.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hård disk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att kunna ladda ned en virtuell hård disk. Om du vill använda en virtuell hård disk som en [avbildning](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar, använder du [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) för att generalisera operativ systemet som finns i filen och sedan stoppa den virtuella datorn. Om du vill använda den virtuella hård disken som en disk för en ny instans av en befintlig virtuell dator eller data disk, behöver du bara stoppa och frigöra den virtuella datorn.

Slutför följande steg för att använda den virtuella hård disken som en avbildning för att skapa andra virtuella datorer:

1.  Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2.  [Anslut till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Öppna kommando tolkens fönster som administratör på den virtuella datorn.
4.  Ändra katalogen till *%WINDIR%\system32\sysprep* och kör Sysprep. exe.
5.  I dialog rutan system förberedelse verktyg väljer du **Skriv system out-of-Box Experience (OOBE)** och kontrollerar att generalize är markerat.
6.  I avslutnings alternativ väljer du **shutdown**och klickar sedan på **OK**. 

Slutför följande steg för att använda den virtuella hård disken som en disk för en ny instans av en befintlig virtuell dator eller data disk:

1.  Klicka på **Virtual Machines**på menyn hubb i Azure Portal.
2.  Välj den virtuella datorn i listan.
3.  Klicka på **stoppa**på bladet för den virtuella datorn.

    ![Stoppa virtuell dator](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Skapa SAS-URL

Om du vill hämta VHD-filen måste du generera en URL för [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . När URL: en genereras tilldelas URL: en förfallo tid.

1.  På menyn på bladet för den virtuella datorn klickar du på **diskar**.
2.  Välj operativ system disk för den virtuella datorn och klicka sedan på **disk export**.
3.  Ange förfallo tiden för URL: en till *36000*.
4.  Klicka på **skapa URL**.

    ![Skapa webbadress](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> Förfallo tiden höjs från standardvärdet för att tillhandahålla tillräckligt med tid för att ladda ned den stora VHD-filen för ett Windows Server-operativsystem. Du kan vänta på att en VHD-fil som innehåller operativ systemet Windows Server ta flera timmar att ladda ned beroende på din anslutning. Om du hämtar en virtuell hård disk för en datadisk är standard tiden tillräckligt. 
> 
> 

## <a name="download-vhd"></a>Hämta VHD

1.  Under den URL som genererades klickar du på Hämta VHD-filen.

    ![Hämta VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva klicka på **Spara** i webbläsaren för att starta nedladdningen. Standard namnet för VHD-filen är *ABCD*.

    ![Klicka på Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp en VHD-fil till Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagrings konto](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

