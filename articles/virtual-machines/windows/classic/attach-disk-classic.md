---
title: Ansluta en disk till en klassisk Azure-VM | Microsoft Docs
description: Ansluta en datadisk till en Windows-dator som skapats med den klassiska distributionsmodellen och initiera den.
services: virtual-machines-windows, storage
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 48a6b96bd611fcc8fa8219aeef359419255b8cef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Koppla en datadisk till en virtuell Windows-dator skapad med den klassiska distributionsmodellen

Den här artikeln visar hur du ansluter nya och befintliga diskar som skapats med den klassiska distributionsmodellen till en Windows-dator med hjälp av Azure portal.



Du kan också [ansluta en datadisk till en Linux-VM i Azure portal](../../linux/attach-disk-portal.md).

Innan du ansluter en disk, granska de här tipsen:

* Storleken på den virtuella datorn styr hur många datadiskar som du kan bifoga. Mer information finns i [storlekar för virtuella datorer](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Om du vill använda Premium-lagring, behöver du en virtuell dator DS-serien eller GS-serien. Du kan använda diskar från Premium- och Standard storage-konton med dessa virtuella datorer. Premium-lagring är tillgänglig i vissa regioner. Mer information finns i [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* För en ny disk behöver du inte skapa först eftersom Azure skapar den när du ansluter den.

Du kan också [ansluta en datadisk med hjälp av Powershell](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>Hitta den virtuella datorn
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj den virtuella datorn från resursen som visas på instrumentpanelen.
3. I det vänstra fönstret under **inställningar**, klickar du på **diskar**.

    ![Öppna diskinställningarna för](./media/attach-disk/virtualmachinedisks.png)

Fortsätt genom att följa anvisningarna för att bifoga antingen en [ny disk](#option-1-attach-a-new-disk) eller en [befintlig disk](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>Alternativ 1: Anslut och initiera en ny disk

1. På den **diskar** bladet, klickar du på **bifoga nya**.
2. Granska standardinställningarna, uppdatera efter behov och klicka sedan på **OK**.

   ![Granska diskinställningarna för](./media/attach-disk/attach-new.png)

3. När Azure skapar disken och kopplar den till den virtuella datorn, den nya disken visas i inställningarna för den virtuella disken under **Datadiskar**.

### <a name="initialize-a-new-data-disk"></a>Initiera en ny datadisk

1. Ansluta till den virtuella datorn. Instruktioner finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. När du loggar in på den virtuella datorn öppnar **Serverhanteraren**. I den vänstra rutan, Välj **fil- och lagringstjänster**.

    ![Open Server Manager](../media/attach-disk-portal/fileandstorageservices.png)

3. Välj **diskar**.
4. Den **diskar** innehåller diskarna. En virtuell dator har mest ofta disk 0, disk 1 och disk 2. 0 är operativsystemdisk, disk 1 är en tillfällig disk och 2 är datadisk som nyligen kopplade till den virtuella datorn. Datadisken visar partitionen som **okänd**.

 Högerklicka på disken och markera **initiera**.

5. Du meddelas att alla data raderas när disken initieras. Klicka på **Ja** att bekräfta varningen och initiera disken. När installationen är klar visas partitionen som **GPT**. Högerklicka på disken igen och välj **ny volym**.

6. Slutför guiden med standardvärden. När guiden är klar i **volymer** avsnitt visas den nya volymen. Disken är nu online och redo att lagra data.

    ![Volymen har startats](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>Alternativ 2: Bifoga en befintlig disk
1. På den **diskar** bladet, klickar du på **bifoga befintliga**.
2. Under **bifoga den befintliga disken**, klickar du på **plats**.

   ![Bifoga den befintliga disken](./media/attach-disk/attachexistingdisksettings.png)
3. Under **lagringskonton**, Välj det konto och behållare som innehåller VHD-filen.

   ![Hitta VHD-plats](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Välj VHD-filen.
5. Under **bifoga den befintliga disken**, den markerade filen finns under **VHD-filen**. Klicka på **OK**.
6. När Azure bifogar disken till den virtuella datorn, visas den i inställningarna för den virtuella disken under **Datadiskar**.

## <a name="use-trim-with-standard-storage"></a>Använd Rensa med standardlagring

Om du använder standardlagring (HDD), bör du aktivera TRIMNING. TRIMNING ignorerar oanvända block på disken så att endast debiteras du för lagring som du faktiskt använder. Med TRIMNING spara kostnader, inklusive oanvända block som uppstår vid borttagning av stora filer.

Du kan köra det här kommandot för att kontrollera TRIM inställningen. Öppna en kommandotolk på din Windows-VM och skriv:

```
fsutil behavior query DisableDeleteNotify
```

Om kommandot returnerar 0, är TRIMNING aktiverat på rätt sätt. Om den returnerar 1, kör du följande kommando för att aktivera TRIMNING:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Nästa steg
Om ditt program behöver använda D: enhet för att lagra data, kan du [ändra enhetsbeteckningen för den tillfälliga disken i Windows](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>Ytterligare resurser
[Om diskar och virtuella hårddiskar för virtuella datorer](../../virtual-machines-linux-about-disks-vhds.md)
