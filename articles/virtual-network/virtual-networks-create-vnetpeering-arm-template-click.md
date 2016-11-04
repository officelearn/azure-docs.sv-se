---
title: Skapa VNet Peering med Resource Manager-mallar | Microsoft Docs
description: Lär dig hur du skapar ett virtuellt nätverk med mallarna i Resource Manager.
services: virtual-network
documentationcenter: ''
author: narayanannamalai
manager: jefco
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayanannamalai;annahar

---
# Skapa VNet Peering med Resource Manager-mallar
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Följ stegen nedan för att skapa en VNet-peering med hjälp av Resource Manager-mallar:

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.
   
   > [!NOTE]
   > Obs! PowerShell-cmdleten för att hantera VNet-peering medföljer [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. Texten nedan visar definitionen av en VNet-peeringlänk för VNet1 till VNet2, baserat på scenariot ovan. Kopiera innehållet nedan och spara det i en fil med namnet VNetPeeringVNet1.json.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
3. Avsnittet nedan visar definitionen av en VNet-peeringlänk för VNet2 VNet1, baserat på scenariot ovan.  Kopiera innehållet nedan och spara det i en fil med namnet VNetPeeringVNet2.json.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
   
    Som du kan se i mallen ovan, finns det några konfigurerbara egenskaper för VNet-peering:
   
   | Alternativ | Beskrivning | Standard |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Oavsett om adressutrymmet för en peer-VNet inkluderas som en del av taggen Virtual_network. |Ja |
   | AllowForwardedTraffic |Oavsett om trafik som inte kommer från ett peer-kopplat VNet godkänns eller tas bort. |Nej |
   | AllowGatewayTransit |Tillåter det peer-kopplade VNet att använda din VNet-gateway. |Nej |
   | UseRemoteGateways |Använd din peers VNet-gateway. En gateway måste konfigureras för det peer-kopplade virtuella nätverket och AllowGatewayTransit måste väljas. Du kan inte använda det här alternativet om du har en konfigurerad gateway. |Nej |
   
    Varje länk i VNet-peering har den uppsättning egenskaper som visas ovan. Du kan till exempel ställa in AllowVirtualNetworkAccess till True för VNet-peeringlänken VNet1 till VNet2 och ställa in den till False för VNet-peeringlänken i den andra riktningen.
4. Om du vill distribuera mallfilen kan du köra cmdleten New-AzureRmResourceGroupDeployment för att skapa eller uppdatera distributionen. Mer information om hur du använder Resource Manager-mallar finns i den här [artikeln](../resource-group-template-deploy.md).
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
   
   > [!NOTE]
   > Ersätt resursgruppsnamnet och mallfilen efter behov.
   > 
   > 
   
    Nedan ser du ett exempel som baseras på scenariot ovan:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
   
    Utdata visar:
   
        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
   
    Utdata visar:
   
        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. När distributionen är klar kan du köra cmdleten nedan för att visa peeringstatus:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
   
    Utdata visar:
   
        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    När peering har upprättats i det här scenariot, bör du kunna initiera anslutningarna från en valfri virtuell dator till en annan valfri virtuell dator för båda VNets. Som standard är AllowVirtualNetworkAccess inställt på True och VNet-peering etablerar rätt ACL:er för att tillåta kommunikation mellan VNets. Du kan fortfarande tillämpa nätverkssäkerhetsgruppregler för att blockera anslutningen mellan specifika undernät eller virtuella datorer för att få noggrann kontroll över åtkomsten mellan två virtuella nätverk.  Mer information om hur du skapar nätverkssäkerhetsgruppregler finns i denna [artikel](virtual-networks-create-nsg-arm-ps.md).

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Följ stegen nedan för att skapa en VNet-peering inom prenumerationerna:

1. Logga in till Azure med privilegierad användare A:s konto i prenumeration A och kör följande cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
   
    Detta är inget krav, peering kan upprättas även om användarna individuellt skickar peering-begäranden för sina respektive virtuella nätverk så länge begärandena matchar. Om du lägger till det andra virtuella nätverkets privilegierade användare som användare i det lokala virtuella nätverket blir det lättare att konfigurera inställningen.
2. Logga in till Azure med privilegierad användare B:s konto för prenumeration B och kör följande cmdlet:
   
        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
3. I användare A:s inloggningssession kör du denna cmdlet:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
   
    Här visas hur JSON-filen definieras.  
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
4. I användare B:s inloggningssession kör du följande cmdlet:
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
    Här visas hur JSON-filen definieras:
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
   
    När peering har upprättats i det här scenariot, bör du kunna initiera anslutningarna från en valfri virtuell dator till en annan valfri virtuell dator för båda VNets och för olika prenumerationer.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. I det här scenariot kan du distribuera provmallen nedan för att upprätta VNet-peeringen.  Du behöver ange egenskapen AllowForwardedTraffic till True, vilket möjliggör för den virtuella nätverksenheten i ditt peer-kopplade VNet att skicka och ta emot trafik.
   
    Här är mallen för att skapa en VNet-peering från HubVNet till VNet1. Observera att AllowForwardedTraffic har värdet false.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }
2. Här är mallen för att skapa en VNet-peering från VNet1 till HubVNet. Observera att AllowForwardedTraffic har värdet true.
   
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }
3. När peering har upprättats kan du referera till denna [artikel](virtual-network-create-udr-arm-ps.md) och definiera en användardefinierad väg (UDR) för att omdirigera VNet1-trafik via en virtuell installation för att använda dess funktioner. När du anger ”nästa hopp”-adressen i vägen kan du ange den till IP-adressen för en virtuell enhet i det virtuella peer-nätverket för HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Skapa en peerkoppling mellan virtuella nätverk från olika distributionsmodeller genom att följa dessa steg:

1. Texten nedan visar definitionen för en VNet-peerkopplingslänk för VNET1 till VNET2 i det här scenariot. Endast en länk krävs för att peerkoppla ett klassiskt virtuellt nätverk till ett virtuellt nätverk i Azure Resource Manager.
   
    Se till att ange ditt prenumerations-ID för den plats där det klassiska virtuella nätverket eller VNET2 finns och ändra MyResouceGroup till lämpligt resursgruppsnamn.
   
    {  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  "contentVersion": "1.0.0.0",  "parameters": {  },  "variables": {  },  "resources": [      {      "apiVersion": "2016-06-01",      "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",      "name": "VNET1/LinkToVNET2",      "location": "[resourceGroup().location]",      "properties": {      "allowVirtualNetworkAccess": true,      "allowForwardedTraffic": false,      "allowGatewayTransit": false,      "useRemoteGateways": false,          "remoteVirtualNetwork": {          "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"  }      }      }  ]  }
2. Distribuera mallfilen genom att köra följande cmdlet för att skapa eller uppdatera distributionen.
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
   
        Output shows:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
3. När distributionen är klar kör du följande cmdlet för att visa peerkopplingens status:
   
        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
   
        Output shows:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

När peerkopplingen har upprättats mellan ett klassiskt VNet och ett Resource Manager-VNet bör du kunna initiera anslutningar från valfri virtuell dator i VNET1 till valfri virtuell dator i VNET2 och tvärtom.

<!--HONumber=Sep16_HO3-->


