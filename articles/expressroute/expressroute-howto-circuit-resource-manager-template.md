---
title: 'Azure ExpressRoute-mall: Skapa en ExpressRoute-krets'
description: Skapa, etablera, ta bort och avetablera en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981141"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Skapa en ExpressRoute-krets med hjälp av Azure Resource Manager-mall

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Lär dig hur du skapar en ExpressRoute-krets genom att distribuera en Azure Resource Manager-mall med hjälp av Azure PowerShell. Mer information om hur du utvecklar Resource Manager-mallar finns i [Dokumentationen](/azure/azure-resource-manager/) till Resource Manager och [mallreferensen](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Innan du börjar

* Granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.
* Kontrollera att du har behörighet att skapa nya nätverksresurser. Kontakta kontoadministratören om du inte har rätt behörighet.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

[Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/) har en bra samling av Resource Manager-mall. Du kan använda en av de [befintliga mallarna](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) för att skapa en ExpressRoute-krets.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Om du vill se fler relaterade mallar väljer du [här](https://azure.microsoft.com/resources/templates/?term=expressroute).

Så här skapar du en ExpressRoute Circuit genom att distribuera en mall:

1. Välj **Prova det** från följande kodblock och följ sedan instruktionerna för att logga in på Azure Cloud-skalet.

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

   * **SKU-nivån** avgör om en ExpressRoute-krets är [Lokal,](expressroute-faqs.md#expressroute-local)Standard eller [Premium](expressroute-faqs.md#expressroute-premium). Du kan ange *Lokal,* *Standard* eller *Premium*.
   * **SKU-familjen** bestämmer faktureringstypen. Du kan ange *Datadata för datapriser* för ett dataabonnemang med datapriser och *Unlimiteddata* för ett obegränsat dataabonnemang. Du kan ändra *faktureringstypen* från Datadata till *Unlimiteddata,* men du kan inte ändra typen från *Unlimiteddata* till *Datapriser*. En *lokal* krets är endast *Unlimiteddata.*
   * **Peering-plats** är den fysiska plats där du peering med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peering med Microsoft. Detta är **inte** länkat till egenskapen Plats, som refererar till geografin där Azure Network Resource Provider finns. Även om de inte är relaterade är det en god idé att välja en nätverksresursprovider geografiskt nära peering-platsen för kretsen.

    Resursgruppsnamnet är namnet på servicebussens namnområde med **rg** till.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på skalkonsolen och välj sedan **Klistra in**.

Det tar en stund att skapa en händelsehubb.

Azure PowerShell används för att distribuera mallen i den här självstudien. Andra malldistributionsmetoder finns i:

* [Genom att använda Azure-portalen](../azure-resource-manager/templates/deploy-portal.md).
* [Genom att använda Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Genom att använda REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Du kan ta bort expressroutekretsen genom att välja **borttagningsikonen.** Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om tillståndet för tillhandahållare av ExpressRoute-kretstjänstleverantören **etablerar** eller **etableras** måste du samarbeta med din tjänsteleverantör för att avetablera kretsen på deras sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänsteleverantören slutför avetablering av kretsen och meddelar oss.
* Om tjänsteleverantören har avetablerat kretsen (etableringstillståndet för tjänsteleverantören är inställt på **Inte etablerat)** kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

Du kan ta bort expressroutekretsen genom att köra följande PowerShell-kommando:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen fortsätter du med följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
