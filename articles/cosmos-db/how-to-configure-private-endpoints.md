---
title: Konfigurera Azure Private Link för ett Azure Cosmos-konto
description: Lär dig hur du konfigurerar Azure Private Link för att komma åt ett Azure Cosmos-konto med hjälp av en privat IP-adress i ett virtuellt nätverk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 944341b1ef88c7e3d64a74536720eb9fb1d17321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152748"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurera Azure Private Link för ett Azure Cosmos-konto

Genom att använda Azure Private Link kan du ansluta till ett Azure Cosmos-konto via en privat slutpunkt. Den privata slutpunkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till ett Azure Cosmos-konto via privata IP-adresser. När Private Link kombineras med begränsade NSG-principer bidrar det till att minska risken för dataexfiltration. Mer information om privata slutpunkter finns i artikeln [Azure Private Link.](../private-link/private-link-overview.md)

Private Link tillåter användare att komma åt ett Azure Cosmos-konto från det virtuella nätverket eller från alla peer-anslutna virtuella nätverk. Resurser som mappas till Privat länk är också tillgängliga lokalt via privat peering via VPN eller Azure ExpressRoute. 

Du kan ansluta till ett Azure Cosmos-konto som konfigurerats med Private Link med hjälp av den automatiska eller manuella godkännandemetoden. Mer information finns i avsnittet [Arbetsflöde för godkännande](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) i dokumentationen för Privat länk. 

I den här artikeln beskrivs stegen för att skapa en privat slutpunkt. Det förutsätter att du använder den automatiska godkännandemetoden.

> [!NOTE]
> Stöd för privata slutpunkter är för närvarande allmänt tillgängligt i regioner som stöds endast för gatewayanslutningsläge. För direktläge är det tillgängligt som en förhandsgranskningsfunktion.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Skapa en privat slutpunkt med hjälp av Azure-portalen

Använd följande steg för att skapa en privat slutpunkt för ett befintligt Azure Cosmos-konto med hjälp av Azure-portalen:

1. Välj ett Azure Cosmos-konto i fönstret **Alla resurser.**

