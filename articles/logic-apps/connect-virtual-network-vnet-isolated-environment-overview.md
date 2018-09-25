---
title: Åtkomst till Azure virtual Network (VNET) från Azure Logic Apps
description: Den här översikten visar hur isolerade logic apps kan ansluta till Azure-nätverk (Vnet) från integreringstjänstmiljöer (ISEs) som använder privata och dedikerade resurser
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b012f1ac9c5f08a7e74871ca215299904f6b1deb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958653"
---
# <a name="access-to-azure-virtual-network-vnet-resources-from-isolated-azure-logic-apps"></a>Åtkomst till resurser i Azure Virtual Network (VNET) från isolerade Azure Logic Apps

> [!NOTE]
> Den här funktionen är i *privat förhandsgranskning*. Att begära åtkomst, [skapa din begäran om att ansluta till här](https://aka.ms/iseprivatepreview).

Ibland kan dina logic apps och integrationskonton behöver åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system och tjänster i en [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). För att ge den här åtkomsten, kan du [skapa en *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) som plats för att skapa dina logic apps och integrationskonton. 

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Skapa en ISE distribuerar en privata och isolerade Logic Apps-instans i ditt virtuella nätverk. Den privata instansen använder dedikerade resurser, till exempel lagring och körs avskilt från offentliga ”global” Logic Apps-tjänsten. Den här separationen hjälper också till att minska effekten som andra Azure-klienter kan ha på din apps prestanda eller [”bort störande grannar” effekt](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). 

Den här översikten beskriver hur skapar en ISE hjälper logikappar och integrationskonton direkt åtkomst till resurser i det virtuella Azure-nätverket och jämför skillnaderna mellan en ISE och globala Logic Apps-tjänsten.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolerade jämfört med globala

När du skapar en integrerad tjänst-miljö (ISE) i Azure kan du välja ett virtuellt Azure-nätverk som en *peer* för din miljö. Azure har distribuerat en privat instans av Logic Apps-tjänsten till ditt VNET, vilket resulterar i en isolerad miljö där du kan skapa och köra dina logic apps på dedikerade resurser. När du skapar en logikapp, väljer du den här miljön som din Apps plats, som också ger dina logic app direkt åtkomst till resurserna i ditt virtuella nätverk.  

Logic apps i en ISE kan du ange samma användarupplevelser och liknande funktioner som tjänsten för global Logic Apps. Inte bara kan du använda samma built-ins och kopplingar som tillhandahålls av tjänsten för global Logic Apps, men du kan välja från kopplingar som ger ISE-versioner. Här är till exempel vissa Standardanslutningar som erbjuder versioner som körs i en ISE:
 
* Azure Blob Storage, File Storage och Table Storage
* Azure Queues
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2-, X 12 och EDIFACT

Skillnaden mellan ISE och icke-ISE-anslutningar är i de platser där utlösare och åtgärder körs:

* Om du använder inbyggda utlösare och åtgärder, till exempel HTTP i din ISE körs dessa utlösare och åtgärder alltid i samma ISE som din logikapp. 

* För kopplingar som erbjuder två versioner: en version som körs i en ISE medan den andra versionen som körs i tjänsten för global Logic Apps.  

  Kopplingar som har den **ISE** etiketten alltid köras i samma ISE som din logikapp. Anslutningar utan den **ISE** etikett som körs i tjänsten för global Logic Apps. 

  ![Välj ISE-tjänster](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* Kopplingar som du konfigurerar i din ISE är också tillgängliga för användning i Logic Apps-tjänsten för global. 

> [!IMPORTANT]
> Logic apps, inbyggda åtgärder och kopplingar som körs i din ISE kan du använda en annan prisplanen inte förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md).

<a name="vnet-access"></a>

## <a name="permissions-for-vnet-access"></a>Behörigheter för VNET-åtkomst

När du skapar en integration service-miljö (ISE) kan du välja ett Azure-nätverk (VNET) som en *peer* för din miljö. Du kan dock *endast* skapa den här relationen eller *peering*, när du skapar din ISE. Den här relationen ger ISE-åtkomst till resurser i ditt VNET, vilket gör att logikappar i att ISE ansluta direkt till resurser i ditt virtuella nätverk. För lokala system i ett virtuellt nätverk som är länkad till en ISE logikappar direkt åtkomst till dessa system med någon av dessa objekt: 

* ISE-anslutning för systemet, till exempel SQL Server

* HTTP-åtgärd 

* Anpassad anslutningsapp

För den lokala system som inte är i ett virtuellt nätverk eller så har inte ISE kopplingar, kan du fortfarande ansluta när du [konfigurera och använda den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md).

Innan du kan välja ett virtuellt Azure nätverk som peer för din miljö, måste du konfigurera behörigheter för rollbaserad åtkomstkontroll (RBAC) i ditt virtuella Azure-nätverk för Azure Logic Apps-tjänsten. Den här uppgiften kräver att du tilldelar den **Nätverksdeltagare** och **klassiska deltagare** roller till Azure Logic Apps-tjänsten. Mer information om de rollbehörigheterna som krävs för peering finns i den [behörigheter avsnittet i Skapa, ändra eller ta bort en virtuell nätverkspeering](../virtual-network/virtual-network-manage-peering.md#permissions).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonton med ISE

Du kan använda konton för integrering med logic apps som körs i en integreringstjänstmiljö (ISE), men dessa integrationskonton måste också använda den *samma ISE* som länkade logic apps. Logic apps i en ISE kan referera till dessa integrationskonton som är i samma ISE. När du skapar ett integrationskonto kan du välja din ISE som platsen för ditt integrationskonto.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forumet för Azure Logic Apps</a>.
* Om du vill skicka in eller rösta på förslag på funktioner besöker du <a href="http://aka.ms/logicapps-wish" target="_blank">webbplatsen för Logic Apps-användarfeedback</a>.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluta till virtuella nätverk (Vnet) från isolerade logikappar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
