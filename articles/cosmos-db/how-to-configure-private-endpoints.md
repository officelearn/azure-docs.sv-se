---
title: Konfigurera en privat Azure-länk för ett Azure Cosmos-konto
description: Lär dig hur du konfigurerar en privat Azure-länk för att få åtkomst till ett Azure Cosmos-konto med en privat IP-adress i ett virtuellt nätverk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 3f987b9e05bcdcda9afe26a1eb1354e5e2450ac5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846538"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurera en privat Azure-länk för ett Azure Cosmos-konto (för hands version)

Med hjälp av en privat Azure-länk kan du ansluta till ett Azure Cosmos-konto via en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Med hjälp av en privat länk kan du begränsa åtkomsten till ett Azure Cosmos-konto via privata IP-adresser. I kombination med begränsade NSG-principer bidrar privata länkar till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](../private-link/private-link-overview.md) .

Dessutom kan en privat länk göra att ett Azure Cosmos-konto kan nås från det virtuella nätverket eller ett peer-kopplat virtuellt nätverk. Resurser som är mappade till en privat länk kan också nås från lokalt via privat peering via VPN eller ExpressRoute. 

Du kan ansluta till ett Azure Cosmos-konto som kon figurer ATS med en privat länk med hjälp av godkännande metoderna "automatisk" eller "Manuell". Läs mer i avsnittet om [godkännande av arbets flöde](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) i dokumentationen för privat länk. I den här artikeln beskrivs stegen för att skapa en privat länk förutsatt att du använder metoden för automatiskt godkännande.

## <a name="create-a-private-link-using-the-azure-portal"></a>Skapa en privat länk med hjälp av Azure Portal

Använd följande steg för att skapa en privat länk för ett befintligt Azure Cosmos-konto med hjälp av Azure Portal:

1. I fönstret **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.

