---
title: Konfigurera Virtual Network-baserad åtkomst för ett Azure Cosmos-konto
description: I det här dokumentet beskrivs de steg som krävs för att konfigurera en tjänst slut punkt för virtuellt nätverk för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 80e77e2caacfed7d662fdfa04aa9524bd7c2083a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445422"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurera åtkomst från virtuella nätverk (VNet)

Du kan konfigurera Azure Cosmos DB konton för att enbart tillåta åtkomst från ett speciellt undernät i ett virtuellt Azure-nätverk. Så här begränsar du åtkomsten till ett Azure Cosmos DB konto med anslutningar från ett undernät i ett virtuellt nätverk:

1. Aktivera under nätet för att skicka undernät och virtuell nätverks identitet till Azure Cosmos DB. Du kan åstadkomma detta genom att aktivera en tjänst slut punkt för Azure Cosmos DB på det angivna under nätet.

1. Lägg till en regel i Azure Cosmos DB kontot för att ange under nätet som en källa från vilken kontot kan nås.

> [!NOTE]
> När en tjänst slut punkt för ditt Azure Cosmos DB-konto är aktive rad i ett undernät är källan till den trafik som når Azure Cosmos DB växlar från en offentlig IP-adress till ett virtuellt nätverk och ett undernät. Trafik växlingen gäller för alla Azure Cosmos DB-konton som nås från det här under nätet. Om dina Azure Cosmos DB-konton har en IP-baserad brand vägg för att tillåta det här under nätet, matchar inte längre begär Anden från det tjänstbaserade under nätet IP-brandväggens regler och de avvisas.
>
> Mer information finns i de steg som beskrivs i avsnittet [Migrera från en IP-brandvägg till en lista över virtuella nätverks åtkomst kontrol listor](#migrate-from-firewall-to-vnet) i den här artikeln.

I följande avsnitt beskrivs hur du konfigurerar en tjänst slut punkt för virtuella nätverk för ett Azure Cosmos DB-konto.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Konfigurera en tjänst slut punkt med hjälp av Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett befintligt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.

1. Välj **brand väggar och virtuella nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett befintligt virtuellt nätverks undernät går du till **virtuella nätverk**och väljer **Lägg till befintligt virtuellt Azure-nätverk**.

1. Välj den **prenumeration** som du vill lägga till ett virtuellt Azure-nätverk från. Välj de virtuella Azure- **nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos DB-konto. Välj sedan **Aktivera** för att aktivera valda nätverk med tjänst slut punkter för "Microsoft. AzureCosmosDB". När den är klar väljer du **Lägg till**.

   ![Välj virtuellt nätverk och undernät](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. När Azure Cosmos DB-kontot har Aktiver ATS för åtkomst från ett virtuellt nätverk kommer det att tillåta trafik från endast det valda under nätet. Det virtuella nätverk och undernät som du har lagt till ska visas som visas på följande skärm bild:

   ![Virtuellt nätverk och undernät har kon figurer ATS](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Om du vill aktivera tjänst slut punkter för virtuella nätverk måste du ha följande prenumerations behörigheter:
>   * Prenumeration med virtuellt nätverk: nätverks deltagare
>   * Prenumeration med Azure Cosmos DB konto: DocumentDB Account Contributor
>   * Om ditt virtuella nätverk och Azure Cosmos DB konto finns i olika prenumerationer, kontrollerar du att prenumerationen som har ett virtuellt nätverk också har `Microsoft.DocumentDB` registrerad resurs leverantör. Information om hur du registrerar en resurs leverantör finns i artikeln [Azure Resource providers och types](../azure-resource-manager/resource-manager-supported-services.md) .

Här följer anvisningar för att registrera prenumeration med Resource Provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett nytt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.  

1. Välj **brand väggar och virtuella Azure-nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.  

1. Om du vill bevilja åtkomst till ett nytt virtuellt Azure-nätverk går du till **virtuella nätverk**och väljer **Lägg till nytt virtuellt nätverk**.  

1. Ange den information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan **skapa**. Under nätet skapas med en tjänst slut punkt för "Microsoft. AzureCosmosDB" aktive rad.

   ![Välj ett virtuellt nätverk och undernät för ett nytt virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Kognitiv sökning eller nås från Stream Analytics eller Power BI kan du tillåta åtkomst genom att välja **acceptera anslutningar från globala Azure-datacenter**.

För att se till att du har åtkomst till Azure Cosmos DB mått från portalen måste du aktivera **Tillåt åtkomst från Azure Portal** alternativ. Mer information om de här alternativen finns i artikeln [Konfigurera en IP-brandvägg](how-to-configure-firewall.md) . När du har aktiverat åtkomst väljer du **Spara** för att spara inställningarna.

## <a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät

1. Från bladet **alla resurser** letar du reda på det Azure Cosmos DB konto som du har tilldelat tjänstens slut punkter för.  

2. Välj **brand väggar och virtuella nätverk** på menyn Inställningar.  

3. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** bredvid det virtuella nätverket eller under nätet och väljer **ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Klicka på **Spara** för att tillämpa dina ändringar.

## <a id="configure-using-powershell"></a>Konfigurera en tjänst slut punkt med hjälp av Azure PowerShell

> [!NOTE]
> När du använder PowerShell eller Azure CLI måste du ange den fullständiga listan med IP-filter och ACL: er för virtuella nätverk i parametrar, inte bara de som behöver läggas till.

Använd följande steg för att konfigurera en tjänst slut punkt till ett Azure Cosmos DB konto genom att använda Azure PowerShell:  

1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) och [Logga](https://docs.microsoft.com/powershell/azure/authenticate-azureps)in.  

1. Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Hämta information om virtuellt nätverk.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Hämta egenskaperna för det Azure Cosmos DB kontot genom att köra följande cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initiera variablerna för användning senare. Konfigurera alla variabler från den befintliga konto definitionen.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Uppdatera Azure Cosmos DB konto egenskaper med den nya konfigurationen genom att köra följande cmdlets: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Kör följande kommando för att kontrol lera att ditt Azure Cosmos DB-konto har uppdaterats med den virtuella nätverks tjänst slut punkt som du konfigurerade i föregående steg:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurera en tjänst slut punkt med hjälp av Azure CLI

Azure Cosmos-konton kan konfigureras för tjänst slut punkter när de skapas eller uppdateras vid ett senare tillfälle om under nätet redan har kon figurer ATS för dem. Tjänst slut punkter kan också aktive ras på Cosmos-kontot där under nätet inte har kon figurer ATS än och sedan börjar fungera när under nätet konfigureras senare. Den här flexibiliteten gör det möjligt för administratörer som inte har åtkomst till både Cosmos-kontot och virtuella nätverks resurser att göra sina konfigurationer oberoende av varandra.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Skapa ett nytt Cosmos-konto och Anslut det till ett Server dels under nät för ett nytt virtuellt nätverk

I det här exemplet skapas det virtuella nätverket och under nätet med tjänst slut punkter som är aktiverade för båda när de skapas.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Anslut och konfigurera ett Cosmos-konto till ett Server dels under nät oberoende av varandra

Det här exemplet är avsett att visa hur du ansluter ett Azure Cosmos-konto till ett befintligt nytt virtuellt nätverk där under nätet ännu inte har kon figurer ATS för tjänst slut punkter. Detta görs med hjälp av parametern `--ignore-missing-vnet-service-endpoint`. Detta gör att konfigurationen av Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät har slutförts. När under näts konfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade under nätet.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a id="migrate-from-firewall-to-vnet"></a>Migrera från en IP-brandväggsregel till en virtuell nätverks-ACL

Använd följande steg endast för Azure Cosmos DB-konton med befintliga IP-brandväggar som tillåter ett undernät, om du vill använda virtuella nätverk och undernätbaserade ACL: er i stället för en IP-brandvägg.

När en tjänst slut punkt för ett Azure Cosmos DB-konto har Aktiver ATS för ett undernät skickas begäran med en källa som innehåller information om virtuellt nätverk och undernät i stället för en offentlig IP-adress. Dessa begär Anden stämmer inte överens med ett IP-filter. Den här käll växeln sker för alla Azure Cosmos DB-konton som nås från under nätet med en tjänst slut punkt aktive rad. Använd följande steg för att förhindra drift stopp:

1. Hämta egenskaperna för det Azure Cosmos DB kontot genom att köra följande cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initiera variablerna för att använda dem senare. Konfigurera alla variabler från den befintliga konto definitionen. Lägg till åtkomst kontrol listan för virtuella nätverk till alla Azure Cosmos DB konton som nås från under nätet med `ignoreMissingVNetServiceEndpoint` flagga.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Uppdatera Azure Cosmos DB konto egenskaper med den nya konfigurationen genom att köra följande cmdlets:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Upprepa steg 1-3 för alla Azure Cosmos DB konton som du kommer åt från under nätet.

1.  Vänta i 15 minuter och uppdatera sedan under nätet för att aktivera tjänstens slut punkt.

1.  Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Ta bort IP-brandväggens regel för under nätet.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i artikeln om [brand Väggs stöd](firewall-support.md) .
