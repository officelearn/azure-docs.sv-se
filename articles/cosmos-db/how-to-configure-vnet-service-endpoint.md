---
title: Konfigurera Virtual Network-baserad åtkomst för ett Azure Cosmos-konto
description: I det här dokumentet beskrivs de steg som krävs för att konfigurera en tjänst slut punkt för virtuellt nätverk för Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/04/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 995e5a1a87ee332c48641f42c4134e3e58f11cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87495428"
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

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Konfigurera en tjänst slut punkt med hjälp av Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett befintligt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.

1. Välj **brand väggar och virtuella nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett befintligt virtuellt nätverks undernät går du till **virtuella nätverk**och väljer **Lägg till befintligt virtuellt Azure-nätverk**.

1. Välj den **prenumeration** som du vill lägga till ett virtuellt Azure-nätverk från. Välj de virtuella Azure- **nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos DB-konto. Välj sedan **Aktivera** för att aktivera valda nätverk med tjänst slut punkter för "Microsoft. AzureCosmosDB". När den är klar väljer du **Lägg till**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Välj virtuellt nätverk och undernät":::

1. När Azure Cosmos DB-kontot har Aktiver ATS för åtkomst från ett virtuellt nätverk kommer det att tillåta trafik från endast det valda under nätet. Det virtuella nätverk och undernät som du har lagt till ska visas som visas på följande skärm bild:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Välj virtuellt nätverk och undernät":::

> [!NOTE]
> Om du vill aktivera tjänst slut punkter för virtuella nätverk måste du ha följande prenumerations behörigheter:
>   * Prenumeration med virtuellt nätverk: nätverks deltagare
>   * Prenumeration med Azure Cosmos DB konto: DocumentDB Account Contributor
>   * Om ditt virtuella nätverk och Azure Cosmos DB konto finns i olika prenumerationer, kontrollerar du att prenumerationen som har ett virtuellt nätverk också har en `Microsoft.DocumentDB` registrerad resurs leverantör. Information om hur du registrerar en resurs leverantör finns i artikeln [Azure Resource providers och types](../azure-resource-manager/management/resource-providers-and-types.md) .

Här följer anvisningar för att registrera prenumeration med Resource Provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera en tjänst slut punkt för ett nytt virtuellt Azure-nätverk och undernät

1. Från bladet **alla resurser** letar du upp det Azure Cosmos DB konto som du vill skydda.  

1. Välj **brand väggar och virtuella Azure-nätverk** på menyn Inställningar och välj att tillåta åtkomst från **valda nätverk**.  

1. Om du vill bevilja åtkomst till ett nytt virtuellt Azure-nätverk går du till **virtuella nätverk**och väljer **Lägg till nytt virtuellt nätverk**.  

1. Ange den information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan **skapa**. Under nätet skapas med en tjänst slut punkt för "Microsoft. AzureCosmosDB" aktive rad.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Välj virtuellt nätverk och undernät":::

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Kognitiv sökning eller nås från Stream Analytics eller Power BI kan du tillåta åtkomst genom att välja **acceptera anslutningar från globala Azure-datacenter**.

För att se till att du har åtkomst till Azure Cosmos DB mått från portalen måste du aktivera **Tillåt åtkomst från Azure Portal** alternativ. Mer information om de här alternativen finns i artikeln [Konfigurera en IP-brandvägg](how-to-configure-firewall.md) . När du har aktiverat åtkomst väljer du **Spara** för att spara inställningarna.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät

1. Från bladet **alla resurser** letar du reda på det Azure Cosmos DB konto som du har tilldelat tjänstens slut punkter för.  

1. Välj **brand väggar och virtuella nätverk** på menyn Inställningar.  

1. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** bredvid det virtuella nätverket eller under nätet och väljer **ta bort**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Välj virtuellt nätverk och undernät":::

1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Konfigurera en tjänst slut punkt med hjälp av Azure PowerShell

> [!NOTE]
> När du använder PowerShell eller Azure CLI måste du ange den fullständiga listan med IP-filter och ACL: er för virtuella nätverk i parametrar, inte bara de som behöver läggas till.

Använd följande steg för att konfigurera en tjänst slut punkt till ett Azure Cosmos DB konto genom att använda Azure PowerShell:  

1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) och [Logga](https://docs.microsoft.com/powershell/azure/authenticate-azureps)in.  

1. Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk.  

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

1. Hämta information om virtuellt nätverk.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Förbereda en regel för Cosmos DB Virtual Network

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB konto egenskaper med den nya Virtual Network slut punkts konfigurationen: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Kör följande kommando för att kontrol lera att ditt Azure Cosmos DB-konto har uppdaterats med den virtuella nätverks tjänst slut punkt som du konfigurerade i föregående steg:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Konfigurera en tjänst slut punkt med hjälp av Azure CLI

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

Det här exemplet är avsett att visa hur du ansluter ett Azure Cosmos-konto till ett befintligt nytt virtuellt nätverk där under nätet ännu inte har kon figurer ATS för tjänst slut punkter. Detta görs med hjälp av `--ignore-missing-vnet-service-endpoint` parametern. Detta gör att konfigurationen av Cosmos-kontot kan slutföras utan fel innan konfigurationen till det virtuella nätverkets undernät har slutförts. När under näts konfigurationen är klar kommer Cosmos-kontot att vara tillgängligt via det konfigurerade under nätet.

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

## <a name="port-range-when-using-direct-mode"></a>Port intervall när Direct-läge används

När du använder tjänst slut punkter med ett Azure Cosmos-konto via en anslutning i direkt läge, måste du se till att TCP-portnumret från 10000 till 20000 är öppen.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migrera från en IP-brandväggsregel till en virtuell nätverks-ACL

Använd följande steg om du vill migrera ett Azure Cosmos DB konto från att använda IP-brandvägg för att använda tjänst slut punkter för virtuella nätverk.

När ett Azure Cosmos DB-konto har kon figurer ATS för en tjänst slut punkt för ett undernät skickas begär Anden från det under nätet till Azure Cosmos DB med information om virtuellt nätverk och undernäts källa i stället för en offentlig IP-adress för källan. Dessa begär Anden matchar inte längre ett IP-filter som kon figurer ATS på Azure Cosmos DB kontot, vilket är orsaken till att följande steg är nödvändiga för att undvika drift stopp.

Innan du fortsätter ska du aktivera Azure Cosmos DB tjänstens slut punkt på det virtuella nätverket och under nätet med hjälp av det steg som visas ovan i "Aktivera tjänstens slut punkt för ett befintligt undernät i ett virtuellt nätverk".

1. Hämta information om virtuellt nätverk och undernät:

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

1. Förbered ett nytt Virtual Network regel objekt för det Azure Cosmos DB kontot:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Uppdatera Azure Cosmos DB-kontot för att aktivera åtkomst till tjänst slut punkten från under nätet:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Upprepa föregående steg för alla Azure Cosmos DB-konton som nås från under nätet.

1. Ta bort IP-brandväggens regel för under nätet från Azure Cosmos DB kontots brand Väggs regler.

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar en brand vägg för Azure Cosmos DB finns i artikeln om [brand Väggs stöd](firewall-support.md) .
