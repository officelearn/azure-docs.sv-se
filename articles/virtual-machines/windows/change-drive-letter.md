---
title: "Gör att enheten D: för en virtuell dator en datadisk | Microsoft Docs"
description: "Beskriver hur du ändrar enhetsbeteckningar för en virtuell Windows-dator så att du kan använda D: enheten som en dataenhet."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: cynthn
ms.openlocfilehash: 7667175c01be2421bfc3badd83b1d8aaeb29bfde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Använda D: enheten som en dataenhet på en Windows VM
Om ditt program behöver använda enhet D för att lagra data, följer du anvisningarna för att använda en annan enhetsbeteckning för den tillfälliga disken. Använd aldrig tillfälliga disken för att lagra data som du behöver.

Om du ändrar storlek eller **stoppa (Deallocate)** en virtuell dator, detta kan utlösa placeringen av den virtuella datorn till en ny hypervisor. En planerad eller oplanerad underhållshändelse kan också utlösa den här placering. I det här scenariot tilldelas tillfälliga disken den första tillgängliga enhetsbeteckningen. Om du har ett program som kräver mer specifikt D: enhet måste följa dessa steg för att tillfälligt flytta pagefile.sys, ansluta en ny datadisk och tilldela den bokstaven D och flytta pagefile.sys tillbaka till den tillfälliga enheten. När du är klar, träder Azure inte tillbaka D: om den virtuella datorn flyttas till en annan hypervisor.

Mer information om hur Azure använder temporär disk finns [förstå den tillfälliga enheten på virtuella datorer i Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Bifoga datadisken
Först måste du bifoga datadisken för den virtuella datorn. Om du vill göra detta med hjälp av portalen finns [så här kopplar du en datadisk hanterade i Azure portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tillfälligt flytta pagefile.sys till C-enheten
1. Ansluta till den virtuella datorn. 
2. Högerklicka på den **starta** -menyn och välj **System**.
3. Välj i den vänstra menyn **avancerade systeminställningar**.
4. I den **prestanda** väljer **inställningar**.
5. Välj den **Avancerat** fliken.
6. I den **virtuellt minne** väljer **ändra**.
7. Välj den **C** enhet och klicka sedan på **storleken** och klicka sedan på **ange**.
8. Välj den **D** enhet och klicka sedan på **Ingen växlingsfil** och klicka sedan på **ange**.
9. Klicka på Använd. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
10. Starta om den virtuella datorn.

## <a name="change-the-drive-letters"></a>Ändra enhetsbeteckningarna
1. När den virtuella datorn har startats om loggar du in på den virtuella datorn.
2. Klicka på den **starta** -menyn och skriv **diskmgmt.msc** och tryck på RETUR. Diskhantering startar.
3. Högerklicka på **D**, tillfällig lagringsenhet och välj **ändra enhetsbeteckning och sökvägar**.
4. Under enhetsbeteckning, väljer en ny enhet som **T** och klicka sedan på **OK**. 
5. Högerklicka på datadisken och välj **ändra enhetsbeteckning och sökvägar**.
6. Välj enhet under enhetsbeteckning **D** och klicka sedan på **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Flytta pagefile.sys tillbaka till tillfällig lagring-enhet
1. Högerklicka på den **starta** -menyn och välj **System**
2. Välj i den vänstra menyn **avancerade systeminställningar**.
3. I den **prestanda** väljer **inställningar**.
4. Välj den **Avancerat** fliken.
5. I den **virtuellt minne** väljer **ändra**.
6. Välj OS-enhet **C** och på **Ingen växlingsfil** och klicka sedan på **ange**.
7. Välj enhet för tillfällig lagring **T** och klicka sedan på **storleken** och klicka sedan på **ange**.
8. Klicka på **Använd**. Du får en varning om att datorn måste startas om för att ändringarna ska börja gälla.
9. Starta om den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
* Du kan öka tillgängligt lagringsutrymme till den virtuella datorn av [koppla en ytterligare datadisk](attach-managed-disk-portal.md).

