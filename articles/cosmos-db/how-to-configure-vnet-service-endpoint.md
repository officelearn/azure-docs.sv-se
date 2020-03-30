---
title: Konfigurera virtuell nätverksbaserad åtkomst för ett Azure Cosmos-konto
description: I det här dokumentet beskrivs de steg som krävs för att konfigurera en slutpunkt för virtuella nätverkstjänster för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366230"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurera åtkomst från virtuella nätverk (VNet)

Du kan konfigurera Azure Cosmos DB konton för att enbart tillåta åtkomst från ett speciellt undernät i ett virtuellt Azure-nätverk. Så här begränsar du åtkomsten till ett Azure Cosmos DB-konto med anslutningar från ett undernät i ett virtuellt nätverk:

1. Aktivera undernätet för att skicka undernätet och den virtuella nätverksidentiteten till Azure Cosmos DB. Du kan uppnå detta genom att aktivera en tjänstslutpunkt för Azure Cosmos DB på det specifika undernätet.

1. Lägg till en regel i Azure Cosmos DB-kontot för att ange undernätet som en källa från vilket kontot kan nås.

> [!NOTE]
> När en tjänstslutpunkt för ditt Azure Cosmos DB-konto är aktiverad på ett undernät, växlar källan till den trafik som når Azure Cosmos DB från en offentlig IP till ett virtuellt nätverk och undernät. Trafikväxlingen gäller för alla Azure Cosmos DB-konton som används från det här undernätet. Om dina Azure Cosmos DB-konton har en IP-baserad brandvägg för att tillåta det här undernätet matchar begäranden från det tjänstaktiverade undernätet inte längre IP-brandväggsreglerna och de avvisas.
>
> Mer information finns i stegen i [migrering från en IP-brandväggsregel till ett](#migrate-from-firewall-to-vnet) avsnitt om kontroll av virtuell nätverksåtkomst i den här artikeln.

I följande avsnitt beskrivs hur du konfigurerar en slutpunkt för en virtuell nätverkstjänst för ett Azure Cosmos DB-konto.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure-portalen

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera en tjänstslutpunkt för ett befintligt virtuellt Azure-nätverk och undernät

1. Leta reda på azure Cosmos DB-kontot som du vill skydda från bladet **Alla resurser.**

1. Välj **Brandväggar och virtuella nätverk** på inställningsmenyn och välj att tillåta åtkomst från valda **nätverk**.

1. Om du vill bevilja åtkomst till ett befintligt virtuellt nätverks undernät väljer du **Lägg till befintligt virtuellt Azure-nätverk**under **Virtuella nätverk**.

1. Välj den **prenumeration** som du vill lägga till ett virtuellt Azure-nätverk från. Välj de **virtuella Azure-nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos DB-konto. Välj sedan **Aktivera** för att aktivera valda nätverk med tjänstslutpunkter för "Microsoft.AzureCosmosDB". När den är klar väljer du **Lägg till**.

   ![Välj virtuellt nätverk och undernät](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. När Azure Cosmos DB-kontot har aktiverats för åtkomst från ett virtuellt nätverk tillåter det trafik från endast det valda undernätet. Det virtuella nätverket och undernätet som du har lagt till ska visas som visas i följande skärmbild:

   ![Virtuellt nätverk och undernät har konfigurerats](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Om du vill aktivera slutpunkter för virtuella nätverkstjänster behöver du följande prenumerationsbehörigheter:
>   * Prenumeration med virtuellt nätverk: Nätverksdeltagare
>   * Prenumeration med Azure Cosmos DB-konto: DocumentDB-kontodeltagare
>   * Om ditt virtuella nätverk och Azure Cosmos DB-konto finns i olika prenumerationer `Microsoft.DocumentDB` kontrollerar du att prenumerationen som har virtuellt nätverk också har resursprovider registrerad. Information om hur du registrerar en resursleverantör finns i [Azure-resursleverantörer och typer.](../azure-resource-manager/management/resource-providers-and-types.md)

Här är anvisningarna för registrering av prenumeration med resursleverantör.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera en tjänstslutpunkt för ett nytt virtuellt Azure-nätverk och undernät

1. Leta reda på azure Cosmos DB-kontot som du vill skydda från bladet **Alla resurser.**  

1. Välj **Brandväggar och virtuella Azure-nätverk** på inställningsmenyn och välj att tillåta åtkomst från **valda nätverk**.  

1. Om du vill bevilja åtkomst till ett nytt virtuellt Azure-nätverk väljer du **Lägg till nytt virtuellt nätverk**under Virtuella **nätverk**.  

1. Ange den information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan **Skapa**. Undernätet skapas med en tjänstslutpunkt för "Microsoft.AzureCosmosDB" aktiverat.

   ![Välj ett virtuellt nätverk och ett undernät för ett nytt virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Cognitive Search, eller nås från Stream Analytics eller Power BI, tillåter du åtkomst genom att välja **Acceptera anslutningar från globala Azure-datacenter**.

För att säkerställa att du har åtkomst till Azure Cosmos DB-mått från portalen måste du aktivera **Tillåt åtkomst från Azure-portalalternativ.** Mer information om de här alternativen finns i artikeln [Konfigurera en IP-brandvägg.](how-to-configure-firewall.md) När du har aktiverat åtkomst väljer du **Spara** för att spara inställningarna.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller ett virtuellt undernät

1. Leta reda på Azure Cosmos DB-kontot som du har tilldelat tjänstslutpunkter för från bladet **Alla resurser.**  

1. Välj **Brandväggar och virtuella nätverk** på inställningsmenyn.  

1. Om du vill ta bort en virtuell nätverks- eller undernätsregel markerar du **...** bredvid det virtuella nätverket eller undernätet och väljer **Ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure PowerShell

> [!NOTE]
> När du använder PowerShell eller Azure CLI måste du ange den fullständiga listan över IP-filter och ACL:er för virtuella nätverk i parametrar, inte bara de som behöver läggas till.

Använd följande steg för att konfigurera en tjänstslutpunkt till ett Azure Cosmos DB-konto med hjälp av Azure PowerShell:  

1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) och [logga in](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Aktivera tjänstslutpunkten för ett befintligt undernät i ett virtuellt nätverk.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Hämta information om virtuella nätverk.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Förbereda en virtuell nätverksregel för Cosmos DB

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB-kontoegenskaper med den nya slutpunktskonfigurationen för virtuellt nätverk: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Kör följande kommando för att verifiera att ditt Azure Cosmos DB-konto uppdateras med slutpunkten för den virtuella nätverkstjänsten som du konfigurerade i föregående steg:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure CLI

Azure Cosmos-konton kan konfigureras för tjänstslutpunkter när de skapas eller uppdateras vid ett senare tillfälle om undernätet redan är konfigurerat för dem. Tjänstslutpunkter kan också aktiveras på Cosmos-kontot där undernätet ännu inte är konfigurerat för dem och sedan börjar fungera när undernätet konfigureras senare. Den här flexibiliteten gör det möjligt för administratörer som inte har åtkomst till både Cosmos-kontot och virtuella nätverksresurser att göra sina konfigurationer oberoende av varandra.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Skapa ett nytt Cosmos-konto och ansluta det till ett backend-undernät för ett nytt virtuellt nätverk

I det här exemplet skapas det virtuella nätverket och undernätet med tjänstslutpunkter aktiverade för båda när de skapas.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Ansluta och konfigurera ett Cosmos-konto till ett serverdelsundernät oberoende av

Det här exemplet är avsett att visa hur du ansluter ett Azure Cosmos-konto till ett befintligt nytt virtuellt nätverk där undernätet ännu inte är konfigurerat för tjänstslutpunkter. Detta görs med `--ignore-missing-vnet-service-endpoint` hjälp av parametern. Detta gör att konfigurationen för Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät är klar. När undernätskonfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade undernätet.

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

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrera från en IP-brandväggsregel till ett virtuellt nätverk ACL

Om du vill migrera ett Azure Cosmos DB-konto från att använda IP-brandväggsregler till att använda slutpunkter för virtuella nätverkstjänster gör du så här.

När ett Azure Cosmos DB-konto har konfigurerats för en tjänstslutpunkt för ett undernät skickas begäranden från det undernätet till Azure Cosmos DB med virtuell nätverks- och undernätskällasinformation i stället för en källa offentlig IP-adress. Dessa begäranden matchar inte längre ett IP-filter som konfigurerats på Azure Cosmos DB-kontot, vilket är anledningen till att följande steg är nödvändiga för att undvika driftstopp.

Innan du fortsätter aktiverar du slutpunkten för Azure Cosmos DB-tjänsten i det virtuella nätverket och undernätet med hjälp av steget som visas ovan i "Aktivera tjänstens slutpunkt för ett befintligt undernät i ett virtuellt nätverk".

1. Hämta information om virtuella nätverk och undernät:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Förbereda ett nytt virtuellt nätverksregelobjekt för Azure Cosmos DB-konto:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB-kontot för att aktivera tjänstslutpunktsåtkomst från undernätet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Upprepa föregående steg för alla Azure Cosmos DB-konton som används från undernätet.

1. Ta bort IP-brandväggsregeln för undernätet från Azure Cosmos DB-kontots brandväggsregler.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar en brandvägg för Azure Cosmos DB finns i artikeln [Brandväggs support.](firewall-support.md)
