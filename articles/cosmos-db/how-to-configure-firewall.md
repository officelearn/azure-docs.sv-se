---
title: Konfigurera en IP-brandvägg för ditt Azure Cosmos DB-konto
description: Lär dig hur du konfigurerar PRINCIPER för IP-åtkomstkontroll för brandväggsstöd på Azure Cosmos-konton.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162952"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurera IP-brandväggen i Azure Cosmos DB

Du kan skydda data som lagras i ditt Azure Cosmos DB-konto med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomstkontroller för stöd för inkommande brandvägg. Du kan ange en IP-brandvägg för Azure Cosmos DB-konto på något av följande sätt:

* Från Azure-portalen
* Deklarativt med hjälp av en Azure Resource Manager-mall
* Programmässigt via Azure CLI eller Azure PowerShell genom att uppdatera egenskapen **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Konfigurera en IP-brandvägg med hjälp av Azure-portalen

Om du vill ange IP-åtkomstkontrollprincipen i Azure-portalen går du till kontot Azure Cosmos DB-konto och väljer **Brandvägg och virtuella nätverk** på navigeringsmenyn. Ändra värdet **Tillåt åtkomst från** till markerade **nätverk**och välj sedan **Spara**.

![Skärmbild som visar hur du öppnar brandväggssidan i Azure-portalen](./media/how-to-configure-firewall/azure-portal-firewall.png)

När IP-åtkomstkontroll är aktiverad ger Azure-portalen möjlighet att ange IP-adresser, IP-adressintervall och växlar. Växlar ger åtkomst till andra Azure-tjänster och Azure-portalen. I följande avsnitt finns information om dessa växlar.

> [!NOTE]
> När du har aktiverat en IP-åtkomstkontrollprincip för ditt Azure Cosmos DB-konto avvisas alla begäranden till ditt Azure Cosmos DB-konto från datorer utanför den tillåtna listan över IP-adressintervall. Att bläddra i Azure Cosmos DB-resurser från portalen blockeras också för att säkerställa integriteten för åtkomstkontroll.

### <a name="allow-requests-from-the-azure-portal"></a>Tillåt begäranden från Azure-portalen

När du aktiverar en IP-åtkomstkontrollprincip programmässigt måste du lägga till IP-adressen för Azure-portalen till egenskapen **ipRangeFilter** för att upprätthålla åtkomsten. Portalens IP-adresser är:

|Region|IP-adress|
|------|----------|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|US Gov|52.244.48.71|
|Alla andra regioner|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Du kan aktivera begäranden om åtkomst till Azure-portalen genom att välja alternativet **Tillåt åtkomst från Azure-portalen,** som visas i följande skärmbild:

