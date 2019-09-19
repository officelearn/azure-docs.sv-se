---
title: Flytta Azure-Virtual Network till en annan Azure-region med hjälp av Azure Portal.
description: Använd Azure Resource Manager mall för att flytta Azure-Virtual Network från en Azure-region till en annan med hjälp av Azure Portal.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 2a1ee358a6c97b721ec6f0da3eb70269239b0737
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077677"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Flytta Azure-Virtual Network till en annan region med hjälp av Azure Portal

Det finns olika scenarier där du vill flytta dina befintliga virtuella Azure-nätverk (virtuella nätverk) från en region till en annan. Du kanske till exempel vill skapa ett virtuellt nätverk med samma konfiguration för testning och tillgänglighet för ditt befintliga virtuella nätverk. Du kanske också vill flytta ett virtuellt produktions nätverk till en annan region som en del av Disaster Recovery-planeringen.

Du kan använda en Azure Resource Manager-mall för att slutföra flyttningen av det virtuella nätverket till en annan region. Du gör detta genom att exportera det virtuella nätverket till en mall, ändra parametrarna för att matcha mål regionen och sedan distribuera mallen till den nya regionen.  Mer information om Resource Manager och mallar finns i [snabb start: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>Förutsättningar

- Se till att Azure-Virtual Network finns i den Azure-region som du vill flytta från.

- Om du vill exportera ett virtuellt nätverk och distribuera en mall för att skapa ett virtuellt nätverk i en annan region behöver du rollen som nätverks deltagare eller högre.

- Peering för virtuella nätverk återskapas inte och kommer att Miss klar om de fortfarande finns i mallen.  Du måste ta bort alla virtuella nätverks-peer-datorer innan du exporterar mallen och sedan återupprätta peer-datorerna när det virtuella nätverket har flyttats.
    
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Den här layouten omfattar men är inte begränsad till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser.

- Kontrol lera att din Azure-prenumeration låter dig skapa virtuella nätverk i mål regionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrol lera att din prenumeration har tillräckligt med resurser för att kunna lägga till virtuella nätverk för den här processen.  Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## <a name="prepare-and-move"></a>Förbered och flytta
Följande steg visar hur du förbereder det virtuella nätverket för flytt med hjälp av en Resource Manager-mall och flyttar det virtuella nätverket till mål regionen via portalen.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportera mallen och distribuera från portalen

1. Logga in på [Azure Portal](http://portal.azure.com) > **resurs grupper**.
2. Leta upp resurs gruppen som innehåller det virtuella käll nätverket och klicka på den.
3. Välj**Exportera mall**för > **Inställningar** > .
4. Välj **distribuera** på bladet **Exportera mall** .
5. Klicka på **mall** > **Redigera parametrar** för att öppna filen **parametrar. JSON** i online-redigeraren.
6. Om du vill redigera parametern för det virtuella nätverks namnet ändrar du egenskapen **Value** under **parametrar**:
    
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
7. Ändra värdet för namnet på den virtuella käll datorn i redigeraren till ett namn som du väljer för det virtuella mål nätverket. Se till att du omger namnet med citat tecken.

8.  Klicka på **Spara** i redigeraren.

9.  Klicka på **mall** > **Redigera mall** för att öppna filen **Template. JSON** i online-redigeraren. 

10. Redigera mål regionen där det virtuella nätverket ska flyttas genom att ändra **plats** egenskapen under **resurser** i online-redigeraren:

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
 
11. Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA** =  **, centrala.**
 
12. Du kan också ändra andra parametrar i mallen om du väljer, och de är valfria beroende på dina krav:
    
    * **Adress utrymme** – det virtuella nätverkets adress utrymme kan ändras innan du sparar genom att ändra avsnittet **Resources** > **addressSpace** och ändra egenskapen **addressPrefixes** i filen **Template. JSON** :

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

    * **Undernät** – under nätets namn och under nätets adress utrymme kan ändras eller läggas till genom att ändra avsnittet **undernät** i filen **Template. JSON** . Namnet på under nätet kan ändras genom att ändra egenskapen **Name** . Adress utrymmet för under nätet kan ändras genom att ändra egenskapen **addressPrefix** i filen **Template. JSON** :

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

         I filen **Template. JSON** , för att ändra adressprefixet, måste det redige ras på två platser, avsnittet som anges ovan och i avsnittet **typ** nedan.  Ändra egenskapen **addressPrefix** så att den matchar det som beskrivs ovan:

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

13. Klicka på **Spara** i redigeraren online.

14. Klicka på **grundläggande** > **prenumeration** för att välja den prenumeration där det virtuella mål nätverket ska distribueras.

15. Klicka på **grundläggande** > **resurs grupp** för att välja den resurs grupp där det virtuella mål nätverket ska distribueras.  Du kan klicka på **Skapa ny** för att skapa en ny resurs grupp för det virtuella mål nätverket.  Se till att namnet inte är samma som käll resurs gruppen för det befintliga virtuella nätverket. 

16. Kontrol lera att **grundläggande** > **platser** är inställt på den målplats där du vill att det virtuella nätverket ska distribueras.

17. Verifiera under **Inställningar** att namnet matchar namnet som du angav i parameter redigeraren ovan.

18. Markera kryss rutan under **allmänna**villkor.

19. Klicka på knappen **köp** för att distribuera det virtuella mål nätverket.

## <a name="discard"></a>Kasta bort 

Om du vill ta bort det virtuella mål nätverket tar du bort resurs gruppen som innehåller det virtuella mål nätverket.  Det gör du genom att välja resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på sidan Översikt.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flyttningen av det virtuella nätverket tar du bort det virtuella käll nätverket eller resurs gruppen. Det gör du genom att välja det virtuella nätverket eller resurs gruppen från din instrument panel i portalen och välja **ta bort** överst på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure-Virtual Network från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
