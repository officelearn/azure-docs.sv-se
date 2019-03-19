---
title: 'Kontrollera enheten D: för en virtuell dator en datadisk | Microsoft Docs'
description: 'Beskriver hur du ändrar enhetsbeteckningar för en virtuell Windows-dator så att du kan använda enheten D: som en dataenhet.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cfd46d5e9750a81d89ed6d3a79bcc9bffdc3d0dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007285"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Använda enheten D: som en dataenhet på en virtuell Windows-dator
Om programmet behöver för att använda D-hårddisken för att lagra data, följer du dessa instruktioner för att använda en annan enhetsbeteckning för den temporära disken. Använd aldrig den temporära disken för att lagra data som du behöver.

Om du ändrar storlek eller **stoppa (frigöra)** en virtuell dator, detta kan utlösa placeringen av den virtuella datorn till en ny hypervisor. En planerad eller oplanerad underhållshändelse kan också utlösa den här placering. I det här scenariot kommer den temporära disken omtilldelas till den första tillgängliga enhetsbeteckningen. Om du har ett program som uttryckligen kräver att enheten D:, måste följa dessa steg för att tillfälligt flytta pagefile.sys, koppla en ny datadisk och tilldela den en enhetsbeteckning D och flytta pagefile.sys tillbaka till den temporära enheten. När du är klar tar Azure inte tillbaka mappen D: om den virtuella datorn flyttas till en annan hypervisor.

Läs mer om hur Azure använder den temporära disken [förstå den temporära enheten på Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Koppla datadisken
Först måste du koppla datadisken till den virtuella datorn. Om du vill göra detta med hjälp av portalen finns i [så här kopplar du en hanterad datadisk i Azure-portalen](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tillfälligt flytta pagefile.sys till C-enheten
1. Ansluta till den virtuella datorn. 
2. Högerklicka på den **starta** menyn och välj **System**.
3. I den vänstra menyn och väljer **avancerade systeminställningar**.
4. I den **prestanda** väljer **inställningar**.
5. Välj den **Avancerat** fliken.
6. I den **virtuellt minne** väljer **ändra**.
7. Välj den **C** enhet och klicka sedan på **storleken** och klicka sedan på **ange**.
8. Välj den **D** enhet och klicka sedan på **Ingen växlingsfil** och klicka sedan på **ange**.
9. Klicka på tillämpa. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
10. Starta om den virtuella datorn.

## <a name="change-the-drive-letters"></a>Ändra enhetsbeteckning
1. När den virtuella datorn har startats om loggar du in på den virtuella datorn.
2. Klicka på den **starta** meny och skriv **diskmgmt.msc** och tryck på RETUR. Diskhantering startar.
3. Högerklicka på **D**, tillfälligt lagringsutrymme enhet och välj **ändra enhetsbeteckning och sökvägar**.
4. Under enhetsbeteckning, välja en ny enhet, till exempel **T** och klicka sedan på **OK**. 
5. Högerklicka på datadisken och välj **ändra enhetsbeteckning och sökvägar**.
6. Välj enhet under enhetsbeteckning, **D** och klicka sedan på **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Flytta pagefile.sys tillbaka till tillfällig lagring-enhet
1. Högerklicka på den **starta** menyn och välj **System**
2. I den vänstra menyn och väljer **avancerade systeminställningar**.
3. I den **prestanda** väljer **inställningar**.
4. Välj den **Avancerat** fliken.
5. I den **virtuellt minne** väljer **ändra**.
6. Välj storleken på operativsystemenheten **C** och klicka på **Ingen växlingsfil** och klicka sedan på **ange**.
7. Välj enhet för tillfällig lagring **T** och klicka sedan på **storleken** och klicka sedan på **ange**.
8. Klicka på **Verkställ**. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
9. Starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
* Du kan öka lagringsutrymmet till den virtuella datorn med [ansluta en disk för ytterligare data](attach-managed-disk-portal.md).

