---
title: Koppla eller koppla ifrån en datadisk till en virtuell dator i Azure DevTest Labs | Microsoft Docs
description: Lär dig att koppla eller koppla ifrån en datadisk till en virtuell dator i Azure DevTest Labs
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
ms.openlocfilehash: 2e168867ed342fb0b0545b5fdc330ba790f78de0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243563"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Koppla eller koppla ifrån en datadisk till en virtuell dator i Azure DevTest Labs
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterar de lagringskonton som är associerade med data för virtuella datordiskar. En användare bifogar en ny datadisk till en virtuell dator, anger typen av och storleken på disk som behövs, och skapar och hanterar disken automatiskt Azure. Datadisken kan sedan kopplas från den virtuella datorn och antingen återansluta senare till samma virtuella dator, eller till en annan virtuell dator som hör till samma användare.

Den här funktionen är praktiskt för att hantera lagring eller programvara utanför varje enskild virtuell dator. Om lagring och programvara finns redan i en datadisk, kan det enkelt ansluten, är oberoende och återansluta för alla virtuella datorer som ägs av användaren som äger data disken.

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Innan du ansluter en datadisk till en virtuell dator granskar du de här tipsen:

- Storleken på den virtuella datorn styr hur många datadiskar som du kan koppla. Mer information finns i [storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Du kan endast koppla en datadisk till en virtuell dator som körs. Kontrollera att Virtuellt datorn körs innan du försöker ansluta en datadisk.

### <a name="attach-a-new-disk"></a>Koppla en ny disk
Följ dessa steg för att skapa och koppla en ny hanterad datadisk till en virtuell dator i Azure DevTest Labs.

1. Logga in på [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster**, och välj sedan **DevTest Labs** i listan.
1. Listan över labbar, Välj önskade labbet. 
1. I listan över **Mina virtuella datorer**, Välj en aktiv virtuell dator.
1. På menyn till vänster väljer **diskar**.

    ![Välj datadiskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Välj **bifoga som ny** att skapa en ny datadisk och koppla den till den virtuella datorn.

    ![Bifoga ny datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Slutför den **bifoga ny disk** genom att ange ett namn på disk, typ och storlek.

    ![Fylla i ”ansluta ny disk”](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Välj **OK**.

Efter en liten stund ny datadisk skapas och ansluts till den virtuella datorn och visas i listan över **DATADISKAR** för den virtuella datorn.

### <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
Följ stegen nedan för att ansluta en befintlig disk tillgängliga data till en aktiv virtuell dator. 

1. Välj en aktiv virtuell dator som du vill ansluta en datadisk.
1. På menyn till vänster väljer **diskar**.
1. Välj **bifoga befintlig** att koppla en tillgängliga data-disk till den virtuella datorn.

    ![Bifoga befintlig datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Från den **bifoga befintlig disk** fönstret Välj OK.

    ![Bifoga befintlig datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Efter en liten stund datadisken är kopplad till den virtuella datorn och visas i listan över **DATADISKAR** för den virtuella datorn.

## <a name="detach-a-data-disk"></a>Koppla ifrån en datadisk
När du inte längre behöver en datadisk som är kopplad till en virtuell dator kan du enkelt koppla bort den. Kopplar bort tar bort disken från den virtuella datorn, men håller den i lagring för senare användning.

Om du vill använda befintliga data på disken igen kan du ansluta den till samma virtuella dator eller till en annan.

### <a name="detach-from-the-vms-management-pane"></a>Koppla från fönstret för hantering av den virtuella datorn
1. Välj en virtuell dator som har en datadisk som är ansluten från din lista över virtuella datorer.
1. På menyn till vänster väljer **diskar**.

    ![Välj datadiskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. I listan över **DATADISKAR**, Välj den datadisk som som du vill koppla från.
1. Välj **Detach** högst upp på diskens informationsfönstret.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Välj **Ja** att bekräfta att du vill koppla från datadisken.

Disken är frånkopplad och är kan kopplas till en annan virtuell dator. 
### <a name="detach-from-the-labs-main-pane"></a>Koppla från den testmiljön huvudfönstret
1. Ditt labb huvudfönstret, Välj **Moje datové disky**.

    ![Komma åt den testmiljön datadiskar](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Högerklicka på datadisken som du vill koppla från – eller välj dess ellips (...) och välj **Detach**.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Välj **Ja** att bekräfta att du vill koppla bort den.

   > [!NOTE]
   > Om en datadisk är redan är oberoende av, kan du ta bort den från listan över tillgängliga diskar genom att välja **ta bort**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uppgradera en ohanterad datadisk
Om du har en befintlig virtuell dator som använder ohanterade diskar kan konvertera du enkelt den virtuella datorn för att använda hanterade diskar. Den här processen konverterar både operativsystemdisken och kopplade datadiskar.

Om du vill uppgradera en ohanterad datadisk, följer du stegen som beskrivs i den här artikeln för att [koppla bort datadisken](#detach-a-data-disk) från en ohanterad virtuell dator. Sedan [återansluta disken](#attach-an-existing-disk) till en hanterad virtuell dator att automatiskt uppgradera data disk från ohanterade till hanterade.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
Lär dig att hantera datadiskar för [tillgängliga virtuella datorer](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

