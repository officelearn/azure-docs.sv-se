---
title: Använd galleriet delad avbildning för att skapa en anpassad pool - Azure Batch | Microsoft-dokument
description: Skapa en batchpool med det delade bildgalleriet för att etablera anpassade avbildningar för att beräkna noder som innehåller den programvara och de data som du behöver för ditt program. Anpassade avbildningar är ett effektivt sätt att konfigurera beräkningsnoder för att köra dina batcharbetsbelastningar.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022944"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Använda galleriet Delad bild för att skapa en anpassad pool

När du skapar en Azure Batch-pool med konfigurationen för virtuella datorer anger du en VM-avbildning som tillhandahåller operativsystemet för varje beräkningsnod i poolen. Du kan skapa en pool med virtuella datorer antingen med en Azure Marketplace-avbildning som stöds eller skapa en anpassad avbildning med [det delade avbildningsgalleriet](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Fördelar med det delade bildgalleriet

När du använder det delade bildgalleriet för den anpassade avbildningen har du kontroll över operativsystemtypen och konfigurationen samt typen av datadiskar. Den delade bilden kan innehålla program och referensdata som blir tillgängliga på alla batchpoolnoder så snart de har etablerats.

Du kan också ha flera versioner av en avbildning efter behov för din miljö. När du använder en avbildningsversion för att skapa en virtuell dator används avbildningsversionen för att skapa nya diskar för den virtuella datorn.

Om du använder en delad avbildning sparar du tid när du förbereder poolens beräkningsnoder för att köra din batch-arbetsbelastning. Det är möjligt att använda en Azure Marketplace-avbildning och installera programvara på varje beräkningsnod efter etablering, men att använda en delad avbildning är vanligtvis effektivare. Dessutom kan du ange flera repliker för den delade avbildningen så att när du skapar pooler med många virtuella datorer (fler än 600 virtuella datorer) sparar du tid när du skapar poolen.

Om du använder en delad avbildning som konfigurerats för ditt scenario kan det ge flera fördelar:

* **Använd samma bilder i regionerna.** Du kan skapa repliker för delad bild i olika regioner så att alla pooler använder samma avbildning.
* **Konfigurera operativsystemet (OS).** Du kan anpassa konfigurationen av avbildningens operativsystemdisk.
* **Förinstallera program.** Förinstallation av program på OS-disken är effektivare och mindre felbenägna än att installera program efter etablering av beräkningsnoderna med en startuppgift.
* **Kopiera stora mängder data en gång.** Gör statiska data till en del av den hanterade delade avbildningen genom att kopiera den till en hanterad avbildningsdatadiskar. Detta behöver bara göras en gång och gör data tillgängliga för varje nod i poolen.
* **Odla pooler till större storlekar.** Med det delade bildgalleriet kan du skapa större pooler med anpassade bilder tillsammans med fler repliker för delad bild.
* **Bättre prestanda än anpassad bild.** Med delade bilder är tiden det tar för poolen att nå det stabila tillståndet upp till 25 % snabbare och den inaktiva svarstiden för den virtuella datorn är upp till 30 % kortare.
* **Bildversionering och gruppering för enklare hantering.** Bildgrupperingsdefinitionen innehåller information om varför bilden skapades, vilket operativsystem den är till för och information om hur du använder bilden. Gruppering av bilder möjliggör enklare bildhantering. Mer information finns i [Bilddefinitioner](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Krav

* **Ett Azure Batch-konto.** Information om hur du skapar ett batchkonto finns i snabbstarten Batch med [Azure-portalen](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

* **En bild i det delade bildgalleriet**. Om du vill skapa en delad bild måste du ha eller skapa en hanterad avbildningsresurs. Avbildningen ska skapas från ögonblicksbilder av den virtuella datorns OS-disk och eventuellt dess anslutna datadiskar. Mer information finns i [Förbereda en hanterad bild](#prepare-a-managed-image).

> [!NOTE]
> Din delade bild måste finnas i samma prenumeration som batchkontot. Den delade bilden kan finnas i olika regioner så länge den har repliker i samma region som ditt batchkonto.

## <a name="prepare-a-managed-image"></a>Förbereda en hanterad bild

I Azure kan du förbereda en hanterad avbildning från:

* Ögonblicksbilder av en Azure VM:s operativsystem och datadiskar
* En generaliserad Azure-virtuell dator med hanterade diskar
* En generaliserad lokal virtuell hårddisk som laddas upp till molnet

Om du vill skala batchpooler på ett tillförlitligt sätt med en anpassad avbildning rekommenderar vi att du skapar en hanterad avbildning med *endast* den första metoden: med hjälp av ögonblicksbilder av den virtuella datorns diskar. Se följande steg för att förbereda en virtuell dator, ta en ögonblicksbild och skapa en avbildning från ögonblicksbilden.

### <a name="prepare-a-vm"></a>Förbereda en virtuell dator

Om du skapar en ny virtuell dator för avbildningen använder du en azure Marketplace-avbildning från första part som stöds av Batch som basavbildning för din hanterade avbildning. Endast första partsbilder kan användas som basbild. Information om hur du får en fullständig lista över Azure Marketplace-avbildningsreferenser som stöds av Azure Batch finns i åtgärden [Lista nodagent SKU: er.](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)

> [!NOTE]
> Du kan inte använda en avbildning från tredje part som har ytterligare licens- och inköpsvillkor som basavbildning. Information om dessa Marketplace-avbildningar finns i vägledningen för [virtuella Linux-](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) eller [Windows-datorer.](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
)

* Se till att den virtuella datorn skapas med en hanterad disk. Det här är standardlagringsinställningen när du skapar en virtuell dator.
* Installera inte Azure-tillägg, till exempel tillägget Anpassat skript, på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan Azure stöta på problem när batch-poolen distribueras.
* När du använder bifogade datadiskar måste du montera och formatera diskarna inifrån en virtuell dator för att kunna använda dem.
* Kontrollera att den bas-OS-avbildning du anger använder standardtempenheten. Batch-nodagenten förväntar sig för närvarande standardtemp-enheten.
* När den virtuella datorn är igång, ansluta till den via RDP (för Windows) eller SSH (för Linux). Installera all nödvändig programvara eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en ögonblicksbild av en virtuell dator

En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. Om du vill skapa en ögonblicksbild av en virtuell dators operativsystem eller datadiskar kan du använda Azure-portalen eller kommandoradsverktygen. Steg och alternativ för att skapa en ögonblicksbild finns i vägledningen för [virtuella Linux-](../virtual-machines/linux/snapshot-copy-managed-disk.md) eller [Windows-datorer.](../virtual-machines/windows/snapshot-copy-managed-disk.md)

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en bild från en eller flera ögonblicksbilder

Om du vill skapa en hanterad avbildning från en ögonblicksbild använder du Azure-kommandoradsverktyg som kommandot [az image create.](/cli/azure/image) Skapa en avbildning genom att ange en ögonblicksbild av en OS-disk och eventuellt en eller flera ögonblicksbilder av datadiskar.

### <a name="create-a-shared-image-gallery"></a>Skapa ett delat bildgalleri

När du har skapat den hanterade avbildningen måste du skapa ett delat bildgalleri för att göra din anpassade avbildning tillgänglig. Mer information om hur du skapar ett delat bildgalleri för dina avbildningar finns i [Skapa ett delat avbildningsgalleri med Azure CLI](../virtual-machines/linux/shared-images.md) eller Skapa ett delat [avbildningsgalleri med Azure-portalen](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Skapa en pool från en delad avbildning med Azure CLI

Om du vill skapa en pool från din `az batch pool create` delade avbildning med Hjälp av Azure CLI använder du kommandot. Ange det delade bild-ID:t `--image` i fältet . Kontrollera att OS-typen och SKU matchar de versioner som anges av`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Skapa en pool från en delad bild med C #

Du kan också skapa en pool från en delad avbildning med C# SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Skapa en pool från en delad avbildning med Azure-portalen

Använd följande steg för att skapa en pool från en delad avbildning i Azure-portalen.

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Gå till **Batch-konton** och välj ditt konto.
1. Välj **Pooler** och **sedan Lägg till** för att skapa en ny pool.
1. Välj **Delat bildgalleri**i avsnittet **Bildtyp** .
1. Fyll i de återstående avsnitten med information om den hanterade avbildningen.
1. Välj **OK**.

![Skapa en pool med från en delad avbildning med portalen.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Överväganden för stora pooler

Om du planerar att skapa en pool med hundratals eller tusentals virtuella datorer eller fler med hjälp av en delad bild använder du följande vägledning.

* **Repliknummer för delat bildgalleri.**  För varje pool med upp till 600 instanser rekommenderar vi att du behåller minst en replik. Om du till exempel skapar en pool med 3 000 virtuella datorer bör du behålla minst 5 repliker av bilden. Vi föreslår alltid att fler repliker än minimikrav för bättre prestanda ska behållas.

* **Ändra storlek på timeout.** Om poolen innehåller ett fast antal noder (om den `resizeTimeout` inte skalas automatiskt) ökar du poolens egenskap beroende på poolens storlek. För varje 1000 virtuella datorer är den rekommenderade tidsgränsen för storleksfördelning minst 15 minuter. Den rekommenderade tidsgränsen för att ändra storlek på timeout för en pool med 2 000 virtuella datorer är till exempel minst 30 minuter.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
