---
title: Etablera Azure Batch-pool från en anpassad avbildning | Microsoft Docs
description: Skapa en Batch pool från en anpassad avbildning för att etablera beräkningsnoder som innehåller program och data som du behöver för ditt program. Anpassade avbildningar är ett effektivt sätt att konfigurera beräkningsnoder för att köra Batch-arbetsbelastningar.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 10/04/2018
ms.author: danlep
ms.openlocfilehash: 7d0526dd233afd3976b22d257300681db0bfcead
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885226"
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Använda en anpassad avbildning för att skapa en pool med virtuella datorer 

När du skapar ett Azure Batch-pool med konfigurationen av den virtuella datorn, anger du en VM-avbildning som innehåller operativsystemet för varje beräkningsnod i poolen. Du kan skapa en pool med virtuella datorer med en Azure Marketplace-avbildning som stöds, eller med en anpassad avbildning (en VM-avbildning du har skapat och konfigurerat själv). Den anpassade avbildningen måste vara en *hanterad avbildning* resource i samma Azure-prenumeration och region som Batch-kontot.

## <a name="why-use-a-custom-image"></a>Varför ska jag använda en anpassad avbildning?

När du anger en anpassad avbildning måste har du kontroll över operativsystemets konfiguration och vilken typ av operativsystem och datadiskar som ska användas. Den anpassade avbildningen kan innehålla program och referensdata som blir tillgängliga på alla noder för Batch-pool när de har etablerats.

Med en anpassad avbildning sparar tid i att förbereda beräkningsnoder för din pool för att köra Batch-arbetsbelastningen. Du kan använda en Azure Marketplace-avbildning och installera programvaran på varje beräkningsnod efter etablering, kan det vara mer effektivt att använda en anpassad avbildning.

Med en anpassad avbildning som konfigurerats för ditt scenario kan ange flera fördelar:

- **Konfigurera operativsystem (OS)**. Du kan anpassa konfigurationen av avbildningens operativsystemdisken. 
- **Förinstallation program.** Förinstallation av program på OS-disk, vilket är mer effektiv och mindre felbenägna än att installera program när du har etablerat compute-noder med hjälp av en startaktivitet.
- **Spara Omstartstid på virtuella datorer.** Programinstallation kräver normalt att starta om den virtuella datorn, vilket är tidskrävande. Du kan spara Omstartstid genom före installation av program. 
- **Kopiera mycket stora mängder data en gång.** Kontrollera statiska data som en del av den anpassade avbildningen genom att kopiera den till en hanterad avbildning datadiskar. Detta endast måste göras en gång och gör data tillgängliga för varje nod i poolen.
- **Val av disktyper.** Du kan välja mellan att använda premium storage för OS-disk och datadisken.
- **Pooler att växa till stora storlekar.** När du använder en hanterad anpassad avbildning för att skapa en pool kan växa poolen utan att behöva göra kopior av avbildningsblob virtuella hårddiskar. 


## <a name="prerequisites"></a>Förutsättningar

