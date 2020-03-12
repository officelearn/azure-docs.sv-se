---
title: Åtkomst till virtuella Azure-nätverk
description: Översikt över hur integrerings tjänst miljöer (ISEs) hjälper Logic Apps åtkomst till Azure Virtual Networks (virtuella nätverk)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127244"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Åtkomst till Azure Virtual Network-resurser från Azure Logic Apps med hjälp av integrerings tjänst miljöer (ISEs)

Ibland behöver dina Logi Kap par åtkomst till skyddade resurser, till exempel virtuella datorer (VM) och andra system eller tjänster som finns i ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du vill konfigurera den här åtkomsten kan du [skapa en *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). En ISE är en isolerad instans av den Logic Apps tjänst som använder dedikerade resurser och körs separat från den "globala" Multi-Tenant Logic Apps-tjänsten.

Genom att köra Logi Kap par i en egen separat isolerad instans kan du minska den påverkan som andra Azure-klienter kan ha på dina appars prestanda, även kallade ["bruset](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)"-påverkan. En ISE tillhandahåller även följande fördelar:

* Dina egna statiska IP-adresser, som är åtskilda från de statiska IP-adresser som delas av logi Kap par i tjänsten för flera innehavare. Du kan också konfigurera en enskild offentlig, statisk och förutsägbar utgående IP-adress för att kommunicera med mål systemen. På så sätt behöver du inte konfigurera ytterligare brand Väggs öppningar på dessa mål system för varje ISE.

* Ökade gränser för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-begäran och svar, meddelande storlekar och anpassade anslutnings begär Anden. Mer information finns i [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md).

> [!NOTE]
> Vissa virtuella Azure-nätverk använder privata slut punkter ([Azures privata länk](../private-link/private-link-overview.md)) för att ge åtkomst till Azure PaaS-tjänster, till exempel Azure Storage, Azure Cosmos DB eller Azure SQL Database, partner tjänster eller kund tjänster som finns i Azure. Om dina Logi Kap par behöver åtkomst till virtuella nätverk som använder privata slut punkter, måste du skapa, distribuera och köra dessa Logic Apps i en ISE.

När du skapar en ISE *injicerar* Azure eller distribuerar detta ISE till ditt virtuella Azure-nätverk. Du kan sedan använda denna ISE som plats för de Logic Apps och integrations konton som behöver åtkomst.

![Välj integrerings tjänst miljö](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Logi Kap par har åtkomst till resurser som finns i eller är anslutna till ditt virtuella nätverk genom att använda dessa objekt, som körs i samma ISE som dina Logi Kap par:

* En **Core**-märkt inbyggd utlösare eller åtgärd, till exempel http-utlösare eller åtgärd
* En **ISE**-märkt koppling för systemet eller tjänsten
* En anpassad anslutning

Du kan fortfarande även använda kopplingar som inte har **Core** -eller **ISE** -etiketten med Logic Apps i din ISE. Dessa anslutningar körs i stället i Logic Apps tjänsten för flera innehavare. Mer information finns i följande avsnitt:

* [Isolerade kontra flera innehavare](#difference)
* [Anslut från en integrerings tjänst miljö](../connectors/apis-list.md#integration-service-environment)
* [ISE-anslutningar](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en pris sättnings plan som skiljer sig från den förbruknings pris planen. Mer information finns i [Logic Apps pris modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris information finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

I den här översikten beskrivs mer information om hur en ISE ger din Logi Kap par åtkomst till ditt virtuella Azure-nätverk och Jämför skillnaderna mellan en ISE och Logic Apps tjänsten för flera innehavare.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Isolerade kontra flera innehavare

När du skapar och kör Logi Kap par i en ISE får du samma användar upplevelse och liknande funktioner som Logic Apps tjänsten för flera innehavare. Du kan använda alla inbyggda utlösare, åtgärder och hanterade anslutningar som är tillgängliga i Logic Apps tjänsten för flera innehavare. Vissa hanterade anslutningar erbjuder ytterligare ISE-versioner. Skillnaden mellan ISE-kopplingar och icke-ISE-anslutningar finns i där de körs och de etiketter som de har i Logic Apps designer när du arbetar i en ISE.

![Kopplingar med och utan etiketter i en ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Inbyggda utlösare och åtgärder visar **Core** -etiketten. De körs alltid i samma ISE som din Logic app. Hanterade anslutningar som visar **ISE** -etiketten körs också i samma ISE som din Logic app.

  Här är till exempel några kopplingar som erbjuder ISE-versioner:

  * Azure Blob Storage, File Storage och Table Storage
  * Azure-köer, Azure Service Bus, Azure Event Hubs och IBM MQ
  * FTP och SFTP – SSH
  * SQL Server Azure SQL Data Warehouse Azure Cosmos DB
  * AS2, X12 och EDIFACT

* Hanterade kopplingar som inte visar några ytterligare etiketter körs alltid i Logic Apps tjänsten för flera innehavare, men du kan fortfarande använda de här anslutningarna i en ISE-värdbaserad Logic-app.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Åtkomst till lokala system

För att komma åt lokala system eller data källor som är anslutna till ett virtuellt Azure-nätverk kan Logic Apps i en ISE använda dessa objekt:

* HTTP-åtgärd

* ISE – märkt koppling för systemet

  > [!NOTE]
  > Om du vill använda Windows-autentisering med SQL Server-anslutaren i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder du anslutningens icke-ISE-version med den [lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md). ISE-märkta versioner stöder inte Windows-autentisering.

* Anpassad anslutning

  * Om du har anpassade anslutningar som kräver den lokala datagatewayen och du har skapat dessa anslutningar utanför en ISE kan Logic Apps i en ISE också använda dessa anslutningar.

  * Anpassade anslutningar som skapats i en ISE fungerar inte med den lokala datagatewayen. Dessa anslutningar kan dock direkt komma åt lokala data källor som är anslutna till det virtuella nätverket som är värd för ISE. Därför behöver Logic Apps i en ISE förmodligen inte datagatewayen när de kommunicerar med dessa resurser.

För lokala system som inte är anslutna till ett virtuellt nätverk eller inte har ISE-märkta anslutningar måste du först [Konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md) innan dina Logi Kap par kan ansluta till dessa system.

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

När du skapar din ISE kan du välja att använda antingen interna eller externa slut punkter för åtkomst. Ditt val bestämmer om begäran eller webhook-utlösare på Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket.

De här slut punkterna påverkar också hur du kan komma åt indata och utdata i dina Logic Apps-körnings historik.

* **Internt**: privata slut punkter som tillåter anrop till logi Kap par i din ISE där du kan visa och komma åt dina Logi Kap par indata och utdata i körnings historiken endast inifrån *ditt virtuella nätverk*

* **Externt**: offentliga slut punkter som tillåter anrop till logi Kap par i din ISE där du kan visa och komma åt dina Logi Kap par indata och utdata i körnings historiken *från utanför det virtuella nätverket*. Om du använder nätverks säkerhets grupper (NSG: er) kontrollerar du att de har kon figurer ATS med regler för inkommande trafik för att tillåta åtkomst till körnings historikens indata och utdata. Mer information finns i [Aktivera åtkomst för ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

> [!IMPORTANT]
> Alternativet åtkomst slut punkt är bara tillgängligt vid skapande av ISE och kan inte ändras senare.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integrations konton med ISE

Du kan använda integrations konton med Logic Apps i en integrerings tjänst miljö (ISE). Dessa integrations konton måste dock använda *samma ISE* som de länkade Logic Apps. Logi Kap par i en ISE kan endast referera till de integrerings konton som finns i samma ISE. När du skapar ett integrations konto kan du välja din ISE som plats för ditt integrations konto. Information om hur priser och fakturering fungerar för integrations konton med en ISE finns i [Logic Apps prissättnings modell](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Nästa steg

* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [Virtual Network-integrering för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
