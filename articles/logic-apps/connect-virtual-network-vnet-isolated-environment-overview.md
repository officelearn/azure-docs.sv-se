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
ms.date: 12/03/2018
ms.openlocfilehash: 5046b1012e0074e9548cad050c16eef25c00cee0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845203"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integreringstjänstmiljöer (ISEs)

> [!NOTE]
> Den här funktionen är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview).

Ibland kan dina logic apps och integrationskonton behöver åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system och tjänster, i en [Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten, kan du [skapa en *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) för att köra dina logic apps och integrationskonton. 

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Skapa en ISE distribuerar en privata och isolerade Logic Apps-instans i Azure-nätverk. Den här privata instansen använder dedikerade resurser, till exempel lagring och körs avskilt från offentliga ”global” Logic Apps-tjänsten. Den här separationen hjälper också till att minska effekten som andra Azure-klienter kan ha på din apps prestanda eller [”bort störande grannar” effekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Den här översikten beskriver hur en ISE ger dina logic apps och integrationskonton direkt åtkomst till din Azure-nätverk och jämför skillnaderna mellan en ISE och globala Logic Apps-tjänsten.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolerade jämfört med globala

När du skapar en integrerad tjänst-miljö (ISE) i Azure kan du välja ett Azure-nätverk där din *mata in* miljö. Azure har distribuerat en privat instans av Logic Apps-tjänsten till ditt virtuella nätverk. Den här åtgärden skapar en isolerad miljö där du kan skapa och köra dina logic apps på dedikerade resurser. När du skapar en logikapp, väljer du den här miljön som din Apps plats, som ger dina logic app direkt åtkomst till resurser i det virtuella nätverket. 

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

> [!IMPORTANT]
> Logic apps, inbyggda åtgärder och kopplingar som körs i din ISE kan du använda en annan prisplanen inte förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>Behörigheter för åtkomst till virtuellt nätverk

När du skapar en integration service-miljö (ISE) kan du välja ett Azure-nätverk var du *mata in* din miljö. Inmatning distribuerar en privat instans av Logic Apps-tjänsten till ditt virtuella nätverk. Den här åtgärden resulterar i en isolerad miljö där du kan skapa och köra dina logic apps på dedikerade resurser. När du skapar väljer dina logikappar din ISE som plats för dina appar. Dessa logic apps kan sedan direkt åtkomst till ditt virtuella nätverk och ansluta till resurser i nätverket. 

För system som är anslutna till ett virtuellt nätverk, kan du mata in en ISE i det virtuella nätverket så att dina logikappar har direkt åtkomst dessa system med någon av dessa objekt: 

* ISE-anslutning för systemet, till exempel SQL Server

* HTTP-åtgärd 

* Anpassad anslutningsapp

För lokala system som inte är ansluten till ett virtuellt nätverk eller inte har ISE-anslutningsappar, som du kan ansluta till de system som skapats av [ställa in och använda den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md).

Innan du kan välja ett Azure-nätverk för din miljö måste ställa du in behörigheter för rollbaserad åtkomstkontroll (RBAC) i det virtuella nätverket för Azure Logic Apps-tjänsten. Den här uppgiften kräver att du tilldelar den **Nätverksdeltagare** och **klassiska deltagare** roller till Azure Logic Apps-tjänsten.
Om du vill konfigurera dessa behörigheter finns i [Anslut till Azure-nätverk från logikappar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)

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
