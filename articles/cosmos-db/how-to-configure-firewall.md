---
title: Konfigurera en IP-Brandvägg för Azure Cosmos DB-kontot
description: Lär dig hur du konfigurerar principer för IP-åtkomstkontroll för brandvägg på Azure Cosmos DB-databaskonton.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 308d4527c9ea56a408ddaf05532f5a0b36136262
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451775"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurera IP-brandvägg i Azure Cosmos DB

Du kan skydda data som lagras i Azure Cosmos DB-kontot med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomstkontroller för Brandvägg för inkommande trafik support. Du kan ange en IP-Brandvägg för Azure Cosmos DB-konto med hjälp av något av följande sätt:

* Från Azure portal
* Deklarativt med en Azure Resource Manager-mall
* Programmässigt via Azure CLI eller Azure PowerShell genom att uppdatera den **ipRangeFilter** egenskap

## <a id="configure-ip-policy"></a> Konfigurera en IP-brandvägg med hjälp av Azure-portalen

Ange IP-principen för åtkomstkontroll i Azure-portalen och gå till sidan för Azure Cosmos DB-konto och välj **brandvägg och virtuella nätverk** på navigeringsmenyn. Ändra den **tillåta åtkomst från** värde att **valda nätverk**, och välj sedan **spara**. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

När IP-åtkomstkontroll är aktiverat, ger möjligheten att ange IP-adresser, IP-adressintervall och växlar i Azure-portalen. Växlar Aktivera åtkomst till andra Azure-tjänster och Azure-portalen. I följande avsnitt innehåller information om dessa växlar.

> [!NOTE]
> När du aktiverar en IP-principer för åtkomstkontroll för Azure Cosmos DB-kontot, avvisas alla förfrågningar till ditt Azure Cosmos DB-konto från datorer som inte listan över tillåtna för IP-adressintervall. Bläddra i Azure Cosmos DB-resurser från portalen blockeras även för att säkerställa integriteten för åtkomstkontroll.

### <a name="allow-requests-from-the-azure-portal"></a>Tillåta begäranden från Azure portal 

När du aktiverar en IP-principer för åtkomstkontroll programmässigt kan du behöva lägga till IP-adressen för Azure portal för att den **ipRangeFilter** egenskapen att upprätthålla åtkomsten. Portalen IP-adresser är:

|Region|IP-adress|
|------|----------|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|USA-förvaltad region|52.244.48.71|
|Alla andra regioner|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Du kan aktivera åtkomst till Azure-portalen genom att välja den **tillåta åtkomst från Azure-portalen** alternativ, enligt följande skärmbild: 

