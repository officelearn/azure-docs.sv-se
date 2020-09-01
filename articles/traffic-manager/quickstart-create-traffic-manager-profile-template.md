---
title: Skapa en Traffic Manager med hjälp av Azure Resource Manager-mall (ARM-mall)
description: Lär dig hur du skapar en Azure Traffic Manager-profil med Azure Resource Manager-mall (ARM-mall).
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 08/24/2020
ms.openlocfilehash: bc7cc3220f228a4c8f15370b7b0fcde56db8186d
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237364"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Snabb start: skapa en Traffic Manager profil med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en Traffic Manager profil med externa slut punkter med metoden för att dirigera prestanda.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

En Azure-resurs har definierats i mallen:

* [**Microsoft. Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Du hittar fler mallar som är relaterade till Azure Traffic Manager i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj **prova** från följande kodblock för att öppna Azure Cloud Shell och följ sedan anvisningarna för att logga in på Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Vänta tills du ser prompten från-konsolen.

1. Kopiera PowerShell-skriptet genom att välja **Kopiera** från föregående kodblock.

1. Högerklicka på fönstret Shell-konsol och välj sedan **Klistra in**.

1. Ange värdena.

    Mallen för distribution skapar en profil med två externa slut punkter. **Endpoint1** använder en mål slut punkt på *w<span>WW. Microsoft</span>. com* med platsen i **Nord Europa**. **Endpoint2** använder en mål slut punkt av *d<span>OCS. Microsoft</span>. com* med platsen i **södra centrala USA**. 

    Resurs gruppens namn är projekt namnet med **RG** tillagt.

    > [!NOTE]
    > **uniqueDNSname** måste vara ett globalt unikt namn för att mallen ska kunna distribueras korrekt. Om distributionen Miss lyckas börjar du med steg 1.

    Det tar några minuter att distribuera mallen. När det är slutfört ser utdata ut ungefär så här:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager-mall PowerShell-distribution av utdata":::

Azure PowerShell används för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Bestäm DNS-namnet för Traffic Manager profilen med [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Kopiera värdet **RelativeDnsName** . DNS-namnet för din Traffic Manager-profil är *<* relativednsname *>. trafficmanager.net*. 

1. Från en lokal PowerShell kör du följande kommando genom att ersätta variabeln **{relativeDNSname}** med *<* relativeDNSname *>. trafficmanager.net*.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```
    Du bör få en NameHost av antingen *w<span>WW. Microsoft</span>. com* eller *d<span>OCS. Microsoft</span>. com* beroende på vilken region som är närmast dig.

1. Du kan kontrol lera om du kan matcha den andra slut punkten genom att inaktivera slut punkten för målet som du fick i det sista steget. Ersätt **{endpointName}** med antingen **endpoint1** eller **endpoint2** för att inaktivera målet för *w<span>WW. Microsoft</span>. com* eller *d<span>OCS. Microsoft</span>. com* .

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```
1. Kör kommandot från steg 2 igen i en lokal PowerShell. Den här gången bör du hämta de andra NameHost för den andra slut punkten. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resurs grupperna, webb programmen och alla relaterade resurser med hjälp av [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:
* Traffic Manager-profil

Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)