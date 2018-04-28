---
title: Hämta Windows VHD från Azure | Microsoft Docs
description: Hämta en Windows-VHD med Azure-portalen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: cynthn
ms.openlocfilehash: 83d6715d6d8178e273131a6bc123627126644271
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="download-a-windows-vhd-from-azure"></a>Hämta Windows VHD från Azure

I den här artikeln får du lära dig hur du hämtar en [Windows virtuell hårddisk (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) filen från Azure med Azure-portalen. 

Virtuella datorer (VM) i Azure används [diskar](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) som en plats att lagra ett operativsystem, program och data. Alla virtuella Azure-datorer har minst två diskar – en disk i Windows-operativsystem och en tillfällig disk. Operativsystemdisken har skapats ursprungligen från en avbildning och både operativsystemdisken och image är virtuella hårddiskar som lagras i ett Azure storage-konto. Virtuella datorer kan också ha en eller flera datadiskar som lagras också som virtuella hårddiskar.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell Hårddisk kan inte hämtas från Azure om den är kopplad till en aktiv virtuell dator. Du måste stoppa den virtuella datorn för att ladda ned en virtuell Hårddisk. Om du vill använda en virtuell Hårddisk som en [bild](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar måste du använda [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) att generalisera operativsystemet finns i filen och stoppa den virtuella datorn. Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, behöver du bara stoppa och ta bort den virtuella datorn.

Om du vill använda den virtuella Hårddisken som en bild för att skapa andra virtuella datorer, gör du följande:

1.  Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2.  [Ansluta till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Öppna Kommandotolken som administratör på den virtuella datorn.
4.  Ändra katalogen till *%windir%\system32\sysprep* och köra sysprep.exe.
5.  Välj i dialogrutan systemförberedelseverktyget **ange System Out of Box Experience (OOBE)**, och se till att **Generalize** är markerad.
6.  Välj i avstängningsalternativ, **avstängning**, och klicka sedan på **OK**. 

Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, gör du följande:

1.  På navmenyn i Azure-portalen klickar du på **virtuella datorer**.
2.  Välj den virtuella datorn från listan.
3.  På bladet för den virtuella datorn klickar du på **stoppa**.

    ![Stoppa VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generera en SAS-URL

Om du vill hämta VHD-filen måste du generera en [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL. När URL: en genereras tilldelas en förfallotid till URL: en.

1.  Klicka på menyn i bladet för den virtuella datorn, **diskar**.
2.  Välj disken som operativsystemet för den virtuella datorn och klicka sedan på **exportera**.
3.  Ange Webbadressen till förfallotid *36000*.
4.  Klicka på **generera URL**.

    ![Generera en URL](./media/download-vhd/export-generate.png)

> [!NOTE]
> Förfallotiden ökas från standardvärdet att ge tillräckligt med tid för att hämta stora VHD-filen för ett Windows Server-operativsystem. Du kan förvänta dig en VHD-fil som innehåller operativsystemet Windows Server för att ta flera timmar att hämta beroende på anslutningen. Om du hämtar en virtuell Hårddisk för en datadisk standardtiden är tillräckliga. 
> 
> 

## <a name="download-vhd"></a>Hämta VHD

1.  Klicka på Hämta VHD-filen under den URL som har genererats.

    ![Hämta VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva klicka på **spara** i webbläsaren om du vill starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

    ![Klicka på Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [överför en VHD-fil till Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagringskonto](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