![Skärmbild som visar hur du aktiverar åtkomst i Azure portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Tillåta förfrågningar från globala Azure-Datacenter eller andra källor i Azure

Du kan använda IP-Brandvägg för att begränsa åtkomst även om du öppnar ditt Azure Cosmos DB-konto från tjänster som inte har en statisk IP-adress (till exempel Azure Stream Analytics och Azure Functions). Om du vill tillåta åtkomst till Azure Cosmos DB-konto från tjänster, att lägga till IP-adressen 0.0.0.0 i listan över tillåtna IP-adresser. 0.0.0.0 adress begränsar begäranden till ditt Azure Cosmos DB-konto från Azure-datacenter IP-adressintervall. Den här inställningen tillåter inte åtkomst för alla andra IP-adressintervall till ditt Azure Cosmos DB-konto.

> [!NOTE]
> Det här alternativet konfigurerar brandväggen så att alla förfrågningar från Azure, inklusive förfrågningar från prenumerationer för andra kunder som distribueras i Azure. Listan över IP-adresser som tillåts av det här alternativet är brett, så det begränsar effekten av en brandväggsprincip. Använd det här alternativet bara om dina förfrågningar som inte kommer från statisk IP-adresser eller undernät i virtuella nätverk. Om du väljer detta alternativ tillåter åtkomst från Azure-portalen eftersom Azure-portalen har distribuerats i Azure.

Du kan aktivera åtkomst till Azure-portalen genom att välja den **accepterar anslutningar från offentliga Azure-Datacenter** alternativ, enligt följande skärmbild: 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Begäranden från din aktuella IP-adress

För att förenkla utvecklingen, kan Azure-portalen du identifiera och lägga till IP-Adressen för klientdatorn i listan över tillåtna. Appar som körs på din dator kan sedan komma åt ditt Azure Cosmos DB-konto. 

Portalen identifierar automatiskt klientens IP-adress. Det kan vara klientens IP-adress på din dator eller IP-adressen för din gateway. Se till att ta bort den här IP-adress innan du utför dina arbetsbelastningar till produktion. 

Om du vill lägga till din aktuella IP-adress i listan över IP-adresser, Välj **Lägg till min nuvarande IP**. Välj sedan **Spara**.

![Skärmbild som visar hur konfigurera brandväggsinställningar för den aktuella IP-Adressen](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Begäranden från cloud services

I Azure är molntjänster ett vanligt sätt som värd för mellannivå service logic med hjälp av Azure Cosmos DB. Om du vill aktivera åtkomst till ditt Azure Cosmos DB-konto från en tjänst i molnet, måste du lägga till offentliga IP-adressen för Molntjänsten i listan med tillåtna IP-adresser som är associerat med ditt Azure Cosmos DB-konto genom att [konfigurera IP-principen för åtkomstkontroll](#configure-ip-policy). Detta säkerställer att alla rollinstanser av molntjänster har åtkomst till ditt Azure Cosmos DB-konto. 

Du kan hämta IP-adresser för dina molntjänster i Azure-portalen, enligt följande skärmbild:

![Skärmbild som visar offentliga IP-adress för en molntjänst som visas i Azure portal](./media/how-to-configure-firewall/public-ip-addresses.png)

När du skalar ut din molntjänst genom att lägga till rollinstanser har dessa nya instanser automatiskt åtkomst till Azure Cosmos DB-kontot eftersom de ingår i samma molntjänst.

### <a name="requests-from-virtual-machines"></a>Begäranden från virtuella datorer

Du kan också använda [virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för mellannivå tjänster med hjälp av Azure Cosmos DB. Om du vill konfigurera ditt Cosmos DB-konto för att tillåta åtkomst från virtuella datorer, måste du konfigurera offentliga IP-adressen för den virtuella datorn och/eller VM-skalningsuppsättning som en av de tillåtna IP-adresserna för ditt Azure Cosmos DB-konto genom att [ konfigurera principen för IP-åtkomstkontroll](#configure-ip-policy). 

Du kan hämta IP-adresser för virtuella datorer i Azure-portalen, enligt följande skärmbild:

![Skärmbild som visar en offentlig IP-adress för en virtuell dator visas i Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

När du lägger till instanser av virtuella datorer i gruppen, får de automatiskt åtkomst till ditt Azure Cosmos DB-konto.

### <a name="requests-from-the-internet"></a>Begäranden från internet

När du använder Azure Cosmos DB-kontot från en dator på internet kan måste klientens IP-adress eller IP-adressintervall för datorn läggas till listan över tillåtna IP-adresser för ditt konto.

## <a id="configure-ip-firewall-arm"></a>Konfigurera en IP-brandvägg med hjälp av Resource Manager-mall

Om du vill konfigurera åtkomstkontroll till ditt Azure Cosmos DB-konto, se till att Resource Manager-mallen anger den **ipRangeFilter** attributet med en lista över tillåtna IP-adressintervall. Till exempel lägga till följande JSON-kod i mallen:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Konfigurera en IP-principer för åtkomstkontroll med hjälp av Azure CLI

Följande kommando visar hur du skapar ett Azure Cosmos DB-konto med IP-åtkomstkontroll: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

Om du vill uppdatera brandväggsinställningarna för ett befintligt konto, kör du följande kommando:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Felsöka problem med en IP-principer för åtkomstkontroll

Du kan felsöka problem med en IP-principer för åtkomstkontroll med hjälp av följande alternativ: 

### <a name="azure-portal"></a>Azure Portal 
Genom att aktivera en IP-principer för åtkomstkontroll för ditt Azure Cosmos DB-konto kan blockera du alla begäranden till ditt konto från datorer som inte listan över tillåtna för IP-adressintervall. Om du vill aktivera dataplanet åtgärder som surfning behållare och Frågedokument, du måste uttryckligen tillåta åtkomst i Azure portal med hjälp av den **brandväggen** rutan i portalen.

### <a name="sdks"></a>SDK:er 
När du får åtkomst till Azure Cosmos DB-resurser med hjälp av SDK: er från datorer som inte ingår i listan över tillåtna, en allmän **403 Åtkomst nekas** svar returneras utan ytterligare information. Kontrollera listan över tillåtna IP för ditt konto och se till att rätt principkonfigurationen tillämpas på ditt Azure Cosmos DB-konto. 

### <a name="source-ips-in-blocked-requests"></a>Käll-IP-adresser i blockerade begäranden
Aktivera Diagnostisk loggning på ditt Azure Cosmos DB-konto. De här loggarna visar varje begäran och svar. -Brandväggen-meddelanden loggas 403 koden. Du kan se källan IP-adresser för blockerade begäranden genom att filtrera dessa meddelanden. Se [Diagnostisk loggning för Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Begäranden från ett undernät med en tjänstslutpunkt för Azure Cosmos DB-aktiverad
Begäranden från ett undernät i ett virtuellt nätverk som har en tjänstslutpunkt för Azure Cosmos DB är aktiverat skickar virtuella nätverk och undernät identiteten till Azure Cosmos DB-konton. Dessa begäranden har inte den offentliga IP-Adressen för källan, så att IP-filter avvisa dem. Lägg till en åtkomstkontrollista för att tillåta åtkomst från specifika undernät i virtuella nätverk, som beskrivs i [så här konfigurerar du virtuellt nätverk och undernät-baserad åtkomst för Azure Cosmos DB-kontot](how-to-configure-vnet-service-endpoint.md). Det kan ta upp till 15 minuter för brandväggsregler för att tillämpa.


## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera en tjänstslutpunkt för virtuellt nätverk för Azure Cosmos DB-kontot finns i följande artiklar:

* [Virtuellt nätverk och undernät åtkomstkontroll för ditt Azure Cosmos DB-konto](vnet-service-endpoint.md)
* [Konfigurera virtuella nätverk och undernät-baserad åtkomst för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)


