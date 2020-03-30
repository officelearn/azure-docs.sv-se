---
title: Flytta en extern Azure-belastningsutjämnare till en annan Azure-region med hjälp av Azure-portalen
description: Använd en Azure Resource Manager-mall för att flytta en extern belastningsutjämnare från en Azure-region till en annan med hjälp av Azure-portalen.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 5cd5ce2635ce05c4d5962f12ddc3945342897ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638535"
---
# <a name="move-an-external-load-balancer-to-another-region-by-using-the-azure-portal"></a>Flytta en extern belastningsutjämnare till en annan region med hjälp av Azure-portalen

Det finns olika scenarier där du vill flytta en extern belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en annan extern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en extern belastningsutjämnare till en annan region som en del av katastrofåterställningsplanering.

I bokstavlig mening kan du inte flytta en extern Azure-belastningsutjämning från en region till en annan. Men du kan använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen och den offentliga IP-adressen för en extern belastningsutjämnare. Du kan sedan arrangera resursen i en annan region genom att exportera belastningsutjämningsutjämningen och den offentliga IP-adressen till en mall, ändra parametrarna så att de matchar målregionen och sedan distribuera mallen till den nya regionen. Mer information om Resurshanteraren och mallar finns i [Exportera resursgrupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Krav

- Kontrollera att Azures externa belastningsutjämnare finns i Azure-regionen som du vill flytta från.

- Azure externa belastningsutjämnare kan inte flyttas mellan regioner. Du måste associera den nya belastningsutjämnaren med resurser i målregionen.

- Om du vill exportera en extern belastningsutjämnad konfiguration och distribuera en mall för att skapa en extern belastningsutjämnare i en annan region måste du tilldelas rollen Nätverksdeltagare eller högre.

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper, offentliga IP-adresser och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa externa belastningsutjämnare i målregionen. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av belastningsutjämnare. Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

## <a name="prepare-and-move"></a>Förbereda och flytta
Följande procedurer visar hur du förbereder den externa belastningsutjämnaren för flytten med hjälp av en Resource Manager-mall och flyttar konfigurationen för extern belastningsutjämnare till målregionen med hjälp av Azure-portalen. Du måste först exportera den offentliga IP-konfigurationen för extern belastningsutjämning.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-the-public-ip-from-the-portal"></a>Exportera den offentliga IP-mallen och distribuera den offentliga IP-adressen från portalen

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.
2. Leta reda på resursgruppen som innehåller käll-IP-adressen och välj den.
3. Välj**mall**för **exportera inställningar** > .
4. Välj **Distribuera** under **Exportmall**.
5. Välj > **MALLRedigeringsparametrar** för att öppna filen parameters.json i onlineredigeraren. **TEMPLATE**
8. Om du vill redigera parametern för det offentliga IP-namnet ändrar du värdeegenskapen under **parametrar** från källans offentliga IP-namn till namnet på din offentliga IP-adress. **value** Omge namnet inom citationstecken.

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```

    Välj **Spara** i redigeraren.

9.  Välj **MALLRedigeringsmall** > **Edit template** för att öppna filen template.json i onlineredigeraren.

10. Om du vill redigera målområdet som den offentliga **location** IP-adressen ska flyttas till ändrar du platsegenskapen under **resurser:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```
  
    Information om hur du hämtar platskoder för region finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är regionnamnet utan blanksteg. Koden för centrala USA är till exempel **centralus**.
    
12. Du kan också ändra andra parametrar i mallen om du vill eller behöver, beroende på dina krav:

    * **SKU**. Du kan ändra SKU för den offentliga IP-adressen i konfigurationen från **name** standard till grundläggande eller från grundläggande till standard genom att ändra namnegenskapen under **sku** i filen template.json:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Information om skillnaderna mellan offentliga vanliga IP-adresser och standard-SKU finns i [Skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

    * **Offentlig IP-allokeringsmetod** och **inaktiv timeout**. Du kan ändra den offentliga IP-allokeringsmetoden genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller från **statisk** till **dynamisk**. Du kan ändra tidsgränsen för inaktiv tidsutskrift genom att ändra egenskapen **idleTimeoutInMinutes** till önskat värde. Standardvärdet är **4**.

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Information om allokeringsmetoder och inaktiva timeout-värden finns i [Skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

 
13. Välj **Spara** i onlineredigeraren.

14. Välj **BASICS-prenumeration** > **Subscription** för att välja den prenumeration där mål-offentliga IP ska distribueras.

15. Välj**RESURSGRUPP** **FÖR BASICS** > om du vill välja den resursgrupp där den offentliga mål-IP-adressen ska distribueras. Du kan välja **Skapa ny** om du vill skapa en ny resursgrupp för den offentliga IP-adressen för målet. Kontrollera att namnet inte är samma som källresursgruppen för den befintliga käll-IP-adressen.

16. Kontrollera att **BASICS-platsen** > **Location** är inställd på målplatsen där du vill att den offentliga IP-adressen ska distribueras.

17. Kontrollera att namnet matchar namnet som du angav tidigare i parameterredigeraren under **INSTÄLLNINGAR.**

18. Markera kryssrutan **VILLKOR.**

19. Välj **Inköp för** att distribuera den offentliga ip-adressen för målet.

20. Om du har en annan offentlig IP som används för utgående NAT för belastningsutjämnaren som flyttas upprepar du föregående steg för att exportera och distribuera den andra utgående offentliga IP-adressen till målregionen.

### <a name="export-the-external-load-balancer-template-and-deploy-the-load-balancer-from-the-azure-portal"></a>Exportera den externa belastningsutjämnaren och distribuera belastningsutjämnaren från Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.
2. Leta reda på resursgruppen som innehåller källans externa belastningsutjämnare och markera den.
3. Välj**mall**för **exportera inställningar** > .
4. Välj **Distribuera** under **Exportmall**.
5. Välj > **MALLRedigeringsparametrar** för att öppna filen parameters.json i onlineredigeraren. **TEMPLATE**

5. Om du vill redigera parametern för namnet på den externa belastningsutjämningen ändrar du **egenskapen för** källans externa belastningsutjämnare till namnet på målutvända belastningsutjämnaren. Omge namnet inom citationstecken.

    ```json
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
          "loadBalancers_myLoadbalancer_ext_name": {
          "value": "<target-external-lb-name>"
    },
          "publicIPAddresses_myPubIP_in_externalid": {
          "value": "<target-publicIP-resource-ID>"
    },

    ```

6.  Om du vill redigera värdet för den offentliga mål-IP-adressen som du flyttade i föregående steg måste du först hämta resurs-ID:et och sedan klistra in det i filen parameters.json. Så här hämtar du ID:et:

    1. I en annan webbläsarflik eller ett annat fönster loggar du in på [Azure-portalen](https://portal.azure.com) och väljer **Resursgrupper**.
    2. Leta reda på målresursgruppen som innehåller den offentliga IP-adress som du flyttade i föregående steg. Välj den.
    3. Välj **inställningsegenskaper** > **Properties**.
    4. Markera **resurs-ID:t** och kopiera det till Urklipp i bladet till höger. Du kan också välja **kopiera till Urklipp till** höger om **sökvägen till resurs-ID.**
    5. Klistra in resurs-ID:t i **egenskapen Value** i redigeraren **Redigera parametrar** som är öppen i det andra webbläsarfönstret eller fliken:

        ```json
           ```json
           "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
           "contentVersion": "1.0.0.0",
           "parameters": {
              "loadBalancers_myLoadbalancer_ext_name": {
              "value": "<target-external-lb-name>"
        },
              "publicIPAddresses_myPubIP_in_externalid": {
              "value": "<target-publicIP-resource-ID>"
        },

        ```
    6. Välj **Spara** i onlineredigeraren.


7.  Om du har konfigurerat utgående NAT och utgående regler för belastningsutjämnaren visas en tredje post i den här filen för det externa ID:t för den utgående offentliga IP-adressen. Upprepa föregående steg i **målregionen** för att hämta ID:et för den utgående offentliga IP-adressen. Klistra in ID:et i filen parameters.json:

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "value": "<target-external-lb-name>",

            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "value": "<target-publicIP-resource-ID>",

            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",

            }
        },
    ```

8.  Välj **MALLRedigeringsmall** > **Edit template** för att öppna filen template.json i onlineredigeraren.
9.  Om du vill redigera målområdet som konfigurationen för extern belastningsutjämnare ska flyttas till ändrar du platsegenskapen under **resurser** i filen template.json: **location**

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

10. Information om hur du hämtar platskoder för region finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är regionnamnet utan blanksteg. Koden för centrala USA är till exempel **centralus**.

11. Du kan också ändra andra parametrar i mallen om du vill eller behöver, beroende på dina krav:

    * **SKU**. Du kan ändra SKU för den externa belastningsutjämnaren i konfigurationen **name** från standard till grundläggande eller från grundläggande till standard genom att ändra namnegenskapen under **sku** i filen template.json:

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-external-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      Information om skillnaderna mellan vanliga SKU-belastningsutjämnlar finns i [översikt över Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

    * **Regler för belastningsutjämning**. Du kan lägga till eller ta bort belastningsutjämningsregler i konfigurationen genom att lägga till eller ta bort poster i avsnittet **loadBalancingRules** i filen template.json:

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
       Information om belastningsutjämningsregler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

    * **Sonder**. Du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i filen template.json:

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
       Mer information finns i [Belastningsutjämnare hälsoavsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

    * **Inkommande NAT-regler**. Du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i avsnittet **inboundNatRules i** filen template.json:

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
        Om du vill slutföra tillägget eller borttagningen av en inkommande **type** NAT-regel måste regeln finnas eller tas bort som en typegenskap i slutet av filen template.json:

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
        Information om inkommande NAT-regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Regler för utgående.** Du kan lägga till eller ta bort utgående regler i konfigurationen genom att redigera egenskapen **outboundRules** i filen template.json:

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         Mer information finns i [Utgående regler för belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).

12. Välj **Spara** i onlineredigeraren.

13. Välj **BASICS-prenumeration** > **Subscription** för att välja den prenumeration där målet externa belastningsutjämnaren ska distribueras.

15. Välj**RESURSGRUPP** **FÖR BASICS** > om du vill välja den resursgrupp där målbelastningsutjämnaren ska distribueras. Du kan välja **Skapa ny** om du vill skapa en ny resursgrupp för målutländsk belastningsutjämnare. Du kan också välja den befintliga resursgrupp som du skapade tidigare för den offentliga IP-adressen. Kontrollera att namnet inte är samma som källresursgruppen för den befintliga källans externa belastningsutjämnare.

16. Kontrollera att **BASICS-platsen** > **Location** är inställd på målplatsen där du vill att den externa belastningsutjämnaren ska distribueras.

17. Kontrollera att namnet matchar namnet som du angav tidigare i parameterredigeraren under **INSTÄLLNINGAR.** Kontrollera att resurs-ID:n är ifyllda för alla offentliga IPs i konfigurationen.

18. Markera kryssrutan **VILLKOR.**

19. Välj **Inköp för** att distribuera den offentliga ip-adressen för målet.

## <a name="discard"></a>Ignorera

Om du vill ignorera målet offentlig IP och extern belastningsutjämnare tar du bort resursgruppen som innehåller dem. Om du vill göra det markerar du resursgruppen från instrumentpanelen i portalen och väljer sedan **Ta bort** högst upp på översiktssidan.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av den offentliga IP-adressen och den externa belastningsutjämnaren tar du bort källan till offentlig IP och extern belastningsutjämnare eller resursgrupp. Om du vill göra det markerar du resursgruppen från instrumentpanelen i portalen och väljer sedan **Ta bort** högst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en extern Azure-belastningsutjämning från en region till en annan och rensade källresurserna. Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