![Skärmbild som visar hur du aktiverar Azure Portal-åtkomst](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Tillåt förfrågningar från globala Azure-datacenter eller andra källor i Azure

Om du öppnar ditt Azure Cosmos DB-konto från tjänster som inte tillhandahåller en statisk IP (till exempel Azure Stream Analytics och Azure Functions) kan du fortfarande använda IP-brandväggen för att begränsa åtkomsten. Du kan aktivera åtkomst från andra källor i Azure genom att välja alternativet **Acceptera anslutningar från Azure-datacenter,** vilket visas i följande skärmbild:

![Skärmbild som visar hur du öppnar brandväggssidan i Azure-portalen](./media/how-to-configure-firewall/enable-azure-services.png)

När du aktiverar det här `0.0.0.0` alternativet läggs IP-adressen till i listan över tillåtna IP-adresser. `0.0.0.0` IP-adressen begränsar begäranden till ditt Azure Cosmos DB-konto från Azure datacenter IP-intervall. Den här inställningen tillåter inte åtkomst för några andra IP-intervall till ditt Azure Cosmos DB-konto.

> [!NOTE]
> Det här alternativet konfigurerar brandväggen så att alla begäranden från Azure tillåts, inklusive begäranden från prenumerationer från andra kunder som distribueras i Azure. Listan över INTERNET-adresser som tillåts av det här alternativet är bred, så den begränsar effektiviteten hos en brandväggsprincip. Använd det här alternativet endast om dina begäranden inte kommer från statiska IP-adresser eller undernät i virtuella nätverk. Om du väljer det här alternativet tillåts åtkomst automatiskt från Azure-portalen eftersom Azure-portalen distribueras i Azure.

### <a name="requests-from-your-current-ip"></a>Förfrågningar från din nuvarande IP

För att förenkla utvecklingen hjälper Azure-portalen dig att identifiera och lägga till IP-adressen för klientdatorn i listan över tillåtna. Appar som kör din dator kan sedan komma åt ditt Azure Cosmos DB-konto.

Portalen identifierar automatiskt klient-IP-adressen. Det kan vara datorns klient-IP-adress eller IP-adressen för nätverksgatewayen. Se till att ta bort den här IP-adressen innan du tar dina arbetsbelastningar till produktion.

Om du vill lägga till din nuvarande IP-adress i listan över IP-adresser väljer du **Lägg till min nuvarande IP**. Välj sedan **Spara**.

![Skärmbild som visar hur du konfigurerar brandväggsinställningar för den aktuella IP-adressen](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Förfrågningar från molntjänster

I Azure är molntjänster ett vanligt sätt att vara värd för tjänstlogik på mellannivå med hjälp av Azure Cosmos DB. Om du vill aktivera åtkomst till ditt Azure Cosmos DB-konto från en molntjänst måste du lägga till molntjänstens offentliga IP-adress i den tillåtna listan över IP-adresser som är associerade med ditt Azure Cosmos [DB-konto genom att konfigurera IP-åtkomstkontrollprincipen](#configure-ip-policy). Detta säkerställer att alla rollinstanser av molntjänster har åtkomst till ditt Azure Cosmos DB-konto.

Du kan hämta IP-adresser för dina molntjänster i Azure-portalen, vilket visas i följande skärmbild:

![Skärmbild som visar den offentliga IP-adressen för en molntjänst som visas i Azure-portalen](./media/how-to-configure-firewall/public-ip-addresses.png)

När du skalar ut din molntjänst genom att lägga till rollinstanser, har dessa nya instanser automatiskt åtkomst till Azure Cosmos DB-kontot eftersom de ingår i samma molntjänst.

### <a name="requests-from-virtual-machines"></a>Begäranden från virtuella datorer

Du kan också använda [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för virtuella datorer för att vara värd för mellannivåtjänster med hjälp av Azure Cosmos DB. Om du vill konfigurera ditt Cosmos DB-konto så att det ger åtkomst från virtuella datorer måste du konfigurera den offentliga IP-adressen för den virtuella datorn och/eller skala för virtuella datorer som en av de tillåtna IP-adresserna för ditt Azure Cosmos [DB-konto genom att konfigurera IP-åtkomstkontrollprincipen](#configure-ip-policy).

Du kan hämta IP-adresser för virtuella datorer i Azure-portalen, vilket visas i följande skärmbild:

![Skärmbild som visar en offentlig IP-adress för en virtuell dator som visas i Azure-portalen](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

När du lägger till instanser av virtuella datorer i gruppen får de automatiskt åtkomst till ditt Azure Cosmos DB-konto.

### <a name="requests-from-the-internet"></a>Förfrågningar från Internet

När du öppnar ditt Azure Cosmos DB-konto från en dator på internet måste datorns klient-IP-adress eller IP-adressintervall läggas till i den tillåtna listan över IP-adresser för ditt konto.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurera en IP-brandvägg med hjälp av en Resource Manager-mall

Om du vill konfigurera åtkomstkontroll till ditt Azure Cosmos DB-konto kontrollerar du att Resource Manager-mallen anger **attributet ipRangeFilter** med en lista över tillåtna IP-intervall. Om du konfigurerar IP-brandväggen till ett `locations` redan distribuerat Cosmos-konto kontrollerar du att matrisen matchar det som för närvarande distribueras. Du kan inte `locations` samtidigt ändra matrisen och andra egenskaper. Mer information och exempel på Azure Resource Manager-mallar för Azure Cosmos DB finns i [Azure Resource Manager-mallar för Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurera en IP-åtkomstkontrollprincip med hjälp av Azure CLI

Följande kommando visar hur du skapar ett Azure Cosmos DB-konto med IP-åtkomstkontroll:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurera en IP-åtkomstkontrollprincip med hjälp av PowerShell

Följande skript visar hur du skapar ett Azure Cosmos DB-konto med IP-åtkomstkontroll:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Felsöka problem med en IP-åtkomstkontrollprincip

Du kan felsöka problem med en IP-åtkomstkontrollprincip med hjälp av följande alternativ:

### <a name="azure-portal"></a>Azure Portal

Genom att aktivera en IP-åtkomstkontrollprincip för ditt Azure Cosmos DB-konto blockerar du alla begäranden till ditt konto från datorer utanför den tillåtna listan över IP-adressintervall. Om du vill aktivera portaldataplansåtgärder som att bläddra i behållare och fråga dokument måste du uttryckligen tillåta Azure-portalåtkomst med hjälp av **brandväggsfönstret** i portalen.

### <a name="sdks"></a>SDK:er

När du öppnar Azure Cosmos DB-resurser med hjälp av SDK:er från datorer som inte finns i listan över tillåtna, returneras ett allmänt **403 Förbjudet** svar utan ytterligare information. Verifiera den tillåtna IP-listan för ditt konto och se till att rätt principkonfiguration tillämpas på ditt Azure Cosmos DB-konto.

### <a name="source-ips-in-blocked-requests"></a>Käll-IPs i blockerade begäranden

Aktivera diagnostikloggning på ditt Azure Cosmos DB-konto. Dessa loggar visar varje begäran och svar. De brandväggsrelaterade meddelandena loggas med en returkod på 403. Genom att filtrera dessa meddelanden kan du se käll-IPs för blockerade begäranden. Se [Azure Cosmos DB-diagnostikloggning](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Begäranden från ett undernät med en tjänstslutpunkt för Azure Cosmos DB aktiverat

Begäranden från ett undernät i ett virtuellt nätverk som har en tjänstslutpunkt för Azure Cosmos DB aktiverat skickar det virtuella nätverket och undernätsidentiteten till Azure Cosmos DB-konton. Dessa begäranden har inte den offentliga IP-adressen för källan, så IP-filter avvisar dem. Om du vill tillåta åtkomst från specifika undernät i virtuella nätverk lägger du till en åtkomstkontrolllista enligt beskrivningen i [Så här konfigurerar du virtuellt nätverk och undernätsbaserad åtkomst för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md). Det kan ta upp till 15 minuter innan brandväggsregler gäller.

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar en slutpunkt för en virtuell nätverkstjänst för ditt Azure Cosmos DB-konto finns i följande artiklar:

* [Åtkomstkontroll för virtuellt nätverk och undernät för ditt Azure Cosmos DB-konto](vnet-service-endpoint.md)
* [Konfigurera virtuellt nätverk och undernätsbaserad åtkomst för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)
