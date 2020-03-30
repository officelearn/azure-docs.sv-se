---
title: Flytta ett virtuellt Azure-nätverk till en annan Azure-region med Azure-portalen.
description: Flytta ett virtuellt Azure-nätverk från en Azure-region till en annan med hjälp av en Resource Manager-mall och Azure-portalen.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: c54725d9a947b0c912a822686d7b2cffe1a7b5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75640796"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Flytta ett virtuellt Azure-nätverk till en annan region med hjälp av Azure-portalen

Det finns olika scenarier för att flytta ett befintligt Virtuellt Azure-nätverk från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet som det befintliga virtuella nätverket. Du kanske vill flytta ett virtuellt produktionsnätverk till en annan region som en del av planeringen för haveriberedskap.

Du kan använda en Azure Resource Manager-mall för att slutföra flytten av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen. Mer information om Resource Manager-mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Krav

- Kontrollera att ditt virtuella nätverk finns i Azure-regionen som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region måste du ha rollen Nätverksbidragsgivare eller högre.

- Virtuella nätverks peerings skapas inte igen och de misslyckas om de fortfarande finns i mallen. Innan du exporterar mallen måste du ta bort alla virtuella nätverks peers. Du kan sedan återupprätta dem igen efter det virtuella nätverket flytta.

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och offentliga IPs.

- Kontrollera att din Azure-prenumeration gör att du kan skapa virtuella nätverk i målregionen. Kontakta supporten för att aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av virtuella nätverk för den här processen. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-for-the-move"></a>Förbered för flytten
I det här avsnittet förbereder du det virtuella nätverket för flytten med hjälp av en Resource Manager-mall. Du flyttar sedan det virtuella nätverket till målregionen med hjälp av Azure-portalen.

Så här exporterar du det virtuella nätverket och distribuerar det virtuella målnätverket med hjälp av Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj sedan **Resursgrupper**.
1. Leta reda på resursgruppen som innehåller det virtuella källnätverket och markera den sedan.
1. Välj**mall**för **exportera inställningar** > .
1. Välj **Distribuera**i **mallfönstret Exportera** .
1. Om du vill öppna *filen parameters.json* i onlineredigeraren väljer du **Parametrar för Mallredigering** > **Edit parameters**.
1. Om du vill redigera parametern för det virtuella nätverksnamnet ändrar du **egenskapen värde** under **parametrar:**

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. I redigeraren ändrar du källvärdet för virtuellt nätverk i redigeraren till ett namn som du vill använda för det virtuella nätverket för målet. Var noga med att bifoga namnet inom citationstecken.

1. Välj **Spara** i redigeraren.

1. Om du vill öppna *filen template.json* i onlineredigeraren väljer du**Mallredigeringsmall** **Template** > .

1. Ändra platsegenskapen under **resurser**för att redigera målområdet **location** där det virtuella nätverket ska flyttas i onlineredigeraren:

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är regionnamnet, utan blanksteg (till exempel **Central US** = **centralus**).

1. (Valfritt) Du kan också ändra andra parametrar i mallen, beroende på dina krav:

    * **Adressutrymme:** Innan du sparar filen kan du ändra adressutrymmet för det virtuella nätverket genom att ändra avsnittet **resurser** > **addressSpace** och ändra egenskapen **addressPrefixes:**

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **Undernät**: Du kan ändra eller lägga till undernätsnamnet och undernätsadressutrymmet genom att ändra avsnittet **mallens undernät.** Du kan ändra namnet på undernätet **name** genom att ändra namnegenskapen. Och du kan ändra undernätsadressutrymmet genom att ändra **egenskapen addressPrefix:**

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        Om du vill ändra adressprefixet i *filen template.json* redigerar du det på två ställen: i koden i föregående avsnitt och i **avsnittet typ** i följande kod. Ändra **egenskapen addressPrefix** i följande kod för att matcha **egenskapen addressPrefix** i koden i föregående avsnitt.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Välj **Spara**i onlineredigeraren .

1. Om du vill välja den prenumeration där målvirka nätverket ska distribueras väljer du **Basics** > **Subscription**.

1. Om du vill välja den resursgrupp där målvirtivt nätverk ska distribueras väljer du**resursgrupp** **för grunder** > . 

    Om du behöver skapa en ny resursgrupp för det virtuella målnätverket väljer du **Skapa nytt**. Kontrollera att namnet inte är samma som källresursgruppnamnet i det befintliga virtuella nätverket.

1. Kontrollera att **Basics-platsen** > **Location** är inställd på målplatsen där du vill att det virtuella nätverket ska distribueras.

1. Kontrollera att namnet matchar namnet som du angav tidigare i parameterredigeraren under **Inställningar.**

1. Markera kryssrutan **Regler och villkor.**

1. Om du vill distribuera det virtuella målnätverket väljer du **Köp**.

## <a name="delete-the-target-virtual-network"></a>Ta bort det virtuella målnätverket

Om du vill ignorera det virtuella målnätverket tar du bort resursgruppen som innehåller det virtuella målnätverket. Gör så här:
1. Välj resursgruppen på Instrumentpanelen i Azure-portalen.
1. Högst upp i **fönstret Översikt** väljer du Ta **bort**.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av det virtuella nätverket tar du bort det virtuella källnätverket eller resursgruppen. Gör så här:
1. På Instrumentpanelen för Azure-portalen väljer du det virtuella nätverket eller resursgruppen.
1. Högst upp i varje fönster väljer du **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett virtuellt Azure-nätverk från en region till en annan med hjälp av Azure-portalen och rensade sedan upp de onödiga källresurserna. Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
