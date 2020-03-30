---
title: Koppla eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
description: Lär dig hur du bifogar eller kopplar från en datadisk till en virtuell dator i Azure DevTest Labs
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
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198870"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Koppla eller koppla från en datadisk till en virtuell dator i Azure DevTest Labs
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) hanterar lagringskonton som är associerade med datadiskar för virtuella datorer. En användare kopplar en ny datadisk till en virtuell dator, anger vilken typ och storlek på disken som behövs och Azure skapar och hanterar disken automatiskt. Datadisken kan sedan kopplas från den virtuella datorn och antingen fästas senare på samma virtuella dator eller kopplas till en annan virtuell dator som tillhör samma användare.

Den här funktionen är praktisk för hantering av lagring eller programvara utanför varje enskild virtuell dator. Om lagringen eller programvaran redan finns på en datadisk kan den enkelt kopplas, tas bort och återanslutas till alla virtuella datorer som ägs av användaren som äger datadisken.

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Innan du kopplar en datadisk till en virtuell dator bör du läsa följande tips:

- Storleken på den virtuella datorn styr hur många datadiskar du kan koppla. Mer information finns i [Storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Du kan bara koppla en datadisk till en virtuell dator som körs. Kontrollera att den virtuella datorn körs innan du försöker koppla en datadisk.

### <a name="attach-a-new-disk"></a>Bifoga en ny disk
Följ dessa steg för att skapa och bifoga en ny hanterad datadisk till en virtuell dator i Azure DevTest Labs.

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. Välj önskat labb i listan över labb. 
1. Välj en virtuell dator som körs i listan över **virtuella datorer.**
1. Välj **Diskar**på menyn till vänster .
1. Välj **Koppla ny** om du vill skapa en ny datadisk och koppla den till den virtuella datorn.

    ![Koppla ny datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Slutför fönstret **Bifoga ny disk** genom att ange ett datadisknamn, en datadisknamn och en storlek.

    ![Fyll i formuläret "bifoga ny disk"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Välj **OK**.

Efter en stund skapas den nya datadisken och kopplas till den virtuella datorn och visas i listan över **datadiskar** för den virtuella datorn.

### <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
Följ dessa steg för att återinsluta en befintlig tillgänglig datadisk till en virtuell dator som körs. 

1. Välj en virtuell dator som körs och som du vill fästa en datadisk för.
1. Välj **Diskar**på menyn till vänster .
1. Välj **Koppla befintlig** om du vill koppla en tillgänglig datadisk till den virtuella datorn.

    ![Koppla befintlig datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. Välj OK i fönstret **Bifoga befintlig disk.**

    ![Koppla befintlig datadisk till en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Efter en stund är datadisken kopplad till den virtuella datorn och visas i listan över **datadiskar** för den virtuella datorn.

## <a name="detach-a-data-disk"></a>Koppla ifrån en datadisk
När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla från den. Om du kopplar från tas disken bort från den virtuella datorn, men den lagras för användning senare.

Om du vill använda befintliga data på disken igen kan du återinsluta dem till samma virtuella dator eller till en annan.

### <a name="detach-from-the-vms-management-pane"></a>Koppla från hanteringsfönstret för den virtuella datorn
1. Välj en virtuell dator som har en datadisk ansluten i listan över virtuella datorer.
1. Välj **Diskar**på menyn till vänster .
1. Välj den datadisk som du vill koppla från i listan över **datadiskar.**

    ![Välj datadiskar för en virtuell dator](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Välj **Koppla från** högst upp i diskens informationsfönster.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Välj **Ja** för att bekräfta att du vill koppla från datadisken.

Disken är fristående och är tillgänglig för att ansluta till en annan virtuell dator. 
### <a name="detach-from-the-labs-main-pane"></a>Koppla från labbets huvudruta
1. I labbets huvudfönster väljer du **Mina datadiskar**.
1. Högerklicka på den datadisk som du vill koppla bort – eller välj ellips (**...**) – och välj **Koppla från**.

    ![Koppla ifrån en datadisk](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Välj **Ja** för att bekräfta att du vill koppla från den.

   > [!NOTE]
   > Om en datadisk redan är fristående kan du välja att ta bort den från listan över tillgängliga datadiskar genom att välja **Ta bort**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uppgradera en ohanterlig datadisk
Om du har en befintlig virtuell dator som använder ohanterade datadiskar kan du enkelt konvertera den virtuella datorn så att den använder hanterade diskar. Den här processen konverterar både OS-disken och alla anslutna datadiskar.

Om du vill uppgradera en ohanterade datadisk följer du stegen i den här artikeln för att [frigöra datadisken](#detach-a-data-disk) från en ohanterat virtuell dator. Sedan, [sätt tillbaka disken](#attach-an-existing-disk) till en hanterad virtuell dator för att automatiskt uppgradera datadisken från ohanterad till hanterad.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du hanterar datadiskar för [anspråksbara virtuella datorer](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

