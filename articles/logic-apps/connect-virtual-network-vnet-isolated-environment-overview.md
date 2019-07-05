---
title: Åtkomst till Azure-nätverk från Azure Logic Apps med integration service-miljöer (ISEs)
description: Den här översikten beskriver hur integreringstjänstmiljöer (ISEs) hjälper logikappar att få åtkomst till Azure-nätverk (Vnet)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: f981452b06ec06f2be1b8fe0319cd49a678ccfe0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441586"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integreringstjänstmiljöer (ISEs)

Ibland kan dina logic apps och integrationskonton behöver åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system och tjänster som är i ett [Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten, kan du [skapa en *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) där du kan köra dina logic apps och skapa din integrering konton.

När du skapar en ISE Azure har distribuerat en privat och isolerad instans av Logic Apps-tjänsten i Azure-nätverk. Den här privata instansen använder dedikerade resurser, till exempel lagring och körs avskilt från offentliga ”global” Logic Apps-tjänsten. Att ange din isolerade privata och offentliga globala instansen också bidrar till att minska effekten som andra Azure-klienter kan ha på din apps prestanda, vilket även kallas den [”bort störande grannar” effekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

När du har skapat din ISE när du går till att skapa ett logic app eller varje konto, kan du välja din ISE som logic app eller integrering kontots plats:

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Din logikapp kan nu komma åt direkt system som finns i eller anslutet till det virtuella nätverket med någon av dessa objekt:

* En **ISE**-märkta connector för systemet, till exempel SQL Server
* En **Core**-märkta inbyggda utlösaren eller åtgärden, till exempel HTTP-utlösaren eller åtgärden
* En anpassad anslutningsapp

Den här översikten innehåller mer information om hur en ISE ger dina logic apps och integrering konton direkt åtkomst till Azure-nätverk och jämför skillnaderna mellan en ISE och globala Logic Apps-tjänsten.

> [!NOTE]
> Logic apps, inbyggda utlösare, inbyggda åtgärder och kopplingar som körs i ISE-användning av en prisplanen skiljer sig från förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md). Din ISE har också ökat gränser på varaktighet för körning, kvarhållning, dataflöde, HTTP-begäran och svaret tidsgränser, meddelandestorlek och anpassad anslutningsapp begäranden. Mer information finns i [gränser och konfigurering för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolerade jämfört med globala

När du skapar en integrerad tjänst-miljö (ISE) i Azure kan du välja Azure-nätverket där du vill *mata in* din ISE. Azure lägger in eller distribuerar en privat instans av Logic Apps-tjänsten till ditt virtuella nätverk. Den här åtgärden skapar en isolerad miljö där du kan skapa och köra dina logic apps på dedikerade resurser. När du skapar din logikapp kan välja du din ISE som din Apps plats, som ger dina logic app direktåtkomst till det virtuella nätverket och resurser i nätverket.

Logic apps i en ISE kan du ange samma användarupplevelser och liknande funktioner som tjänsten för global Logic Apps. Inte bara kan du använda samma inbyggda utlösare, inbyggda åtgärder och anslutningar från tjänsten för global Logic Apps, men du kan också använda ISE-specifika anslutningsappar. Här är till exempel vissa Standardanslutningar som erbjuder versioner som körs i en ISE:

* Azure Blob Storage, File Storage och Table Storage
* Azure-köer, Azure Service Bus, Azure Event Hubs och IBM MQ
* FTP- och SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2-, X 12 och EDIFACT

Skillnaden mellan ISE och icke-ISE-anslutningar är i de platser där utlösare och åtgärder körs:

* I din ISE inbyggda utlösare och åtgärder, till exempel HTTP, kör alltid i samma ISE som din logikapp och visa den **Core** etikett.

  ![Välj ”grundläggande” inbyggda utlösare och åtgärder](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Kopplingar som körs i en ISE har offentligt värdbaserad versioner som är tillgängliga i tjänsten för global Logic Apps. För kopplingar som erbjuder två versioner: kopplingar med den **ISE** etiketten alltid köras i samma ISE som din logikapp. Anslutningar utan den **ISE** etikett som körs i tjänsten för global Logic Apps.

  ![Välj ISE-tjänster](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

En ISE ger också ökade gränser för varaktighet för körning, kvarhållning, dataflöde, HTTP-begäran och svaret tidsgränser, meddelandestorlek och anpassad anslutningsapp begäranden. Mer information finns i [gränser och konfigurering för Azure Logic Apps](logic-apps-limits-and-config.md).

### <a name="access-to-on-premises-data-sources"></a>Åtkomst till lokala datakällor

För lokala system som är anslutna till ett Azure-nätverk, att mata in en ISE i nätverket så att dina logikappar har direkt åtkomst dessa system med någon av dessa objekt:

* ISE-version connector för systemet, till exempel SQL Server
* HTTP-åtgärd
* Anpassad anslutningsapp

  * Om du har anpassade anslutningar som kräver en lokal datagateway och du har skapat dessa anslutningar utanför en ISE, kan logic apps i en ISE också använda dessa anslutningar.
  
  * Anpassade anslutningsappar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt åtkomst till lokala datakällor som är anslutna till det virtuella nätverket som är värd för ISE. Därför behöver logic apps i en ISE troligen inte datagateway vid kommunikation med dessa resurser.

För lokala system som inte är ansluten till ett virtuellt nätverk eller inte har anslutningsappar för ISE-version, måste du först [konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) innan dina logic apps kan ansluta till dessa system.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonton med ISE

Du kan använda integrationskonton med logic apps i en integreringstjänstmiljö (ISE). Dessa konton för logikappsintegration måste emellertid använda den *samma ISE* som länkade logic apps. Logic apps i en ISE kan referera till dessa integrationskonton som är i samma ISE. När du skapar ett integrationskonto kan du välja din ISE som platsen för ditt integrationskonto.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluta till virtuella Azure-nätverk från isolerade logikappar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
