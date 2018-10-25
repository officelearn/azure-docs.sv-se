---
title: Skydda åtkomst till ett Azure Cosmos DB-konto med hjälp av Azure Virtual Network-tjänstslutpunkt | Microsoft Docs
description: Det här dokumentet beskriver steg som krävs för att installationen Azure Cosmos DB tjänstslutpunkt för virtuellt nätverk.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: 6d9d05a56376c07013fdea1c94b0a3262d2397c2
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026304"
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Skydda åtkomst till ett Azure Cosmos DB-konto med hjälp av Azure Virtual Network-tjänstslutpunkt

Azure cosmos DB-konton kan konfigureras för att tillåta åtkomst från specifika undernät i Azure Virtual Network. Genom att aktivera en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure cosmos DB från ett virtuellt nätverk och ett undernät, trafik säkerställs en optimal och säker väg till Azure Cosmos DB.  

Azure Cosmos DB är en globalt distribuerad databas för flera datamodeller. Du kan replikera data som finns i Azure Cosmos DB-konto till flera regioner. När Azure Cosmos DB är konfigurerad med en tjänstslutpunkt för virtuellt nätverk, tillåter åtkomst från IP-adresser som tillhör specicifc undernät i varje virtuellt nätverk. Följande bild visar en illustration av en Azure Cosmos DB med tjänstslutpunkt för virtuellt nätverk är aktiverat:

