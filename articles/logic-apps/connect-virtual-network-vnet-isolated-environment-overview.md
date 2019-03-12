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
ms.date: 03/11/2019
ms.openlocfilehash: b53cd54afdf6243769602971ab77145cfa9ba9cc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57758785"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integreringstjänstmiljöer (ISEs)

> [!NOTE]
> Den här funktionen är i [ *förhandsversion*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ibland kan dina logic apps och integrationskonton behöver åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system och tjänster, i en [Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten, kan du [skapa en *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) för att köra dina logic apps och integrationskonton. När du skapar en ISE Azure har distribuerat en privat och isolerad instans av Logic Apps-tjänsten i Azure-nätverk. Den här privata instansen använder dedikerade resurser, till exempel lagring och körs avskilt från offentliga ”global” Logic Apps-tjänsten. Att ange din isolerade privata och offentliga globala instansen också bidrar till att minska effekten som andra Azure-klienter kan ha på din apps prestanda, vilket även kallas den [”bort störande grannar” effekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

När du har skapat din ISE när du går till att skapa ett logic app eller varje konto, kan du välja din ISE som logic app eller integrering kontots plats:

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Din logikapp kan nu komma åt direkt system som finns i eller anslutet till det virtuella nätverket med någon av dessa objekt:

* En ISE versionshanterat koppling för systemet, till exempel SQL Server
* En inbyggd utlösaren eller åtgärden, till exempel HTTP-utlösaren eller åtgärden
* En anpassad anslutningsapp

Den här översikten innehåller mer information om hur en ISE ger dina logic apps och integrering konton direkt åtkomst till Azure-nätverk och jämför skillnaderna mellan en ISE och globala Logic Apps-tjänsten.
För lokala system som inte är ansluten till ett virtuellt nätverk eller inte har ISE-version anslutningsappar, som du kan ansluta till de system som skapats av [ställa in och använda den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md).

> [!IMPORTANT]
> Logic apps, inbyggda åtgärder och kopplingar som körs i din ISE kan du använda en annan prisplanen inte förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolerade jämfört med globala

När du skapar en integrerad tjänst-miljö (ISE) i Azure kan du välja Azure-nätverket där du vill *mata in* din ISE. Azure lägger in eller distribuerar en privat instans av Logic Apps-tjänsten till ditt virtuella nätverk. Den här åtgärden skapar en isolerad miljö där du kan skapa och köra dina logic apps på dedikerade resurser. När du skapar din logikapp kan välja du din ISE som din Apps plats, som ger dina logic app direktåtkomst till det virtuella nätverket och resurser i nätverket.

Logic apps i en ISE kan du ange samma användarupplevelser och liknande funktioner som tjänsten för global Logic Apps. Inte bara kan du använda samma inbyggda åtgärder och anslutningsprogram i tjänsten för global Logic Apps, men du kan också använda ISE-specifika anslutningsappar. Här är till exempel vissa Standardanslutningar som erbjuder versioner som körs i en ISE:

* Azure Blob Storage, File Storage och Table Storage
* Azure-köer, Azure Service Bus, Azure Event Hubs och IBM MQ
* FTP- och SFTP-SSH
* SQL Server, SQL Data Warehouse, Azure Cosmos DB
* AS2-, X 12 och EDIFACT

Skillnaden mellan ISE och icke-ISE-anslutningar är i de platser där utlösare och åtgärder körs:

* I din ISE inbyggda utlösare och åtgärder, till exempel HTTP körs alltid i samma ISE som din logikapp.

* För anslutningsappar som erbjuder två versioner, en version som körs i en ISE medan den andra versionen som körs i tjänsten för global Logic Apps.  

  Kopplingar som har den **ISE** etiketten alltid köras i samma ISE som din logikapp. Anslutningar utan den **ISE** etikett som körs i tjänsten för global Logic Apps.

  ![Välj ISE-tjänster](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Kopplingar som körs i en ISE är också tillgängliga i tjänsten för global Logic Apps.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonton med ISE

Du kan använda integrationskonton med logic apps i en integreringstjänstmiljö (ISE). Dessa konton för logikappsintegration måste emellertid använda den *samma ISE* som länkade logic apps. Logic apps i en ISE kan referera till dessa integrationskonton som är i samma ISE. När du skapar ett integrationskonto kan du välja din ISE som platsen för ditt integrationskonto.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forumet för Azure Logic Apps</a>.
* Om du vill skicka in eller rösta på förslag på funktioner besöker du <a href="https://aka.ms/logicapps-wish" target="_blank">webbplatsen för Logic Apps-användarfeedback</a>.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluta till virtuella Azure-nätverk från isolerade logikappar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