- **En hanterad avbildningsresurs**. Om du vill skapa en pool med virtuella datorer med en anpassad avbildning som du behöver ha eller skapa en hanterad avbildning-resurs i samma Azure-prenumeration och region som Batch-kontot. Avbildningen skapas från ögonblicksbilder av den Virtuella datorns OS-disken och eventuellt dess anslutna datadiskar. Mer information och stegen för att förbereda en hanterad avbildning finns i följande avsnitt. 
  - Använd en anpassad avbildning som är unika för varje pool som du skapar.
  - Om du vill skapa en pool med den avbildningen med hjälp av Batch-API: er, ange den **resurs-ID** av avbildningen, som är i formatet `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Om du vill använda portalen använder den **namn** för avbildningen.  
  - Hanterad avbildningsresursen ska finnas för livslängden för poolen så att skala upp och kan tas bort när poolen har tagits bort.

- **Azure Active Directory (AAD)-autentisering**. Batch-klientens API använda AAD-autentisering. Azure Batch-stöd för AAD dokumenteras i [autentisera Batch service-lösningar med Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Förbereda en anpassad avbildning

I Azure kan du förbereda en hanterad avbildning från ögonblicksbilder av en Azure VM OS och datadiskar, från en generaliserad virtuell Azure-dator med hanterade diskar eller från en generaliserad lokal virtuell Hårddisk som du överför. Om du vill skala Batch-pooler på ett tillförlitligt sätt med en anpassad avbildning vi rekommenderar att du skapar en hanterad avbildning med hjälp av *endast* den första metoden: med hjälp av ögonblicksbilder av den Virtuella datorns diskar. Se följande steg för att förbereda en virtuell dator, ta en ögonblicksbild och skapa en avbildning från ögonblicksbilden. 

### <a name="prepare-a-vm"></a>Förbered en virtuell dator 

Om du skapar en ny virtuell dator för avbildningen, använda en Azure Marketplace-avbildning som stöds av Batch som basavbildning för en hanterad avbildning och sedan anpassa den.  Om du vill hämta en lista över Azure Marketplace-bildreferenser som stöds av Azure Batch finns i den [lista nodagent](/rest/api/batchservice/account/listnodeagentskus) igen. Du kan inte använda en avbildning från tredje part som i basavbildningen.

* Se till att den virtuella datorn skapas med en hanterad disk. Det här är standardinställningen för lagring när du skapar en virtuell dator.
* Installera inte Azure-tillägg, till exempel tillägget för anpassat skript på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan stöta Azure på problem när du distribuerar Batch-pool.
* Kontrollera att den grundläggande OS-avbildningen som du anger använder den temporära standardenheten. Batch-nodagenten för närvarande förväntar sig den temporära standardenheten.
* När Virtuellt datorn körs, kan du ansluta till den via RDP (för Windows) eller SSH (för Linux). Installera programvara som krävs eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en VM-ögonblicksbild

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell Hårddisk. Du kan använda Azure portal eller kommandoradsverktygen för att skapa en ögonblicksbild av en virtuell dators OS- eller datadiskar. Steg och alternativ för att skapa en ögonblicksbild finns vägledning för [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) eller [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) virtuella datorer.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en avbildning från en eller flera ögonblicksbilder

Skapa en hanterad avbildning från en ögonblicksbild genom att använda Azures kommandoradsverktyg som den [az bild skapa](/cli/azure/image#az_image_create) kommando. Du kan skapa en avbildning genom att ange en OS-diskens ögonblicksbild och alternativt en eller flera ögonblicksbilder för data.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Skapa en pool från en anpassad avbildning i portalen

När du har sparat den anpassade avbildningen och du vet att dess resurs-ID eller namn kan du skapa en Batch-pool från avbildningen. Följande steg visar hur du skapar en pool från Azure-portalen.

> [!NOTE]
> Om du skapar poolen med någon av Batch-API: er, se till att den identitet som du använder för AAD-autentisering har behörighet att avbildningsresursen. Se [autentisera Batch service-lösningar med Active Directory](batch-aad-auth.md).
>

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som den resursgrupp som innehåller den anpassade avbildningen. 
2. I den **inställningar** fönstret till vänster, Välj den **pooler** menyalternativ.
3. I den **pooler** väljer den **Lägg till** kommando.
4. På den **Lägg till Pool** väljer **anpassad avbildning (Linux/Windows)** från den **bildtyp** listrutan. Från den **anpassade VM-avbildning** listrutan Välj avbildningsnamn (kort form av resurs-ID).
5. Välj rätt **utgivare/erbjudande/Sku** för den anpassade avbildningen.
6. Ange de återstående nödvändiga inställningar, inklusive den **nodstorlek**, **reserverade Målnoder**, och **låg prioritet noder**, samt alla önskade valfria inställningar.

    Till exempel för en anpassad avbildning i Microsoft Windows Server Datacenter 2016 den **Lägg till Pool** visas enligt nedan:

    ![Lägg till pool från anpassade Windows-avbildning](media/batch-custom-images/add-pool-custom-image.png)
  
Du kan kontrollera om en befintlig pool är baserad på en anpassad avbildning i **operativsystemet** -egenskapen i resursöversiktsavsnittet på den **Pool** fönster. Om poolen har skapats från en anpassad avbildning, den är inställd på **anpassad virtuell datoravbildning**.

Alla anpassade avbildningar som är associerade med en pool visas på poolens **egenskaper** fönster.

## <a name="considerations-for-large-pools"></a>Överväganden för stora pooler

Om du planerar att skapa en pool med hundratals virtuella datorer eller mer med hjälp av en anpassad avbildning är det viktigt att du följer den föregående rekommendationer om du vill använda en avbildning som skapats från en VM-ögonblicksbild.

Tänk också på följande:

- **Storleksbegränsningar** -Batch begränsar poolstorleken på 2500 dedikerade beräkningsnoder eller 1000 lågprioriterade virtuella noder när du använder en anpassad avbildning.

  Om du använder samma avbildning (eller flera avbildningar som baseras på samma underliggande ögonblicksbild) för att skapa flera pooler, totalt antal beräkningsnoderna i poolerna får inte överskrida de föregående gränserna. Vi rekommenderar inte med hjälp av en avbildning eller dess underliggande ögonblicksbild i mer än en enda pool.

  Gränserna kan komma att försämras om du konfigurerade poolen med [inkommande NAT-pooler](pool-endpoint-configuration.md).

- **Tidsgräns för storleksändring** – om din pool innehåller en fast ökar antalet noder (inte automatisk skalning), egenskapen resizeTimeout för poolen till ett värde, till exempel 20 – 30 minuter. Om din pool inte når sin Målstorlek inom tidsgränsen kan du köra en [att ändra storlek på](/rest/api/batchservice/pool/resize).

  Om du planerar en pool med beräkningsnoder i mer än 300, kan du behöva ändra storleken på poolen flera gånger för att nå målstorleken.

## <a name="next-steps"></a>Nästa steg

- En detaljerad översikt över Batch finns [utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
