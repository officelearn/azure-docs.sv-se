---
title: Så här konfigurerar du virtuellt nätverk och undernät-baserad åtkomst för ditt Azure Cosmos-konto
description: Det här dokumentet beskriver steg som krävs för att installationen Azure Cosmos DB tjänstslutpunkt för virtuellt nätverk.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 16cd959a83850a3bc940803cd23e7542e34825c8
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283220"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>Hur du kommer åt Azure Cosmos DB-resurser från virtuella nätverk

Azure cosmos DB-konton kan konfigureras för att tillåta åtkomst från specifika undernät i Azure Virtual Network. Det finns två steg som krävs för att begränsa åtkomsten till Azure Cosmos-konto med anslutningar från ett undernät i ett virtuellt nätverk (VNET).
 
1. Aktivera undernätet för att skicka undernät och VNET-identitet till Azure Cosmos DB. Du kan åstadkomma detta genom att aktivera tjänstslutpunkten för Azure Cosmos DB i specifika undernät.

1. Lägga till en regel i Azure Cosmos-konto att ange undernätet som en källa där, går att komma åt kontot.

> [!NOTE]
> När service-slutpunkten för din Azure-Cosmos kontot är aktiverat på ett undernät och källan för trafiken når Azure Cosmos DB växlar från offentlig IP-adress till VNET och undernät. Trafiken växlar gäller för alla Azure Cosmos-konto som du ansluter från det här undernätet. Om dina Azure Cosmos-konton har IP-baserad Brandvägg för att det här undernätet kan begär från aktiverad undernät inte längre matchar IP-brandväggsregler och de avvisas. Mer information finns i de steg som beskrivs i [migrera från IP-brandväggsregel till VNET Access Control List](#migrate-from-firewall-to-vnet) i den här artikeln. 

I följande avsnitt beskrivs hur du konfigurerar VNET-tjänstslutpunkt för ett Azure Cosmos-konto.

## <a id="configure-using-portal"></a>Konfigurera tjänsteslutpunkt med hjälp av Azure-portalen

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera tjänstslutpunkt för en befintlig Azure-nätverk och undernät

1. Från **alla resurser** hitta Azure-Cosmos-konto som du vill skydda bladet.

1. Välj **brandväggar och virtuella nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett befintligt virtuellt nätverk undernät under **virtuella nätverk**väljer **lägga till befintliga Azure-nätverk**.

1. Välj den **prenumeration** från vilken du vill lägga till Azure-nätverk. Välj Azure **virtuella nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos-konto. Sedan väljer du **aktivera** att aktivera valda nätverken med Tjänsteslutpunkter för ”Microsoft.AzureCosmosDB”. När den är klar väljer du **Lägg till**. 

   ![Välj ett virtuellt nätverk och undernät](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. När Azure Cosmos-kontot är aktiverat för att komma åt från ett virtuellt nätverk, tillåta den endast trafik från det valda undernätet. Det virtuella nätverk och undernät som du har lagt till ska se ut som i följande skärmbild:

   ![virtuellt nätverk och undernät som har konfigurerats](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Om du vill aktivera tjänstslutpunkter i virtuella nätverk, behöver du följande Prenumerationsbehörigheter:
  * Prenumeration med ett virtuellt nätverk: nätverksdeltagare
  * Prenumeration med Azure Cosmos-konto: DocumentDB-Kontodeltagare

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera tjänstslutpunkt för en ny Azure-nätverk och undernät

1. Från **alla resurser** hitta Azure-Cosmos-konto som du vill skydda bladet.

1. Välj **brandväggar och virtuella Azure-nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **valda nätverk**.  

1. Om du vill bevilja åtkomst till en ny Azure-nätverk under virtuella nätverk, Välj **Lägg till nytt virtuellt nätverk**.  

1. Ange de information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan skapa. Undernätet skapas med en tjänstslutpunkt för ”Microsoft.AzureCosmosDB” aktiverat.

   ![Välj ett virtuellt nätverk och undernät för nytt virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Om ditt Azure Cosmos-konto används av andra Azure-tjänster som Azure Search, eller komma åt från Stream analytics eller Power BI, du tillåter åtkomst genom att kontrollera **accepterar anslutningar från offentliga Azure-Datacenter**.

För att säkerställa att du har åtkomst till Azure Cosmos DB-mått från portalen, måste du aktivera **tillåter åtkomst från Azure Portal** alternativ. Mer information om de här alternativen finns begäranden från Azure-portalen och begäran från Azure PaaS-tjänster delar av Konfigurera [IP-brandvägg](how-to-configure-firewall.md) artikeln. När du har valt åtkomst, Välj **spara** att spara inställningarna.

## <a id="remove-vnet-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät 

1. Från **alla resurser** bladet hittar Azure Cosmos-kontot som du tilldelats tjänstslutpunkter.  

2. Välj **brandväggar och virtuella nätverk** från inställningsmenyn.  

3. Om du vill ta bort ett virtuellt nätverk eller undernät regeln, Välj ”...” bredvid det virtuella nätverket eller undernätet och välj **ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  Klicka på **spara** att tillämpa ändringarna.

## <a id="configure-using-powershell"></a>Konfigurera tjänsteslutpunkt med hjälp av Azure PowerShell

> [!NOTE]
> När du använder PowerShell eller CLI, måste du ange den fullständiga listan med IP-filter och VNET ACL: er i parametrar, inte bara de som behöver läggas till.

Använd följande steg för att konfigurera tjänstslutpunkt på ett Azure Cosmos-konto med hjälp av Azure PowerShell:  

1. Installera senast [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) och [inloggning](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

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

1. Hämta egenskaper för Azure Cosmos-konto genom att köra följande cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos account name>"

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

1. Uppdatera egenskaper för Azure Cosmos-konto med den nya konfigurationen genom att köra följande cmdlets: 

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

1. Kör följande kommando för att kontrollera att ditt Azure Cosmos-konto har uppdaterats med tjänstslutpunkt för virtuellt nätverk som du konfigurerade i föregående steg:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Konfigurera tjänsteslutpunkt med hjälp av Azure CLI 

1. Aktivera tjänstslutpunkt för ett undernät i ett virtuellt nätverk.

1. Uppdatera befintliga Azure-Cosmos-konto med undernätet ACL: er

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. Skapa ett nytt Azure Cosmos-konto med undernät ACL: er

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

## <a id="migrate-from-firewall-to-vnet"></a>Migrera från IP-brandväggsregel till VNET-ACL 

Följande steg krävs endast för Azure Cosmos-konton med befintliga IP-brandväggsregler som tillåter ett undernät och du vill använda VNET och undernät-baserade åtkomstkontrollistor i stället för IP-brandväggsregel.

När tjänstslutpunkt för Azure Cosmos-kontot är aktiverat för ett undernät, skickas begäranden med källan som innehåller information om VNET och undernät i stället för offentlig IP-adress. Därför matchar sådana begäranden inte ett IP-filter. Den här källan växeln sker för alla Azure Cosmos-konton som nås från undernätet med aktiverad tjänstslutpunkt. Använd följande steg för att förhindra avbrott:

1. Hämta egenskaper för Azure Cosmos-konto genom att köra följande cmdlet:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initiera variabler för att använda dem senare. Konfigurera alla variabler från den befintliga definitionen för kontot. Lägg till VNET-ACL till alla Azure-Cosmos-konton som du ansluter från undernätet med `ignoreMissingVNetServiceEndpoint` flaggan.

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

1. Uppdatera egenskaper för Azure Cosmos-konto med den nya konfigurationen genom att köra följande cmdlets:

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

1. Upprepa steg 1 – 3 för alla Azure Cosmos-konton som du kommer åt från undernätet.

1.  Vänta 15 minuter och sedan uppdatera undernät för att aktivera tjänstslutpunkten.

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

* För att konfigurera en brandvägg för Azure Cosmos DB, se [stöd i brandväggen](firewall-support.md) artikeln.
