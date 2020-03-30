---
title: 'Göra D:-enheten för en virtuell dator till en datadisk '
description: I artikeln beskrivs hur du ändrar enhetsbeteckningar för en Virtuell Windows så att du kan använda D:-enheten som en dataenhet.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033655"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Använda D:-enheten som en dataenhet på en Windows VM
Om ditt program behöver använda D-enheten för att lagra data följer du dessa instruktioner för att använda en annan enhetsbeteckning för den temporära disken. Använd aldrig den temporära disken för att lagra data som du behöver behålla.

Om du ändrar storlek på eller **stoppar (Deallocate)** en virtuell dator kan detta utlösa placering av den virtuella datorn till en ny hypervisor. En planerad eller oplanerad underhållshändelse kan också utlösa den här placeringen. I det här fallet kommer den temporära disken att tilldelas den första tillgängliga enhetsbeteckningen. Om du har ett program som specifikt kräver D:-enheten måste du följa dessa steg för att tillfälligt flytta pagefile.sys, bifoga en ny datadisk och tilldela den bokstaven D och sedan flytta tillbaka pagefile.sys till den tillfälliga enheten. När det är klart tar Azure inte tillbaka D: om den virtuella datorn flyttas till en annan hypervisor.

Mer information om hur Azure använder den tillfälliga disken finns i [Förstå den tillfälliga enheten på Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Koppla datadisken
Först måste du koppla datadisken till den virtuella datorn. Information om hur du gör detta med hjälp av portalen finns [i Så här ansluter du en hanterad datadisk i Azure-portalen](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Flytta pagefile.sys tillfälligt till C-enhet
1. Ansluta till den virtuella datorn. 
2. Högerklicka på **Start-menyn** och välj **System**.
3. Välj **Avancerade systeminställningar**på menyn till vänster .
4. Välj **Inställningar**i avsnittet **Prestanda** .
5. Välj fliken **Avancerat**.
6. Välj **Ändra**i avsnittet **Virtuellt minne** .
7. Markera **C-enheten** och klicka sedan på **Systemhanterad storlek** och klicka sedan på **Ange**.
8. Markera **D-enheten** och klicka sedan på **Ingen växlingsfil** och klicka sedan på **Ange**.
9. Klicka på Använd. Du får en varning om att datorn måste startas om för att ändringarna ska kunna påverka.
10. Starta om den virtuella datorn.

## <a name="change-the-drive-letters"></a>Ändra enhetsbeteckningar
1. När den virtuella datorn startas om loggar du in på den virtuella datorn igen.
2. Klicka på **Start-menyn** och skriv **diskmgmt.msc** och tryck på Retur. Diskhantering startar.
3. Högerklicka på **D**, enheten för tillfälligt lagringsutrymme och välj **Ändra enhetsbeteckning och sökvägar**.
4. Under Enhetsbeteckning väljer du en ny enhet som **T** och klickar sedan på **OK**. 
5. Högerklicka på datadisken och välj **Ändra enhetsbeteckning och banor**.
6. Under Enhetsbeteckning väljer du enhet **D** och klickar sedan på **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Flytta tillbaka pagefile.sys till den tillfälliga lagringsenheten
1. Högerklicka på **Start-menyn** och välj **System**
2. Välj **Avancerade systeminställningar**på menyn till vänster .
3. Välj **Inställningar**i avsnittet **Prestanda** .
4. Välj fliken **Avancerat**.
5. Välj **Ändra**i avsnittet **Virtuellt minne** .
6. Markera OS-enhet **C** och klicka på **Ingen växlingsfil** och klicka sedan på **Ange**.
7. Markera den tillfälliga lagringsenheten **T** och klicka sedan på **Systemhanterad storlek** och klicka sedan på **Ange**.
8. Klicka på **Använd**. Du får en varning om att datorn måste startas om för att ändringarna ska kunna påverka.
9. Starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
* Du kan öka lagringsutrymmet som är tillgängligt för den virtuella datorn genom [att koppla ytterligare en datadisk](attach-managed-disk-portal.md).

