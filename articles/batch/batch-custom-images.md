---
title: "Etablera Azure Batch-pooler från anpassade avbildningar | Microsoft Docs"
description: "Du kan skapa en Batch pool från en anpassad avbildning för att etablera compute-noder som innehåller program och data som du behöver för ditt program. Anpassade avbildningar är ett effektivt sätt att konfigurera compute-noder för att köra Batch-arbetsbelastningar."
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 10/11/2017
ms.author: v-dotren
ms.openlocfilehash: d62abd673f89fd51edba721119d1680762a60c76
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Använda en anpassad avbildning som hanterad för att skapa en pool med virtuella datorer 

När du skapar en Azure Batch-pool med hjälp av den virtuella Datorkonfiguration kan ange du en VM-avbildning som innehåller operativsystemet för varje beräkningsnod i poolen. Du kan skapa en pool med virtuella datorer med en Azure Marketplace-avbildning, eller med en anpassad avbildning (en VM-avbildning har skapats och konfigurerats själv). Den anpassade bilden måste vara en *hanterad avbildning* resurs i samma Azure-prenumeration och region som Batch-kontot.

## <a name="why-use-a-custom-image"></a>Varför ska jag använda en anpassad avbildning?
När du anger en anpassad avbildning har kontroll över operativsystemets konfiguration och vilken typ av operativsystem och datadiskar som ska användas. Den anpassade avbildningen kan innehålla program och referensdata som blir tillgängliga på alla noder för Batch-pool så snart de har etablerats.

Med hjälp av en anpassad avbildning sparar tid när du förbereder din pool compute-noder för att köra Batch-arbetsbelastning. Även om du kan använda en Azure Marketplace-avbildning och installera programvara på varje beräkningsnod när du har etablerat, kan med hjälp av en anpassad avbildning vara mer effektivt.

En anpassad avbildning som konfigurerats för ditt scenario ger många fördelar:

- **Konfigurera operativsystem (OS)**. Du kan utföra specialkonfiguration av operativsystemet på den anpassade avbildningen operativsystemdisken. 
- **Förinstallation av program.** Du kan skapa en anpassad avbildning med förinstallerade program på OS-disk, vilket är effektivare och mindre felbenägna än att installera program när du har etablerat datornoderna med startuppgift har ställts.
- **Spara tid för omstart på virtuella datorer.** Installationen kräver normalt på att startas om den virtuella datorn, vilket är tidskrävande. Du kan spara tid för omstart av före installation av program. 
- **Kopiera mycket stora mängder data en gång.** Du kan göra statiska data som en del av den hanterade anpassad avbildningen genom att kopiera den till en hanterad avbildning datadiskar. Detta endast måste göras en gång och gör data tillgängliga för varje nod i poolen.
- **Val av disktyper.** Du kan skapa en anpassad hanterad avbildning från en VHD från en hanterad disken på en ögonblicksbild av diskarna eller egna Linux eller Windows-installation som du har konfigurerat en Azure-VM. Du kan välja att använda premium-lagring för OS-disken och datadisken.
- **Utöka pooler till önskad storlek.** När du använder en hanterad anpassad avbildning för att skapa en pool, kan växa poolen till valfri storlek som du begär. Du behöver inte göra kopior av avbildningsbloben virtuella hårddiskar för antal virtuella datorer. 


## <a name="prerequisites"></a>Krav