1. Välj **Privata slutpunktsanslutningar** i listan med inställningar och välj sedan **Privat slutpunkt:**

   ![Val för att skapa en privat slutpunkt i Azure-portalen](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. I fönstret **Skapa en privat slutpunkt - Grunderna** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj en resursgrupp.|
    | **Information om instans** |  |
    | Namn | Ange vilket namn som helst för din privata slutpunkt. Om det här namnet tas skapar du ett unikt namn. |
    |Region| Välj den region där du vill distribuera Private Link. Skapa den privata slutpunkten på samma plats där det virtuella nätverket finns.|
    |||
1. Välj **Nästa: Resurs**.
1. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Resurs:**

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i katalogen**. <br/><br/> Du kan sedan välja en av dina resurser för att ställa in Private Link. Du kan också ansluta till någon annans resurs med hjälp av ett resurs-ID eller ett alias som de har delat med dig.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Resurs |Välj ditt Azure Cosmos-konto. |
    |Målunderresurs |Välj den AZURE Cosmos DB API-typ som du vill mappa. Detta är bara ett val för API:erna SQL, MongoDB och Cassandra. För Gremlin- och Table API:er kan du också välja **Sql** eftersom dessa API:er är driftskompatibla med SQL API. |
    |||

1. Välj **Nästa: Konfiguration**.
1. Ange eller välj den här informationen i **Skapa en privat slutpunkt - Konfiguration:**

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj ditt virtuella nätverk. |
    | Undernät | Välj ditt undernät. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. <br><br/> Om du vill ansluta privat till din privata slutpunkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slutpunkt med en privat DNS-zon. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av värdfilerna på dina virtuella datorer. |
    |Privat DNS-zon |Välj **privatelink.documents.azure.com**. <br><br/> Den privata DNS-zonen bestäms automatiskt. Du kan inte ändra den med hjälp av Azure-portalen.|
    |||

1. Välj **Granska + skapa**. På sidan **Granska + skapa** validerar Azure din konfiguration.
1. När meddelandet **Validering har skickats** väljer du **Skapa**.

När du har godkänt Private Link för ett Azure Cosmos-konto är alternativet **Alla nätverk** i fönstret **Brandvägg och virtuella nätverk** inte tillgängligt i Azure-portalen.

I följande tabell visas mappningen mellan olika AZURE Cosmos-konto-API-typer, underresurser som stöds och motsvarande namn på privata zoner. Du kan också komma åt Gremlin- och Table API-konton via SQL API, så det finns två poster för dessa API:er.

|API-typ av Azure Cosmos-konto  |Underresurser som stöds (eller grupp-ID) |Namn på privat zon  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabell    |    Tabell     |   privatelink.table.cosmos.azure.com    |
|Tabell     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slutpunkten har etablerats kan du fråga IP-adresserna. Så här visar du IP-adresser från Azure-portalen:

1. Välj **Alla resurser**.
1. Sök efter den privata slutpunkten som du skapade tidigare. I det här fallet är det **cdbPrivateEndpoint3**.
1. Välj fliken **Översikt** om du vill visa DNS-inställningar och IP-adresser.

![Privata IP-adresser i Azure-portalen](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Flera IP-adresser skapas per privat slutpunkt:

* En för den globala slutpunkten (regionagnostisk) för Azure Cosmos-kontot
* En för varje region där Azure Cosmos-kontot distribueras

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Skapa en privat slutpunkt med hjälp av Azure PowerShell

Kör följande PowerShell-skript för att skapa en privat slutpunkt med namnet "MyPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Ersätt variabelvärdena med information för din miljö.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slutpunkten med en privat DNS-zon

När du har skapat den privata slutpunkten kan du integrera den med en privat DNS-zon med hjälp av följande PowerShell-skript:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slutpunkten har etablerats kan du fråga IP-adresserna och FQDN-mappningen med hjälp av följande PowerShell-skript:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Skapa en privat slutpunkt med hjälp av Azure CLI

Kör följande Azure CLI-skript för att skapa en privat slutpunkt med namnet "myPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Ersätt variabelvärdena med information för din miljö.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slutpunkten med en privat DNS-zon

När du har skapat den privata slutpunkten kan du integrera den med en privat DNS-zon med hjälp av följande Azure CLI-skript:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Skapa en privat slutpunkt med hjälp av en Resource Manager-mall

Du kan ställa in Privat länk genom att skapa en privat slutpunkt i ett virtuellt nätverksundernät. Du uppnår detta genom att använda en Azure Resource Manager-mall.

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateEndpoint_template.json". Den här mallen skapar en privat slutpunkt för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definiera parameterfilen för mallen**

Skapa en parameterfil för mallen och ge den namnet "PrivateEndpoint_parameters.json". Lägg till följande kod i parameterfilen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Distribuera mallen med hjälp av ett PowerShell-skript**

Skapa ett PowerShell-skript med hjälp av följande kod. Innan du kör skriptet ersätter du prenumerations-ID: t, resursgruppsnamn och andra variabelvärden med information för din miljö.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

I PowerShell-skriptet `GroupId` kan variabeln bara innehålla ett värde. Det värdet är kontots API-typ. Tillåtna värden `Sql` `MongoDB`är: `Gremlin`, `Table`, `Cassandra`, och . Vissa Azure Cosmos-kontotyper är tillgängliga via flera API:er. Ett exempel:

* Ett Gremlin API-konto kan nås från både Gremlin- och SQL API-konton.
* Ett tabell-API-konto kan nås från både tabell- och SQL API-konton.

För dessa konton måste du skapa en privat slutpunkt för varje API-typ. Motsvarande API-typ anges i `GroupId` matrisen.

När mallen har distribuerats kan du se ett utdata som liknar vad följande bild visar. Värdet `provisioningState` är `Succeeded` om de privata slutpunkterna är korrekt inställda.

![Distributionsutdata för Resource Manager-mallen](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

När mallen har distribuerats reserveras de privata IP-adresserna i undernätet. Brandväggsregeln för Azure Cosmos-kontot är konfigurerad för att endast acceptera anslutningar från den privata slutpunkten.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slutpunkten med en privat DNS-zon

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateZone_template.json". Den här mallen skapar en privat DNS-zon för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definiera parameterfilen för mallen**

Skapa följande två parameterfil för mallen. Skapa "PrivateZone_parameters.json". med följande kod:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Skapa "PrivateZoneRecords_parameters.json". med följande kod:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Distribuera mallen med hjälp av ett PowerShell-skript**

Skapa ett PowerShell-skript med hjälp av följande kod. Innan du kör skriptet ersätter du prenumerations-ID: t, resursgruppsnamn och andra variabelvärden med information för din miljö.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurera anpassad DNS

Du bör använda en privat DNS-zon i undernätet där du har skapat den privata slutpunkten. Konfigurera slutpunkterna så att varje privat IP-adress mappas till en DNS-post. (Se `fqdns` egenskapen i svaret som visas tidigare.)

När du skapar den privata slutpunkten kan du integrera den med en privat DNS-zon i Azure. Om du väljer att i stället använda en anpassad DNS-zon måste du konfigurera den för att lägga till DNS-poster för alla privata IP-adresser som är reserverade för den privata slutpunkten.

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brandväggsregler

Följande situationer och resultat är möjliga när du använder Private Link i kombination med brandväggsregler:

* Om du inte konfigurerar några brandväggsregler kan all trafik som standard komma åt ett Azure Cosmos-konto.

* Om du konfigurerar offentlig trafik eller en tjänstslutpunkt och skapar privata slutpunkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brandväggsregel.

* Om du inte konfigurerar någon offentlig trafik- eller tjänstslutpunkt och du skapar privata slutpunkter är Azure Cosmos-kontot endast tillgängligt via de privata slutpunkterna. Om du inte konfigurerar offentlig trafik eller en tjänstslutpunkt är kontot öppet för hela nätverket när alla godkända privata slutpunkter har avvisats eller tagits bort.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Uppdatera en privat slutpunkt när du lägger till eller tar bort en region

Om du lägger till eller tar bort regioner i ett Azure Cosmos-konto måste du lägga till eller ta bort DNS-poster för det kontot. När regioner har lagts till eller tagits bort kan du uppdatera undernätets privata DNS-zon för att återspegla de tillagda eller borttagna DNS-posterna och deras motsvarande privata IP-adresser.

Anta till exempel att du distribuerar ett Azure Cosmos-konto i tre regioner: "Västra USA", "Centrala USA" och "Västeuropa". När du skapar en privat slutpunkt för ditt konto reserveras fyra privata IPs i undernätet. Det finns en IP för var och en av de tre regionerna och det finns en IP för den globala/regionoberoende slutpunkten.

Senare kan du lägga till en ny region (till exempel "Östra USA") i Azure Cosmos-kontot. När du har lagt till den nya regionen måste du lägga till en motsvarande DNS-post i antingen din privata DNS-zon eller din anpassade DNS.

Du kan använda samma steg när du tar bort en region. När du har tagit bort regionen måste du ta bort motsvarande DNS-post från antingen din privata DNS-zon eller din anpassade DNS.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder Privat länk med ett Azure Cosmos-konto:

* Private Link-stöd för Azure Cosmos-konton och virtuella nätverk är endast tillgängligt i specifika regioner. En lista över regioner som stöds finns i avsnittet [Tillgängliga regioner](../private-link/private-link-overview.md#availability) i artikeln Privat länk. 

  > [!NOTE]
  > Om du vill skapa en privat slutpunkt kontrollerar du att både det virtuella nätverket och Azure Cosmos-kontot finns i regioner som stöds.

* När du använder Privat länk med ett Azure Cosmos-konto med hjälp av en direktlägesanslutning kan du bara använda TCP-protokollet. HTTP-protokollet stöds ännu inte.

* Stöd för privata slutpunkter är för närvarande allmänt tillgängligt i regioner som stöds endast för gatewayanslutningsläge. För direktläge är det tillgängligt som en förhandsgranskningsfunktion.

* När du använder Azure Cosmos DB:s API för MongoDB-konton stöds en privat slutpunkt endast för konton på serverversion 3.6 (det vill säga konton som använder slutpunkten i formatet `*.mongo.cosmos.azure.com`). Private Link stöds inte för konton på serverversion 3.2 (det vill `*.documents.azure.com`än konton som använder slutpunkten i formatet ). Om du vill använda Privat länk bör du migrera gamla konton till den nya versionen.

* När du använder Azure Cosmos DB:s API för MongoDB-konton som har Privat länk kan du inte använda verktyg som Robo 3T, Studio 3T och Mongoose. Slutpunkten kan bara ha stöd `appName=<account name>` för privat länk om parametern har angetts. Ett exempel är `replicaSet=globaldb&appName=mydbaccountname`. Eftersom dessa verktyg inte skickar appnamnet i anslutningssträngen till tjänsten kan du inte använda Privat länk. Men du kan fortfarande komma åt dessa konton med hjälp av SDK-drivrutiner med 3.6-versionen.

* Du kan inte flytta eller ta bort ett virtuellt nätverk om det innehåller Privat länk.

* Du kan inte ta bort ett Azure Cosmos-konto om det är kopplat till en privat slutpunkt.

* Du kan inte växla över ett Azure Cosmos-konto till en region som inte är mappad till alla privata slutpunkter som är kopplade till kontot.

* En nätverksadministratör bör beviljas minst behörigheten "*/PrivateEndpointConnectionsApproval" i Azure Cosmos-kontoomfånget för att skapa automatiskt godkända privata slutpunkter.

### <a name="limitations-to-private-dns-zone-integration"></a>Begränsningar för integrering av privata DNS-zoner

DNS-poster i den privata DNS-zonen tas inte bort automatiskt när du tar bort en privat slutpunkt eller tar bort en region från Azure Cosmos-kontot. Du måste ta bort DNS-posterna manuellt innan:

* Lägga till en ny privat slutpunkt som är länkad till den här privata DNS-zonen.
* Lägga till en ny region i alla databaskonton som har privata slutpunkter kopplade till den här privata DNS-zonen.

Om du inte rensar DNS-posterna kan oväntade problem med dataplan uppstå. Dessa problem omfattar dataavbrott i regioner som lagts till efter borttagning av privata slutpunkter eller borttagning av region.

## <a name="next-steps"></a>Nästa steg

Mer information om azure Cosmos DB-säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brandvägg för Azure Cosmos DB finns i [Brandväggsstöd](firewall-support.md).

* Mer information om hur du konfigurerar en slutpunkt för en virtuell nätverkstjänst för ditt Azure Cosmos-konto finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md).

* Mer information om Private Link finns i Azure [Private Link-dokumentationen.](../private-link/private-link-overview.md)
