---
title: Konfigurera en privat Azure-länk för ett Azure Cosmos-konto
description: Lär dig hur du konfigurerar en privat Azure-länk för att få åtkomst till ett Azure Cosmos-konto genom att använda en privat IP-adress i ett virtuellt nätverk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007424"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurera en privat Azure-länk för ett Azure Cosmos-konto (för hands version)

Med hjälp av en privat Azure-länk kan du ansluta till ett Azure Cosmos-konto via en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till ett Azure Cosmos-konto via privata IP-adresser. När en privat länk kombineras med begränsade NSG-principer bidrar den till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](../private-link/private-link-overview.md) .

Med privat länk kan användarna få åtkomst till ett Azure Cosmos-konto inifrån det virtuella nätverket eller från ett peer-kopplat virtuellt nätverk. Resurser som är mappade till privat länk är också tillgängliga lokalt via privat peering via VPN eller Azure ExpressRoute. 

Du kan ansluta till ett Azure Cosmos-konto som kon figurer ATS med en privat länk med hjälp av metoden för automatisk eller manuell godkännande. Läs mer i avsnittet om [godkännande av arbets flöde](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) i dokumentationen för privat länk. 

I den här artikeln beskrivs stegen för att skapa en privat slut punkt. Det förutsätter att du använder metoden för automatiskt godkännande.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Skapa en privat slut punkt med hjälp av Azure Portal

Använd följande steg för att skapa en privat slut punkt för ett befintligt Azure Cosmos-konto med hjälp av Azure Portal:

1. I fönstret **alla resurser** väljer du ett Azure Cosmos-konto.

