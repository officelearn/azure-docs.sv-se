---
title: Använd det delade avbildnings galleriet för att skapa en anpassad pool
description: Skapa en batch-pool med det delade avbildnings galleriet för att etablera anpassade avbildningar för att beräkna noder som innehåller den program vara och de data som du behöver för ditt program. Anpassade avbildningar är ett effektivt sätt att konfigurera datornoder för att köra batch-arbetsbelastningar.
ms.topic: article
ms.date: 08/28/2019
ms.openlocfilehash: 45f721dbdf11e0a6f58da71c644acf687dfadd49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82116527"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>Använd det delade avbildnings galleriet för att skapa en anpassad pool

När du skapar en Azure Batch-pool med den virtuella dator konfigurationen anger du en VM-avbildning som tillhandahåller operativ systemet för varje Compute-nod i poolen. Du kan skapa en pool med virtuella datorer antingen med en Azure Marketplace-avbildning som stöds eller skapa en anpassad avbildning med det [delade avbildnings galleriet](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Fördelar med galleriet för delad avbildning

När du använder det delade avbildnings galleriet för din anpassade avbildning har du kontroll över operativ systemets typ och konfiguration, samt typ av data diskar. Den delade avbildningen kan innehålla program och referens data som blir tillgängliga på alla noder i batch-poolen så snart de är etablerade.

Du kan också ha flera versioner av en avbildning efter behov för din miljö. När du använder en avbildnings version för att skapa en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn.

Genom att använda en delad avbildning sparar du tid när du förbereder poolens datornoder för att köra batch-arbetsbelastningen. Det är möjligt att använda en Azure Marketplace-avbildning och installera program vara på varje Compute-nod efter etableringen, men med hjälp av en delad avbildning är det oftast mer effektivt. Dessutom kan du ange flera repliker för den delade avbildningen, så när du skapar pooler med många virtuella datorer (över 600 virtuella datorer) sparar du tid när du skapar en pool.

Att använda en delad avbildning som kon figurer ATS för ditt scenario kan ge flera fördelar:

* **Använd samma bilder i regionerna.** Du kan skapa delade avbildnings repliker i olika regioner så att alla pooler använder samma bild.
* **Konfigurera operativ systemet (OS).** Du kan anpassa konfigurationen för avbildningens operativ system disk.
* **Förinstallera program.** Förinstallerade program på operativ system disken är mer effektivt och mindre fel känsliga än att installera program när du har upprättat Compute-noderna med en start uppgift.
* **Kopiera stora mängder data en gång.** Gör statisk data del av den hanterade delade avbildningen genom att kopiera den till en hanterad avbildnings data diskar. Detta måste bara göras en gång och göra data tillgängliga för varje nod i poolen.
* **Utöka pooler till större storlekar.** Med det delade avbildnings galleriet kan du skapa större pooler med dina anpassade avbildningar tillsammans med fler delade avbildnings repliker.
* **Bättre prestanda än anpassad avbildning.** Med hjälp av delade avbildningar kan den tid det tar för poolen att uppnå stabilt tillstånd vara upp till 25% snabbare och svars tiden för den virtuella datorn är upp till 30% kortare.
* **Avbildnings version och gruppering för enklare hantering.** Definitionen för avbildnings gruppering innehåller information om varför avbildningen skapades, vilket operativ system den är för och information om hur du använder avbildningen. Gruppering av avbildningar möjliggör enklare bild hantering. Mer information finns i [bild definitioner](../virtual-machines/windows/shared-image-galleries.md#image-definitions).

## <a name="prerequisites"></a>Krav

> [!NOTE]
> Du måste autentisera med hjälp av Azure AD. Om du använder Shared Key auth får du ett autentiseringsfel.  

* **Ett Azure Batch-konto.** Information om hur du skapar ett batch-konto finns i batch-startstarter med [Azure Portal](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

* **En bild av ett delat bild galleri**. Om du vill skapa en delad avbildning måste du ha eller skapa en hanterad avbildnings resurs. Avbildningen bör skapas från ögonblicks bilder av den virtuella datorns OS-disk och eventuellt anslutna data diskar. Mer information finns i [förbereda en hanterad avbildning](#prepare-a-managed-image).

> [!NOTE]
> Din delade avbildning måste vara i samma prenumeration som batch-kontot. Din delade avbildning kan finnas i olika regioner så länge den har repliker i samma region som batch-kontot.

## <a name="prepare-a-managed-image"></a>Förbereda en hanterad avbildning

I Azure kan du förbereda en hanterad avbildning från:

* Ögonblicks bilder av en virtuell Azure-dators operativ system och data diskar
* En generaliserad virtuell Azure-dator med Managed disks
* En generaliserad lokal virtuell hård disk som laddats upp till molnet

För att skala batch-pooler på ett tillförlitligt sätt med en anpassad avbildning rekommenderar vi att du skapar en hanterad avbildning med *bara* den första metoden: använda ögonblicks bilder av den virtuella datorns diskar. Se följande steg för att förbereda en virtuell dator, ta en ögonblicks bild och skapa en avbildning från ögonblicks bilden.

### <a name="prepare-a-vm"></a>Förbered en virtuell dator

Om du skapar en ny virtuell dator för avbildningen använder du en Azure Marketplace-avbildning från första part som stöds av batch som bas avbildning för din hanterade avbildning. Endast första parts bilder kan användas som en bas avbildning. Om du vill få en fullständig lista över referens för Azure Marketplace-avbildningar som stöds av Azure Batch, se [noden List Node agent SKU: er](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Du kan inte använda en avbildning från tredje part som har ytterligare licens-och inköps villkor som bas avbildning. Information om dessa Marketplace-avbildningar finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -eller [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -datorer.

* Se till att den virtuella datorn har skapats med en hanterad disk. Det här är standard lagrings inställningen när du skapar en virtuell dator.
* Installera inte Azure-tillägg, till exempel tillägget för anpassat skript, på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan Azure drabbas av problem när du distribuerar batch-poolen.
* När du använder anslutna data diskar måste du montera och Formatera diskarna inifrån en virtuell dator för att använda dem.
* Kontrol lera att den grundläggande OS-avbildningen som du anger använder standard-Temp-enheten. Batch Node-agenten förväntar sig för närvarande standard-Temp-enheten.
* När den virtuella datorn är igång ansluter du till den via RDP (för Windows) eller SSH (för Linux). Installera nödvändig program vara eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en ögonblicks bild av en virtuell dator

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Om du vill skapa en ögonblicks bild av en virtuell dators operativ system eller data diskar kan du använda Azure Portal eller kommando rads verktygen. Steg och alternativ för att skapa en ögonblicks bild finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -eller [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -datorer.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en avbildning från en eller flera ögonblicks bilder

Om du vill skapa en hanterad avbildning från en ögonblicks bild använder du kommando rads verktyg i Azure, till exempel kommandot [AZ image Create](/cli/azure/image) . Skapa en avbildning genom att ange en ögonblicks bild av en OS-disk och eventuellt en eller flera ögonblicks bilder av data diskar.

### <a name="create-a-shared-image-gallery"></a>Skapa ett galleri för delad avbildning

När du har skapat den hanterade avbildningen måste du skapa ett delat avbildnings Galleri för att göra din anpassade avbildning tillgänglig. Information om hur du skapar ett delat avbildnings Galleri för dina avbildningar finns i [skapa ett delat avbildnings galleri med Azure CLI](../virtual-machines/linux/shared-images.md) eller [skapa ett delat avbildnings galleri med hjälp av Azure Portal](../virtual-machines/linux/shared-images-portal.md).

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Skapa en pool från en delad avbildning med hjälp av Azure CLI

Använd `az batch pool create` kommandot för att skapa en pool från den delade avbildningen med hjälp av Azure CLI. Ange det delade avbildnings-ID `--image` : t i fältet. Kontrol lera att OS-typen och SKU: n matchar de versioner som anges av`--node-agent-sku-id`

> [!NOTE]
> Du måste autentisera med hjälp av Azure AD. Om du använder Shared Key auth får du ett autentiseringsfel.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Skapa en pool från en delad avbildning med hjälp av C #

Du kan också skapa en pool från en delad avbildning med hjälp av C# SDK.

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

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Skapa en pool från en delad avbildning med hjälp av Azure Portal

Använd följande steg för att skapa en pool från en delad avbildning i Azure Portal.

1. Öppna [Azure Portal](https://portal.azure.com).
1. Gå till **batch-konton** och välj ditt konto.
1. Välj **pooler** och **Lägg** sedan till för att skapa en ny pool.
1. I avsnittet **avbildnings typ** väljer du **delat avbildnings Galleri**.
1. Slutför de återstående avsnitten med information om din hanterade avbildning.
1. Välj **OK**.

![Skapa en pool med från en delad avbildning med portalen.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Att tänka på för stora pooler

Använd följande vägledning om du planerar att skapa en pool med hundratals eller tusentals virtuella datorer eller mer med en delad avbildning.

* **Delade avbildnings galleriets replik nummer.**  För varje pool med upp till 600 instanser rekommenderar vi att du behåller minst en replik. Om du till exempel skapar en pool med 3000 virtuella datorer bör du behålla minst 5 repliker av avbildningen. Vi rekommenderar alltid att du behåller fler repliker än minimi kraven för bättre prestanda.

* **Tids gräns för storleks ändring.** Om poolen innehåller ett fast antal noder (om den inte är autoskalning) ökar du `resizeTimeout` egenskapen för poolen beroende på Poolens storlek. För varje 1000 virtuella datorer är den rekommenderade tids gränsen minst 15 minuter. Till exempel är den rekommenderade tids gränsen för en pool med 2000 virtuella datorer minst 30 minuter.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
