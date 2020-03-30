---
title: Åtkomst till virtuella Azure-nätverk
description: Översikt över hur integrationstjänstmiljöer (ISE) hjälper logikappar att komma åt virtuella Azure-nätverk (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127244"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Virtuella Azure-nätverksresurser från Azure Logic Apps med hjälp av integrationstjänstmiljöer (ISE)

Ibland behöver dina logikappar åtkomst till säkra resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten kan du [skapa en *integrationstjänstmiljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). En ISE är en isolerad instans av Logic Apps-tjänsten som använder dedikerade resurser och körs separat från den "globala" Logic Apps-tjänsten för flera innehavare.

Om du kör logikappar i din egen separata isolerade instans minskar effekten som andra Azure-klienter kan ha på dina appars prestanda, även känd som [effekten "bullriga grannar".](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) En ISE ger också dessa fördelar:

* Dina egna statiska IP-adresser, som är åtskilda från de statiska IP-adresser som delas av logikapparna i tjänsten för flera innehavare. Du kan också ställa in en enda offentlig, statisk och förutsägbar utgående IP-adress för att kommunicera med målsystem. På så sätt behöver du inte ställa in ytterligare brandväggsöppningar på dessa målsystem för varje ISE.

* Ökade gränser för körningstid, lagringskvarhållning, dataflöde, HTTP-begärande- och svarstidsgränser, meddelandestorlekar och anpassade anslutningsbegäranden. Mer information finns i [Gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Vissa virtuella Azure-nätverk använder privata slutpunkter ([Azure Private Link](../private-link/private-link-overview.md)) för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partnertjänster eller kundtjänster som finns på Azure. Om dina logikappar behöver åtkomst till virtuella nätverk som använder privata slutpunkter måste du skapa, distribuera och köra dessa logikappar i en ISE.

När du skapar en ISE injicerar eller *distribuerar* Azure som ISE i ditt virtuella Azure-nätverk. Du kan sedan använda den här ISE som plats för logikappar och integrationskonton som behöver åtkomst.

![Välj integrationstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Logikappar kan komma åt resurser som finns inuti eller är anslutna till det virtuella nätverket med hjälp av dessa objekt, som körs i samma ISE som dina logikappar:

* En **CORE-märkt**inbyggd utlösare eller åtgärd, till exempel HTTP-utlösaren eller åtgärden
* En **ISE-märkt**anslutning för det systemet eller tjänsten
* En anpassad anslutningsapp

Du kan fortfarande också använda kopplingar som inte har **CORE-** eller **ISE-etiketten** med logikapparna i ISE. Dessa kopplingar körs i logic apps-tjänsten för flera innehavare i stället. Mer information finns i följande avsnitt:

* [Isolerad kontra flera innehavare](#difference)
* [Ansluta från en integrationstjänstmiljö](../connectors/apis-list.md#integration-service-environment)
* [ISE-kopplingar](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Logikappar, inbyggda utlösare, inbyggda åtgärder och kopplingar som körs i ISE använder en prisplan som skiljer sig från den förbrukningsbaserade prisplanen. Mer information finns i [Logic Apps prismodell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prisinformation finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

I den här översikten beskrivs mer information om hur en ISE ger dina logikappar direkt åtkomst till ditt virtuella Azure-nätverk och jämför skillnaderna mellan en ISE- och Logic Apps-tjänsten för flera innehavare.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolerad kontra flera innehavare

När du skapar och kör logikappar i en ISE får du samma användarupplevelser och liknande funktioner som Logic Apps-tjänsten för flera innehavare. Du kan använda alla samma inbyggda utlösare, åtgärder och hanterade kopplingar som är tillgängliga i logic apps-tjänsten för flera innehavare. Vissa hanterade kopplingar erbjuder ytterligare ISE-versioner. Skillnaden mellan ISE-kopplingar och icke-ISE-kopplingar finns där de körs och de etiketter som de har i Logic App Designer när du arbetar i en ISE.

![Kopplingar med och utan etiketter i en ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Inbyggda utlösare och åtgärder **CORE** visar CORE-etiketten. De körs alltid i samma ISE som logikappen. Hanterade kopplingar som visar **ISE-etiketten** körs också i samma ISE som logikappen.

  Här är till exempel några kopplingar som erbjuder ISE-versioner:

  * Azure Blob Storage, fillagring och tabelllagring
  * Azure-köer, Azure Service Bus, Azure Event Hubs och IBM MQ
  * FTP och SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 och EDIFACT

* Hanterade kopplingar som inte visar några ytterligare etiketter körs alltid i Logic Apps-tjänsten med flera innehavare, men du kan fortfarande använda dessa kopplingar i en logikapp med ISE-värd.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Tillgång till lokala system

Om du vill komma åt lokala system eller datakällor som är anslutna till ett virtuellt Azure-nätverk kan logikappar i en ISE använda följande objekt:

* HTTP-åtgärd

* ISE-märkt kontakt för det systemet

  > [!NOTE]
  > Om du vill använda Windows-autentisering med SQL Server-anslutningen i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder du kopplingens icke-ISE-version med den [lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md). Den ISE-märkta versionen stöder inte Windows-autentisering.

* Anpassad anslutningsapp

  * Om du har anpassade kopplingar som kräver den lokala datagatewayen och du har skapat dessa kopplingar utanför en ISE, kan logikappar i en ISE också använda dessa kopplingar.

  * Anpassade kopplingar som skapas i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock komma åt lokala datakällor som är anslutna till det virtuella nätverket som är värd för ISE. Logikappar i en ISE behöver därför troligen inte datagatewayen när du kommunicerar med dessa resurser.

För lokala system som inte är anslutna till ett virtuellt nätverk eller inte har ISE-märkta kopplingar måste du först [konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) innan logikapparna kan ansluta till dessa system.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU:er

När du skapar ISE kan du välja Developer SKU eller Premium SKU. Här är skillnaderna mellan dessa SKU:

* **Developer**

  Ger en ISE med lägre kostnad som du kan använda för experiment, utveckling och testning, men inte för produktions- eller prestandatestning. Developer SKU innehåller inbyggda utlösare och åtgärder, standardkopplingar, Enterprise-kopplingar och ett enda konto för integrering [på den kostnadsfria nivån](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) för ett fast månadspris. Den här SKU:n innehåller dock inget servicenivåavtal (SLA), alternativ för att skala upp kapacitet eller redundans under återvinning, vilket innebär att du kan uppleva förseningar eller driftstopp.

* **Premium**

  Tillhandahåller en ISE som du kan använda för produktion och inkluderar SLA-stöd, inbyggda utlösare [Standard tier](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) och åtgärder, standardkopplingar, Enterprise-kopplingar, ett enda standardnivåintegrationskonto, alternativ för att skala upp kapacitet och redundans under återvinning till ett fast månadspris.

> [!IMPORTANT]
> SKU-alternativet är endast tillgängligt när ISE skapas och kan inte ändras senare.

Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Mer information om hur prissättning och fakturering fungerar för ISE finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-slutpunktsåtkomst

När du skapar ISE kan du välja att använda antingen interna eller externa åtkomstslutpunkter. Ditt val avgör om begäran eller webhook-utlösare på logikappar i DIN ISE kan ta emot samtal utanför det virtuella nätverket.

Dessa slutpunkter påverkar också hur du kan komma åt indata och utdata i logikapparnas körhistorik.

* **Internt**: Privata slutpunkter som tillåter anrop till logikappar i din ISE där du kan visa och komma åt logikapparnas indata och utgångar i körningshistoriken *endast inifrån det virtuella nätverket*

* **Extern**: Offentliga slutpunkter som tillåter anrop till logikappar i din ISE där du kan visa och komma åt logikapparnas indata och utgångar i körningshistorik *utanför det virtuella nätverket*. Om du använder NSG-grupper (Network Security Groups) kontrollerar du att de är konfigurerade med inkommande regler för att tillåta åtkomst till körningshistorikens indata och utdata. Mer information finns i [Aktivera åtkomst för ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Alternativet åtkomstslutpunkt är endast tillgängligt när ISE skapas och kan inte ändras senare.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrationskonton med ISE

Du kan använda integrationskonton med logikappar i en integrationstjänstmiljö (ISE). Dessa integrationskonton måste dock använda *samma ISE* som de länkade logikapparna. Logikappar i en ISE kan bara referera till de integrationskonton som finns i samma ISE. När du skapar ett integrationskonto kan du välja din ISE som plats för ditt integrationskonto. Mer information om hur prissättning och fakturering fungerar för integrationskonton med en ISE finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prispriser finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