1. Välj **privata slut punkts anslutningar** i listan över inställningar och välj sedan **privat slut punkt**:

   ![Val för att skapa en privat slut punkt i Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. I fönstret **skapa en privat slut punkt (för hands version) – grunderna** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj en resursgrupp.|
    | **Instans information** |  |
    | Namn | Ange ett namn för din privata slut punkt. Om det här namnet tas skapar du ett unikt. |
    |Region| Välj den region där du vill distribuera privat länk. Skapa den privata slut punkten på samma plats som det virtuella nätverket finns på.|
    |||
1. Välj **Nästa: resurs**.
1. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i min katalog**. <br/><br/> Du kan sedan välja en av dina resurser för att konfigurera en privat länk. Eller så kan du ansluta till någon annans resurs genom att använda ett resurs-ID eller alias som de har delat med dig.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Resurs |Välj ditt Azure Cosmos-konto. |
    |Mål under resurs |Välj den Azure Cosmos DB API-typ som du vill mappa. Standardvärdet är bara ett alternativ för SQL-, MongoDB-och Cassandra-API: er. För Gremlin-och tabell-API: er kan du också välja **SQL** eftersom dessa API: er är kompatibla med SQL-API: et. |
    |||

1. Välj **Nästa: konfiguration**.
1. I **skapa en privat slut punkt (för hands version) – konfiguration**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj ditt virtuella nätverk. |
    | Undernät | Välj ditt undernät. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. <br><br/> För att kunna ansluta privat med din privata slut punkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slut punkt med en privat DNS-zon. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av värd filerna på dina virtuella datorer. |
    |Privat DNS zon |Välj **privatelink.Documents.Azure.com**. <br><br/> Den privata DNS-zonen fastställs automatiskt. Du kan inte ändra den med hjälp av Azure Portal.|
    |||

1. Välj **Granska + skapa**. På sidan **Granska + skapa** verifierar Azure konfigurationen.
1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

När du har godkänt en privat länk för ett Azure Cosmos-Azure Portal konto är alternativet **alla nätverk** i rutan **brand vägg och virtuella nätverk** inte tillgängligt.

Följande tabell visar mappningen mellan olika Azure Cosmos-kontos API-typer, underordnade resurser som stöds och motsvarande namn för privata zoner. Du kan också komma åt Gremlin-och Tabell-API konton via SQL API, så det finns två poster för dessa API: er.

|API-typ för Azure Cosmos-konto  |Under resurser som stöds (eller grupp-ID) |Namn på privat zon  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabell    |    Tabell     |   privatelink.table.cosmos.azure.com    |
|Tabell     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slut punkten har allokerats kan du fråga IP-adresserna. Så här visar du IP-adresserna från Azure Portal:

1. Välj **Alla resurser**.
1. Sök efter den privata slut punkt som du skapade tidigare. I det här fallet är det **cdbPrivateEndpoint3**.
1. Välj fliken **Översikt** för att se DNS-inställningar och IP-adresser.

![Privata IP-adresser i Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Flera IP-adresser skapas per privat slut punkt:

* En för den globala (region-oberoende) slut punkten för Azure Cosmos-kontot
* En för varje region där Azure Cosmos-kontot har distribuerats

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Skapa en privat slut punkt med hjälp av Azure PowerShell

Kör följande PowerShell-skript för att skapa en privat slut punkt med namnet "MyPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Ersätt variabel värden med informationen för din miljö.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integrera den privata slut punkten med en privat DNS-zon

När du har skapat den privata slut punkten kan du integrera den med en privat DNS-zon med hjälp av följande PowerShell-skript:

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

När den privata slut punkten har allokerats kan du fråga IP-adresserna och FQDN-mappningen med hjälp av följande PowerShell-skript:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Skapa en privat slut punkt med hjälp av en Resource Manager-mall

Du kan konfigurera en privat länk genom att skapa en privat slut punkt i ett undernät för virtuellt nätverk. Du uppnår detta genom att använda en Azure Resource Manager mall. 

Använd följande kod för att skapa en Resource Manager-mall med namnet "PrivateEndpoint_template. JSON". Den här mallen skapar en privat slut punkt för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk.

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

### <a name="define-the-parameters-file-for-the-template"></a>Definiera parameter filen för mallen

Skapa en parameter fil för mallen och ge den namnet "PrivateEndpoint_parameters. JSON". Lägg till följande kod i parameter filen:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Distribuera mallen med hjälp av ett PowerShell-skript

Skapa ett PowerShell-skript med hjälp av följande kod. Innan du kör skriptet ersätter du prenumerations-ID, resurs gruppens namn och andra variabel värden med informationen för din miljö.

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

I PowerShell-skriptet kan variabeln `GroupId` bara innehålla ett värde. Det här värdet är kontots API-typ. Tillåtna värden är: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`och `Table`. Vissa Azure Cosmos-konto typer är tillgängliga via flera API: er. Exempel:

* Ett Gremlin-API-konto kan nås från både Gremlin-och SQL-API-konton.
* Ett Tabell-API konto kan nås från både tabell-och SQL-API-konton.

För dessa konton måste du skapa en privat slut punkt för varje API-typ. Motsvarande API-typ anges i `GroupId` matrisen.

När mallen har distribuerats kan du se utdata som liknar det som visas i följande bild. `provisioningState` svärdet är `Succeeded` om de privata slut punkterna har ställts in korrekt.

![Distributions utdata för Resource Manager-mallen](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

När mallen har distribuerats reserveras de privata IP-adresserna i under nätet. Brand Väggs regeln för Azure Cosmos-kontot har kon figurer ATS för att endast godkänna anslutningar från den privata slut punkten.

## <a name="configure-custom-dns"></a>Konfigurera anpassad DNS

Du bör använda en privat DNS-zon i det undernät där du har skapat den privata slut punkten. Konfigurera slut punkterna så att varje privat IP-adress mappas till en DNS-post. (Se `fqdns`-egenskapen i svaret som visas ovan.)

När du skapar den privata slut punkten kan du integrera den med en privat DNS-zon i Azure. Om du väljer att använda en anpassad DNS-zon i stället måste du konfigurera den för att lägga till DNS-poster för alla privata IP-adresser som reserver ATS för den privata slut punkten.

## <a name="private-link-combined-with-firewall-rules"></a>Privat länk kombinerat med brand Väggs regler

Följande situationer och resultat är möjliga när du använder en privat länk i kombination med brand Väggs regler:

* Om du inte konfigurerar några brand Väggs regler kan all trafik komma åt ett Azure Cosmos-konto som standard.

* Om du konfigurerar offentlig trafik eller en tjänst slut punkt och skapar privata slut punkter, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om du inte konfigurerar någon offentlig trafik eller tjänst slut punkt och skapar privata slut punkter kan du bara komma åt Azure Cosmos-kontot via de privata slut punkterna. Om du inte konfigurerar offentlig trafik eller en tjänst slut punkt efter att alla godkända privata slut punkter har avvisats eller tagits bort, är kontot öppet för hela nätverket.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Uppdatera en privat slut punkt när du lägger till eller tar bort en region

Om du lägger till eller tar bort regioner i ett Azure Cosmos-konto måste du lägga till eller ta bort DNS-poster för det kontot. Uppdatera dessa ändringar i enlighet med den privata slut punkten med hjälp av följande steg:

1. När Azure Cosmos DB-administratören lägger till eller tar bort regioner, får nätverks administratören ett meddelande om väntande ändringar. För den privata slut punkten som är mappad till ett Azure Cosmos-konto ändras värdet för egenskapen `ActionsRequired` från `None` till `Recreate`. Sedan uppdaterar nätverks administratören den privata slut punkten genom att utfärda en skicka-begäran med samma Resource Manager-nyttolast som användes för att skapa den.

1. När den privata slut punkten har uppdaterats kan du uppdatera under nätets privata DNS-zon så att den återspeglar de tillagda eller borttagna DNS-posterna och deras motsvarande privata IP-adresser.

Anta till exempel att du distribuerar ett Azure Cosmos-konto i tre regioner: "västra USA", "Central USA" och "Västeuropa". När du skapar en privat slut punkt för ditt konto reserveras fyra privata IP-adresser i under nätet. Det finns en IP-adress för var och en av de tre regionerna och det finns en IP-adress för global/region-oberoende-slutpunkten.

Senare kan du lägga till en ny region (till exempel "USA, östra") till Azure Cosmos-kontot. Den nya regionen är som standard inte tillgänglig från den befintliga privata slut punkten. Azure Cosmos-konto administratören bör uppdatera den privata slut punkts anslutningen innan du får åtkomst till den från den nya regionen. 

När du kör kommandot ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` innehåller kommandots utdata `actionsRequired`-parametern. Den här parametern har angetts till `Recreate`. Det här värdet anger att den privata slut punkten ska uppdateras. Därefter kör Azure Cosmos-konto administratören `Set-AzPrivateEndpoint`-kommandot för att utlösa uppdateringen av den privata slut punkten.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

En ny privat IP reserveras automatiskt i under nätet under den här privata slut punkten. Värdet för `actionsRequired` blir `None`. Om du inte har någon privat DNS zon integrering (med andra ord, om du använder en anpassad privat DNS-zon) måste du konfigurera din privata DNS-zon för att lägga till en ny DNS-post för den privata IP-adressen som motsvarar den nya regionen.

Du kan använda samma steg när du tar bort en region. Den borttagna regionens privata IP-adress återtas automatiskt och `actionsRequired`-flaggan blir `None`. Om du inte har någon privat integrering av DNS-zonen måste du konfigurera din privata DNS-zon för att ta bort DNS-posten för den borttagna regionen.

DNS-poster i den privata DNS-zonen tas inte bort automatiskt när en privat slut punkt tas bort eller en region från Azure Cosmos-kontot tas bort. Du måste ta bort DNS-posterna manuellt.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder en privat länk med ett Azure Cosmos-konto:

* Stöd för privata Länkar för Azure Cosmos-konton och virtuella nätverk är bara tillgängligt i vissa regioner. En lista över regioner som stöds finns i avsnittet [tillgängliga regioner](../private-link/private-link-overview.md#availability) i artikeln om privat länk. 

  > [!NOTE]
  > Om du vill skapa en privat slut punkt, se till att både det virtuella nätverket och Azure Cosmos-kontot finns i regioner som stöds.

* När du använder en privat länk med ett Azure Cosmos-konto med hjälp av en anslutning via direkt läge, kan du bara använda TCP-protokollet. HTTP-protokollet stöds inte ännu.

* När du använder Azure Cosmos DBs API för MongoDB-konton stöds en privat slut punkt för konton på Server version 3,6 (det vill säga konton som använder slut punkten i formatet `*.mongo.cosmos.azure.com`). Privat länk stöds inte för konton på Server version 3,2 (det vill säga konton som använder slut punkten i formatet `*.documents.azure.com`). Om du vill använda en privat länk bör du migrera gamla konton till den nya versionen.

* När du använder Azure Cosmos DBs API för MongoDB-konton som har en privat länk kan du inte använda verktyg som Robo 3T, Studio 3T och Mongoose. Slut punkten kan bara ha stöd för privata länkar om parametern `appName=<account name>` har angetts. Ett exempel är `replicaSet=globaldb&appName=mydbaccountname`. Eftersom dessa verktyg inte skickar appens namn i anslutnings strängen till tjänsten kan du inte använda privat länk. Men du kan fortfarande komma åt dessa konton genom att använda SDK-drivrutiner med 3,6-versionen.

* Du kan inte flytta eller ta bort ett virtuellt nätverk om det innehåller en privat länk.

* Du kan inte ta bort ett Azure Cosmos-konto om det är kopplat till en privat slut punkt.

* Det går inte att redundansväxla ett Azure Cosmos-konto till en region som inte är mappad till alla privata slut punkter som är kopplade till kontot.

* En nätverks administratör ska beviljas minst behörigheten "*/PrivateEndpointConnectionsApproval" på Azure Cosmos-kontots omfång för att skapa automatiskt godkända privata slut punkter.

### <a name="limitations-to-private-dns-zone-integration"></a>Begränsningar för integrering av privata DNS-zoner

DNS-poster i den privata DNS-zonen tas inte bort automatiskt när du tar bort en privat slut punkt eller tar bort en region från Azure Cosmos-kontot. Du måste ta bort DNS-posterna manuellt innan:

* Lägger till en ny privat slutpunkt som är länkad till den här privata DNS-zonen.
* Lägga till en ny region i alla databas konton som har privata slut punkter länkade till den här privata DNS-zonen.

Om du inte rensar DNS-posterna kan oväntade data Plans problem uppstå. Dessa problem omfattar data avbrott i regioner som lagts till efter borttagning av privata slut punkter eller region borttagning.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB säkerhetsfunktioner finns i följande artiklar:

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i [brand Väggs stöd](firewall-support.md).

* Information om hur du konfigurerar en tjänst slut punkt för ett virtuellt nätverk för ditt Azure Cosmos-konto finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md).

* Mer information om privat länk finns i dokumentationen till [Azures privata länkar](../private-link/private-link-overview.md) .
