---
title: Azure Cosmos DB-brandväggsstöd & IP-åtkomstkontroll | Microsoft Docs
description: Lär dig hur du använder principer för IP-åtkomstkontroll för brandväggen support för Azure Cosmos DB-databaskonton.
keywords: IP-åtkomstkontroll, brandväggsstöd
services: cosmos-db
author: kanshiG
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: govindk
ms.openlocfilehash: ebfba4d54b4d4158a2dc0bc2aed09699012ac157
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038059"
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure Cosmos DB-brandväggsstöd
Om du vill skydda data som lagras i ett Azure Cosmos DB-databaskonto Azure Cosmos DB har lagt till stöd för en hemlighet baserat [auktoriseringsmodellen](https://msdn.microsoft.com/library/azure/dn783368.aspx) som använder sig av en stark hashbaserad meddelandeautentiseringskod (HMAC). Förutom den hemliga baserat auktoriseringsmodellen stöder nu Azure Cosmos DB-princip driven IP-baserade åtkomstkontroller för Brandvägg för inkommande trafik support. Den här modellen liknar brandväggsreglerna för traditionella databassystem och ger en extra nivå av säkerhet för Azure Cosmos DB-databaskonto. Med den här modellen kan du nu konfigurera ett Azure Cosmos DB-databaskonto för att endast vara tillgängliga från en godkänd uppsättning datorer och/eller molntjänster. Åtkomst till Azure Cosmos DB-resurser från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande anroparen att presentera en giltig auktoriseringstoken.

## <a name="ip-access-control-overview"></a>IP-åtkomstkontroll: översikt
Ett databaskonto för Azure Cosmos DB är som standard tillgängligt från det offentliga Internet så länge begäran åtföljs av en giltig auktoriseringstoken. Principbaserad IP-åtkomstkontroll kan konfigureras genom att användaren anger en uppsättning IP-adresser eller IP-adressintervall i CIDR-form som ska ingå i listan med tillåtna klient-IP-adresser för ett visst databaskonto. När den här konfigurationen tillämpas blockerar servern alla begäranden från datorer som inte finns med på den här listan.  Anslutningen processchema för IP-baserad åtkomstkontroll beskrivs i följande diagram:

![Diagram över anslutningsprocessen för IP-baserad åtkomstkontroll](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurera principen för IP-åtkomstkontroll
Principen för IP-åtkomstkontroll kan ställas in i Azure portal eller programmässigt via [Azure CLI](cli-samples.md), [Azure Powershell](powershell-samples.md), eller [REST API](/rest/api/cosmos-db/) genom att uppdatera **ipRangeFilter** egenskapen. 

Ange IP-principen för åtkomstkontroll i Azure-portalen och gå till sidan för Azure Cosmos DB-konto, klickar du på **brandvägg och virtuella nätverk** navigeringsmenyn, ändra den **tillåta åtkomst från** värde att **valda nätverk**, och klicka sedan på **spara**. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/firewall-support/azure-portal-firewall.png)

När IP-åtkomstkontroll på ger portalen möjligheten att ange IP-adresser och intervall samt växlar för att ge åtkomst till andra Azure-tjänster och Azure-portalen. Ytterligare information om de växlarna finns i följande avsnitt.

> [!NOTE]
> Genom att aktivera en IP-principer för åtkomstkontroll för ditt Azure Cosmos DB-databaskonto tillåts all åtkomst till ditt Azure Cosmos DB-databaskonto från datorer som inte den konfigurerade listan över IP-adressintervall är blockerade. Tack vare den här modellen kommer surfning plan för åtgärden från portalen också att blockeras för att säkerställa integriteten för åtkomstkontroll.

## <a name="connections-from-the-azure-portal"></a>Anslutningar från Azure portal 

När du aktiverar en IP-principer för åtkomstkontroll programmässigt kan du behöva lägga till IP-adressen för Azure portal för att den **ipRangeFilter** egenskapen att upprätthålla åtkomsten. Portalen IP-adresser är:

|Region|IP-adress|
|------|----------|
|Alla regioner utom de som anges nedan|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|USA-förvaltad region|52.244.48.71|

Åtkomst till Azure-portalen är aktiverat som standard när du ändrar brandväggsinställningen till **markerat nätverk** i Azure-portalen. 

![Skärmbild som visar hur du aktiverar åtkomst i Azure portal](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>Anslutningar från globala Azure-datacenter och Azure PaaS-tjänster

Azure PaaS-tjänster som Azure Stream analytics, Azure Functions etc. används tillsammans med Azure Cosmos DB. En brandväggsinställning måste aktiveras för att tillåta program från andra Azure PaaS-tjänster för att ansluta till din Azure Cosmos DB-resurser. Lägg till IP-adress-0.0.0.0 i listan över tillåtna IP-adresser om du vill aktivera den här brandväggsinställningen. 0.0.0.0 begränsar anslutningar till Azure Cosmos DB-konto från Azure-datacenter IP-adressintervall. Den här inställningen tillåter inte åtkomst för alla andra IP-adressintervall till Azure Cosmos DB-konto.

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att alla anslutningar från Azure tillåts, inklusive anslutningar från prenumerationer för andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast auktoriserade användare.
> 

Åtkomst till anslutningar från i globala Azure-Datacenter är aktiverat som standard när du ändrar brandväggsinställningen till **markerat nätverk** i Azure-portalen. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure portal](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Anslutningar från din aktuella IP-adress

För att förenkla utvecklingen, kan Azure-portalen du identifiera och lägga till IP-Adressen för klientdatorn i listan över tillåtna, så att appar som körs på datorn kan komma åt Azure Cosmos DB-kontot. Klientens IP-adress här identifieras som visas av portalen. Det kan vara klientens IP-adress på din dator, men det kan också vara IP-adressen för din gateway. Glöm inte att ta bort den innan du fortsätter till produktion.

Aktivera din aktuella IP-adress **Lägg till min nuvarande IP**, som lägger till din aktuella IP-adress i listan över IP-adresser och klicka sedan på **spara**.

![Skärmbild som visar hur konfigurera brandväggsinställningar för nuvarande IP-adress](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Anslutningar från cloud services
I Azure är molntjänster ett vanligt sätt som värd för tjänsten mellannivålogik med Azure Cosmos DB. Om du vill aktivera åtkomst till ett Azure Cosmos DB-databaskonto från en molnbaserad tjänst, offentliga IP-adressen för Molntjänsten måste läggas till i listan med IP-adresser som är associerade med ditt Azure Cosmos DB-databaskonto med tillåtna [konfigurera IP-åtkomst Kontrollera principen](#configure-ip-policy). Detta säkerställer att alla rollinstanser av molntjänster har åtkomst till ditt Azure Cosmos DB-databaskonto. Du kan hämta IP-adresser för dina molntjänster i Azure-portalen, enligt följande skärmbild:

![Skärmbild som visar offentliga IP-adress för en molntjänst som visas i Azure portal](./media/firewall-support/public-ip-addresses.png)

När du skalar ut din molntjänst genom att lägga till ytterligare roll instanser har dessa nya instanser automatiskt åtkomst till Azure Cosmos DB-databaskontot eftersom de ingår i samma molntjänst.

## <a name="connections-from-virtual-machines"></a>Anslutningar från virtuella datorer
[Virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [VM-skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) kan också användas som värd för mellannivå tjänster med hjälp av Azure Cosmos DB.  Om du vill konfigurera Azure Cosmos DB-databaskonto för att tillåta åtkomst från virtuella datorer, offentliga IP-adresser för virtuella datorer och/eller VM måste scale Sets konfigureras som en av de tillåtna IP-adresserna för ditt Azure Cosmos DB-databaskonto av [konfigurera principen för IP-åtkomstkontroll](#configure-ip-policy). Du kan hämta IP-adresser för virtuella datorer i Azure-portalen, enligt följande skärmbild.

![Skärmbild som visar en offentlig IP-adress för en virtuell dator visas i Azure portal](./media/firewall-support/public-ip-addresses-dns.png)

När du lägger till ytterligare VM-instanser i gruppen får de automatiskt tillgång till ditt Azure Cosmos DB-databaskonto.

## <a name="connections-from-the-internet"></a>Anslutningar från internet
När du använder ett Azure Cosmos DB-databaskonto från en dator på internet kan måste klientens IP-adress eller IP-adressintervall för datorn läggas till listan över tillåtna IP-adress för Azure Cosmos DB-databaskonto. 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Använder Azure Resource Manager-mall för att ställa in IP-åtkomstkontroll

Lägg till följande JSON till mallen för att konfigurera IP-åtkomstkontroll. Resource Manager-mall för ett konto har ipRangeFilter attribut som är lista med IP-adressintervall som ska vitlistas.

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
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>Felsökning av principen för IP-åtkomstkontroll
### <a name="portal-operations"></a>Portalåtgärder
Genom att aktivera en IP-principer för åtkomstkontroll för ditt Azure Cosmos DB-databaskonto tillåts all åtkomst till ditt Azure Cosmos DB-databaskonto från datorer som inte den konfigurerade listan över IP-adressintervall är blockerade. Därför om du vill aktivera portal data hanteringsplanets åtgärder som att bläddra behållare och köra frågor mot dokument måste du uttryckligen tillåta åtkomst i Azure portal med hjälp av den **brandväggen** i portalen. 

### <a name="sdk--rest-api"></a>SDK och Rest API
För säkerhetsskäl åtkomst via SDK eller REST-API från datorer inte på listan över tillåtna returnerar den ett allmänt 404 det gick inte att hitta svar utan ytterligare information. Kontrollera IP-Adressen konfigurerad för ditt Azure Cosmos DB-databaskonto listan över tillåtna för att säkerställa att rätt principkonfigurationen tillämpas på ditt Azure Cosmos DB-databaskonto.

## <a name="next-steps"></a>Nästa steg
Läs om hur nätverksrelaterade prestandatips [prestandatips](performance-tips.md).

