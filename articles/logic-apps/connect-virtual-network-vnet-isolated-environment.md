---
title: Ansluta till virtuella Azure-nätverk från Azure Logic Apps via en integration service-miljö (ISE)
description: Skapa en integration service-miljö (ISE) så att logic apps och integrationskonton kan komma åt Azure-nätverk (Vnet), utan att förlora privata och isolerade från offentligt eller ”global” Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 2f84c48092581a313ff7bead7a862221e0fe4eee
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400928"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till Azure-nätverk från Azure Logic Apps med hjälp av en integration service-miljö (ISE)

> [!NOTE]
> Den här funktionen är i [ *förhandsversion*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

För scenarier där dina logic apps och integrationskonton behöver åtkomst till en [Azure-nätverk](../virtual-network/virtual-networks-overview.md), skapa en [ *integreringstjänstmiljön* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). En ISE är en privat och isolerad miljö som använder dedikerade lagringsutrymmen och andra resurser som är åtskilda från den offentliga eller ”global” Logic Apps-tjänsten. Den här separationen minskar också påverkas som andra Azure-klienter kan ha på din apps prestanda. Din ISE *in* till till din Azure-nätverk, som sedan distribuerar Logic Apps-tjänsten till ditt virtuella nätverk. När du skapar ett logic app eller varje konto, Välj den här ISE som deras plats. Ditt logic app eller varje konto kan sedan direkt åtkomst till resurser, till exempel virtuella datorer (VM), servrar, system och tjänster i ditt virtuella nätverk.

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Den här artikeln visar hur du utför dessa uppgifter:

* Konfigurera portar på Azure-nätverk så att trafik kan skickas genom din integration service-environment (ISE) i alla undernät i det virtuella nätverket.

* Skapa din integration service-environment (ISE).

* Skapa en logikapp som kan köras i din ISE.

* Skapa ett integrationskonto för logikappar i din ISE.

Läs mer om integreringstjänstmiljöer [åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

  > [!IMPORTANT]
  > Logic apps, inbyggda åtgärder och kopplingar som körs i din ISE kan du använda en annan prisplanen inte förbrukningsbaserad prisplanen. Mer information finns i [Logic Apps-priser](../logic-apps/logic-apps-pricing.md).

* En [Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du inte har ett virtuellt nätverk kan du lära dig hur du [skapa en Azure-nätverk](../virtual-network/quick-create-portal.md). 

  * Det virtuella nätverket måste ha fyra *tom* undernät för att distribuera och skapa resurser i din ISE. Du kan skapa de här undernäten i förväg eller vänta tills du har skapat din ISE där du kan skapa undernät på samma gång. Läs mer om [undernät krav](#create-subnet). 
  
    > [!NOTE]
    > Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), som innehåller en privat anslutning till Microsofts molntjänster, måste du [skapar en routningstabell](../virtual-network/manage-route-table.md) som har följande dirigera och länka tabellen med varje undernät som används av din ISE:
    > 
    > **Namnet**: <*route-name*><br>
    > **Adressprefixet**: 0.0.0.0/0<br>
    > **Nästa hopp**: Internet

  * Se till att det virtuella nätverket [tillgängliggör dessa portar](#ports) så att din ISE fungerar och är tillgänglig.

* Om du vill använda anpassade DNS-servrar för Azure-nätverk, [konfigurera dessa servrar genom att följa dessa steg](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) innan du distribuerar din ISE till ditt virtuella nätverk. I annat fall behöva varje gång du ändrar din DNS-server du också starta om din ISE som är en funktion som är tillgängliga i offentlig förhandsversion i ISE.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Konfigurera nätverksportar

För att fungera korrekt och håll tillgänglig, måste din integration service-environment (ISE) ha specifika portar som är tillgängliga i det virtuella nätverket. Annars, om något av de här portarna är inte tillgänglig, du kan förlora åtkomst till din ISE som kan sluta fungera. När du använder en ISE i ett virtuellt nätverk ett vanligt installationsproblem har en eller flera blockerade portar. Den koppling som du använder kan också ha en egen portkraven för anslutningar mellan dina ISE och målsystemet. Till exempel om du kommunicera med en FTP-system med hjälp av FTP-anslutningen kontrollerar du den port som du använder på att FTP-system, till exempel port 21 för att skicka kommandon och är tillgänglig.

Om du vill styra trafiken över det virtuella nätverkets undernät där du distribuerar din ISE kan du ställa in [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) för dessa undernät av [filtrerar nätverkstrafik mellan undernät](../virtual-network/tutorial-filter-network-traffic.md). Dessa tabeller beskrivs portarna i ditt virtuella nätverk som använder din ISE och där de portarna som får användas. Den [servicetagg](../virtual-network/security-overview.md#service-tags) representerar en grupp med IP-adressprefix som syfte att minska komplexiteten när du skapar säkerhetsregler.

> [!IMPORTANT]
> För intern kommunikation inom dina undernät kräver ISE att du öppnar alla portar i dessa undernät.

| Syfte | Riktning | Portar | Källtjänsttagg | Måltjänsttagg | Anteckningar |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Kommunikation från Azure Logic Apps | Utgående | 80 & 443 | VIRTUAL_NETWORK | INTERNET | Porten är beroende av den externa tjänsten som Logic Apps-tjänsten kommunicerar |
| Azure Active Directory | Utgående | 80 & 443 | VIRTUAL_NETWORK | AzureActiveDirectory | |
| Beroende av Azure Storage | Utgående | 80 & 443 | VIRTUAL_NETWORK | Storage | |
| Intersubnet kommunikation | Inkommande och utgående | 80 & 443 | VIRTUAL_NETWORK | VIRTUAL_NETWORK | För kommunikation mellan undernät |
| Kommunikation till Azure Logic Apps | Inkommande | 443 | INTERNET  | VIRTUAL_NETWORK | IP-adressen för datorn eller tjänsten som anropar en begäransutlösare eller en webhook som finns i din logikapp. Stänga eller blockerar den här porten förhindrar att HTTP-anrop till logikappar med frågeutlösare.  |
| Historik för logikappskörningen | Inkommande | 443 | INTERNET  | VIRTUAL_NETWORK | IP-adressen för den dator där du se logikappen körningshistorik. Även om stänga eller blockerar den här porten inte hindra dig från att visa körningshistoriken, du kan inte visa indata och utdata för varje steg i som körningshistorik. |
| Anslutningshanteringen | Utgående | 443 | VIRTUAL_NETWORK  | INTERNET | |
| Publicera diagnostikloggar och mått | Utgående | 443 | VIRTUAL_NETWORK  | AzureMonitor | |
| Logikappdesigner – dynamiska egenskaper | Inkommande | 454 | INTERNET  | VIRTUAL_NETWORK | Begäranden som kommer från Logic Apps [åt slutpunkten för inkommande IP-adresser i den regionen](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Service Management-appberoendet | Inkommande | 454 & 455 | AppServiceManagement | VIRTUAL_NETWORK | |
| Connector-distribution | Inkommande | 454 & 3443 | INTERNET  | VIRTUAL_NETWORK | Krävs för att distribuera och uppdatera kopplingar. Stänga eller blockerar den här porten orsakar ISE distributioner misslyckas och förhindrar anslutningen uppdateringar och korrigeringar. |
| Azure SQL-beroenden | Utgående | 1433 | VIRTUAL_NETWORK | SQL |
| Azure Resource Health | Utgående | 1886 | VIRTUAL_NETWORK | INTERNET | För att publicera hälsostatus till Resource Health |
| API Management - hanteringsslutpunkt | Inkommande | 3443 | APIManagement  | VIRTUAL_NETWORK | |
| Beroende från loggen till Event Hub-principen och övervakningsagent | Utgående | 5672 | VIRTUAL_NETWORK  | EventHub | |
| Få åtkomst till Azure Cache för Redis-instanser mellan Rollinstanser | Inkommande <br>Utgående | 6379-6383 | VIRTUAL_NETWORK  | VIRTUAL_NETWORK | Dessutom för ISE för att arbeta med Azure Cache för Redis, måste du öppna dessa [utgående och inkommande portar som beskrivs i Azure Cache för Redis vanliga frågor och svar](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Inkommande | * | AZURE_LOAD_BALANCER | VIRTUAL_NETWORK |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

Följ dessa steg för att skapa din integration service-environment (ISE):

1. I den [Azure-portalen](https://portal.azure.com), på Azure-huvudmenyn väljer **skapa en resurs**.

   ![Skapa ny resurs](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I sökrutan anger du ”integreringstjänstmiljön” som filter.
Listan med resultat väljer **Integreringstjänstmiljön (förhandsversion)**, och välj sedan **skapa**.

   ![Välj ”Integreringstjänstmiljön”](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Välj ”Skapa”](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Ange följande information för din miljö och välj sedan **granska + skapa**, till exempel:

   ![Ange information om miljön](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap  | Krävs | Value | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Azure-prenumeration för din miljö |
   | **Resursgrupp** | Ja | <*Azure-resource-group-name*> | Azure-resursgrupp där du vill skapa en miljö |
   | **Namn på integreringstjänstmiljö** | Ja | <*environment-name*> | Namn för att ge din miljö |
   | **Plats** | Ja | <*Azure-datacenter-region*> | Azure-datacenterregion var du vill distribuera din miljö |
   | **Ytterligare kapacitet** | Ja | 0, 1, 2, 3 | Antal enheter för den här ISE-resursen. Om du vill lägga till kapacitet när du har skapat, se [lägga till kapacitet](#add-capacity). |
   | **Virtuellt nätverk** | Ja | <*Azure-virtual-network-name*> | Azure-nätverket där du vill att mata in din miljö så att logic apps i denna miljö kan komma åt det virtuella nätverket. Om du inte har ett nätverk kan du skapa en här. <p>**Viktiga**: Du kan *endast* utföra den här inmatning när du skapar din ISE. Men innan du kan skapa den här relationen, se till att du redan [konfigurera rollbaserad åtkomstkontroll i ditt virtuella nätverk för Azure Logic Apps](#vnet-access). |
   | **Undernät** | Ja | <*subnet-resource-list*> | En ISE kräver fyra *tom* undernät för att skapa resurser i din miljö. Att skapa varje undernät, [att följa stegen i den här tabellen](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   För att skapa resurser i din miljö, måste din ISE fyra *tom* undernät som inte delegeras till alla tjänster. 
   Du *kan* ändra undernätsadresserna när du har skapat din miljö. Varje undernät måste uppfylla följande kriterier:

   * Har ett namn som börjar med ett alfabetiskt tecken eller ett understreck och inte har följande tecken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Använder den [Classless Inter-Domain Routing CIDR-format](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) och en klass B-adressutrymmet.

   * Använder minst en `/27` i adressen utrymme eftersom varje undernät måste ha 32 adresser som den *minsta*. Exempel:

     * `10.0.0.0/27` har 32 adresser eftersom 2<sup>(32-27)</sup> är 2<sup>5</sup> eller 32.

     * `10.0.0.0/24` har 256 adresser eftersom 2<sup>(32-24)</sup> är 2<sup>8</sup> eller 256.

     * `10.0.0.0/28` har bara 16 adresser och är för liten eftersom 2<sup>(32-28)</sup> är 2<sup>4</sup> eller 16.

     Mer information om hur du beräknar adresser finns [IPv4 CIDR-block som](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), Kom ihåg att [skapar en routningstabell](../virtual-network/manage-route-table.md) som har följande dirigera och länka tabellen med varje undernät som används av din ISE:

     **Namnet**: <*route-name*><br>
     **Adressprefixet**: 0.0.0.0/0<br>
     **Nästa hopp**: Internet

   1. Under den **undernät** väljer **hantera undernätskonfiguration**.

      ![Hantera konfiguration av undernät](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. På den **undernät** fönstret Välj **undernät**.

      ![Lägga till undernät](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. På den **Lägg till undernät** fönstret anger den här informationen.

      * **Namn**: Namn för ditt undernät
      * **Adressintervall (CIDR-block)**: Intervall för ditt undernät i det virtuella nätverket och i CIDR-format

      ![Lägg till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. När du är klar väljer du **OK**.

   1. Upprepa dessa steg för tre flera undernät.

1. När Azure verifierar har ISE-information, väljer **skapa**, till exempel:

   ![Välj ”Skapa” efter lyckad validering](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, men den här processen *kan* ta upp till två timmar innan du avslutar. 
   Välj meddelandeikonen, vilket öppnar meddelandefönstret för att kontrollera Distributionsstatus i din Azure verktygsfältet.

   ![Kontrollera Distributionsstatus för](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen har slutförts visas det här meddelandet i Azure:

   ![Distribueringen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Om distributionen misslyckas eller om du ta bort din ISE Azure *kan* ta upp till en timme innan du publicerar dina undernät. Därför kanske du måste vänta innan du återanvänder dessa undernät i ett annat ISE.

1. Om du vill visa din miljö, Välj **gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.  

<a name="add-capacity"></a>

### <a name="add-capacity"></a>Lägga till kapacitet

Basenheten ISE har fast kapacitet, så om du behöver större dataflöde kan du lägga till fler skalningsenheter. Du kan skala automatiskt baserat på prestandavärden eller baserat på ett antal bearbetningsenheter. Om du väljer automatisk skalning baserat på mått du väljer bland olika kriterier och anger tröskelvärdet villkor i syfte att uppfylla dessa villkor.

1. Hitta din ISE i Azure-portalen.

1. Om du vill visa prestandamått för din ISE på din ISE Huvudmeny väljer **översikt**.

1. Du ställer in automatisk skalning och under **inställningar**väljer **skala ut**. På den **konfigurera** fliken **aktivera autoskalning**.

1. I den **standard** väljer du antingen **skala baserat på ett mått** eller **skala till ett specifikt instansantal**.

1. Om du väljer instans-baserade måste du ange hur många bearbetningsenheter mellan 0 och 3 portintervallet. Annars för mått-baserade, Följ dessa steg:

   1. I den **standard** väljer **lägga till en regel**.

   1. På den **skalningsregeln** fönstret har skapat dina villkor och åtgärden ska vidtas när regeln utlöses.

   1. När du är klar väljer **Lägg till**.

1. När du är klar ska du komma ihåg att spara dina ändringar.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Skapa logikapp – ISE

För att skapa logikappar som använder din integration service-environment (ISE), följer du stegen i [så här skapar du en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) men med följande skillnader: 

* När du skapar din logikapp under den **plats** egenskapen, Välj din ISE från den **integreringstjänstmiljöer** avsnittet, till exempel:

  ![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Du kan använda samma inbyggda utlösare och åtgärder, till exempel HTTP, som körs i samma ISE som din logikapp. Kopplingar med den **ISE** märka också köras i samma ISE som din logikapp. Anslutningar utan den **ISE** etikett som körs i tjänsten för global Logic Apps.

  ![Välj ISE-tjänster](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* När du mata in din ISE i Azure-nätverk, logikappar i din ISE direkt åtkomst till resurser i det virtuella nätverket. För lokala system som är anslutna till ett virtuellt nätverk, att mata in en ISE i nätverket så att dina logikappar har direkt åtkomst dessa system med någon av dessa objekt: 

  * ISE-anslutning för systemet, till exempel SQL Server
  
  * HTTP-åtgärd 
  
  * Anpassad anslutningsapp

  För lokala system som inte är i ett virtuellt nätverk eller inte har ISE kopplingar först [konfigurera den lokala datagatewayen](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Skapa integrationskonto – ISE

Om du vill använda en integrering med logic apps i en integreringstjänstmiljö (ISE) måste det integrationskontot måste använda den *samma miljö* som logic apps. Logic apps i en ISE kan referera till integrationskonton i samma ISE. 

Om du vill skapa ett integrationskonto som använder en ISE, följer du stegen i [hur du skapar konton för logikappsintegration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) undantag för den **plats** egenskapen där den **integreringstjänstmiljöer**  avsnittet visas nu. Välj i stället dina ISE i stället för en region, till exempel:

![Välj integreringstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forumet för Azure Logic Apps</a>.
* Om du vill skicka in eller rösta på förslag på funktioner besöker du <a href="https://aka.ms/logicapps-wish" target="_blank">webbplatsen för Logic Apps-användarfeedback</a>.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [virtual network-integration för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
