---
title: 'Skapa en ExpressRoute-krets - Resource Manager-mall: Azure | Microsoft Docs'
description: Skapa, etablera, ta bort och Avetablerar en ExpressRoute-krets.
services: expressroute;azure-resource-manager
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc;ganesr
ms.openlocfilehash: bf56145d0a8cd3b01d0d74fcaf3348c1916cee5a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659689"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Skapa en ExpressRoute-krets med hjälp av Azure Resource Manager-mall

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Lär dig hur du skapar en ExpressRoute-krets genom att distribuera en Azure Resource Manager-mall med hjälp av Azure PowerShell. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallreferensen](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Innan du börjar

* Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Kontrollera att du har behörighet att skapa nya nätverksresurser. Kontakta din kontoadministratör om du inte har rätt behörigheter.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du påbörjar för att bättre förstå stegen.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets

[Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) har en bra uppsättning Resource Manager-mall. Du använder en av de [befintliga mallar](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) att skapa en ExpressRoute-krets.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Om du vill se flera relaterade mallar, [här](https://azure.microsoft.com/resources/templates/?term=expressroute).

Skapa en ExpressRoute-krets genom att distribuera en mall:

1. Välj **prova** från följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud shell.

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

   * **Nivån** bestämmer om en ExpressRoute-standard eller en ExpressRoute-premiumtillägget är aktiverat. Du kan ange **Standard** att hämta standard-SKU eller **Premium** premium-tillägg.

   * **Peeringplatsen** är den fysiska platsen där du peer med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysisk plats](expressroute-locations.md) där du peer med Microsoft. Det här är **inte** länkad till ”plats”-egenskap som refererar till geografiska plats där Azure-nätverkets Resursprovider finns. Även om de inte är relaterade är en bra idé att välja en Provider för nätverksresurser geografiskt nära platsen Peering för kretsen.

    Resursgruppens namn är den service bus-namnrymden med **rg** sist.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på shell-konsolen och välj sedan **klistra in**.

Det tar en stund att skapa en händelsehubb.

Azure PowerShell används för att distribuera mallen i den här självstudien. Andra metoder för distribution av mallen, finns här:

* [Med hjälp av Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Med hjälp av Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Med hjälp av REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

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
