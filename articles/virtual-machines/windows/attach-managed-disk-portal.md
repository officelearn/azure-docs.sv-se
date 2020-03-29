---
title: Koppla en hanterad datadisk till en Windows VM - Azure
description: Så här ansluter du en hanterad datadisk till en Windows-virtuell dator med hjälp av Azure-portalen.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919387"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Koppla en hanterad datadisk till en Virtuell Windows-dator med hjälp av Azure-portalen

Den här artikeln visar hur du bifogar en ny hanterad datadisk till en virtuell Dator (VM) i Windows med hjälp av Azure-portalen. Storleken på den virtuella datorn avgör hur många datadiskar du kan koppla. Mer information finns i [Storlekar för virtuella datorer](sizes.md).


## <a name="add-a-data-disk"></a>Lägga till en datadisk

1. Gå till [Azure-portalen](https://portal.azure.com) för att lägga till en datadisk. Sök efter och välj **Virtuella datorer**.
2. Välj en virtuell dator i listan.
3. Välj **Diskar**på sidan **Virtuell dator** .
4. På sidan **Diskar** väljer du **Lägg till datadisk**.
5. Välj **Skapa disk**i listrutan för den nya disken .
6. Skriv in ett namn på disken på sidan **Skapa hanterad disk** och justera de andra inställningarna efter behov. När du är klar väljer du **Skapa**.
7. På sidan **Diskar** väljer du **Spara** för att spara den nya diskkonfigurationen för den virtuella datorn.
8. När Azure har skapat disken och bifogat den till den virtuella datorn visas den nya disken i den virtuella datorns diskinställningar under **Datadiskar**.


## <a name="initialize-a-new-data-disk"></a>Initiera en ny datadisk

1. Anslut till den virtuella datorn.
1. Välj **Start-menyn** i Windows i den virtuella datorn som körs och ange **diskmgmt.msc** i sökrutan. **Diskhanteringskonsolen** öppnas.
2. Diskhanteringen känner igen att du har en ny, oinitierad disk och att fönstret **Initiera disk** visas.
3. Kontrollera att den nya disken är markerad och välj sedan **OK** för att initiera den.
4. Den nya disken visas som **oallokerad**. Högerklicka var som helst på disken och välj **Ny enkel volym**. Fönstret **Guiden Ny enkel volym** öppnas.
5. Fortsätt genom guiden och behåll alla standardvärden och välj **Slutför**när du är klar .
6. Stäng **diskhantering**.
7. Ett popup-fönster visas som meddelar dig att du måste formatera den nya disken innan du kan använda den. Välj **formatera disk**.
8. I fönstret **Formatera ny disk** kontrollerar du inställningarna och väljer sedan **Start**.
9. En varning visas som meddelar dig att formateringen av diskarna raderar alla data. Välj **OK**.
10. När formateringen är klar väljer du **OK**.

## <a name="next-steps"></a>Nästa steg

- Du kan också [koppla en datadisk med PowerShell](attach-disk-ps.md).
- Om programmet behöver använda *D:-enheten* för att lagra data kan du [ändra enhetsbeteckningen på den tillfälliga windows-disken](change-drive-letter.md).
