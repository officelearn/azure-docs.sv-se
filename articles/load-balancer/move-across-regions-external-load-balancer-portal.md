---
title: Flytta externa Azure-Load Balancer till en annan Azure-region med hjälp av Azure Portal
description: Använd Azure Resource Manager mall för att flytta externa Azure-Load Balancer från en Azure-region till en annan med hjälp av Azure Portal.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: ad7e4c5aaa20722e6158973571fb95eb8d853f4d
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219803"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-the-azure-portal"></a>Flytta externa Azure-Load Balancer till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta din befintliga externa belastningsutjämnare från en region till en annan. Du kanske till exempel vill skapa en extern belastningsutjämnare med samma konfiguration för testning. Du kanske också vill flytta en extern belastningsutjämnare till en annan region som en del av Disaster Recovery-planeringen.

Det går inte att flytta externa Azure-belastningsutjämnare från en region till en annan. Du kan dock använda en Azure Resource Manager mall för att exportera den befintliga konfigurationen och den offentliga IP-adressen till en extern belastningsutjämnare.  Du kan sedan mellanlagra resursen i en annan region genom att exportera belastningsutjämnaren och offentliga IP-adresser till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallarna till den nya regionen.  Mer information om Resource Manager och mallar finns i [Exportera resurs grupper till mallar](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Förutsättningar

- Se till att den externa Azure-belastningsutjämnaren finns i den Azure-region som du vill flytta.

- Det går inte att flytta externa Azure-belastningsutjämnare mellan regioner.  Du måste koppla den nya belastningsutjämnaren till resurser i mål regionen.

- Om du vill exportera en extern belastnings Utjämnings konfiguration och distribuera en mall för att skapa en extern belastningsutjämnare i en annan region behöver du rollen som nätverks deltagare eller högre.

- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper, offentliga IP-adresser och virtuella nätverk.

- Kontrol lera att din Azure-prenumeration låter dig skapa externa belastningsutjämnare i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att lägga till belastningsutjämnare för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder den externa belastningsutjämnaren för flytt med hjälp av en Resource Manager-mall och flyttar den externa belastnings Utjämnings konfigurationen till mål regionen med hjälp av Azure Portal.  Som en del av den här processen måste den offentliga IP-konfigurationen för den externa belastningsutjämnaren inkluderas och måste först utföras innan den externa belastningsutjämnaren flyttas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-the-portal"></a>Exportera den offentliga IP-mallen och distribuera den från portalen

1. Logga in på [Azure Portal](https://portal.azure.com) > **resurs grupper**.
2. Leta upp resurs gruppen som innehåller den offentliga käll-IP-adressen och klicka på den.
3. Välj**Exportera mall**för > **Inställningar** > .
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall** > **Redigera parametrar** för att öppna filen **parametrar. JSON** i online-redigeraren.
8. Om du vill redigera parametern för det offentliga IP-namnet ändrar du egenskapen under **parameter** > **värde** från den offentliga IP-adressen till namnet på din mål-offentliga IP-adress, se till att namnet är inom citat tecken:

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

    Klicka på **Spara** i redigeraren.

9.  Klicka på **mall** > **Redigera mall** för att öppna filen **Template. JSON** i online-redigeraren.

10. Om du vill redigera mål regionen där den offentliga IP-adressen ska flyttas ändrar du egenskapen **location** under **resurser**:

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

11. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA** =  **, centrala.**

12. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **SKU** – du kan ändra SKU: n för den offentliga IP-adressen i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen **SKU** > -**namn** i filen **Template. JSON** :

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

        Mer information om skillnaderna mellan grundläggande och standard-SKU: er för offentliga IP-adresser finns i [skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address):

    * **Metod för offentlig IP-tilldelning** och **tids gräns för inaktivitet** – du kan ändra båda alternativen i mallen genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller **statisk** till **dynamisk** . Tids gränsen för inaktivitet kan ändras genom att ändra egenskapen **idleTimeoutInMinutes** till önskad mängd.  Standardvärdet är **4**:

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

        Mer information om fördelnings metoder och tids gräns värden för inaktivitet finns i [skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Klicka på **Spara** i redigeraren online.

14. Klicka på **grundläggande** > **prenumeration** för att välja den prenumeration där den offentliga mål-IP-adressen ska distribueras.

15. Klicka på **grundläggande** > **resurs grupp** för att välja den resurs grupp där den offentliga mål-IP-adressen ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för den offentliga IP-adressen.  Se till att namnet inte är samma som käll resurs gruppen för den befintliga offentliga IP-adressen.

16. Kontrol lera att **grundläggande** > **platser** är inställt på den mål plats där du vill att den offentliga IP-adressen ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.

18. Markera kryss rutan under **allmänna**villkor.

19. Klicka på knappen **köp** för att distribuera den offentliga mål-IP-adressen.
20. Om du har en annan offentlig IP-adress som används för utgående NAT för den belastningsutjämnare som flyttas upprepar du stegen ovan för att exportera och distribuera den andra utgående offentliga IP-adressen till mål regionen.

### <a name="export-the-external-load-balancer-template-and-deploy-from-the-azure-portal"></a>Exportera den externa belastnings Utjämnings mal len och distribuera från Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) > **resurs grupper**.
2. Leta upp den resurs grupp som innehåller den externa käll-belastningsutjämnaren och klicka på den.
3. Välj**Exportera mall**för > **Inställningar** > .
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall** > **Redigera parametrar** för att öppna filen **parametrar. JSON** i online-redigeraren.

5. Om du vill redigera-parametern för den externa belastnings Utjämnings namnet ändrar du egenskap **svärdet** för det externa namnet på den externa belastningsutjämnaren till namnet på den externa mål belastnings utjämningen, se till att namnet är i citat tecken:

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

6.  Om du vill redigera värdet för den offentliga mål-IP-adress som flyttades ovan måste du först hämta resurs-ID: t och sedan kopiera och klistra in det i filen **Parameters. JSON** . Hämta ID:

    1. Logga in på [Azure Portal](https://portal.azure.com) > **resurs grupper** i en annan flik i webbläsaren eller fönstret.
    2. Leta upp mål resurs gruppen som innehåller den flyttade offentliga IP-adressen från stegen ovan och klicka på den.
    3. Välj > **Inställningar** > **Egenskaper**.
    4. Markera **resurs-ID** i bladet till höger och kopiera det till Urklipp.  Alternativt kan du klicka på knappen **Kopiera till Urklipp** till höger om sökvägen till **resurs-ID: t** .
    5. Klistra in resurs-ID: t i egenskapen **Value** i **Redigera parameter** redigeraren öppna i det andra webbläsarfönstret eller fliken:

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
    6. Klicka på **Spara** i redigeraren online.


7.  Om du har konfigurerat utgående NAT och utgående regler för belastningsutjämnaren finns en tredje post i den här filen för det externa ID: t för den utgående offentliga IP-adressen.  Upprepa stegen ovan i **mål regionen** för att hämta ID: t för den utgående offentliga IP-adressen och klistra in posten i filen **Parameters. JSON** :

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

8.  Klicka på **mall** > **Redigera mall** för att öppna filen **Template. JSON** i online-redigeraren.
9.  Om du vill redigera mål regionen där den externa belastnings Utjämnings konfigurationen ska flyttas ändrar du egenskapen **location** under **resurser** i filen **Template. JSON** :

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

10. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA** =  **, centrala.**

11. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:

    * **SKU** – du kan ändra SKU: n för den externa belastningsutjämnaren i konfigurationen från standard till Basic eller Basic till standard genom att ändra egenskapen **SKU** > -**namn** i filen **Template. JSON** :

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
      Mer information om skillnaderna mellan Basic-och standard SKU-belastningsutjämnare finns i [Översikt över Azure standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)

    * **Belastnings Utjämnings regler** – du kan lägga till eller ta bort belastnings Utjämnings regler i konfigurationen genom att lägga till eller ta bort poster i avsnittet **loadBalancingRules** i filen **Template. JSON** :

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
       Mer information om belastnings Utjämnings regler finns i [Vad är Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **Avsökningar** – du kan lägga till eller ta bort en avsökning för belastningsutjämnaren i konfigurationen genom att lägga till eller ta bort poster i avsnittet **avsökningar** i filen **Template. JSON** :

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
       Mer information om Azure Load Balancer hälso avsökningar finns i [Load Balancer hälso avsökningar](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **Ingående NAT-regler** – du kan lägga till eller ta bort inkommande NAT-regler för belastningsutjämnaren genom att lägga till eller ta bort poster i avsnittet **inboundNatRules** i filen **Template. JSON** :

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
        Om du vill slutföra tillägg eller borttagning av en inkommande NAT-regel måste regeln finnas eller tas bort som en **typ** egenskap i slutet av **mallens. JSON** -fil:

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

    * **Utgående regler** – du kan lägga till eller ta bort utgående regler i konfigurationen genom att redigera egenskapen **outboundRules** i filen **Template. JSON** :

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

         Mer information om utgående regler finns i [Load Balancer utgående regler](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)

12. Klicka på **Spara** i redigeraren online.

13. Klicka på **grundläggande** > **prenumeration** för att välja den prenumeration där den externa mål belastnings balansen ska distribueras.

15. Klicka på **grundläggande** > **resurs grupp** för att välja resurs gruppen där mål belastnings utjämningen ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för den externa mål belastnings utjämningen eller välja den befintliga resurs grupp som skapades ovan för den offentliga IP-adressen.  Se till att namnet inte är samma som käll resurs gruppen för den befintliga externa käll belastningsutjämnaren.

16. Kontrol lera att **grundläggande** > **platser** är inställt på den mål plats där du vill att den externa belastningsutjämnaren ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.  Kontrol lera att resurs-ID: n är ifyllda för alla offentliga IP-adresser i konfigurationen.

18. Markera kryss rutan under **allmänna**villkor.

19. Klicka på knappen **köp** för att distribuera den offentliga mål-IP-adressen.

## <a name="discard"></a>Kasta bort

Om du vill ta bort den offentliga mål-IP-adressen och den externa belastningsutjämnaren tar du bort resurs gruppen som innehåller den offentliga mål-IP-adressen och den externa belastningsutjämnaren.  Det gör du genom att välja resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av den offentliga IP-adressen och den externa belastningsutjämnaren, tar du bort den offentliga IP-adressen för källan och den externa belastningsutjämnaren eller resurs gruppen. Det gör du genom att välja den offentliga IP-adressen och den externa belastningsutjämnaren eller resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en extern Azure-belastningsutjämnare från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
