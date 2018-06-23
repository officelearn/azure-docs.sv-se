---
title: Säker åtkomst till ett Azure DB som Cosmos-konto med hjälp av Azure Virtual Network tjänstslutpunkten | Microsoft Docs
description: Det här dokumentet beskriver steg som krävs för installationen Azure Cosmos DB virtuellt nätverk tjänstslutpunkten.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: de52521824c146f63fb16e2690e2a24167ae2efe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333920"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Säker åtkomst till ett Azure DB som Cosmos-konto med hjälp av Azure Virtual Network tjänstslutpunkten

Azure CosmosDB konton kan konfigureras för att tillåta åtkomst från specifika undernätet på Azure Virtual Network. Genom att aktivera en [tjänstslutpunkten](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure CosmosDB från ett virtuellt nätverk och dess undernät, trafik säkerställs ett optimalt och säkert flöde Cosmos-DB Azure.  

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Du kan replikera data som finns i Azure DB som Cosmos-konto till flera regioner. När Azure Cosmos DB konfigureras med ett virtuellt nätverk tjänstslutpunkten tillåter åtkomst från IP-adresser som tillhör specicifc undernät i varje virtuellt nätverk. Följande bild visar en illustration av en Azure Cosmos-databas som har virtuella nätverk tjänstslutpunkten aktiverad:

![arkitektur för virtuellt nätverk slutpunkt](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

När ett Azure DB som Cosmos-konto har konfigurerats med ett virtuellt nätverk tjänstslutpunkten kan den kan nås från det angivna undernätet, alla offentliga/internet-åtkomst tas bort. Om du vill veta i detaljerad om Tjänsteslutpunkter avser Azure [översikt över virtuella nätverk service slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) artikel.

> [!NOTE]
> För närvarande kan slutpunkter för virtuellt nätverk konfigureras för Azure SQL DB-API Cosmos eller Mongo API-konton. Möjligheten att konfigurera slutpunkter för andra API: er och suveräna moln, till exempel Azure Tyskland eller Azure Government blir snart tillgänglig. Om du har en IP-brandvägg som konfigurerats för ditt konto i Azure Cosmos DB Observera brandväggskonfigurationen, ta bort IP-brandväggen och konfigurera tjänstslutpunkten ACL. När du har konfigurerat tjänstslutpunkten aktivera du IP-brandväggen igen om det behövs.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurera tjänstslutpunkten med hjälp av Azure-portalen
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera tjänsteslutpunkt för en befintlig Azure-nätverk och undernät

1. Från **alla resurser** hitta det virtuella nätverket du vill konfigurera tjänstslutpunkten för Azure Cosmos DB bladet. Navigera till den **tjänstens slutpunkter** bladet och se till att undernätet i det virtuella nätverket har aktiverats för ”Azure.CosmosDB” tjänstslutpunkten.  

   ![Bekräfta tjänstslutpunkten aktiverad](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Från **alla resurser** hitta Cosmos-DB Azure-konto du vill skydda bladet.  

3. Kopiera IP brandväggen informationen som är kopplad till ditt Azure DB som Cosmos-konto för framtida användning innan du aktiverar virtuellt nätverk tjänstslutpunkten. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänstslutpunkten.  

4. Välj **brandväggar och virtuella nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **markerat nätverk**.  

3. Om du vill bevilja åtkomst till undernätet för ett befintligt virtuellt nätverk under virtuella nätverk, Välj **lägga till befintliga virtuella Azure-nätverket**.  

4. Välj den **prenumeration** från vilken du vill lägga till virtuella Azure-nätverket. Välj Azure **virtuella nätverken** och **undernät** som du vill ge åtkomst till ditt konto i Azure Cosmos DB. Sedan väljer du **aktivera** att aktivera valda nätverk med slutpunkter för ”Microsoft.AzureCosmosDB”. När den är klar, Välj **Lägg till**.  

   ![Välj virtuella nätverk och undernät](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Om du inte tidigare har konfigurerat tjänstslutpunkten för Azure Cosmos DB för de valda virtuella Azure-nätverk och undernät, kan den konfigureras som en del av den här åtgärden. Aktiverar åtkomst tar upp till 15 minuter att slutföra. Det är mycket viktigt att inaktivera brandväggen IP efter påpekas ned innehållet i brandväggen ACL för renabling dem senare. 

   ![virtuellt nätverk och undernät som har konfigurerats](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Ditt konto i Azure Cosmos DB kan nu trafik från det valda undernätet. Om du hade tidigare aktiverat IP-brandväggen kan du aktivera dem igen med hjälp av tidigare information.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera tjänsteslutpunkt för en ny Azure-nätverk och undernät

1. Från **alla resurser** hitta Cosmos-DB Azure-konto du vill skydda bladet.  

> [!NOTE]
> Om du har en IP-brandvägg som konfigurerats för ditt konto i Azure Cosmos DB Observera brandväggskonfigurationen, ta bort IP-brandvägg och aktivera sedan tjänstslutpunkten. Om du aktiverar tjänstslutpunkten utan disbling brandväggen trafik från det ip-adressintervallet går förlorat den virtuella IP-identitet och tas den bort med ett felmeddelande för IP-filter. Så du bör alltid inaktivera brandväggsreglerna för att förhindra det här felet, kopierar du dem, aktivera tjänstslutpunkten från undernätet och slutligen ACL undernät från Cosmos-databasen. När du konfigurerar tjänstslutpunkten och lägger till ACL kan du återaktivera IP brandväggen igen om det behövs.

2. Kopiera IP brandväggen informationen som är kopplad till ditt Azure DB som Cosmos-konto för framtida användning innan du aktiverar virtuellt nätverk tjänstslutpunkten. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänstslutpunkten.  

3. Välj **brandväggar och virtuella Azure-nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **markerat nätverk**.  

4. Om du vill bevilja åtkomst till ett nytt Azure virtuellt nätverk under virtuella nätverk, Välj **Lägg till nytt virtuellt nätverk**.  

5. Ange de information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan skapa. Undernätet skapas med en tjänstslutpunkt för ”Microsoft.AzureCosmosDB” aktiverad.

   ![Välj virtuella nätverk och undernät för ett nytt virtuellt nätverk](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Tillåt åtkomst från Azure-portalen

När Azure Virtual Network service slutpunkter har aktiverats för din Azure Cosmos DB databaskonto är åtkomst från portalen eller andra Azure-tjänster inaktiverad som standard. Åtkomst till din Azure Cosmos DB databaskonto från datorer utanför det konfigurerade undernätet blockeras inklusive åtkomst från portalen.

![Tillåt åtkomst från portalen](./media/vnet-service-endpoint/allow-access-from-portal.png)

Om ditt konto i Azure Cosmos DB används av andra Azure-tjänster som Azure Search eller åt från Stream analytics eller Power BI, du tillåter åtkomst genom att kontrollera **Tillåt åtkomst till Azure-tjänster**.

För att säkerställa att du har åtkomst till Azure Cosmos DB statistik från portalen, måste du aktivera **Tillåt åtkomst till Azure portal** alternativ. Mer information om dessa alternativ finns [anslutningar från Azure-portalen](firewall-support.md#connections-from-the-azure-portal) och [anslutningar från Azure PaaS tjänster](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) avsnitt. När du har valt åtkomst, Välj **spara** spara inställningarna.

## <a name="remove-a-virtual-network-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät 

1. Från **alla resurser** bladet, hitta Azure Cosmos-DB-konto som du tilldelats slutpunkter.  

2. Välj **brandväggar och virtuella nätverk** från inställningsmenyn.  

3. Om du vill ta bort ett virtuellt nätverk eller undernät regeln, Välj ”...” bredvid virtuellt nätverk eller undernät och markera **ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Klicka på **spara** tillämpa dina ändringar.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurera tjänstslutpunkten med hjälp av Azure PowerShell 

Använd följande steg för att konfigurera tjänstslutpunkten till ett Azure DB som Cosmos-konto med hjälp av Azure PowerShell:  

1. Installera senaste [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) och [inloggning](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Se till att du Observera IP-brandväggsinställningar och ta bort IP-brandväggen helt innan du aktiverar tjänstslutpunkten för kontot.


> [!NOTE]
> Om du har en IP-brandvägg som konfigurerats för ditt konto i Azure Cosmos DB Observera brandväggskonfigurationen, ta bort IP-brandvägg och aktivera sedan tjänstslutpunkten. Om du aktiverar tjänstslutpunkten utan disbling brandväggen trafik från det ip-adressintervallet går förlorat den virtuella IP-identitet och tas den bort med ett felmeddelande för IP-filter. Så du bör alltid inaktivera brandväggsreglerna för att förhindra det här felet, kopierar du dem, aktivera tjänstslutpunkten från undernätet och slutligen ACL undernät från Cosmos-databasen. När du konfigurerar tjänstslutpunkten och lägger till ACL kan du återaktivera IP brandväggen igen om det behövs.

2. Kopiera IP brandväggen informationen som är kopplad till ditt Azure DB som Cosmos-konto för framtida användning innan du aktiverar virtuellt nätverk tjänstslutpunkten. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänstslutpunkten.  

3. Aktivera tjänstslutpunkten för ett befintligt undernät i ett virtuellt nätverk.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. Förbered dig för aktivering av ACL för kontot CosmosDB genom att se till att det virtuella nätverk och undernät har tjänstslutpunkten aktiverat för Azure Cosmos DB.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Hämta egenskaper för Azure DB som Cosmos-konto genom att köra följande cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Initiera variabler för senare användning. Konfigurera alla variabler från den befintliga definitionen för kontot om du har flera platser, måste du lägga till dem som en del av matrisen. I det här steget kan konfigurera du också virtuella nätverk tjänstslutpunkten genom att ange variabeln ”accountVNETFilterEnabled” till ”True”. Det här värdet tilldelas senare ”isVirtualNetworkFilterEnabled”-parametern.  

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. Uppdatera egenskaper för Azure DB som Cosmos-konto med den nya konfigurationen genom att köra följande cmdlets: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Kör följande kommando för att bekräfta att Azure DB som Cosmos-konto har uppdaterats med tjänstslutpunkten virtuella nätverk som du konfigurerade i föregående steg:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Lägg till virtuellt nätverk tjänstslutpunkten för ett konto i Azure Cosmos DB som IP-brandväggen är aktiverad

1. Först inaktivera IP brandväggen åtkomst till Azure DB som Cosmos-konto.  

2. Aktivera virtuella nätverksslutpunkten på Azure DB som Cosmos-kontot med någon av metoderna som beskrivs i föregående avsnitt.  

3. Aktivera åtkomst för IP-brandväggen. 

## <a name="test-the-access"></a>Testa åtkomst

Om du vill kontrollera om slutpunkter för Azure Cosmos DB är konfigurerade som förväntat, använder du följande steg:

* Ställa in två undernät i ett virtuellt nätverk som klient- och servergränssnitten, aktivera Cosmos DB tjänstslutpunkten för backend-undernät.  

* Aktivera åtkomst i Cosmos-DB-konto för backend-undernät.  

* Skapa två virtuella datorer en i backend-undernät och en annan i klientdelens undernät.  

* Försök att komma åt kontot Azure Cosmos DB från både virtuella datorer. Du ska kunna ansluta från den virtuella datorn skapas i backend-undernät, men inte från som skapades i undernätet frontend. Begäran kommer fel 404 ut när du försöker ansluta från klientdel undernätet som bekräftar att åtkomst till Azure Cosmos DB skyddas med hjälp av tjänstslutpunkten virtuellt nätverk. Datorn i backend-undernät kommer att kunna fungera.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Vad händer när du har åtkomst till ett Azure DB som Cosmos-konto som har virtuella nätverket åtkomstkontrollista (ACL) aktiverat?  

HTTP 404 fel returneras.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Är undernät i ett virtuellt nätverk som skapats i olika regioner som har beviljats åtkomst till ett Azure DB som Cosmos-konto i en annan region? Om Azure Cosmos DB kontot finns i USA, västra och östra USA och virtuella nätverk kan du till exempel i flera områden, kan det virtuella nätverket komma åt Azure Cosmos DB?  

Ja, virtuella nätverk som skapats i olika regioner kan använda med den nya funktionen. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Ett Azure DB som Cosmos-konto kan ha både tjänstslutpunkten för virtuellt nätverk och brandvägg?  

Ja, virtuella nätverk tjänstslutpunkten och en brandvägg kan finnas tillsammans. I allmänhet bör du kontrollera att åtkomst till portalen är alltid aktiverat innan du konfigurerar en tjänstslutpunkt för virtuellt nätverk så att du kan visa de mätvärden som är kopplade till behållaren.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan jag ”Tillåt åtkomst till andra Azure-tjänster från en viss Azure-region” när tjänsten endpoint åtkomst är aktiverat för Azure Cosmos DB?  

Detta krävs bara när du vill att ditt Azure DB som Cosmos-konto som kan nås av andra Azure första part tjänster som Azure Data factory, Azure Search eller någon tjänsten som har distribuerats i det angivna Azure-region.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Hur många slutpunkter för virtuellt nätverk är tillåtna för Azure Cosmos DB?  

slutpunkter 64 virtuella nätverket tillåts för ett konto i Azure Cosmos DB.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Vad är relationen mellan tjänstslutpunkten och Nätverkssäkerhetsgrupp (NSG)?  

NSG-regler i Azure Cosmos-databasen kan du begränsa åtkomsten till specifika Azure Cosmos DB IP-adressintervall. Om du vill tillåta åtkomst till en Azure DB som Cosmos-instans som finns i en specifik [region](https://azure.microsoft.com/global-infrastructure/regions/), du kan ange regionen i följande format: 

    AzureCosmosDB.<region name>

Läs mer om NSG-taggar finns [virtuellt nätverk service taggar](../virtual-network/security-overview.md#service-tags) artikel. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Vad är förhållandet mellan ett IP-brandväggen och endpoint kapaciteten för Virtual Network service?  

Dessa två funktioner kompletterar varandra för att säkra isolering av Azure Cosmos DB tillgångar och skydda dem. Med IP-garanterar brandväggen att statiska IP-adresser kan komma åt Azure DB som Cosmos-konto.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Har en lokal enhet IP-adress som är anslutna via Azure Virtual Network gateway(VPN) eller expressroute gateway åtkomst till Azure Cosmos-DB-konto?  

Lokala enhetens IP-adress eller IP-adressintervall bör läggas till i listan över statisk IP för att komma åt Azure DB som Cosmos-konto.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Vad händer om du tar bort ett virtuellt nätverk som har endpoint tjänstinställning för Azure Cosmos DB?  

När du tar bort ett virtuellt nätverk ACL-information har associerats med att Azure Cosmos DB tas bort och det tar bort interaktionen mellan virtuella nätverk och Azure DB som Cosmos-konto. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Vad händer om ett Azure DB som Cosmos-konto som har virtuella nätverk tjänstslutpunkten aktiverat raderas?

Metadata som associeras med specifika Azure DB som Cosmos-kontot tas bort från undernätet. Och det är användarens ansvar att ta bort undernät och virtuella nätverk som används.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Kan jag använda peered virtuella nätverk för att skapa tjänstslutpunkten för Azure Cosmos DB?  

Nej, endast direkt virtuella nätverk och deras undernät kan skapa Azure Cosmos DB slutpunkter.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Vad händer med källans IP-adress för resurs, till exempel virtuell dator i undernätet som har Azure Cosmos DB tjänstslutpunkten aktiverad?

När slutpunkter för virtuellt nätverk är aktiverade, växlar källans IP-adresser för resurser i undernät för virtuellt nätverk från att använda offentliga IPV4-adresser till Azure Virtual Network privata adresser för trafik till Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Finns Azure Cosmos DB i virtuella Azure-nätverket kunden?  

Azure Cosmos-DB är en tjänst med flera klienter med en offentlig IP-adress. När du begränsar åtkomsten till ett undernät för ett virtuellt Azure-nätverk med hjälp av funktionen tjänsten endpoint är åtkomsten begränsad för ditt Azure DB som Cosmos-konto via det angivna virtuella Azure-nätverket och undernätet.  Azure DB Cosmos-konto finns inte i det virtuella Azure-nätverket. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Vad händer om något loggas i logganalys/OMS om den är aktiverad?  

Azure Cosmos-DB skickar loggar med IP-adress (utan den sista oktetten) med status 403 för begäran blockeras av ACL.  

## <a name="next-steps"></a>Nästa steg
Konfigurera en brandvägg finns i Azure Cosmos DB [stöd för brandvägg](firewall-support.md) artikel.

