---
title: Ladda ned en Windows VHD från Azure
description: Ladda ned en Windows VHD med Azure-portalen.
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
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940442"
---
# <a name="download-a-windows-vhd-from-azure"></a>Ladda ned en Windows VHD från Azure

I den här artikeln får du lära dig hur du hämtar en Virtuell Windows-hårddiskfil (VHD) från Azure med Azure-portalen.

## <a name="optional-generalize-the-vm"></a>Valfritt: Generalisera den virtuella datorn

Om du vill använda den virtuella hårddisken som en [avbildning](tutorial-custom-images.md) för att skapa andra virtuella datorer bör du använda [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) för att generalisera operativsystemet. 

Om du vill använda den virtuella hårddisken som en avbildning för att skapa andra virtuella datorer generaliserar du den virtuella datorn.

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. [Anslut till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Öppna kommandotolksfönstret som administratör på den virtuella datorn.
4. Ändra katalogen till *%windir%\system32\sysprep* och kör sysprep.exe.
5. I dialogrutan Systemförberedelseverktyg väljer du **Ange oobe (System out-of-Box Experience)** och kontrollerar att **Generalize** är markerat.
6. I Avstängningsalternativ väljer du **Avstängning**och klickar sedan på **OK**. 


## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hårddisk kan inte hämtas från Azure om den är kopplad till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att hämta en virtuell hårddisk. 

1. Klicka på **Virtuella datorer**på Hub-menyn i Azure-portalen .
1. Välj den virtuella datorn i listan.
1. Klicka på **Stoppa**på bladet för den virtuella datorn.


## <a name="generate-download-url"></a>Generera nedladdnings-URL

Om du vill hämta VHD-filen måste du skapa en [SAS-URL (Shared Access Signature).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) När URL:en genereras tilldelas url:en förfallotid en förfallotid till webbadressen.

1. Klicka på **Diskar** på den vänstra menyn på sidan för den virtuella datorn.
1. Välj operativsystemdisken för den virtuella datorn.
1. Välj **Diskexport** på disken på disken på den vänstra menyn.
1. Url:en förfallotid för standardtiden är *3600* sekunder. Öka detta till **36000** för Windows OS-diskar.
1. Klicka på **Generera URL**.

> [!NOTE]
> Utgångstiden ökas från standardinställningen för att ge tillräckligt med tid för att hämta den stora VHD-filen för ett Windows Server-operativsystem. Du kan förvänta dig att en VHD-fil som innehåller operativsystemet Windows Server tar flera timmar att hämta beroende på din anslutning. Om du hämtar en virtuell hårddisk för en datadisk räcker standardtiden. 
> 
> 

## <a name="download-vhd"></a>Ladda ner VHD

1. Klicka på Hämta VHD-filen under url:en som genererades.
1. Du kan behöva klicka på **Spara** i webbläsaren för att starta hämtningen. Standardnamnet för VHD-filen är *abcd*.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp en VHD-fil till Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagringskonto](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

