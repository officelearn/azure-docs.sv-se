---
title: Koppla en hanterad datadisk till en Windows-dator – Azure | Microsoft Docs
description: Så här kopplar du en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure portal.
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
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 8d83af114ebb5e5ff78372897d3e08ed592d4012
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093909"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Ansluta en hanterad datadisk till en virtuell Windows-dator med hjälp av Azure portal

Den här artikeln visar hur du ansluter en ny hanterad datadisk till en Windows-dator (VM) med hjälp av Azure portal. Storleken på den virtuella datorn avgör hur många datadiskar som du kan koppla. Mer information finns i [storlekar för virtuella datorer](sizes.md).


## <a name="add-a-data-disk"></a>Lägga till en datadisk

1. I den [Azure-portalen](https://portal.azure.com), på menyn till vänster väljer **virtuella datorer**.
2. Välj en virtuell dator i listan.
3. På den **VM** väljer **diskar**.
4. På den **diskar** väljer **Lägg till datadisk**.
5. Välj i listrutan för den nya disken **skapa disk**.
6. I den **skapa hanterad disk** skriver ett namn på disken och justera de andra inställningarna efter behov. När du är klar väljer du **Skapa**.
7. I den **diskar** väljer **spara** att spara den nya diskkonfigurationen för den virtuella datorn.
8. När Azure skapar disken och kopplar den till den virtuella datorn, den nya disken visas i den virtuella datorns diskinställningarna under **datadiskar**.


## <a name="initialize-a-new-data-disk"></a>Initiera en ny datadisk

1. Anslut till den virtuella datorn.
1. Välj Windows **starta** menyn i virtuell dator som körs och ange **diskmgmt.msc** i sökrutan. Den **Diskhantering** konsolen öppnas.
2. Diskhantering identifierar att du har en ny, oinitierad disk och **initiera Disk** fönster visas.
3. Kontrollera den nya disken är markerat och välj sedan **OK** att initiera den.
4. Den nya disken visas som **lediga**. Högerklicka på disken och välj **ny enkel volym**. Den **guiden Ny enkel volym** öppnas.
5. Gå igenom guiden att hålla alla standardvärden, och när du är klar väljer du **Slutför**.
6. Stäng **Diskhantering**.
7. Ett popup-fönster visas om att du behöver för att formatera den nya disken innan du kan använda den. Välj **Formatera disk**.
8. I den **formatera ny disk** , kontrollerar du inställningarna och välj sedan **starta**.
9. Meddelar dig om att formatera diskarna raderar alla data visas en varning. Välj **OK**.
10. När formateringen är klar, väljer **OK**.

## <a name="use-trim-with-standard-storage"></a>Använd Rensa med standardlagring

Om du använder standard-lagring (HDD), bör du aktivera den **TRIMMA** kommando. Den **TRIMMA** kommandot tar bort oanvända block på disken så att du faktureras bara för lagring som du faktiskt använder. Med hjälp av **TRIMMA**, du kan spara på kostnaderna om du skapar stora filer och därefter tar bort dem. 

Kontrollera den **TRIMMA** , öppna en kommandotolk på den virtuella Windows-datorn och ange följande kommando:

```
fsutil behavior query DisableDeleteNotify
```

Om kommandot returnerar 0, **TRIMMA** har aktiverats korrekt. Annars, om den returnerar 1, kör följande kommando för att aktivera **TRIMMA**:

```
fsutil behavior set DisableDeleteNotify 0
```

När du har tagit bort data från disken, du kan kontrollera att den **TRIMMA** operations tömning korrekt genom att köra defragmenterar med **TRIMMA**:

```
defrag.exe <volume:> -l
```

Du kan också formatera volymen för att säkerställa att hela volymen beskärs.

## <a name="next-steps"></a>Nästa steg

- Du kan också [ansluter en datadisk med hjälp av PowerShell](attach-disk-ps.md).
- Om programmet behöver för att använda den *D:* enhet för att lagra data, kan du [ändra enhetsbeteckningen för den temporära disken Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
