---
title: Ladda ned Windows virtuella Hårddisken från Azure | Microsoft Docs
description: Hämta en Windows virtuell Hårddisk med Azure portal.
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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329378"
---
# <a name="download-a-windows-vhd-from-azure"></a>Ladda ned Windows virtuella Hårddisken från Azure

I den här artikeln får du lära dig hur du hämtar en fil för Windows-virtuell hårddisk (VHD) från Azure med hjälp av Azure-portalen.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell Hårddisk kan inte hämtas från Azure om den är ansluten till en aktiv virtuell dator. Du måste stoppa den virtuella datorn för att ladda ned en virtuell Hårddisk. Om du vill använda en virtuell Hårddisk som en [bild](tutorial-custom-images.md) för att skapa andra virtuella datorer med nya diskar, som du använder [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) att generalisera operativsystemet finns i filen och sedan stoppa den virtuella datorn. Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, behöver du bara stoppa och frigör den virtuella datorn.

Om du vill använda den virtuella Hårddisken som en bild för att skapa andra virtuella datorer, gör du följande:

1.  Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2.  [Ansluta till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Öppna Kommandotolken som administratör på den virtuella datorn.
4.  Ändra katalogen till *%windir%\system32\sysprep* och köra sysprep.exe.
5.  I dialogrutan systemförberedelseverktyget väljer **ange System Out-of-Box Experience (OOBE)**, och se till att **Generalize** har valts.
6.  Välj i Avslutningsalternativ, **avstängning**, och klicka sedan på **OK**. 

Om du vill använda den virtuella Hårddisken som en disk för en ny instans av en befintlig virtuell dator eller en datadisk, gör du följande:

1.  På navmenyn i Azure-portalen klickar du på **virtuella datorer**.
2.  Välj den virtuella datorn i listan.
3.  Bladet för den virtuella datorn och klicka på **stoppa**.

    ![Stoppa virtuell dator](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generera SAS-URL

För att hämta VHD-filen, måste du generera en [signatur för delad åtkomst (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL: en. När URL: en genereras, tilldelas en förfallotid till URL: en.

1.  På menyn på bladet för den virtuella datorn, **diskar**.
2.  Välj disken som operativsystemet för den virtuella datorn och klicka sedan på **exportera**.
3.  Ange Webbadressen till förfallotid *36000*.
4.  Klicka på **generera URL: en**.

    ![Skapa webbadress](./media/download-vhd/export-generate.png)

> [!NOTE]
> Förfallotid har ökats från standard för att ge tillräckligt med tid för att ladda ned stora VHD-filen för ett Windows Server-operativsystem. Du kan förvänta dig en VHD-fil som innehåller operativsystemet Windows Server för att ta flera timmar att ladda ned beroende på din anslutning. Om du hämtar en virtuell Hårddisk för en datadisk standardtiden är tillräckliga. 
> 
> 

## <a name="download-vhd"></a>Ladda ned VHD

1.  Klicka på Hämta VHD-filen under den URL som har genererats.

    ![Ladda ned VHD](./media/download-vhd/export-download.png)

2.  Du kan behöva klicka på **spara** i webbläsaren för att starta nedladdningen. Standardnamnet för VHD-filen är *abcd*.

    ![Klicka på Spara i webbläsaren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [ladda upp en VHD-fil till Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagringskonto](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