1. Välj **privat slut punkts anslutning** på menyn Inställningar och välj alternativet **privat slut punkt** :

   ![Skapa en privat slut punkt med hjälp av Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. I **skapa en privat slut punkt (för hands version) – grunderna**, fyller du i fönstret eller väljer följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projekt information** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj en resursgrupp.|
    | **Instans information** |  |
    | Namn | Ange ett namn för din privata slut punkt. om det här namnet tas skapar du ett unikt. |
    |Region| Välj den region där du vill distribuera den privata länken. Den privata slut punkten måste skapas på samma plats som det virtuella nätverket finns på.|
    |||
1. Välj **Nästa: resurs**.
1. I **skapa en privat slut punkt – resurs**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj Anslut till en Azure-resurs i min katalog. <br/><br/> Med det här alternativet kan du välja en av dina resurser för att skapa en privat länk eller ansluta till någon annans resurs med ett resurs-ID eller alias som de har delat med dig.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Resurs |Välj ditt Azure Cosmos-konto |
    |Mål under resurs |Välj den Cosmos DB API-typ som du vill mappa. Standardvärdet är bara ett alternativ för SQL-, MongoDB-och Cassandra-API: er. För Gremlin-och tabell-API: er kan du också välja *SQL* eftersom dessa API: er är kompatibla med SQL-API: et. |
    |||

1. Välj **Nästa: konfiguration**.
1. I **skapa en privat slut punkt (för hands version) – konfiguration**, anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj ditt virtuella nätverk. |
    | Undernät | Välj ditt undernät. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. <br><br/> För att kunna ansluta privat med din privata slut punkt behöver du en DNS-post. Vi rekommenderar att du integrerar din privata slut punkt med en privat DNS-zon. Du kan också använda dina egna DNS-servrar eller skapa DNS-poster med hjälp av Host-filerna på dina virtuella datorer. |
    |Privat DNS zon |Välj *privatelink.Documents.Azure.com* <br><br/> Zonen Privat DNS fastställs automatiskt och kan inte ändras för tillfället med hjälp av Azure Portal.|
    |||

1. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.
1. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

När du har godkänt privata Länkar för ett Azure Cosmos-konto går du till Azure Portal alternativet **alla nätverk** i fönstret **brand vägg och virtuella nätverk** är nedtonad.

I följande tabell visas mappning mellan olika API-typer för Azure Cosmos-konton, under resurser som stöds och motsvarande privata zon namn. Gremlin-och Tabell-API-kontona är tillgängliga via SQL API och det finns 2 poster för dessa API: er:

|API-typ för Azure Cosmos-konto  |Under resurser som stöds (eller groupIds) |Namn på privat zon  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabell    |    Tabell     |   privatelink.table.cosmos.azure.com    |
|Tabell     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Hämta de privata IP-adresserna

När den privata slut punkten har allokerats kan du fråga IP-adresserna. Om du vill visa IP-adresserna från Azure Portal väljer du **alla resurser**, söker efter den privata slut punkt som du skapade tidigare i det här fallet är den "dbPrivateEndpoint3" och väljer fliken Översikt för att visa DNS-inställningar och IP-adresser:

![Privata IP-adresser i Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Flera IP-adresser skapas per privat slut punkt:

* En för den globala (region-oberoende) slut punkten för Azure Cosmos-kontot.
* En för varje region där Azure Cosmos-kontot har distribuerats.

## <a name="create-a-private-link-using-azure-powershell"></a>Skapa en privat länk med Azure PowerShell

Kör följande PowerSehll-skript för att skapa en privat länk med namnet "MyPrivateEndpoint" för ett befintligt Azure Cosmos-konto. Se till att ersätta variabel värden med information som är speciell för din miljö.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Integrera den privata slut punkten med en privat DNS-zon

När du har skapat den privata slut punkten kan du integrera den med en privat DNS-zon med hjälp av följande PowerSehll-skript:

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

När den privata slut punkten har allokerats kan du fråga IP-adresserna och mappningen av fullständigt kvalificerade domän namn med hjälp av följande PowerShell-skript:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Skapa en privat länk med en Resource Manager-mall

Du kan konfigurera en privat länk genom att skapa en privat slut punkt i ett undernät för virtuellt nätverk. Detta uppnås med hjälp av en Azure Resource Manager mall. Skapa en Resource Manager-mall med namnet "PrivateEndpoint_template. JSON" med följande kod. Den här mallen skapar en privat slut punkt för ett befintligt Azure Cosmos SQL API-konto i ett befintligt virtuellt nätverk:

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

### <a name="define-the-template-parameters-file"></a>Definiera filen med mallparametrar

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

Skapa sedan ett PowerShell-skript med följande kod. Innan du kör skriptet ser du till att ersätta prenumerations-ID, resurs gruppens namn och andra variabel värden med information som är speciell för din miljö:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

I PowerShell-skriptet kan "" ID "-variabeln bara innehålla ett värde, vilket är kontots API-typ. Tillåtna värden är: SQL, MongoDB, Cassandra, Gremlin och Table. Vissa Azure Cosmos-konto typer är tillgängliga via flera API: er. Till exempel:

* Ett Gremlin-API-konto kan nås från både Gremlin-och SQL-API-konton.
* Ett Tabell-API konto kan nås från både tabell-och SQL-API-konton.

För sådana konton måste du skapa en privat slut punkt för varje API-typ, med motsvarande API-typ angiven i "multithe-matrisen".

När mallen har distribuerats kan du se utdata som liknar vad som visas i följande bild. ProvisioningState-värdet är "lyckades" om de privata slut punkterna har kon figurer ATS korrekt.

![Distributions utdata för Resource Manager-mall](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

När mallen har distribuerats reserveras de privata IP-adresserna i under nätet. Brand Väggs regeln för Azure Cosmos-kontot har kon figurer ATS för att endast godkänna anslutningar från den privata slut punkten.

## <a name="configure-custom-dns"></a>Konfigurera anpassad DNS

Du bör använda en privat DNS i under nätet där den privata slut punkten har skapats. Och konfigurera slut punkterna så att var och en av de privata IP-adresserna mappas till en DNS-post (se FQDN-egenskapen i svaret som visas ovan).

När du skapar den privata slut punkten kan du integrera den med en privat DNS-zon i Azure. Om du väljer att inte integrera din privata slut punkt med en privat DNS-zon i Azure och istället använda en anpassad DNS måste du konfigurera DNS för att lägga till DNS-poster för alla privata IP-adresser som reserver ATS för den privata slut punkten.

## <a name="firewall-configuration-with-private-link"></a>Brand Väggs konfiguration med privat länk

Följande är olika situationer och resultat visas när du använder en privat länk i kombination med brand Väggs regler:

* Om inga brand Väggs regler har kon figurer ATS är ett Azure Cosmos-konto som standard tillgängligt för all trafik.

* Om den offentliga trafiken eller tjänst slut punkten har kon figurer ATS och privata slut punkter skapas, auktoriseras olika typer av inkommande trafik av motsvarande typ av brand Väggs regel.

* Om ingen offentlig trafik eller tjänst slut punkt har kon figurer ATS och privata slut punkter skapas, är Azure Cosmos-kontot bara tillgängligt via de privata slut punkterna. Om ingen offentlig trafik eller tjänst slut punkt konfigureras, när alla godkända privata slut punkter har avvisats eller tagits bort, är kontot öppet för alla nätverk.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Uppdatera privat slut punkt när du lägger till eller tar bort en region

Om du lägger till eller tar bort regioner i ett Azure Cosmos-konto måste du lägga till eller ta bort DNS-poster för det kontot. Ändringarna bör uppdateras i enlighet med detta i den privata slut punkten. För närvarande bör du utföra den här ändringen manuellt med hjälp av följande steg:

1. Azure Cosmos DB-administratören lägger till eller tar bort regioner. Sedan meddelas nätverks administratörerna om de väntande ändringarna. Den privata slut punkten som är mappad till ett Azure Cosmos-konto ser dess "ActionsRequired"-egenskaper ändrad från "ingen" till "återskapa". Sedan uppdaterar nätverks administratören den privata slut punkten genom att utfärda en skicka-begäran med samma Resource Manager-nyttolast som användes för att skapa den.

1. Efter den här åtgärden måste under nätets privata DNS också uppdateras för att avspegla de tillagda eller borttagna DNS-posterna och deras motsvarande privata IP-adresser.

Om du till exempel distribuerar ett Azure Cosmos-konto i tre regioner: "västra USA", "Central USA" och "Västeuropa". När du skapar en privat slut punkt för ditt konto reserveras 4 privata IP-adresser i under nätet. En för varje region, som räknas till totalt 3, och en för den globala/oberoende slut punkten.

Senare om du lägger till en ny region, till exempel "östra USA" till Azure Cosmos-kontot. Den nya regionen är som standard inte tillgänglig från den befintliga privata slut punkten. Azure Cosmos-konto administratören bör uppdatera den privata slut punkts anslutningen innan du får åtkomst till den från den nya regionen. 

När du kör kommandot ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` innehåller kommandots utdata `actionsRequired`-parametern, som är inställt på "återskapa". Det här värdet anger att den privata slut punkten ska uppdateras. Därefter kör Azure Cosmos-konto administratören `Set-AzPrivateEndpoint`-kommandot för att utlösa uppdateringen av den privata slut punkten.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

En ny privat IP reserveras automatiskt i under nätet under den här privata slut punkten och värdet `actionsRequired` blir `None`. Om du inte har någon privat DNS zon integrering (om du använder en anpassad privat DNS) måste du konfigurera din privata DNS för att lägga till en ny DNS-post för den privata IP-adressen som motsvarar den nya regionen.

Du kan använda samma steg när du tar bort en region. Den borttagna regionens privata IP-adress återtas automatiskt och `actionsRequired`-flaggan blir `None`. Om du inte har någon privat integrering av DNS-zonen måste du konfigurera din privata DNS för att ta bort DNS-posten för den borttagna regionen.

DNS-poster i den privata DNS-zonen tas inte bort automatiskt när en privat slut punkt tas bort eller en region från Azure Cosmos-kontot tas bort. Du måste ta bort DNS-posterna manuellt.

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder den privata länken med ett Azure Cosmos-konto:

* Stöd för privata Länkar för Azure Cosmos-konton och virtuella nätverk är endast tillgängligt i vissa regioner. En lista över regioner som stöds finns i avsnittet [tillgängliga regioner](../private-link/private-link-overview.md#availability) i artikeln om privat länk. **Både VNet-och Azure Cosmos-kontot ska finnas i de regioner som stöds för att kunna skapa en privat slut punkt**.

* När du använder privata länkar med Azure Cosmos-konto med anslutning till direkt läge kan du bara använda TCP-protokollet. HTTP-protokollet stöds inte ännu

* När du använder Azure Cosmos DBs API för MongoDB-konton stöds privat slut punkt för konton på Server version 3,6 endast (som är konton som använder slut punkten i formatet `*.mongo.cosmos.azure.com`). Privat länk stöds inte för konton på Server version 3,2 (som är konton som använder slut punkten i formatet `*.documents.azure.com`). Om du vill använda en privat länk bör du migrera gamla konton till en ny version.

* När du använder Azure Cosmos DBs API för MongoDB-konton som har en privat länk kan du inte använda verktyg som Robo 3T, Studio 3T, Mongoose osv. Slut punkten kan bara ha stöd för privata länkar om parametern appName =<account name> anges. Till exempel: replicaSet = globaldb & appName = mydbaccountname. Eftersom dessa verktyg inte skickar appens namn i anslutnings strängen till tjänsten så kan du inte använda en privat länk. Men du kan fortfarande komma åt dessa konton med SDK-drivrutiner med 3,6-versionen.

* Det går inte att flytta eller ta bort ett virtuellt nätverk om det innehåller en privat länk.

* Det går inte att ta bort ett Azure Cosmos-konto om det är kopplat till en privat slut punkt.

* Ett Azure Cosmos-konto kan inte växlas över till en region som inte är mappad till alla privata slut punkter som är kopplade till kontot. Mer information finns i lägga till eller ta bort regioner i föregående avsnitt.

* En nätverks administratör ska beviljas minst behörigheten "*/PrivateEndpointConnectionsApproval" på Azure Cosmos-kontots omfattning av en administratör för att skapa automatiskt godkända privata slut punkter.

### <a name="private-dns-zone-integration-limitations"></a>Privat DNS zon integrerings begränsningar

DNS-poster i den privata DNS-zonen tas inte bort automatiskt när en privat slut punkt tas bort eller en region från Azure Cosmos-kontot tas bort. Du måste ta bort DNS-posterna manuellt innan:

* Lägger till en ny privat slutpunkt som är länkad till den här privata DNS-zonen.
* Lägga till en ny region i alla databas konton som har privata slut punkter länkade till den här privata DNS-zonen.

Utan att rensa DNS-posterna kan oväntade data Plans problem, t. ex. data avbrott i regioner som lagts till efter borttagning av privat slut punkt eller borttagning av region inträffa

## <a name="next-steps"></a>Nästa steg

Mer information om de andra Azure Cosmos DB säkerhetsfunktionerna finns i följande artikel:

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i [brand Väggs stöd](firewall-support.md).

* [Konfigurera tjänst slut punkten för virtuellt nätverk för ditt Azure Cosmos-konto.](how-to-configure-vnet-service-endpoint.md)

* Mer information om privat länk finns i dokumentationen till [Azures privata länkar](../private-link/private-link-overview.md) .
