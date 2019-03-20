---
title: Konfigurera virtuella nätverk och undernät-baserad åtkomst för ditt Azure Cosmos DB-konto
description: Det här dokumentet beskriver de steg som krävs för att konfigurera en tjänstslutpunkt för virtuellt nätverk för Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: efe85e017d883ca82414fe5ff10b5c2932366e48
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101133"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Konfigurera åtkomst från virtuella nätverk (VNet)

Du kan konfigurera Azure Cosmos DB-konton för att tillåta åtkomst från ett specifikt undernät för ett Azure-nätverk. Begränsa åtkomsten till ett Azure Cosmos DB-konto med anslutningar från ett undernät i ett virtuellt nätverk:
 
1. Aktivera undernätet för att skicka undernät och virtuella nätverksidentitet till Azure Cosmos DB. Du kan åstadkomma detta genom att aktivera en tjänstslutpunkt för Azure Cosmos DB i specifika undernät.

1. Lägg till en regel i Azure Cosmos DB-konto för att ange undernätet som en källa som konton som kan nås.

> [!NOTE]
> När en tjänstslutpunkt för Azure Cosmos DB-kontot är aktiverat på ett undernät, växlar källan för den trafik som når Azure Cosmos DB från en offentlig IP-adress till ett virtuellt nätverk och undernät. Trafiken växlar gäller för alla Azure Cosmos DB-konto som kan nås från det här undernätet. Om dina Azure Cosmos DB-konton har en IP-baserad Brandvägg för att det här undernätet kan begäranden från tjänsten aktiverad undernätet matchar inte längre IP-brandväggsregler och de har avvisats. 
>
> Mer information finns i stegen i den [migrera från en IP-brandväggsregel till ett virtuellt nätverk för åtkomstkontrollista](#migrate-from-firewall-to-vnet) i den här artikeln. 

I följande avsnitt beskrivs hur du konfigurerar en tjänstslutpunkt för virtuellt nätverk för ett Azure Cosmos DB-konto.

## <a id="configure-using-portal"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure-portalen

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera en tjänstslutpunkt för en befintlig Azure-nätverk och undernät

1. Från den **alla resurser** bladet, hitta Azure Cosmos DB-konto som du vill skydda.

1. Välj **brandväggar och virtuella nätverk** från inställningsmenyn och välja att tillåta åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett befintligt virtuellt nätverk undernät under **virtuella nätverk**väljer **lägga till befintliga Azure-nätverk**.

1. Välj den **prenumeration** från vilken du vill lägga till ett Azure-nätverk. Välj Azure **virtuella nätverk** och **undernät** att du vill ge åtkomst till ditt Azure Cosmos DB-konto. Välj sedan **aktivera** att aktivera valda nätverken med Tjänsteslutpunkter för ”Microsoft.AzureCosmosDB”. När den är klar väljer du **Lägg till**. 

   ![Välj ett virtuellt nätverk och undernät](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. När Azure Cosmos DB-kontot har aktiverats för åtkomst från ett virtuellt nätverk, tillåta den trafik från endast det valda undernätet. Virtuellt nätverk och undernät som du har lagt till ska se ut som i följande skärmbild:

   ![virtuellt nätverk och undernät som har konfigurerats](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Om du vill aktivera tjänstslutpunkter i virtuella nätverk behöver du följande Prenumerationsbehörigheter:
>   * Prenumeration med ett virtuellt nätverk: Nätverksdeltagare
>   * Prenumeration med Azure Cosmos DB-konto: DocumentDB-kontodeltagare
>   * Om ditt virtuella nätverk och Azure Cosmos DB-konto finns i olika prenumerationer, se till att det också har den prenumeration som virtuella nätverket `Microsoft.DocumentDB` resursprovidern registrerats. Om du vill registrera en resursleverantör, se [Azure resursproviders och resurstyper](../azure-resource-manager/resource-manager-supported-services.md) artikeln. 

Här följer anvisningarna för att registrera prenumerationen med resursprovidern.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera en tjänstslutpunkt för en ny Azure-nätverk och undernät

1. Från den **alla resurser** bladet, hitta Azure Cosmos DB-konto som du vill skydda.  

1. Välj **brandväggar och virtuella Azure-nätverk** från inställningsmenyn och välja att tillåta åtkomst från **valda nätverk**.  

1. Att bevilja åtkomst till ett nytt Azure virtuellt nätverk, under **virtuella nätverk**väljer **Lägg till nytt virtuellt nätverk**.  

1. Ange informationen som krävs för att skapa ett nytt virtuellt nätverk och välj sedan **skapa**. Undernätet skapas med en tjänstslutpunkt för ”Microsoft.AzureCosmosDB” aktiverat.

   ![Välj ett virtuellt nätverk och undernät för ett nytt virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Search eller nås från Stream analytics eller Power BI kan du tillåta åtkomst genom att välja **accepterar anslutningar från offentliga Azure-Datacenter**.

För att säkerställa att du har åtkomst till Azure Cosmos DB-mått från portalen, måste du aktivera **tillåta åtkomst från Azure-portalen** alternativ. Mer information om alternativen finns i [konfigurera en IP-brandvägg](how-to-configure-firewall.md) artikeln. När du aktiverar åtkomst väljer **spara** att spara inställningarna.

## <a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät 

1. Från den **alla resurser** bladet, Sök i Azure Cosmos DB-konto som du tilldelats tjänstslutpunkter.  

2. Välj **brandväggar och virtuella nätverk** från inställningsmenyn.  

3. Om du vill ta bort ett virtuellt nätverk eller undernät regeln, Välj **...**  bredvid det virtuella nätverket eller undernätet och välj **ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Välj **spara** att tillämpa ändringarna.

## <a id="configure-using-powershell"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure PowerShell

> [!NOTE]
> När du använder PowerShell eller Azure CLI, måste du ange den fullständiga listan med IP-filter och det virtuella nätverket ACL: er i parametrar, inte bara de som behöver läggas till.

Använd följande steg för att konfigurera en tjänstslutpunkt till ett Azure Cosmos DB-konto med hjälp av Azure PowerShell:  

1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) och [logga in](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Aktivera tjänstslutpunkt för ett befintligt undernät i ett virtuellt nätverk.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. Få information om virtuellt nätverk.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Hämta egenskaper för Azure Cosmos DB-kontot genom att köra följande cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initiera variabler för användning senare. Konfigurera alla variabler från den befintliga definitionen för kontot.

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

1. Uppdatera egenskaper för Azure Cosmos DB-konto med den nya konfigurationen genom att köra följande cmdlets: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Kör följande kommando för att bekräfta att Azure Cosmos DB-kontot har uppdaterats med tjänstslutpunkt för virtuellt nätverk som du konfigurerade i föregående steg:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurera en tjänstslutpunkt med hjälp av Azure CLI 

1. Aktivera tjänstslutpunkt för ett undernät i ett virtuellt nätverk.

1. Uppdatera det befintliga Azure Cosmos DB-kontot med undernätet åtkomstkontrollistor (ACL).

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Skapa ett nytt Azure Cosmos DB-konto med undernätet ACL: er.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>Migrera från en IP-brandväggsregel till ett virtuellt nätverk ACL 

Använd följande steg om du bara för Azure Cosmos DB-konton med befintliga IP-brandväggsregler som tillåter ett undernät när du vill använda virtuellt nätverk och undernät-baserade åtkomstkontrollistor i stället för en IP-brandväggsregel.

När en tjänstslutpunkt för ett Azure Cosmos DB-konto är aktiverat för ett undernät, skickas begäranden med en källa som innehåller virtuella nätverk och undernät i stället för en offentlig IP-adress. Dessa begäranden matcha inte ett IP-filter. Den här källan växeln sker för alla Azure Cosmos DB-konton som nås från undernätet med ingen tjänstslutpunkt aktiverad. Använd följande steg för att förhindra avbrott:

1. Hämta egenskaper för Azure Cosmos DB-kontot genom att köra följande cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initiera variabler för att använda dem senare. Konfigurera alla variabler från den befintliga definitionen för kontot. Lägga till det virtuella nätverket ACL till alla Azure Cosmos DB-konton som du ansluter från undernätet med `ignoreMissingVNetServiceEndpoint` flaggan.

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

1. Uppdatera egenskaper för Azure Cosmos DB-konto med den nya konfigurationen genom att köra följande cmdlets:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzureRmResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Upprepa steg 1 – 3 för alla Azure Cosmos DB-konton som du kommer åt från undernätet.

1.  Vänta 15 minuter och uppdatera sedan undernätet för att aktivera tjänstslutpunkten.

1.  Aktivera tjänstslutpunkt för ett befintligt undernät i ett virtuellt nätverk.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzureRmVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
    ```

1. Ta bort IP-brandväggsregel för undernätet.

## <a name="next-steps"></a>Nästa steg

* För att konfigurera en brandvägg för Azure Cosmos DB, se den [stöd i brandväggen](firewall-support.md) artikeln.
