---
title: Flytta Azures interna belastningsutjämnare till en annan Azure-region med Azure-portalen
description: Använd Azure Resource Manager-mall för att flytta Azure intern belastningsutjämning från en Azure-region till en annan med hjälp av Azure-portalen
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/18/2019
ms.author: allensu
ms.openlocfilehash: f23923b9d847ef393ebd609eb5fbba530b1a07d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638817"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-the-azure-portal"></a>Flytta Azures interna belastningsutjämnare till en annan region med Azure-portalen

Det finns olika scenarier där du vill flytta din befintliga interna belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en intern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en intern belastningsutjämnare till en annan region som en del av katastrofåterställningsplanering.

Azure interna belastningsutjämnare kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och det virtuella nätverket för en intern belastningsutjämnare.  Du kan sedan arrangera resursen i en annan region genom att exportera belastningsutjämnaren och det virtuella nätverket till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Krav

- Kontrollera att Azures interna belastningsutjämnare finns i Azure-regionen som du vill flytta från.

- Azure interna belastningsutjämnare kan inte flyttas mellan regioner.  Du måste associera den nya belastningsutjämnaren med resurser i målregionen.

- Om du vill exportera en intern belastningsutjämnad konfiguration och distribuera en mall för att skapa en intern belastningsutjämnare i en annan region behöver du rollen Nätverksbidragsgivare eller högre.

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper, virtuella datorer och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa interna belastningsutjämnare i målområdet som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av belastningsutjämnare för den här processen.  Se [Azure-prenumerations- och tjänstbegränsningar, kvoter och begränsningar](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder den interna belastningsutjämnaren för flytten med hjälp av en Resource Manager-mall och flyttar den interna belastningsutjämnarens konfiguration till målregionen med hjälp av Azure-portalen.  Som en del av den här processen måste den virtuella nätverkskonfigurationen för den interna belastningsutjämnaren inkluderas och måste göras först innan den interna belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-the-azure-portal"></a>Exportera den virtuella nätverksmallen och distribuera från Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com) > **Resource Groups**.
2. Leta upp resursgruppen som innehåller det virtuella källnätverket och klicka på den.
3. Välj**mallen** **Exportera inställningar** > > inställningar .
4. Välj **Distribuera** i **mallbladet Exportera.**
5. Klicka på > **MALLRedigeringsparametrar** för att öppna **filen parameters.json** i onlineredigeraren. **TEMPLATE**
6. Om du vill redigera parametern för det virtuella nätverksnamnet ändrar du **egenskapen värde** under **parametrar:**

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
7. Ändra källvärdet för virtuellt nätverk i redigeraren till ett namn som du väljer för mål-virtuella nätverk. Se till att du bifogar namnet inom citationstecken.

8. Klicka på **Spara** i redigeraren.

9. Klicka på > **MALLRedigeringsmall för** att öppna **filen template.json** i onlineredigeraren. **TEMPLATE**

10. Om du vill redigera målområdet där VNET **location** ska flyttas ändrar du platsegenskapen under resurser:

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

11. Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regionnamnet utan blanksteg, **Central US** = **centralus**.

12. Du kan också ändra andra parametrar i **filen template.json** om du vill och är valfria beroende på dina behov:

    * **Adressutrymme** - Adressutrymmet för det virtuella nätverket kan ändras innan du sparar genom att ändra avsnittet **resurser** > **addressSpace** och ändra egenskapen **addressPrefixes** i **filen template.json:**

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

    * **Undernät** - Undernätsnamnet och undernätets adressutrymme kan ändras eller läggas till genom att ändra **undernätsavsnittet** i **filen template.json.** Namnet på undernätet kan ändras genom **name** att ändra namnegenskapen. Undernätsadressutrymmet kan ändras genom att ändra egenskapen **addressPrefix** i **filen template.json:**

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

         Om du vill ändra adressprefixet i filen **template.json** måste det redigeras på två ställen, avsnittet ovan och **avsnittet** nedan.  Ändra **egenskapen addressPrefix** så att den matchar den ovan:

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

13. Klicka på **Spara** i onlineredigeraren.

14. Klicka på > **BASICS-prenumerationen** om du vill välja den prenumeration där mål-virtuella nätverk ska distribueras. **BASICS**

15. Klicka på**RESURSGRUPP** FÖR **BASICS** > om du vill välja den resursgrupp där mål-virtuella nätverk ska distribueras.  Du kan klicka på **Skapa ny** om du vill skapa en ny resursgrupp för mål-virtuella nätverk.  Kontrollera att namnet inte är samma som källresursgruppen för det befintliga virtuella nätverket.

16. Kontrollera **att BASICS-platsen** > **Location** är inställd på målplatsen där du vill att det virtuella nätverket ska distribueras.

17. Kontrollera under **INSTÄLLNINGAR** att namnet matchar namnet som du angav i parameterredigeraren ovan.

18. Markera rutan under **VILLKOR**.

19. Klicka på knappen **Inköp** om du vill distribuera det virtuella målnätverket.

### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>Exportera den interna belastningsutjämnaren och distribuera från Azure PowerShell

1. Logga in på [Azure Portal](https://portal.azure.com) > **Resource Groups**.
2. Leta upp resursgruppen som innehåller källans interna belastningsutjämnare och klicka på den.
3. Välj**mallen** **Exportera inställningar** > > inställningar .
4. Välj **Distribuera** i **mallbladet Exportera.**
5. Klicka på > **MALLRedigeringsparametrar** för att öppna **filen parameters.json** i onlineredigeraren. **TEMPLATE**

6. Om du vill redigera parametern för det interna belastningsutjämnarnamnet ändrar du **egenskapsstandardvärdevärdet** för källans interna belastningsutjämnad namn till namnet på ditt interna målbelastningsutjämnar, se till att namnet är inom citationstecken:

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

6. Om du vill redigera värdet för det virtuella målnätverk som flyttades ovan måste du först hämta resurs-ID:et och sedan kopiera och klistra in det i **filen parameters.json.** Så här hämtar du ID:et:

    1. Logga in på [Azure Portal](https://portal.azure.com) > **Resource Groups** i en annan webbläsarflik eller ett annat fönster.
    2. Leta reda på målresursgruppen som innehåller det flyttade virtuella nätverket från stegen ovan och klicka på den.
    3. Välj**egenskaper**> **Inställningar** > .
    4. Markera **resurs-ID:t** och kopiera det till Urklipp i bladet till höger.  Du kan också klicka på **knappen Kopiera till Urklipp** till höger om **sökvägen till resurs-ID.**
    5. Klistra in resurs-ID:t i **egenskapen defaultValue** i redigeraren **Redigera parametrar** som är öppen i det andra webbläsarfönstret eller fliken:

        ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-internal-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_internalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
        ```
    6. Klicka på **Spara** i onlineredigeraren.

7. Klicka på > **MALLRedigeringsmall för** att öppna **filen template.json** i onlineredigeraren. **TEMPLATE**
8. Om du vill redigera målområdet där den interna belastningsutjämna konfigurationen ska flyttas ändrar du platsegenskapen under **resurser** i **filen template.json:** **location**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

9.  Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regionnamnet utan blanksteg, **Central US** = **centralus**.

10. Du kan också ändra andra parametrar i mallen om du vill och är valfria beroende på dina behov:

    * **Sku** - Du kan ändra sku för den interna belastningsutjämnaren i konfigurationen från standard till grundläggande eller grundläggande till standard genom att ändra **egenskapen sku** > **name** i **filen template.json:**

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Mer information om skillnaderna mellan grundläggande belastningsutjämnare och standardstandard sku finns i [översikt över Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Belastningsutjämningsregler** - Du kan lägga till eller ta bort belastningsutjämningsregler i konfigurationen genom att lägga till eller ta bort poster i avsnittet **loadBalancingRules** i **filen template.json:**

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       Mer information om belastningsutjämningsregler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Avsökningar** - Du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i **filen template.json:**

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Mer information om hälsoavsökningar för Azure Load Balancer finns i [belastningsutjämnare hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Inkommande NAT-regler** - Du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i avsnittet **inboundNatRules i** **filen template.json:**

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        Om du vill slutföra tillägget eller borttagningen av en inkommande **type** NAT-regel måste regeln finnas eller tas bort som en typegenskap i slutet av **filen template.json:**

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        Mer information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

12. Klicka på **Spara** i onlineredigeraren.

13. Klicka på > **BASICS-prenumerationen** om du vill välja den prenumeration där målet för intern belastningsutjämnare ska distribueras. **BASICS**

15. Klicka på**RESURSGRUPPEN** **BASICS** > om du vill välja den resursgrupp där målbelastningsutjämnaren ska distribueras.  Du kan klicka på **Skapa ny** om du vill skapa en ny resursgrupp för den interna belastningsutjämningen för målet eller välja den befintliga resursgruppen som skapades ovan för det virtuella nätverket.  Kontrollera att namnet inte är samma som källresursgruppen för den befintliga källrepliken för belastningsutjämnad källa.

16. Kontrollera **att BASICS-platsen** > **Location** är inställd på målplatsen där du vill att den interna belastningsutjämnaren ska distribueras.

17. Kontrollera under **INSTÄLLNINGAR** att namnet matchar namnet som du angav i parameterredigeraren ovan.  Kontrollera att resurs-ID:n är ifyllda för alla virtuella nätverk i konfigurationen.

18. Markera rutan under **VILLKOR**.

19. Klicka på knappen **Inköp** om du vill distribuera det virtuella målnätverket.

## <a name="discard"></a>Ignorera

Om du vill ignorera det virtuella målnätverket och den interna belastningsutjämnaren tar du bort resursgruppen som innehåller det virtuella målnätverket och den interna belastningsutjämnaren.  Om du vill göra det markerar du resursgruppen från instrumentpanelen i portalen och väljer **Ta bort** högst upp på översiktssidan.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av det virtuella nätverket och intern belastningsutjämnare tar du bort det virtuella källnätverket och den interna belastningsutjämnaren eller resursgruppen. Det gör du genom att välja det virtuella nätverket och den interna belastningsutjämnaren eller resursgruppen från instrumentpanelen i portalen och välja **Ta bort** högst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en intern Azure-belastningsutjämnare från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
