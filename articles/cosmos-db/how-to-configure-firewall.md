---
title: 'Anvisningar: konfigurera IP-Brandvägg för ditt Azure Cosmos-konto'
description: Lär dig hur du konfigurerar principer för IP-åtkomstkontroll för brandvägg på Azure Cosmos DB-databaskonton.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: e1253d4a43e45fc7df7efbb6d5f9dd4176957ca2
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629250"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Anvisningar: konfigurera IP-Brandvägg för ditt Azure Cosmos-konto

Du kan skydda data som lagras i ditt Azure Cosmos-konto med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomstkontroller för Brandvägg för inkommande trafik support. Du kan ange en IP-Brandvägg för Azure Cosmos-kontot genom att använda något av följande sätt:

1. Från Azure portal
2. Deklarativt med en Azure Resource Manager-mall
3. Programmässigt via Azure CLI eller Azure Powershell genom att uppdatera den **ipRangeFilter** egenskapen.

## <a id="configure-ip-policy"></a> Konfigurera IP-brandvägg med hjälp av Azure portal

Ange IP-principen för åtkomstkontroll i Azure-portalen och gå till sidan för Azure Cosmos DB-konto, klickar du på **brandvägg och virtuella nätverk** navigeringsmenyn, ändra den **tillåta åtkomst från** värde att **valda nätverk**, och klicka sedan på **spara**. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

När IP-åtkomstkontroll är aktiverat, ger Azure-portalen kan du ange IP-adresser, IP-adressintervall, samt växlar för att ge åtkomst till andra Azure-tjänster och Azure-portalen. Information om dessa växlar finns i följande avsnitt.

> [!NOTE]
> När du har aktiverat principer för IP-åtkomstkontroll för ditt Azure Cosmos-konto, avvisas alla förfrågningar till ditt Azure Cosmos-konto från datorer som inte listan över tillåtna för IP-adressintervall. Bläddra i Azure Cosmos DB-resurser från portalen är också blockeras för att säkerställa integriteten för åtkomstkontroll.

### <a name="allow-requests-from-the-azure-portal"></a>Tillåta begäranden från Azure portal 

När du aktiverar en IP-principer för åtkomstkontroll programmässigt kan du behöva lägga till IP-adressen för Azure portal för att den **ipRangeFilter** egenskapen att upprätthålla åtkomsten. Portalen IP-adresser är:

|Region|IP-adress|
|------|----------|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|USA-förvaltad region|52.244.48.71|
|Alla regioner förutom ovanstående tre|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Du kan aktivera åtkomst till Azure-portalen genom att välja den **tillåta åtkomst från Azure-portalen** enligt i följande skärmbild: 

