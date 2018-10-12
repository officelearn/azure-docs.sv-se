---
title: Skapa en virtuell Windows-dator från en specialiserad virtuell Hårddisk i Azure portal | Microsoft Docs
description: Skapa en ny virtuell Windows-dator från en VHD i Azure-portalen.
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
ms.date: 09/20/2018
ms.author: cynthn
ms.openlocfilehash: 905f00842c5ce74f681a6c5c09ff8bf6c7a9e162
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091257"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Skapa en virtuell dator från en virtuell Hårddisk med hjälp av Azure portal

Det finns flera sätt att skapa en virtuell dator (VM) i Azure: 

- Om du redan har en virtuell hårddisk (VHD) för att använda eller du vill kopiera den virtuella Hårddisken från en befintlig virtuell dator att använda, kan du skapa en ny virtuell dator av *koppla* den virtuella Hårddisken till den nya virtuella datorn som en OS-disk. 

- Du kan skapa en ny virtuell dator från den virtuella Hårddisken för en virtuell dator som har tagits bort. Om du har en Azure-dator som inte fungerar korrekt kan du ta bort den virtuella datorn och använda dess VHD för att skapa en ny virtuell dator. Du kan återanvända samma virtuella Hårddisk, eller så kan du skapa en kopia av den virtuella Hårddisken genom att skapa en ögonblicksbild och sedan skapa en ny hanterad disk från ögonblicksbilden. Även om det tar några få steg för att skapa en ögonblicksbild, bevarar den ursprungliga virtuella Hårddisken och ger dig en reserv.
 
- Du kan skapa en Azure-dator från en lokal virtuell Hårddisk genom att ladda upp en lokal VHD och kopplar den till en ny virtuell dator. Du använder PowerShell eller något annat verktyg för att överföra den virtuella Hårddisken till ett lagringskonto och sedan du skapar en hanterad disk från den virtuella Hårddisken. Mer information finns i [överföra en specialiserad virtuell Hårddisk](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Använd inte en särskild disk om du vill skapa flera virtuella datorer. I stället för större distributioner [skapa en avbildning](capture-image-resource.md) och sedan [använda avbildningen för att skapa flera virtuella datorer](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Kopiera en disk

Skapa en ögonblicksbild och skapa sedan en disk från ögonblicksbilden. Den här strategin kan du behålla den ursprungliga virtuella Hårddisken som reserv:

1. Från den [Azure-portalen](https://portal.azure.com), på den vänstra menyn väljer **alla tjänster**.
2. I den **alla tjänster** sökrutan, ange **diskar** och välj sedan **diskar** att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. Den **Disk** sidan för disken visas.
4. På menyn högst upp väljer **Skapa ögonblicksbild**. 
5. Ange en **namn** för ögonblicksbilden.
6. Välj en **resursgrupp** för ögonblicksbilden. Du kan använda en befintlig resursgrupp eller skapa en ny.
7. För **kontotyp**, väljer du antingen **Standard (HDD)** eller **Premium (SSD)** lagring.
8. När du är klar väljer du **skapa** att skapa ögonblicksbilden.
9. När ögonblicksbilden har skapats, Välj **skapa en resurs** på den vänstra menyn.
10. I sökrutan anger **hanterad disk** och välj sedan **Managed Disks** i listan.
11. På den **Managed Disks** väljer **skapa**.
12. Ange en **namn** för disken.
13. Välj en **resursgrupp** för disken. Du kan använda en befintlig resursgrupp eller skapa en ny. Det här alternativet ska också användas som resursgruppen där du skapar den virtuella datorn från disken.
14. För **kontotyp**, väljer du antingen **Standard (HDD)** eller **Premium (SSD)** lagring.
15. I **källtyp**, se till att **ögonblicksbild** har valts.
16. I den **källögonblicksbild** listrutan, Välj den ögonblicksbild som du vill använda.
17. Gör eventuella justeringar efter behov och välj sedan **skapa** att skapa disken.

## <a name="create-a-vm-from-a-disk"></a>Skapa en virtuell dator från en disk

När du har den hanterade disken virtuella Hårddisk som du vill använda kan skapa du den virtuella datorn i portalen:

1. Från den [Azure-portalen](https://portal.azure.com), på den vänstra menyn väljer **alla tjänster**.
2. I den **alla tjänster** sökrutan, ange **diskar** och välj sedan **diskar** att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. Den **Disk** sidan för disken öppnas.
4. I den **översikt** kontrollerar du att **DISKSTATUSEN** anges som **ej ansluten**. Om det inte finns kan du behöva antingen ta bort disken från den virtuella datorn eller ta bort den virtuella datorn att frigöra disken.
4. På menyn överst på sidan Välj **Create VM**.
5. På den **grunderna** för den nya virtuella datorn och ange en **virtuellt datornamn** och välj antingen en befintlig **resursgrupp** eller skapa en ny.
6. För **storlek**väljer **ändra storleken på** åtkomst till den **storlek** sidan.
7. Markera en rad för VM-storlek och välj sedan **Välj**.
8. På den **nätverk** sidan kan du antingen låta portalen skapa alla nya resurser eller kan du välja en befintlig **virtuellt nätverk** och **nätverkssäkerhetsgrupp**. Portalen skapar alltid ett nytt nätverksgränssnitt och offentliga IP-adressen för den nya virtuella datorn. 
9. På den **Management** sidan, göra ändringar i vilka övervakningsalternativ.
10. På den **gäst config** , Lägg till några tillägg efter behov.
11. När du är klar väljer du **granska + skapa**. 
12. Om VM-konfigurationen har klarat verifieringen väljer **skapa** att starta distributionen.

## <a name="next-steps"></a>Nästa steg

Du kan också använda PowerShell för att [ladda upp en virtuell Hårddisk till Azure och skapa en specialiserad virtuell dator](create-vm-specialized.md).


