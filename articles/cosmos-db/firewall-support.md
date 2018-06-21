---
title: Åtkomstkontroll för Azure DB Cosmos-brandväggsstöd & IP | Microsoft Docs
description: Lär dig hur du använder IP-principer för åtkomstkontroll för brandväggsstöd för på Azure Cosmos DB databasen konton.
keywords: IP-åtkomstkontroll, brandväggsstöd
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: 0407d3c58fa63a11c8391f069039f7c35a15ceb7
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294745"
---
# <a name="azure-cosmos-db-firewall-support"></a>Azure DB Cosmos-brandväggsstöd
Om du vill skydda data som lagras i Azure DB som Cosmos-databaskonto har Azure Cosmos DB fanns stöd för en hemlighet baserat [auktoriseringsmodellen](https://msdn.microsoft.com/library/azure/dn783368.aspx) som använder en stark hashbaserad meddelandeautentiseringskod (HMAC). Förutom den hemliga baserat auktoriseringsmodellen stöder nu Azure Cosmos DB drivs IP-baserade åtkomstkontroller för inkommande brandväggsstöd-princip. Den här modellen liknar brandväggsregler vid traditionella system och ger en extra nivå av säkerhet till databaskontot Azure Cosmos DB. Med den här modellen kan du nu konfigurera en Azure Cosmos DB konto om du vill att endast nås från en godkänd uppsättning datorer och/eller molntjänster. Åtkomst till Azure Cosmos DB resurser från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande anroparen presentera en giltig auktoriserings-token.

> [!NOTE]
> Brandväggsstöd är för närvarande tillgänglig för Azure SQL DB-API Cosmos-och Mongo-API. Möjligheten att konfigurera brandväggar för andra API: er och suveräna moln, till exempel Azure Tyskland eller Azure Government blir snart tillgänglig. Om du planerar att konfigurera tjänsten slutpunkts-ACL för ditt Azure DB som Cosmos-konto som har en befintlig IP-brandvägg konfigurerad Observera brandväggskonfigurationen, ta bort IP-brandväggen och konfigurera tjänstslutpunkten ACL. När du har konfigurerat tjänstslutpunkten aktivera du IP-brandväggen igen om det behövs.

## <a name="ip-access-control-overview"></a>IP-åtkomstkontroll: översikt
Ett databaskonto för Azure Cosmos DB är som standard tillgängligt från det offentliga Internet så länge begäran åtföljs av en giltig auktoriseringstoken. Principbaserad IP-åtkomstkontroll kan konfigureras genom att användaren anger en uppsättning IP-adresser eller IP-adressintervall i CIDR-form som ska ingå i listan med tillåtna klient-IP-adresser för ett visst databaskonto. När den här konfigurationen tillämpas blockerar servern alla begäranden från datorer som inte finns med på den här listan.  Anslutningen processchema för IP-baserad åtkomstkontroll beskrivs i följande diagram:

![Diagram över anslutningsprocessen för IP-baserad åtkomstkontroll](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurera de IP-principer för åtkomstkontroll
De IP-principer för åtkomstkontroll kan anges i Azure-portalen eller programmässigt via [Azure CLI](cli-samples.md), [Azure Powershell](powershell-samples.md), eller [REST API](/rest/api/cosmos-db/) genom att uppdatera **ipRangeFilter** egenskapen. 

Om du vill ange de IP-principer för åtkomstkontroll i Azure portal, gå till sidan Azure Cosmos DB konto klickar du på **brandvägg och virtuella nätverk** i navigeringsmenyn, ändra den **tillåta åtkomst från** värde att **markerat nätverk**, och klicka sedan på **spara**. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure-portalen](./media/firewall-support/azure-portal-firewall.png)

När IP-åtkomstkontroll på möjligheten portalen att ange IP-adresser och intervall samt växlar för att ge åtkomst till andra Azure-tjänster och Azure portal. Ytterligare information om de växlarna finns i följande avsnitt.

> [!NOTE]
> Genom att aktivera en IP-principer för åtkomstkontroll för din Azure Cosmos DB databaskonto tillåtna all åtkomst till din Azure Cosmos DB databaskonto från datorer utanför den konfigurerade listan över IP-adressintervall blockeras. Tack vare den här modellen blockeras surfning plan för åtgärden från portalen också för att kontrollera integriteten för åtkomstkontroll.

## <a name="connections-from-the-azure-portal"></a>Anslutningar från Azure-portalen 

När du aktiverar en IP-principer för åtkomstkontroll programmässigt du behöver lägga till IP-adressen för Azure-portalen till den **ipRangeFilter** egenskapen att upprätthålla åtkomsten. Portalen IP-adresser är:

|Region|IP-adress|
|------|----------|
|Alla regioner utom de som anges nedan|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Tyskland|51.4.229.218|
|Kina|139.217.8.252|
|USA-förvaltad region|52.244.48.71|

Åtkomst till Azure portal är aktiverad som standard när du ändrar inställningen för brandväggen att **markerat nätverk** i Azure-portalen. 

![Skärmbild som visar hur du aktiverar Azure portalåtkomst](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-public-azure-datacenters-or-azure-paas-services"></a>Anslutningar från offentliga Azure-Datacenter eller Azure PaaS-tjänster
I Azure används PaaS-tjänster som Azure Stream analytics, Azure Functions och Azure App Service tillsammans med Azure Cosmos DB. Lägga till IP-adress 0.0.0.0 i listan över tillåtna IP-adresser som är associerade med din Azure Cosmos DB databaskonto programmässigt för att aktivera åtkomst till Azure Cosmos DB databaskonto från tjänsterna vars IP-adresser inte är tillgänglig. 

Åtkomst till anslutningar från inom offentliga Azure-Datacenter är aktiverad som standard när du ändrar inställningen för brandväggen att **markerat nätverk** i Azure-portalen. 

![Skärmbild som visar hur du öppnar sidan brandväggen i Azure-portalen](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Anslutningar från din aktuella IP

För att förenkla utvecklingen hjälper Azure-portalen dig att identifiera och lägga till IP-Adressen för klientdatorn i listan över tillåtna, så att appar som körs på datorn kan komma åt Azure Cosmos DB-konto. Klientens IP-adress här har identifierats som visas av portalen. Det kan vara klientens IP-adress för datorn, men det kan också vara IP-adressen för din nätverks-gateway. Glöm inte att ta bort det innan du fortsätter till produktionen.

Välj för att aktivera din aktuella IP **lägga till min aktuella IP**, som lägger till din aktuella IP i listan över IP-adresser och klicka sedan på **spara**.

![Skärmbild som visar en så att konfigurera brandväggsinställningar för aktuella IP](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Anslutningar från molntjänster
I Azure är cloud services ett vanligt sätt som värd för tjänsten mellannivålogik med hjälp av Azure Cosmos DB. Om du vill aktivera åtkomst till ett konto för Azure DB som Cosmos-databasen från en molnbaserad tjänst, offentliga IP-adressen för Molntjänsten måste läggas till listan över tillåtna IP-adresser som är associerade med din Azure Cosmos DB databaskonto av [konfigurera IP-åtkomst Kontrollera principen](#configure-ip-policy). Detta säkerställer att alla rollinstanser av molntjänster har åtkomst till ditt konto för Azure DB som Cosmos-databasen. Du kan hämta IP-adresserna för dina molntjänster i Azure-portalen som visas i följande skärmbild:

![Skärmbild som visar den offentliga IP-adressen för en molnbaserad tjänst som visas i Azure-portalen](./media/firewall-support/public-ip-addresses.png)

När du skalar upp din molntjänst genom att lägga till ytterligare rollen instanser har de nya instanserna automatiskt åtkomst till Azure Cosmos DB databaskontot eftersom de ingår i samma molntjänst.

## <a name="connections-from-virtual-machines"></a>Anslutningar från virtuella datorer
[Virtuella datorer](https://azure.microsoft.com/services/virtual-machines/) eller [skalningsuppsättningar i virtuella](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) kan också användas som värd för mellannivån tjänster med hjälp av Azure Cosmos DB.  Om du vill konfigurera Azure DB som Cosmos-databaskonto för att tillåta åtkomst från virtuella datorer, offentliga IP-adresser för virtuell dator och/eller virtuella måste skaluppsättning konfigureras som en av de tillåtna IP-adresserna för dina Azure Cosmos DB databaskonto genom [konfigurera de IP-principer för åtkomstkontroll](#configure-ip-policy). Du kan hämta IP-adresser för virtuella datorer i Azure-portalen som visas i följande skärmbild.

![Skärmbild som visar en offentlig IP-adress för en virtuell dator visas i Azure-portalen](./media/firewall-support/public-ip-addresses-dns.png)

När du lägger till ytterligare virtuella datorinstanser i gruppen finns de automatiskt åtkomst till ditt konto för Azure DB som Cosmos-databasen.

## <a name="connections-from-the-internet"></a>Anslutningar från internet
När du använder ett konto för Azure Cosmos-DB-databas från en dator på internet kan läggas klientens IP-adress eller IP-adressintervall för datorn till listan över tillåtna IP-adress för databaskontot Azure Cosmos DB. 

## <a name="troubleshooting-the-ip-access-control-policy"></a>Felsökning av de IP-principer för åtkomstkontroll
### <a name="portal-operations"></a>Portalen åtgärder
Genom att aktivera en IP-principer för åtkomstkontroll för din Azure Cosmos DB databaskonto tillåtna all åtkomst till din Azure Cosmos DB databaskonto från datorer utanför den konfigurerade listan över IP-adressintervall blockeras. Om du vill aktivera portal plan dataåtgärder som surfning samlingar och fråga dokument måste du därför uttryckligen tillåta Azure portal med hjälp av den **brandväggen** sida i portalen. 

### <a name="sdk--rest-api"></a>SDK & Rest API
För säkerhetsskäl åtkomst via SDK eller REST-API från datorer som inte finns på listan över tillåtna returneras ett allmänt 404 gick inte att hitta svar utan ytterligare information. Kontrollera IP-Adressen tillåten lista som konfigurerats för ditt konto för Azure DB som Cosmos-databasen för att säkerställa rätt principkonfigurationen tillämpas på ditt konto för Azure DB som Cosmos-databasen.

## <a name="next-steps"></a>Nästa steg
Information om nätverksrelaterade prestandatips finns [prestandatips](performance-tips.md).

