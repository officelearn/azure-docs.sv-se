---
title: Ansluta eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig att ansluta eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 193b66cf8bdaaefed5f073bec3ecb9050d076f19
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Ansluta eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
[Azure-hanterade diskar](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterar storage-konton som är kopplade till virtuella hårddiskar. En användare bifogar nya data disken till en virtuell dator Anger typ och storlek på disken som behövs, och Azure skapar och hanterar disken automatiskt. Datadisken kan sedan kopplas från den virtuella datorn och antingen anbringas på nytt senare till samma virtuella dator, eller till en annan virtuell dator som hör till samma användare.

Den här funktionen är praktiskt för att hantera lagring eller programvaran utanför varje enskild virtuell dator. Om lagrings- och programvara finns redan i en datadisk, kan den enkelt ansluten, oberoende och därför kopplats till någon virtuell dator som ägs av den användare som äger data disken.

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Innan du ansluta en datadisk till en virtuell dator granskar du de här tipsen:

- Storleken på den virtuella datorn styr hur många datadiskar som du kan bifoga. Mer information finns i [storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Du kan bara ansluta en datadisk till en virtuell dator som körs. Kontrollera att den virtuella datorn körs innan du försöker ansluta en datadisk.

### <a name="attach-a-new-disk"></a>Koppla en ny disk
Följ dessa steg för att skapa och koppla en ny datadisk hanterade till en virtuell dator i Azure DevTest Labs.

1. Logga in på [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** från listan.
1. Lista över labs, Välj önskade labbet. 
1. I listan över **Mina virtuella datorer**, Välj en aktiv virtuell dator.
1. Välj på menyn till vänster **diskar**.

    ![Välj diskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Välj **bifoga nya** att skapa en ny datadisk och koppla den till den virtuella datorn.

    ![Koppla en ny datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Slutför den **bifoga den nya disken** fönstret genom att ange en data diskens namn, typ och storlek.

    ![Fylla i ”koppla en ny disk”](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Välj **OK**.

Efter en liten stund datadisken för nya skapas och bifogas till den virtuella datorn och visas i listan över **DATADISKAR** för den virtuella datorn.

### <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
Följ dessa steg om du vill återansluta en befintlig disk tillgängliga data till en aktiv virtuell dator. 

1. Välj en aktiv virtuell dator som du vill koppla en datadisk.
1. Välj på menyn till vänster **diskar**.
1. Välj **koppla befintliga** att ansluta en disk med tillgängliga data till den virtuella datorn.

    ![Bifoga den befintliga datadisken till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Från den **bifoga den befintliga disken** rutan, Välj OK.

    ![Bifoga den befintliga datadisken till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Efter en liten stund datadisken är kopplad till den virtuella datorn och visas i listan över **DATADISKAR** för den virtuella datorn.

## <a name="detach-a-data-disk"></a>Koppla ifrån en datadisk
När du inte längre behöver en datadisk som är kopplad till en virtuell dator kan du enkelt kan koppla från den. Kopplar bort tar bort disken från den virtuella datorn, men behåller i lagring för senare användning.

Om du vill använda befintliga data på disken igen kan du återansluta den till samma virtuella dator eller till en annan.

### <a name="detach-from-the-vms-management-pane"></a>Koppla från den virtuella datorn hanteringsfönstret
1. Välj en virtuell dator som har en datadisk som är ansluten i listan med virtuella datorer.
1. Välj på menyn till vänster **diskar**.

    ![Välj diskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. I listan över **DATADISKAR**, Välj den datadisk som som du vill koppla från.
1. Välj **Detach** överst i informationsfönstret för disken.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Välj **Ja** att bekräfta att du vill koppla bort datadisken.

Disken är frånkopplat och är tillgänglig för att ansluta till en annan virtuell dator. 
### <a name="detach-from-the-labs-main-pane"></a>Koppla från den testmiljön huvudfönstret
1. På ditt labb huvudfönstret väljer **min datadiskar**.

    ![Åtkomst till den testmiljön datadiskar](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Högerklicka på datadisken som du vill koppla från – eller välj dess ellips (...) – och välj **Detach**.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Välj **Ja** att bekräfta att du vill koppla från den.

   > [!NOTE]
   > Om en datadisk redan kopplats från kan du ta bort den från listan med tillgängliga datadiskar genom att välja **ta bort**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uppgradera en ohanterad datadisk
Du kan enkelt konvertera den virtuella datorn om du vill använda hanterade diskar om du har en befintlig virtuell dator som använder ohanterade datadiskar. Den här processen konverterar både OS-disken och eventuella anslutna hårddiskar.

Följ stegen som beskrivs i den här artikeln om du vill uppgradera en ohanterad datadisk [koppla bort datadisken](#detach-a-data-disk) från en ohanterad dator. Sedan [återansluta disken](#attach-an-existing-disk) till en hanterad virtuell dator att automatiskt uppgradera data disk från ohanterade till hanterade.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig att hantera datadiskar för [claimable virtuella datorer](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

