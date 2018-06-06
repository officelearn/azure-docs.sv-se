---
title: Skapa en virtuell Windows-dator från en särskild virtuell Hårddisk i Azure portal | Microsoft Docs
description: Skapa en ny Windows virtuell dator från en virtuell Hårddisk i Azure-portalen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: cynthn
ms.openlocfilehash: 428003747b7c746a2849a042e54647e86361c562
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716568"
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>Skapa en virtuell dator från en virtuell Hårddisk med Azure-portalen


Det finns flera sätt att skapa virtuella datorer i Azure. Om du redan har en virtuell Hårddisk att använda eller vill kopiera VHD och befintlig virtuell dator för att använda kan du skapa en ny virtuell dator genom att koppla den virtuella Hårddisken som OS-disk. Den här processen *bifogar* den virtuella Hårddisken till en ny virtuell dator som OS-disk.

Du kan också skapa en ny virtuell dator från den virtuella Hårddisken för en virtuell dator som har tagits bort. Om du har en virtuell Azure-dator som inte fungerar kan du ta bort den virtuella datorn och använda den virtuella Hårddisken för att skapa en ny virtuell dator. Du kan återanvända samma VHD eller skapa en kopia av den virtuella Hårddisken genom att skapa en ögonblicksbild och sedan skapa en ny hanterade disk från ögonblicksbilden. Detta tar några fler steg, men garanterar du kan hålla den ursprungliga virtuella Hårddisken och ger dig även en ögonblicksbild återställde inte om det behövs.

Du har en lokal virtuell dator som du vill använda för att skapa en virtuell dator i Azure. Du kan ladda upp den virtuella Hårddisken och koppla den till en ny virtuell dator. Du måste använda PowerShell eller något annat verktyg för att överföra den till ett lagringskonto och sedan skapa en hanterade diskar från den virtuella Hårddisken för att överföra en virtuell Hårddisk. Mer information finns i [överföra en särskild virtuell Hårddisk](create-vm-specialized.md#option-2-upload-a-specialized-vhd)

Om du vill använda en virtuell dator eller virtuell Hårddisk för att skapa flera virtuella datorer bör du använda den här metoden. För större distributioner bör du [skapa en avbildning](capture-image-resource.md) och sedan [använda avbildningen för att skapa flera virtuella datorer](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Kopiera en disk

Skapa en ögonblicksbild och sedan skapa en disk från ögonblicksbilden. På så sätt kan du behålla den ursprungliga virtuella Hårddisken som en faller tillbaka.

1. I den vänstra menyn klickar du på **alla resurser**.
2. I den **alla typer av** listrutan, avmarkerar du **Markera alla** och bläddrar sedan nedåt och välj **diskar** att hitta tillgängliga diskar.
3. Klicka på den disk som du vill använda. Den **översikt** sidan för disken öppnas.
4. Klicka på på menyn överst på sidan Översikt **+ Skapa ögonblicksbild**. 
5. Ange ett namn för ögonblicksbilden.
6. Välj en **resursgruppen** för ögonblicksbilden. Du kan antingen använda en befintlig resursgrupp eller skapa en ny.
7. Välj om du vill använda (HDD)-standard eller Premium (Solid State-Hårddisk) lagring.
8. När du är klar klickar du på **skapa** att skapa en ögonblicksbild.
9. När ögonblicksbilden har skapats, klickar du på **+ skapa en resurs** i den vänstra menyn.
10. Skriv i sökfältet, **hanterade diskar** och välj **hanterade diskar** från listan.
11. På den **hanterade diskar** klickar du på **skapa**.
12. Ange ett namn för disken.
13. Välj en **resursgruppen** för disken. Du kan antingen använda en befintlig resursgrupp eller skapa en ny. Detta kan också vara resursgruppen där du skapar den virtuella datorn från disken.
14. Välj om du vill använda (HDD)-standard eller Premium (Solid State-Hårddisk) lagring.
15. I **typ av datakälla**, se till att **ögonblicksbild** är markerad.
16. I den **källa ögonblicksbild** listrutan, Välj den ögonblicksbild som du vill använda.
17. Gör eventuella justeringar efter behov och klicka sedan på **skapa** att skapa disken.

## <a name="create-a-vm-from-a-disk"></a>Skapa en virtuell dator från en disk

När du har den hantera disken VHD som du vill använda kan skapa du den virtuella datorn i portalen.

1. I den vänstra menyn klickar du på **alla resurser**.
2. I den **alla typer av** listrutan, avmarkerar du **Markera alla** och bläddrar sedan nedåt och välj **diskar** att hitta tillgängliga diskar.
3. Klicka på den disk som du vill använda. Den **översikt** sidan för disken öppnas.
På översiktssidan, se till att **DISKSTATUSEN** anges som **okopplad**. Om det inte behöva du ta bort disken från den virtuella datorn eller ta bort den virtuella datorn att frigöra disken.
4. Klicka på menyn längst upp i fönstret **+ skapa VM**.
5. På den **grunderna** för den nya virtuella datorn kan ange ett namn och välj en befintlig resursgrupp eller skapa en ny.
6. På den **storlek** , Välj en sida för VM-storlek och klickar sedan på **Välj**.
7. På den **inställningar** sidan kan du antingen låta portalen skapar alla nya resurser eller kan du välja en befintlig **för virtuella nätverk** och **nätverkssäkerhetsgruppen**. Portalen alltid skapa ett nytt nätverkskort och offentliga IP-adressen för den nya virtuella datorn. 
8. Göra ändringar i alternativen för övervakning och Lägg till alla tillägg efter behov.
9. Klicka på **OK** när du är klar. 
10. Om VM-konfiguration har klarat valideringen klickar du på **OK** att starta distributionen.

## <a name="next-steps"></a>Nästa steg

Du kan också använda PowerShell för att [överför en virtuell Hårddisk till Azure och skapa en särskild virtuell dator](create-vm-specialized.md).