![Skärmbild som visar hur du aktiverar åtkomst i Azure portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Tillåta förfrågningar från globala Azure-Datacenter eller andra källor i Azure

Om du ansluter till Azure Cosmos-konto från tjänster som inte har en statisk IP-adress, till exempel Azure Stream analytics, Använd Azure Functions o.s.v. Du kan fortfarande IP-brandväggen för att begränsa åtkomsten. En brandväggsinställning måste vara aktiverat för att få åtkomst till Azure Cosmos-konto från tjänster. Lägg till IP-adress-0.0.0.0 i listan över tillåtna IP-adresser om du vill aktivera den här brandväggsinställningen. 0.0.0.0 begränsar begäranden till ditt Azure Cosmos-konto från Azure-datacenter IP-adressintervall. Den här inställningen tillåter inte åtkomst för alla andra IP-adressintervall till ditt Azure Cosmos-konto.

> [!NOTE]
> Det här alternativet konfigurerar brandväggen så att alla förfrågningar från Azure inklusive begäranden från prenumerationer för andra kunder som distribueras i Azure. Listan över IP-adresser som tillåts av det här alternativet är brett så det begränsar brandväggsprincipen effektivitet. Det här alternativet bör användas endast om dina förfrågningar som inte kommer från statisk IP-adresser eller undernät i virtuella nätverk. Om du väljer detta alternativ tillåter åtkomst från Azure-portalen eftersom Azure-portalen har distribuerats i Azure.

Du kan aktivera åtkomst till Azure-portalen genom att välja den **godkänna anslutningar från offentliga Azure-Datacenter** enligt i följande skärmbild: 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Begäranden från din aktuella IP-adress

För att förenkla utvecklingen, Azure-portalen hjälper dig att identifiera och lägga till IP-Adressen för klientdatorn i listan över tillåtna, så att appar som körs på datorn kan komma åt ditt Azure Cosmos-konto. Klientens IP-adress identifieras automatiskt av portalen. Det kan vara klientens IP-adress på din dator eller IP-adressen för din gateway. Se till att ta bort den här IP-adress innan du att dina arbetsbelastningar till produktion. 

Aktivera din aktuella IP-adress **Lägg till min nuvarande IP**, som lägger till din aktuella IP-adress i listan över IP-adresser och klicka sedan på **spara**.

![Skärmbild som visar hur konfigurera brandväggsinställningar för nuvarande IP-adress](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Begäranden från cloud services

I Azure är molntjänster ett vanligt sätt som värd för tjänsten mellannivålogik med Azure Cosmos DB. För att ge åtkomst till ditt Azure Cosmos-konto från en molnbaserad tjänst, offentliga IP-adressen för Molntjänsten måste läggas till i listan med IP-adresser som är associerat med ditt Azure Cosmos-konto med tillåtna [konfigurera IP-principen för åtkomstkontroll](#configure-ip-policy). Detta säkerställer att alla rollinstanser av molntjänster har åtkomst till ditt Azure Cosmos-konto. Du kan hämta IP-adresser för dina molntjänster i Azure-portalen, enligt följande skärmbild:

![Skärmbild som visar offentliga IP-adress för en molntjänst som visas i Azure portal](./media/how-to-configure-firewall/public-ip-addresses.png)

När du skalar ut din molntjänst genom att lägga till ytterligare roll instanser har dessa nya instanser automatiskt åtkomst till Azure Cosmos-kontot eftersom de ingår i samma molntjänst.

### <a name="requests-from-virtual-machines"></a>Begäranden från virtuella datorer

[Virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) kan också användas som värd för mellannivå tjänster med hjälp av Azure Cosmos DB. Om du vill konfigurera Cosmos-konto för att tillåta åtkomst från virtuella datorer, offentliga IP-adresser för virtuella datorer och/eller virtual machine scale Sets måste konfigureras som en av de tillåtna IP-adresserna för ditt Azure Cosmos-konto genom att [konfigurering av IP princip för åtkomstkontroll](#configure-ip-policy). Du kan hämta IP-adresser för virtuella datorer i Azure-portalen, enligt följande skärmbild:

![Skärmbild som visar en offentlig IP-adress för en virtuell dator visas i Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

När du lägger till ytterligare VM-instanser i gruppen får de automatiskt tillgång till ditt Azure Cosmos-konto.

### <a name="requests-from-the-internet"></a>Begäranden från internet

När du har åtkomst till ditt Azure Cosmos-konto från en dator på internet, måste klientens IP-adress eller IP-adressintervall för datorn läggas till listan över tillåtna IP-adressen för ditt konto.

## <a id="configure-ip-firewall-arm"></a>Konfigurera IP-brandvägg med hjälp av Resource Manager-mall

Om du vill konfigurera åtkomstkontroll till ditt Azure Cosmos-konto, Resource Manager-mallen bör ange den **ipRangeFilter** attributet med en lista över IP-adressintervall som ska vitlistas. Till exempel lägga till följande JSON-kod i mallen:

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

## <a id="configure-ip-firewall-cli"></a>Konfigurera IP-principer för åtkomstkontroll med Azure CLI

Följande kommando visar hur du skapar ett Azure Cosmos-konto med IP-åtkomstkontroll: 

```azurecli-interactive
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

## <a id="troubleshoot-ip-firewall"></a>Så här felsöker du problem med IP-access control principer

Du kan felsöka problem med IP-access control principer med hjälp av följande alternativ: 

### <a name="azure-portal"></a>Azure Portal 
Genom att aktivera en IP-principer för åtkomstkontroll för ditt Azure Cosmos-konto, blockeras alla förfrågningar till ditt konto från datorer som inte listan över tillåtna för IP-adressintervall. Därför, om du vill aktivera portal data hanteringsplanets åtgärder som att bläddra behållare och köra frågor mot dokument måste du uttryckligen tillåta åtkomst i Azure portal med hjälp av den **brandväggen** rutan i portalen.

### <a name="sdks"></a>SDK:er 
När du använder Azure Cosmos-resurser med hjälp av SDK: er från datorer som inte ingår i listan över tillåtna, **allmän 404 det gick inte att hitta svar returneras med ingen ytterligare information om**. Kontrollera listan över tillåtna IP för ditt konto och se till att rätt principkonfigurationen tillämpas på ditt Cosmos-konto. 

### <a name="check-source-ips-in-blocked-requests"></a>Kontrollera käll-IP-adresser i blockerade begäranden
Aktivera Diagnostisk loggning på ditt Azure Cosmos-konto, dessa loggar skulle visa varje begäran och svar. **-Brandväggen-meddelanden loggas internt 403 koden**. Du kan se källan IP-adresser för blockerade begäranden genom att filtrera dessa meddelanden. Se [Diagnostisk loggning för Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Begäranden från undernätet med tjänstslutpunkt för Azure Cosmos-databasen
Begäranden från ett undernät i virtuella nätverk som har tjänstslutpunkt för Azure Cosmos DB aktiverat skickar VNET och undernät identitet till Azure Cosmos-konton. Dessa begäranden har inte den offentliga IP-Adressen för källan så att de avvisas av IP-filter. Om du vill tillåta åtkomst från specifika undernät i virtuella nätverk, lägger du till VNET åtkomstkontrollista som beskrivs i [så här konfigurerar du virtuellt nätverk och undernät-baserad åtkomst för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md). Det kan ta upp till 15 minuter för brandväggsregler för att tillämpa.


## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera tjänstslutpunkt för virtuellt nätverk för Azure Cosmos DB-kontot finns i följande artiklar:

* [Åtkomstkontroll för VNET och undernät för ditt Azure Cosmos-konto](vnet-service-endpoint.md)
* [Så här konfigurerar du virtuellt nätverk och undernät baserat åtkomst för ditt Azure Cosmos-konto](how-to-configure-vnet-service-endpoint.md)


