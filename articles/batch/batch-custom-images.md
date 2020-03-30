---
title: Etablera en anpassad pool från en hanterad avbildning – Azure Batch | Microsoft-dokument
description: Skapa en batchpool från en hanterad avbildningsresurs för att etablera beräkningsnoder med programvaran och data för ditt program.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77020156"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Använda en hanterad avbildning för att skapa en pool med virtuella datorer

Om du vill skapa en anpassad avbildning för batchpoolens virtuella datorer kan du använda antingen [det delade bildgalleriet](batch-sig-images.md)eller en *hanterad avbildningsresurs.*

> [!TIP]
> I de flesta fall bör du skapa anpassade bilder med det delade bildgalleriet. Genom att använda det delade bildgalleriet kan du etablera pooler snabbare, skala större mängder virtuella datorer och få bättre tillförlitlighet när du etablerar virtuella datorer. Mer information finns [i Använda det delade bildgalleriet för att skapa en anpassad pool](batch-sig-images.md).

## <a name="prerequisites"></a>Krav

- **En hanterad bildresurs**. Om du vill skapa en pool med virtuella datorer med en anpassad avbildning måste du ha eller skapa en hanterad avbildningsresurs i samma Azure-prenumeration och -region som batchkontot. Avbildningen ska skapas från ögonblicksbilder av den virtuella datorns OS-disk och eventuellt dess anslutna datadiskar. Mer information och steg för att förbereda en hanterad avbildning finns i följande avsnitt.
  - Använd en unik anpassad avbildning för varje pool du skapar.
  - Om du vill skapa en pool med avbildningen med hjälp av batch-API:erna anger du avbildningens **resurs-ID,** som är av formuläret `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Använd **namnet** på bilden om du vill använda portalen.  
  - Den hanterade avbildningsresursen bör finnas under poolens livstid för att tillåta nedskalning och kan tas bort när poolen har tagits bort.

- **Azure Active Directory (AAD) autentisering**. Batch-klient-API:et måste använda AAD-autentisering. Azure Batch-stöd för AAD dokumenteras i [Authenticate Batch-tjänstlösningar med Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Förbereda en anpassad bild

I Azure kan du förbereda en hanterad avbildning från:

- Ögonblicksbilder av en Azure VM:s operativsystem och datadiskar
- En generaliserad Azure-virtuell dator med hanterade diskar
- En generaliserad lokal virtuell hårddisk som laddas upp till molnet

Om du vill skala batchpooler på ett tillförlitligt sätt med en anpassad avbildning rekommenderar vi att du skapar en hanterad avbildning med *endast* den första metoden: med hjälp av ögonblicksbilder av den virtuella datorns diskar. Se följande steg för att förbereda en virtuell dator, ta en ögonblicksbild och skapa en avbildning från ögonblicksbilden.

### <a name="prepare-a-vm"></a>Förbereda en virtuell dator

Om du skapar en ny virtuell dator för avbildningen använder du en azure Marketplace-avbildning från första part som stöds av Batch som basavbildning för din hanterade avbildning. Endast första partsbilder kan användas som basbild. Information om hur du får en fullständig lista över Azure Marketplace-avbildningsreferenser som stöds av Azure Batch finns i åtgärden [Lista nodagent SKU: er.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Du kan inte använda en avbildning från tredje part som har ytterligare licens- och inköpsvillkor som basavbildning. Information om dessa Marketplace-avbildningar finns i vägledningen för [virtuella Linux-](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) eller [Windows-datorer.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

- Se till att den virtuella datorn skapas med en hanterad disk. Det här är standardlagringsinställningen när du skapar en virtuell dator.
- Installera inte Azure-tillägg, till exempel tillägget Anpassat skript, på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan Azure stöta på problem när batch-poolen distribueras.
- När du använder bifogade datadiskar måste du montera och formatera diskarna inifrån en virtuell dator för att kunna använda dem.
- Kontrollera att den bas-OS-avbildning du anger använder standardtempenheten. Batch-nodagenten förväntar sig för närvarande standardtemp-enheten.
- När den virtuella datorn är igång, ansluta till den via RDP (för Windows) eller SSH (för Linux). Installera all nödvändig programvara eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en ögonblicksbild av en virtuell dator

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. Om du vill skapa en ögonblicksbild av en virtuell dators operativsystem eller datadiskar kan du använda Azure-portalen eller kommandoradsverktygen. Steg och alternativ för att skapa en ögonblicksbild finns i vägledningen för [virtuella Linux-](../virtual-machines/linux/snapshot-copy-managed-disk.md) eller [Windows-datorer.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en bild från en eller flera ögonblicksbilder

Om du vill skapa en hanterad avbildning från en ögonblicksbild använder du Azure-kommandoradsverktyg som kommandot [az image create.](/cli/azure/image) Du kan skapa en avbildning genom att ange en ögonblicksbild av en OS-disk och eventuellt en eller flera ögonblicksbilder av datadiskar.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Skapa en pool från en anpassad avbildning i portalen

När du har sparat din anpassade avbildning och du känner till dess resurs-ID eller namn skapar du en batchpool från den avbildningen. Följande steg visar hur du skapar en pool från Azure-portalen.

> [!NOTE]
> Om du skapar poolen med hjälp av något av batch-API:erna kontrollerar du att den identitet du använder för AAD-autentisering har behörighet till avbildningsresursen. Se [Autentiska batch-servicelösningar med Active Directory](batch-aad-auth.md).
>
> Resursen för den hanterade avbildningen måste finnas under poolens livstid. Om den underliggande resursen tas bort kan poolen inte skalas.

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som resursgruppen som innehåller den anpassade avbildningen.
2. Välj menyalternativet **Pooler** i fönstret **Inställningar** till vänster.
3. Välj kommandot **Lägg till** i fönstret **Pooler.**
4. I fönstret **Lägg till pool** väljer du Anpassad **avbildning (Linux/Windows)** i listrutan **Avbildningstyp.** I listrutan **Anpassad vm-avbildning** väljer du bildnamnet (kort form av resurs-ID).
5. Välj rätt **Utgivare/Erbjudande/Sku** för din anpassade bild.
6. Ange de återstående nödvändiga inställningarna, inklusive **nodstorleken,** **måldedicerade noder**och **noder**med låg prioritet , samt eventuella önskade valfria inställningar.

    För en anpassad avbildning i Microsoft Windows Server Datacenter 2016 visas fönstret **Lägg till pool** som visas nedan:

    ![Lägga till pool från anpassad Windows-avbildning](media/batch-custom-images/add-pool-custom-image.png)
  
Information om du vill kontrollera om en befintlig pool baseras på en anpassad avbildning finns i egenskapen **Operativsystem** i resurssammanfattningsavsnittet i **poolfönstret.** Om poolen skapades från en anpassad avbildning är den inställd på **Anpassad vm-avbildning**.

Alla anpassade bilder som är associerade med en **Properties** pool visas i poolens egenskapsfönster.

## <a name="considerations-for-large-pools"></a>Överväganden för stora pooler

Om du planerar att skapa en pool med hundratals virtuella datorer eller fler med hjälp av en anpassad avbildning är det viktigt att följa föregående vägledning för att använda en avbildning som skapats från en ögonblicksbild av den virtuella datorn.

Observera också följande överväganden:

- **Storleksbegränsningar** - Batch begränsar poolstorleken till 2500 dedikerade beräkningsnoder, eller 1000 lågprioriterade noder, när du använder en anpassad avbildning.

  Om du använder samma avbildning (eller flera bilder baserat på samma underliggande ögonblicksbild) för att skapa flera pooler, kan de totala beräkningsnoderna i poolerna inte överskrida föregående gränser. Vi rekommenderar inte att du använder en avbildning eller dess underliggande ögonblicksbild för mer än en enda pool.

  Gränserna kan minskas om du konfigurerar poolen med [inkommande NAT-pooler](pool-endpoint-configuration.md).

- **Ändra storlek på timeout** – Om poolen innehåller ett fast antal noder (inte automatiskt ska börja), öka egenskapen resizeTimeout för poolen till ett värde som 20-30 minuter. Om poolen inte når sin målstorlek inom timeout-perioden utför du en annan [storleksstorlek.](/rest/api/batchservice/pool/resize)

  Om du planerar en pool med mer än 300 beräkningsnoder kan du behöva ändra storlek på poolen flera gånger för att nå målstorleken.
  
Genom att använda det [delade bildgalleriet](batch-sig-images.md)kan du skapa större pooler med anpassade bilder tillsammans med fler repliker för delad bild. Med delade bilder är tiden det tar för poolen att nå det stabila tillståndet upp till 25 % snabbare och den inaktiva svarstiden för den virtuella datorn är upp till 30 % kortare.

## <a name="considerations-for-using-packer"></a>Överväganden för att använda Packer

Skapa en hanterad avbildningsresurs direkt med Packer kan bara göras med batchkonton för användarprenumerationsläge. För batchtjänstlägeskonton måste du först skapa en virtuell hårddisk och sedan importera den virtuella hårddisken till en hanterad avbildningsresurs. Beroende på poolallokeringsläget (användarprenumeration eller Batch-tjänst) varierar stegen för att skapa en hanterad avbildningsresurs.

Kontrollera att resursen som används för att skapa den hanterade avbildningen finns under livstiden för alla poolreferenser som refererar till den anpassade avbildningen. Om du inte gör det kan det leda till att poolallokeringsfel och/eller fel ändras.

Om bilden eller den underliggande resursen tas bort kan `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`du få ett fel som liknar: . Om du får det här felet kontrollerar du att den underliggande resursen inte har tagits bort.

Mer information om hur du använder Packer för att skapa en virtuell dator finns i [Skapa en Linux-avbildning med Packer](../virtual-machines/linux/build-image-with-packer.md) eller [Skapa en Windows-avbildning med Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Nästa steg

En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
