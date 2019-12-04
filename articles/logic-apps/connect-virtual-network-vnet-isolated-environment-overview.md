---
title: Åtkomst till virtuella Azure-nätverk
description: Översikt över hur integrerings tjänst miljöer (ISEs) hjälper Logic Apps åtkomst till Azure Virtual Networks (virtuella nätverk)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: 9c4dca6dc5def1b1c458f28aa2d3ab992bd705d2
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792727"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integrerings tjänst miljöer (ISEs)

Ibland behöver dina Logic Apps-och integrations konton åtkomst till skyddade resurser, till exempel virtuella datorer och andra system eller tjänster, som finns i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten kan du [skapa en *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) där du kan köra dina Logi Kap par och skapa integrations konton.

När du skapar en ISE *infogar* Azure den ISE i ditt virtuella Azure-nätverk, som sedan distribuerar en privat och isolerad instans av Logic Apps tjänsten till ditt virtuella Azure-nätverk. Den här privata instansen använder dedikerade resurser, till exempel lagring, och körs separat från den offentliga "globala Logic Apps-tjänsten. Genom att avgränsa den isolerade privata instansen och den offentliga globala instansen kan du också minska den påverkan som andra Azure-klienter kan ha på dina appars prestanda, vilket även kallas för ["störningar på grannar"-effekten](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors).

När du har skapat din ISE kan du, när du går för att skapa din Logic app eller integrations konto, välja din ISE som din Logic app eller integrations kontots plats:

