---
title: Använd det delade avbildnings galleriet för att skapa en pool – Azure Batch | Microsoft Docs
description: Skapa en batch-pool med det delade avbildnings galleriet för att etablera anpassade avbildningar för att beräkna noder som innehåller den program vara och de data som du behöver för ditt program. Anpassade avbildningar är ett effektivt sätt att konfigurera datornoder för att köra batch-arbetsbelastningar.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: lahugh
ms.openlocfilehash: 3c2213c25a8fdc6d6545711bd2af9b94662ee609
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141833"
---
# <a name="use-the-shared-image-gallery-to-create-a-pool"></a>Använd det delade avbildnings galleriet för att skapa en pool

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

## <a name="prerequisites"></a>Förutsättningar

* **Ett Azure Batch-konto.** Information om hur du skapar ett batch-konto finns i batch-startstarter med [Azure Portal](quick-create-portal.md) eller [Azure CLI](quick-create-cli.md).

* **En bild av ett delat bild galleri**. Mer information och steg för att förbereda en delad avbildning finns i [skapa ett delat avbildnings galleri med Azure CLI](../virtual-machines/linux/shared-images.md) eller [skapa ett delat avbildnings galleri med hjälp av Azure Portal](../virtual-machines/linux/shared-images-portal.md).

> [!NOTE]
> Din delade avbildning måste vara i samma prenumeration som batch-kontot. Din delade avbildning kan finnas i olika regioner så länge den har repliker i samma region som batch-kontot.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Skapa en pool från en delad avbildning med hjälp av Azure CLI

Använd `az batch pool create` kommandot för att skapa en pool från den delade avbildningen med hjälp av Azure CLI. Ange det delade avbildnings-ID `--image` : t i fältet. Kontrol lera att OS-typen och SKU: n matchar de versioner som anges av`--node-agent-sku-id`

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Skapa en pool från en delad avbildning med hjälp avC#

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

1. Öppna [Azure-portalen](https://portal.azure.com).
1. Gå till **batch-konton** och välj ditt konto.
1. Välj **pooler** och **Lägg** sedan till för att skapa en ny pool.
1. I avsnittet **avbildnings typ** väljer du **delat avbildnings Galleri**.
1. Slutför de återstående avsnitten med information om din hanterade avbildning.
1. Välj **OK**.

![Skapa en pool med från en delad avbildning med portalen.](media/batch-custom-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Att tänka på för stora pooler

Använd följande vägledning om du planerar att skapa en pool med hundratals eller tusentals virtuella datorer eller mer med en delad avbildning.

* **Delade avbildnings galleriets replik nummer.**  För varje pool med upp till 600 instanser rekommenderar vi att du behåller minst en replik. Om du till exempel skapar en pool med 3000 virtuella datorer bör du behålla minst 5 repliker av avbildningen. Vi rekommenderar alltid att du behåller fler repliker än minimi kraven för bättre prestanda.

* **Tids gräns för storleks ändring** Om poolen innehåller ett fast antal noder (om den inte är autoskalning) ökar du `resizeTimeout` egenskapen för poolen beroende på Poolens storlek. För varje 1000 virtuella datorer är den rekommenderade tids gränsen minst 15 minuter. Till exempel är den rekommenderade tids gränsen för en pool med 2000 virtuella datorer minst 30 minuter.

## <a name="next-steps"></a>Nästa steg

* En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
