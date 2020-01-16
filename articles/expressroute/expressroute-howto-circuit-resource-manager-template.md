---
title: 'Azure ExpressRoute-mall: skapa en ExpressRoute-krets'
description: Skapa, etablera, ta bort och avetablera en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981141"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Skapa en ExpressRoute-krets med hjälp av Azure Resource Manager mall

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Lär dig hur du skapar en ExpressRoute-krets genom att distribuera en Azure Resource Manager-mall med hjälp av Azure PowerShell. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallen referens](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Innan du börjar

* Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Kontrollera att du har behörighet att skapa nya nätverksresurser. Kontakta din kontoadministratör om du inte har rätt behörigheter.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du påbörjar för att bättre förstå stegen.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets

[Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) har en utmärkt samling Resource Manager-mall. Du kan använda en av de [befintliga mallarna](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) för att skapa en ExpressRoute-krets.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Om du vill se fler relaterade mallar väljer du [här](https://azure.microsoft.com/resources/templates/?term=expressroute).

Så här skapar du en ExpressRoute-krets genom att distribuera en mall:

1. Välj **prova** med följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **SKU-nivån** avgör om en ExpressRoute-krets är [lokal](expressroute-faqs.md#expressroute-local), standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *lokal*, *standard* eller *Premium*.
   * **SKU-serien** bestämmer fakturerings typen. Du kan ange *Metereddata* för ett abonnemang med förbrukade data och *Unlimiteddata* för en obegränsad dataplan. Du kan ändra typen fakturering från *Metereddata* till *Unlimiteddata*, men du kan inte ändra typen från *Unlimiteddata* till *Metereddata*. En *lokal* krets är endast *Unlimiteddata* .
   * **Peeringplatsen** är den fysiska platsen där du peer med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysisk plats](expressroute-locations.md) där du peer med Microsoft. Det här är **inte** länkad till ”plats”-egenskap som refererar till geografiska plats där Azure-nätverkets Resursprovider finns. Även om de inte är relaterade är en bra idé att välja en Provider för nätverksresurser geografiskt nära platsen Peering för kretsen.

    Resurs gruppens namn är namnet på Service Bus-namnområdet med **RG** tillagt.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på Shell-konsolen och välj sedan **Klistra in**.

Det tar en stund att skapa en Event Hub.

Azure PowerShell används för att distribuera mallen i den här självstudien. För andra metoder för mall distribution, se:

* Med [hjälp av Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Med [hjälp av Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* Med [hjälp av REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Du kan ta bort ExpressRoute-kretsen genom att välja den **ta bort** ikon. Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om åtgärden misslyckas, kan du kontrollera om alla virtuella nätverken är länkade till kretsen.
* Om leverantören för ExpressRoute-krets Etableringsstatus är **etablering** eller **etablerad** måste du samarbeta med din tjänstleverantör för att avetablera kretsen på sidan. Vi fortsätter att reservera resurser och debitera dig tills tjänstleverantören har slutförts avetablera kretsen och meddelar oss.
* Om tjänsteleverantören har tagit bort etableringen kretsen (tjänstleverantören Etableringsstatus är inställd på **inte etablerats**), kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort ExpressRoute-kretsen genom att köra följande PowerShell-kommando:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets fortsätter du med följande steg:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