![Välj integrerings tjänst miljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Din Logi Kap par kan nu direkt komma åt system som är inuti eller anslutna till ditt virtuella nätverk genom att använda något av följande objekt:

* En **ISE**-märkt koppling för systemet
* En **Core**-märkt inbyggd utlösare eller åtgärd, till exempel http-utlösare eller åtgärd
* En anpassad anslutning

I den här översikten beskrivs mer information om hur en ISE ger dina Logic Apps och integrations konton direkt åtkomst till ditt virtuella Azure-nätverk och jämför skillnader mellan en ISE och den globala Logic Appss tjänsten.

> [!IMPORTANT]
> Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en pris sättnings plan som skiljer sig från den förbruknings pris planen. Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).
>
> Din ISE har också ökat gränserna för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-begäran och svar, meddelande storlekar och anpassade anslutnings begär Anden. 
> Mer information finns i [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Isolerade kontra globala

När du skapar en integrerad service Environment (ISE) i Azure kan du välja det virtuella Azure-nätverket där du vill *mata* in din ISE. Azure matar sedan in eller distribuerar en privat instans av tjänsten Logic Apps till ditt virtuella nätverk. Den här åtgärden skapar en isolerad miljö där du kan skapa och köra Logi Kap par på dedikerade resurser. När du skapar din Logic app väljer du din ISE som appens plats, vilket ger din Logi Kap appen direkt åtkomst till ditt virtuella nätverk och resurserna i det nätverket.

Logic Apps i en ISE ger samma användar upplevelse och liknande funktioner som den globala Logic Appss tjänsten. Du kan inte bara använda samma inbyggda utlösare, inbyggda åtgärder och anslutningar från den globala Logic Appss tjänsten, men du kan också använda ISE-/regionsspecifika anslutningar. Här är till exempel några standard anslutningar som erbjuder versioner som körs i en ISE:

* Azure Blob Storage, File Storage och Table Storage
* Azure-köer, Azure Service Bus, Azure Event Hubs och IBM MQ
* Fil system, FTP och SFTP – SSH
* SQL Server Azure SQL Data Warehouse Azure Cosmos DB
* AS2, X12 och EDIFACT

Skillnaden mellan ISE-och icke-ISE-anslutningar är på platser där utlösare och åtgärder körs:

* I din ISE har inbyggda utlösare och åtgärder, till exempel HTTP, alltid körs i samma ISE som din Logic app och visar **Core** -etiketten.

  ![Välj "Core" inbyggda utlösare och åtgärder](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Anslutningar som körs i en ISE har offentliga värdbaserade versioner som är tillgängliga i den globala Logic Appss tjänsten. För kopplingar som erbjuder två versioner körs kopplingar med **ISE** -etiketten alltid i samma ISE som din Logic app. Kopplingar utan **ISE** -etiketten körs i den globala Logic Appss tjänsten.

  ![Välj ISE-kopplingar](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

En ISE ger också ökade gränser för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-förfrågningar och svar, meddelande storlekar och anpassade anslutnings begär Anden. Mer information finns i [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU: er

När du skapar din ISE kan du välja Developer SKU eller Premium SKU. Här följer skillnaderna mellan dessa SKU: er:

* **Developer**

  Tillhandahåller en miljö med lägre kostnad som du kan använda för experimentering, utveckling och testning, men inte för produktions-eller prestanda testning. Developer SKU: n innehåller inbyggda utlösare och åtgärder, standard anslutningar, företags anslutningar och ett enda [kostnads fritt nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integrations konto för ett fast månads pris. Detta SKU omfattar dock inte något service avtal (SLA), alternativ för att skala upp kapacitet eller redundans vid återvinning, vilket innebär att du kan uppleva fördröjningar eller drift stopp.

* **Premium**

  Tillhandahåller en ISE som du kan använda för produktion och innehåller SLA-support, inbyggda utlösare och åtgärder, standard anslutningar, företags anslutningar, ett enda integrations konto på [Standard nivå](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , alternativ för att skala upp kapaciteten och redundans under återvinning för ett fast månads pris.

> [!IMPORTANT]
> Alternativet SKU är bara tillgängligt vid skapande av ISE och kan inte ändras senare.

Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/). Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE slut punkts åtkomst

När du skapar din ISE kan du välja att använda antingen interna eller externa slut punkter för åtkomst. Dessa slut punkter avgör om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket. Dessa slut punkter påverkar också åtkomsten till indata och utdata i körnings historiken för Logic app.

* **Internt**: privata slut punkter som tillåter anrop till logi Kap par i din ISE plus åtkomst till indata och utdata i körnings historiken endast inifrån *ditt virtuella nätverk*

* **Externt**: offentliga slut punkter som tillåter anrop till logi Kap par i din ISE plus åtkomst till indata och utdata i körnings historiken *från utanför det virtuella nätverket*

> [!IMPORTANT]
> Alternativet åtkomst slut punkt är bara tillgängligt vid skapande av ISE och kan inte ändras senare.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Åtkomst till lokala data källor

För lokala system som är anslutna till ett virtuellt Azure-nätverk kan du injicera en ISE i nätverket så att dina Logi Kap par kan komma åt dessa system genom att använda något av följande objekt:

* HTTP-åtgärd

* ISE – märkt koppling för systemet

  > [!NOTE]
  > Om du vill använda Windows-autentisering med SQL Server-anslutaren i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder du anslutningens icke-ISE-version med den [lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md). ISE-märkta versioner stöder inte Windows-autentisering.

* Anpassad anslutning

  * Om du har anpassade anslutningar som kräver den lokala datagatewayen och du har skapat dessa anslutningar utanför en ISE kan Logic Apps i en ISE också använda dessa anslutningar.
  
  * Anpassade anslutningar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt komma åt lokala data källor som är anslutna till det virtuella nätverket som är värd för ISE. Därför behöver Logic Apps i en ISE förmodligen inte datagatewayen när de kommunicerar med dessa resurser.

För lokala system som inte är anslutna till ett virtuellt nätverk eller inte har ISE-labled-kopplingar måste du först [Konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) innan dina Logi Kap par kan ansluta till dessa system.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrations konton med ISE

Du kan använda integrations konton med Logic Apps i en integrerings tjänst miljö (ISE). Dessa integrations konton måste dock använda *samma ISE* som de länkade Logic Apps. Logi Kap par i en ISE kan endast referera till de integrerings konton som finns i samma ISE. När du skapar ett integrations konto kan du välja din ISE som plats för ditt integrations konto. Information om hur priser och fakturering fungerar för integrations konton med en ISE finns i [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från isolerade Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Lägga till artefakter i integrerings tjänst miljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integrerings tjänst miljöer](../logic-apps/ise-manage-integration-service-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [Virtual Network-integrering för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