![virtuellt nätverk service-slutpunkt-arkitektur](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

När ett Azure Cosmos DB-konto har konfigurerats med en tjänstslutpunkt för virtuellt nätverk kan den kan nås från det angivna undernätet, alla offentliga/internet-åtkomst tas bort. Du lär dig på detaljerad om tjänstslutpunkter finns Azure [virtuella nätverk översikt över tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md) artikeln.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Konfigurera tjänsteslutpunkt med hjälp av Azure-portalen
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Konfigurera tjänstslutpunkt för en befintlig Azure-nätverk och undernät

1. Från **alla resurser** hitta den virtuella nätverk du vill konfigurera tjänstslutpunkt för Azure Cosmos DB bladet. Navigera till den **tjänstslutpunkter** bladet och se till att undernätet för det virtuella nätverket har aktiverats för tjänstslutpunkten ”Azure.CosmosDB”.  

   ![Bekräfta aktiverad tjänstslutpunkt](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. Från **alla resurser** hittar Azure Cosmos DB-konto som du vill skydda bladet.  

3. Innan du aktiverar tjänstslutpunkt för virtuellt nätverk, kopiera IP-brandväggen information som är associerade med ditt Azure Cosmos DB-konto för framtida användning. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänsteslutpunkt.  

4. Välj **brandväggar och virtuella nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **valda nätverk**.  

3. Om du vill bevilja åtkomst till ett befintligt virtuellt nätverk undernät under virtuella nätverk, Välj **lägga till befintliga Azure-nätverk**.  

4. Välj den **prenumeration** från vilken du vill lägga till Azure-nätverk. Välj Azure **virtuella nätverk** och **undernät** som du vill ge åtkomst till ditt Azure Cosmos DB-konto. Sedan väljer du **aktivera** att aktivera valda nätverken med Tjänsteslutpunkter för ”Microsoft.AzureCosmosDB”. När den är klar väljer du **Lägg till**.  

   ![Välj ett virtuellt nätverk och undernät](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Om tjänstslutpunkt för Azure Cosmos DB inte är tidigare har konfigurerat för den valda Azure-nätverk och undernät, kan den konfigureras som en del av den här åtgärden. Att aktivera åtkomst tar upp till 15 minuter att slutföra. Det är mycket viktigt att inaktivera brandväggen IP efter noterar innehållet i brandväggen ACL för renabling dem senare. 

   ![virtuellt nätverk och undernät som har konfigurerats](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

Azure Cosmos DB-kontot kommer nu endast tillåta trafik från det valda undernätet. Om du hade tidigare aktiverat IP-brandväggen kan du aktivera dem igen med hjälp av tidigare information.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Konfigurera tjänstslutpunkt för en ny Azure-nätverk och undernät

1. Från **alla resurser** hittar Azure Cosmos DB-konto som du vill skydda bladet.  

   > [!NOTE]
   > Om du har en befintlig IP-brandväggen som är konfigurerad för ditt Azure Cosmos DB-konto, Observera brandväggskonfigurationen, ta bort IP-brandvägg och sedan aktivera tjänstslutpunkten. Om du aktiverar tjänstslutpunkten utan disbling brandväggen trafiken från det ip-adressintervallet kommer gå förlorade virtuella IP-identiteten och det släpps med ett felmeddelande för IP-filter. Så du bör alltid inaktivera brandväggsreglerna för att förhindra det här felet, kopiera dem, aktivera tjänstslutpunkt i undernät och slutligen ACL undernät från Cosmos DB. När du konfigurerar tjänstslutpunkt och lägga till ACL: du kan återaktivera IP-brandvägg igen om det behövs.

2. Innan du aktiverar tjänstslutpunkt för virtuellt nätverk, kopiera IP-brandväggen information som är associerade med ditt Azure Cosmos DB-konto för framtida användning. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänsteslutpunkt.  

3. Välj **brandväggar och virtuella Azure-nätverk** från inställningsmenyn och väljer Tillåt åtkomst från **valda nätverk**.  

4. Om du vill bevilja åtkomst till en ny Azure-nätverk under virtuella nätverk, Välj **Lägg till nytt virtuellt nätverk**.  

5. Ange de information som krävs för att skapa ett nytt virtuellt nätverk och välj sedan skapa. Undernätet skapas med en tjänstslutpunkt för ”Microsoft.AzureCosmosDB” aktiverat.

   ![Välj ett virtuellt nätverk och undernät för nytt virtuellt nätverk](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Tillåt åtkomst från Azure-portalen

När Azure Virtual Network-tjänstslutpunkter är aktiverade för ditt Azure Cosmos DB-databaskonto är åtkomst från portalen eller andra Azure-tjänster inaktiverad som standard. Åtkomst till ditt Azure Cosmos DB-databaskonto från datorer utanför det konfigurera undernätet blockeras åtkomst från portalen.

![Tillåt åtkomst från portalen](./media/vnet-service-endpoint/allow-access-from-portal.png)

Om ditt Azure Cosmos DB-konto används av andra Azure-tjänster som Azure Search, eller komma åt från Stream analytics eller Power BI, du tillåter åtkomst genom att kontrollera **Tillåt åtkomst till Azure-tjänster**.

För att säkerställa att du har åtkomst till Azure Cosmos DB-mått från portalen, måste du aktivera **Tillåt åtkomst till Azure-portalen** alternativ. Mer information om alternativen finns [anslutningar från Azure-portalen](firewall-support.md#connections-from-the-azure-portal) och [anslutningar från Azure PaaS-tjänster](firewall-support.md#connections-from-global-azure-datacenters-or-azure-paas-services) avsnitt. När du har valt åtkomst, Välj **spara** att spara inställningarna.

## <a name="remove-a-virtual-network-or-subnet"></a>Ta bort ett virtuellt nätverk eller undernät 

1. Från **alla resurser** bladet, Sök i Azure Cosmos DB-konto som du tilldelats tjänstslutpunkter.  

2. Välj **brandväggar och virtuella nätverk** från inställningsmenyn.  

3. Om du vill ta bort ett virtuellt nätverk eller undernät regeln, Välj ”...” bredvid det virtuella nätverket eller undernätet och välj **ta bort**.

   ![Ta bort ett virtuellt nätverk](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  Klicka på **spara** att tillämpa ändringarna.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Konfigurera tjänsteslutpunkt med hjälp av Azure PowerShell 

Använd följande steg för att konfigurera tjänstslutpunkt till ett Azure Cosmos DB-konto med hjälp av Azure PowerShell:  

1. Installera senast [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) och [inloggning](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  Se till att du Observera IP-brandväggsinställningarna och ta bort IP-brandvägg helt innan du aktiverar tjänstslutpunkten för det kontot.

  > [!NOTE]
  > Om du har en befintlig IP-brandväggen som är konfigurerad för ditt Azure Cosmos DB-konto, Observera brandväggskonfigurationen, ta bort IP-brandvägg och sedan aktivera tjänstslutpunkten. Om du aktiverar tjänstslutpunkten utan disbling brandväggen trafiken från det ip-adressintervallet kommer gå förlorade virtuella IP-identiteten och det släpps med ett felmeddelande för IP-filter. Så du bör alltid inaktivera brandväggsreglerna för att förhindra det här felet, kopiera dem, aktivera tjänstslutpunkt i undernät och slutligen ACL undernät från Cosmos DB. När du konfigurerar tjänstslutpunkt och lägga till ACL: du kan återaktivera IP-brandvägg igen om det behövs.

2. Innan du aktiverar tjänstslutpunkt för virtuellt nätverk, kopiera IP-brandväggen information som är associerade med ditt Azure Cosmos DB-konto för framtida användning. Du kan återaktivera IP-brandväggen när du har konfigurerat tjänsteslutpunkt.  

3. Aktivera tjänstslutpunkt för ett befintligt undernät i ett virtuellt nätverk.  

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

4. Gör dig redo för aktivering av ACL på cosmos DB-konto genom att se till att det virtuella nätverk och undernät har aktiverats för Azure Cosmos DB tjänstslutpunkten.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

5. Hämta egenskaper för Azure Cosmos DB-konto genom att köra följande cmdlet:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. Initiera variabler för användning senare. Konfigurera alla variabler från den befintliga definitionen för kontot om du har flera platser, som du behöver lägga till dem som en del av matrisen. I det här steget ska konfigurera du också tjänstslutpunkt för virtuellt nätverk genom att ange variabeln ”accountVNETFilterEnabled” till ”True”. Det här värdet är senare tilldelad till parametern ”isVirtualNetworkFilterEnabled”.  

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

7. Uppdatera egenskaper för Azure Cosmos DB-konto med den nya konfigurationen genom att köra följande cmdlets: 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. Kör följande kommando för att bekräfta att Azure Cosmos DB-kontot har uppdaterats med tjänstslutpunkt för virtuellt nätverk som du konfigurerade i föregående steg:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>Lägg till tjänstslutpunkt för virtuellt nätverk för ett Azure Cosmos DB-konto som har IP-brandväggen är aktiverad

1. Först inaktivera IP-brandväggsinställningarna åtkomst till Azure Cosmos DB-konto.  

2. Aktivera slutpunkten för virtuellt nätverk till Azure Cosmos DB-konto med någon av metoderna som beskrivs i föregående avsnitt.  

3. Återaktivera åtkomst för IP-brandväggen. 

## <a name="test-the-access"></a>Testa åtkomst

Om du vill kontrollera om Tjänsteslutpunkter för Azure Cosmos DB är konfigurerade som förväntat, använder du följande steg:

* Ställa in två undernät i ett virtuellt nätverk frontend och backend, låter Cosmos DB-tjänstslutpunkt för backend-undernät.  

* Aktivera åtkomst i Cosmos DB-konto för backend-undernät.  

* Skapa två virtuella datorer – en i backend-undernät och en annan i undernätet på klientsidan.  

* Försök att öppna Azure Cosmos DB-kontot från båda de virtuella datorerna. Du ska kunna ansluta från den virtuella datorn skapas i backend-undernät, men inte från den som skapats i frontend-undernätet. Begäran kommer ett fel med 404 när du försöker ansluta från undernätet på klientsidan som bekräftar att åtkomst till Azure Cosmos DB är skyddat med hjälp av tjänstslutpunkt för virtuellt nätverk. Dator i backend-undernät kommer att kunna fungera.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>Vad händer när du har åtkomst till ett Azure Cosmos DB-konto som har virtuella nätverket åtkomstkontrollistan (ACL) är aktiverat?  

HTTP 404 returneras fel.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>Är undernät i ett virtuellt nätverk som skapats i olika regioner som har tillgång till ett Azure Cosmos DB-konto i en annan region? Om Azure Cosmos DB-konto är i USA, västra eller USA, östra och virtuellt nätverk kan du till exempel i flera regioner, kan det virtuella nätverket åtkomst till Azure Cosmos DB?  

Ja, virtuella nätverk som skapats i olika regioner kan komma åt genom den nya funktionen. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Ett Azure Cosmos DB-konto kan ha både tjänstslutpunkt för virtuellt nätverk och en brandvägg?  

Ja, tjänstslutpunkt för virtuellt nätverk och en brandvägg kan finnas tillsammans. I allmänhet bör du kontrollera att åtkomst till portalen alltid är aktiverat innan du konfigurerar en tjänstslutpunkt för virtuellt nätverk så att du kan visa de mått som är kopplade till behållaren.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan jag ”acceptera anslutningar från offentliga Azure-Datacenter” när tjänstens slutpunkt åtkomst är aktiverat för Azure Cosmos DB?  

Detta krävs bara när du vill att Azure Cosmos DB-kontot som kan nås av andra Azure första part tjänster som Azure Data factory, Azure Search eller någon tjänst som distribueras i angivna Azure-region.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Hur många virtuella nätverksslutpunkter tillåts för Azure Cosmos DB?  

64 virtuella nätverksslutpunkter tillåts för ett Azure Cosmos DB-konto.

### <a name="what-is-the-relationship-between-service-endpoint-and-network-security-group-nsg-rules"></a>Vad är relationen mellan tjänstslutpunkt och Nätverkssäkerhetsgrupp (NSG)?  

NSG-regler i Azure Cosmos DB kan du begränsa åtkomsten till specifika Azure Cosmos DB-IP-adressintervall. Om du vill tillåta åtkomst till en Azure Cosmos DB-instans som finns i en viss [region](https://azure.microsoft.com/global-infrastructure/regions/), anger du regionen i följande format: 

    AzureCosmosDB.<region name>

Läs mer om NSG-taggar finns i [tjänsttaggar för virtuellt nätverk](../virtual-network/security-overview.md#service-tags) artikeln. 
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>Vad är relationen mellan en IP-brandvägg och virtuellt nätverk service endpoint funktionen?  

De här två funktionerna kompletterar varandra för att säkra isolering av Azure Cosmos DB-tillgångar och skydda dem. Med hjälp av IP säkerställer brandväggen att statiska IP-adresser kan komma åt Azure Cosmos DB-konto.  

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Har en lokal enhet IP-adress som är anslutna via Azure Virtual Network gateway(VPN) eller Express route-gateway åtkomst till Azure Cosmos DB-konto?  

Lokala enhetens IP-adress eller IP-adressintervall ska läggas till i listan över statiska IP-adresser för att komma åt Azure Cosmos DB-kontot.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Vad händer om du tar bort ett virtuellt nätverk som har endpoint installationsprogram för Azure Cosmos DB?  

När ett virtuellt nätverk har tagits bort, ACL-information som associeras med att Azure Cosmos DB har tagits bort och samverkan mellan virtuella nätverk och Azure Cosmos DB-kontot tas bort. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>Vad händer om ett Azure Cosmos DB-konto som har tjänstslutpunkt för virtuellt nätverk är aktiverat raderas?

Metadata som associeras med specifika Azure Cosmos DB-kontot tas bort från undernätet. Och det är användarens ansvar att ta bort undernät och virtuella nätverk som används.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>Kan jag använda ett peer-kopplat virtuellt nätverk för att skapa tjänstslutpunkt för Azure Cosmos DB?  

Nej, endast direkt virtuellt nätverk och deras undernät kan skapa tjänstslutpunkter för Azure Cosmos DB.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Vad händer med IP-källadressen för resursen som virtuell dator i det undernät som har Azure Cosmos DB-tjänstslutpunkt aktiverad?

När tjänstslutpunkter i virtuella nätverk är aktiverade, växlar källans IP-adresser av resurser i ditt virtuella nätverk undernät från att använda offentliga IPV4-adresser till Azure Virtual Network privata adresser för trafik till Azure Cosmos DB.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Finns Azure Cosmos DB i Azure-nätverk som tillhandahålls av kunden?  

Azure Cosmos DB är en tjänst med flera klienter med en offentlig IP-adress. När du begränsa åtkomsten till ett undernät för ett virtuellt Azure-nätverk med hjälp av funktionen service-slutpunkt är åtkomsten begränsad för ditt Azure Cosmos DB-konto via det angivna virtuella Azure-nätverket och dess undernät.  Azure Cosmos DB-kontot finns inte i den Azure-nätverk. 

### <a name="what-if-anything-will-be-logged-in-log-analytics-if-it-is-enabled"></a>Vad händer om något loggas i Log Analytics om den är aktiverad?  

Azure Cosmos DB kommer skicka loggar med IP-adress (utan den sista oktetten) med status 403 för begäran som blockerats av ACL.  

## <a name="next-steps"></a>Nästa steg
Konfigurera en brandvägg för Azure Cosmos DB finns i [stöd i brandväggen](firewall-support.md) artikeln.