- **En hanterad bildresurs**. Om du vill skapa en pool av virtuella datorer med en anpassad avbildning måste du skapa en hanterad bildresurs i samma Azure-prenumeration och region som Batch-kontot. Alternativ att förbereda en hanterad avbildning finns i följande avsnitt.
- **Azure Active Directory (AAD)-autentisering**. API för Batch-klienten måste använda AAD-autentisering. Azure Batch-stöd för AAD dokumenteras i [autentiserar Batch tjänstelösningar med Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Förbereda en anpassad avbildning
Du kan förbereda en hanterad avbildning från en virtuell Hårddisk, en virtuell Azure-dator med hanterade diskar eller en VM-ögonblicksbild. 

När du förbereder avbildningen, Tänk på följande saker övervägas:

* Se till att den grundläggande OS-avbildningen som du använder för att etablera Batch-pooler är har några förinstallerade Azure-tillägg, till exempel tillägget för anpassat skript. Om avbildningen innehåller ett förinstallerat tillägg kan Azure stöta på problem med att distribuera den virtuella datorn.
* Kontrollera att den grundläggande OS-avbildningen som du anger använder temp-standardenheten. Batch nod agenten förväntas för närvarande temp-standardenheten.
* Kan inte tas bort resursen hanterad avbildning som refereras av ett Batch-Pool för livslängden för poolen. Om hanterad avbildningsresursen tas bort och sedan poolen inte kan utökas någon ytterligare. 

### <a name="to-create-a-managed-image"></a>Så här skapar du en hanterad avbildning
Du kan använda alla befintliga förberedda Windows eller Linux operativsystemdisken för att skapa en hanterad avbildning. Till exempel om du vill använda en lokal image sedan ladda upp den lokala disken till ett Azure Storage-konto som är i samma prenumeration och region som Batch-kontot med hjälp av AzCopy eller något annat överföringsverktyg för. Detaljerade steg för att överföra en virtuell Hårddisk och skapa en hanterad avbildning, finns riktlinjer för [Windows](../virtual-machines/windows/upload-generalized-managed.md) eller [Linux](../virtual-machines/linux/upload-vhd.md) virtuella datorer.

Dessutom kan du förbereda en hanterad avbildning från en ny eller befintlig Azure-VM eller VM-ögonblicksbild. 

* Om du skapar en ny virtuell dator kan du använda en Azure Marketplace-avbildning som basavbildningen för hanterade bilden och anpassa den. 

* Se till att den virtuella datorn har skapats med en hanterad disk om du planerar att spara avbildningen med hjälp av portalen. Detta är standardinställningen för lagring när du skapar en virtuell dator.

* När den virtuella datorn körs, kan du ansluta till den via RDP (för Windows) eller SSH (för Linux). Installera programvara som krävs eller kopiera önskade data och sedan generalisera den virtuella datorn.  

Steg för att generalisera en Azure VM och skapa en hanterad avbildning, finns riktlinjer för [Windows](../virtual-machines/windows/capture-image-resource.md) eller [Linux](../virtual-machines/linux/capture-image.md) virtuella datorer.

Beroende på hur du planerar att skapa en Batch-pool med avbildningen, behöver du följande identifierare för avbildningen:

* Om du planerar att skapa en pool med avbildningen med hjälp av Batch-API: er, den **resurs-ID** av avbildningen, som är i formatet `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Om du planerar att använda portalen, den **namn** för bilden. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Skapa en programpool från en anpassad avbildning i portalen

Du kan skapa en Batch-pool för från den avbildningen när du har sparat den anpassade avbildningen och du vet dess resurs-ID eller namn. Följande steg visar hur du skapar en pool från Azure-portalen.

> [!NOTE]
> Om du skapar poolen med hjälp av ett Batch-API: er, se till att identiteten som du använder för AAD-autentisering har behörighet till avbildningsresursen i. Se [autentiserar Batch tjänstelösningar med Active Directory](batch-aad-auth.md).
>

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste vara i samma prenumeration och region som den resursgrupp som innehåller den anpassade avbildningen. 
2. I den **inställningar** fönstret till vänster, Välj den **pooler** menyalternativet.
3. I den **pooler** väljer den **Lägg till** kommando.
4. På den **Lägg till Pool för** väljer **anpassad bild (Linux/Windows)** från den **bildtypen** listrutan. Från den **anpassad VM-avbildning** listrutan väljer du Avbildningsnamnet på (kort form av resurs-ID).
5. Välj rätt **Publisher-erbjudande-Sku** för den anpassade avbildningen.
6. Ange de återstående nödvändiga inställningar, inklusive den **nodstorlek**, **mål dedikerade noder**, och **med låg prioritet noder**, liksom alla önskade valfria inställningar.

    Till exempel för en anpassad avbildning i Microsoft Windows Server Datacenter 2016 den **Lägg till Pool för** visas enligt nedan:

    ![Lägg till pool från anpassade Windows-avbildning](media/batch-custom-images/add-pool-custom-image.png)
  
Du kan kontrollera om en befintlig adresspool baseras på en anpassad avbildning i **operativsystemet** egenskap i sammanfattningen för resurs i den **Pool** fönstret. Om poolen har skapats från en anpassad avbildning, den är inställd på **anpassade VM-avbildning**.

Alla anpassade avbildningar som är associerade med en pool visas på poolens **egenskaper** fönster.
 
## <a name="next-steps"></a>Nästa steg

- En detaljerad översikt över Batch finns [utveckla storskaliga parallell compute lösningar med Batch](batch-api-basics.md).
