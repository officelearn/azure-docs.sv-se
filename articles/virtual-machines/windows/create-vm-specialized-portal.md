---
title: Skapa en Virtuell Windows-dator från en specialiserad virtuell hårddisk i Azure-portalen
description: Skapa en ny Windows-virtuell dator från en virtuell hårddisk i Azure-portalen.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: d360ac5a57fe72b092a6694721905c066527bba3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086461"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Skapa en virtuell dator från en virtuell hårddisk med hjälp av Azure-portalen

Det finns flera sätt att skapa en virtuell dator (VM) i Azure: 

- Om du redan har en virtuell hårddisk (VHD) att använda eller om du vill kopiera den virtuella hårddisken från en befintlig virtuell dator som ska användas, kan du skapa en ny virtuell dator genom att *koppla* den virtuella hårddisken till den nya virtuella datorn som en OS-disk. 

- Du kan skapa en ny virtuell dator från den virtuella datorn för en virtuell dator som har tagits bort. Om du till exempel har en Virtuell Azure-dator som inte fungerar som den ska kan du ta bort den virtuella datorn och använda dess virtuella hårddisk för att skapa en ny virtuell dator. Du kan antingen återanvända samma virtuella hårddisk eller skapa en kopia av den virtuella hårddisken genom att skapa en ögonblicksbild och sedan skapa en ny hanterad disk från ögonblicksbilden. Även om det tar några steg att skapa en ögonblicksbild, bevaras den ursprungliga virtuella hårddisken och du får en reserv.

- Ta en klassisk virtuell dator och använd den virtuella hårddisken för att skapa en ny virtuell dator som använder Resurshanterarens distributionsmodell och hanterade diskar. För bästa resultat, **Stoppa** den klassiska virtuella datorn i Azure-portalen innan du skapar ögonblicksbilden.
 
- Du kan skapa en virtuell Azure-dator från en lokal virtuell hårddisk genom att ladda upp den lokala virtuella hårddisken och koppla den till en ny virtuell dator. Du använder PowerShell eller ett annat verktyg för att överföra den virtuella hårddisken till ett lagringskonto och sedan skapa en hanterad disk från den virtuella hårddisken. Mer information finns i [Ladda upp en specialiserad virtuell hårddisk](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Använd inte en specialiserad disk om du vill skapa flera virtuella datorer. Skapa [en avbildning](capture-image-resource.md) för större distributioner och använd sedan [avbildningen för att skapa flera virtuella datorer](create-vm-generalized-managed.md).

Vi rekommenderar att du begränsar antalet samtidiga distributioner till 20 virtuella datorer från en enda ögonblicksbild eller virtuell hårddisk. 

## <a name="copy-a-disk"></a>Kopiera en disk

Skapa en ögonblicksbild och skapa sedan en disk från ögonblicksbilden. Med den här strategin kan du behålla den ursprungliga virtuella hårddisken som en reserv:

1. Välj **Alla tjänster**på [Azure-portalen](https://portal.azure.com)på den vänstra menyn .
2. I sökrutan **Alla tjänster** anger du **diskar** och väljer sedan **Diskar** för att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. **Disksidan** för den disken visas.
4. Välj **Skapa ögonblicksbild**högst upp. 
5. Ange ett **namn** för ögonblicksbilden.
6. Välj en **resursgrupp** för ögonblicksbilden. Du kan använda antingen en befintlig resursgrupp eller skapa en ny.
7. För **kontotyp**väljer du antingen **Standard-lagring (HDD)** eller **Premium (SSD).**
8. När du är klar väljer du **Skapa** för att skapa ögonblicksbilden.
9. När ögonblicksbilden har skapats väljer du **Skapa en resurs** på den vänstra menyn.
10. Ange **hanterad disk** i sökrutan och välj sedan **Hanterade diskar** i listan.
11. På sidan **Hanterade diskar** väljer du **Skapa**.
12. Ange ett **namn** för disken.
13. Välj en **resursgrupp** för disken. Du kan använda antingen en befintlig resursgrupp eller skapa en ny. Det här valet används också som resursgrupp där du skapar den virtuella datorn från disken.
14. För **kontotyp**väljer du antingen **Standard-lagring (HDD)** eller **Premium (SSD).**
15. I **Källtyp**kontrollerar du att **ögonblicksbild** är markerat.
16. I **listrutan Källa ögonblicksbild** väljer du den ögonblicksbild du vill använda.
17. Gör andra justeringar efter behov och välj sedan **Skapa** för att skapa disken.

## <a name="create-a-vm-from-a-disk"></a>Skapa en virtuell dator från en disk

När du har den hanterade hårddisken VHD som du vill använda kan du skapa den virtuella datorn i portalen:

1. Välj **Alla tjänster**på [Azure-portalen](https://portal.azure.com)på den vänstra menyn .
2. I sökrutan **Alla tjänster** anger du **diskar** och väljer sedan **Diskar** för att visa listan över tillgängliga diskar.
3. Välj den disk som du vill använda. **Disksidan** för den disken öppnas.
4. Kontrollera att **DISKTILLSTÅND** visas som **Oattached**på sidan **Översikt** . Om den inte är det kan du behöva antingen koppla från disken från den virtuella datorn eller ta bort den virtuella datorn för att frigöra disken.
4. Välj **Skapa virtuell dator**högst upp på sidan .
5. På sidan **Grunderna** för den nya virtuella datorn anger du ett **namn på en virtuell dator** och väljer antingen en befintlig **resursgrupp** eller skapar en ny.
6. För **Storlek**väljer du **Ändra storlek** för att komma åt sidan **Storlek.**
7. Välj en vm-storleksrad och välj sedan **Välj**.
8. På sidan **Nätverk** kan du antingen låta portalen skapa alla nya resurser eller välja en befintlig **säkerhetsgrupp för virtuellt nätverk** och **nätverk**. Portalen skapar alltid ett nytt nätverksgränssnitt och offentlig IP-adress för den nya virtuella datorn. 
9. Gör ändringar i övervakningsalternativen på sidan **Hantering.**
10. Lägg till eventuella tillägg efter behov på sidan **Gäst config.**
11. När du är klar väljer du **Granska + skapa**. 
12. Om vm-konfigurationen skickar validering väljer du **Skapa** för att starta distributionen.


## <a name="next-steps"></a>Nästa steg

Du kan också använda PowerShell för att [ladda upp en virtuell hårddisk till Azure och skapa en specialiserad virtuell dator](create-vm-specialized.md).


