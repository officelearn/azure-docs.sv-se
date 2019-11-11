---
title: Ansluta till virtuella Azure-nätverk med en ISE-Azure Logic Apps
description: Skapa en integrerings tjänst miljö (ISE) som kan komma åt Azure Virtual Networks (virtuella nätverk) från Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 883778360bd2315e1424f9f207cbfd994ec1a373
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901182"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till virtuella Azure-nätverk från Azure Logic Apps med hjälp av en integrerings tjänst miljö (ISE)

För scenarier där dina Logi Kap par och integrations konton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md)kan du skapa en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). En ISE är en privat och isolerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den offentliga eller "globala" Logic Apps-tjänsten. Den här separationen minskar också eventuell påverkan som andra Azure-klienter kan ha på dina appars prestanda.

När du skapar en ISE, *injicerar* Azure som ISE i ditt virtuella Azure-nätverk, som sedan distribuerar tjänsten Logic Apps till ditt virtuella nätverk. När du skapar en Logic app eller ett integrations konto väljer du din ISE som plats. Din Logic app-eller integrations konto kan sedan få direkt åtkomst till resurser, till exempel virtuella datorer, servrar, system och tjänster, i ditt virtuella nätverk.

![Välj integrerings tjänst miljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> För att Logic Apps och integrations konton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som plats.

En ISE har ökat gränserna för körnings tid, lagrings kvarhållning, data flöde, timeout för HTTP-begäran och svar, meddelande storlekar och anpassade anslutnings begär Anden. Mer information finns i [gränser och konfiguration för Azure Logic Apps](logic-apps-limits-and-config.md). Mer information om ISEs finns i [åtkomst till Azure Virtual Network-resurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Den här artikeln visar hur du utför dessa uppgifter:

* Se till att alla nödvändiga portar på det virtuella nätverket är öppna så att trafiken kan färdas genom din ISE över undernät i det virtuella nätverket.

* Skapa din ISE.

* Lägg till extra kapacitet i ISE.

> [!IMPORTANT]
> Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en pris plan som skiljer sig från den förbruknings bara pris planen. Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du inte har ett virtuellt nätverk kan du läsa om hur du [skapar ett virtuellt Azure-nätverk](../virtual-network/quick-create-portal.md). 

  * Ditt virtuella nätverk måste ha fyra *tomma* undernät för att skapa och distribuera resurser i din ISE. Du kan skapa dessa undernät i förväg eller vänta tills du har skapat din ISE där du kan skapa undernät på samma tid. Läs mer om [krav för undernät](#create-subnet).

  * Under näts namn måste börja med antingen ett alfabetiskt tecken eller ett under streck och kan inte använda dessa tecken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Om du vill distribuera ISE via en Azure Resource Manager-mall ska du först kontrol lera att du delegerar ett tomt undernät till Microsoft. Logic/integrationServiceEnvironment. Du behöver inte utföra den här delegeringen när du distribuerar via Azure Portal.

  * Kontrol lera att det virtuella nätverket [gör dessa portar tillgängliga](#ports) så att din ISE fungerar som den ska och är tillgänglig.

  * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), som tillhandahåller en privat anslutning till Microsofts moln tjänster, måste du [skapa en](../virtual-network/manage-route-table.md) routningstabell som har följande väg och länka tabellen till varje undernät som används av din ISE:

    **Namn**: <*route-Name*><br>
    **Adressprefix**: 0.0.0.0/0<br>
    **Nästa hopp**: Internet

* Om du vill använda anpassade DNS-servrar för ditt virtuella Azure-nätverk [konfigurerar du dessa servrar genom att följa de här stegen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) innan du distribuerar din ISE till ditt virtuella nätverk. Annars måste du starta om ISE varje gången du ändrar DNS-servern.

  > [!IMPORTANT]
  > Om du ändrar inställningarna för DNS-servern efter att du har skapat en ISE måste du starta om ISE. Mer information om hur du hanterar DNS-serverinställningar finns i [skapa, ändra eller ta bort ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Kontrol lera nätverks portarna

När du använder en ISE med ett virtuellt Azure-nätverk har ett vanligt installations problem en eller flera blockerade portar. De anslutningar som du använder för att skapa anslutningar mellan din ISE och mål systemet kan också ha egna port krav. Om du till exempel kommunicerar med ett FTP-system med hjälp av FTP-anslutningen kontrollerar du att porten som du använder på FTP-systemet är tillgänglig, till exempel port 21 för att skicka kommandon. För att se till att din ISE är tillgänglig och kan fungera korrekt, öppnar du portarna som anges i tabellen nedan. Om några av de portar som krävs inte är tillgängliga, slutar även din ISE att fungera.

> [!IMPORTANT]
> Käll portar är tillfälliga, så se till att du ställer in dem på `*` för alla regler.
> För intern kommunikation i dina undernät kräver din ISE att du öppnar alla portar i dessa undernät.

* Om du har skapat ett nytt virtuellt nätverk och undernät utan begränsningar behöver du inte konfigurera [nätverks säkerhets grupper (NSG: er)](../virtual-network/security-overview.md#network-security-groups) i det virtuella nätverket för att styra trafiken mellan undernät.

* I ett befintligt virtuellt nätverk kan du *välja* att konfigurera NSG: er genom att [filtrera nätverks trafik över undernät](../virtual-network/tutorial-filter-network-traffic.md). Om du väljer den här vägen, i det virtuella nätverk där du vill konfigurera NSG: er, måste du öppna de portar som anges i tabellen nedan. Om du använder [NSG säkerhets regler](../virtual-network/security-overview.md#security-rules)behöver du både TCP-och UDP-protokoll.

* Om du tidigare har en befintlig NSG: er eller brand vägg i ditt virtuella nätverk, se till att du öppnar de portar som anges i tabellen nedan. Om du använder [NSG säkerhets regler](../virtual-network/security-overview.md#security-rules)behöver du både TCP-och UDP-protokoll.

Här är den tabell som beskriver de portar i ditt virtuella nätverk som används av ISE och var dessa portar används. [Resource Manager-tjänstens Taggar](../virtual-network/security-overview.md#service-tags) representerar en grupp IP-adressprefix som bidrar till att minimera komplexiteten när du skapar säkerhets regler.

| Syfte | Riktning | Målportar | Käll tjänst tag gen | Måltjänsttagg | Anteckningar |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Kommunikation från Azure Logic Apps | Utgående | 80, 443 | VirtualNetwork | Internet | Porten är beroende av den externa tjänst som Logic Apps tjänsten kommunicerar med |
| Azure Active Directory | Utgående | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage beroende | Utgående | 80, 443 | VirtualNetwork | Storage | |
| Kommunikation mellan undernät | Inkommande & utgående | 80, 443 | VirtualNetwork | VirtualNetwork | För kommunikation mellan undernät |
| Kommunikation till Azure Logic Apps | Inkommande | 443 | Interna åtkomst slut punkter: <br>VirtualNetwork <p><p>Externa åtkomst slut punkter: <br>Internet <p><p>**Obs!** de här slut punkterna refererar till den slut punkts inställning som [valdes vid skapande av ISE](#create-environment). Mer information finns i [slut punkts åtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | IP-adressen för datorn eller tjänsten som anropar en begär ande utlösare eller en webhook som finns i din Logic app. Om du stänger eller blockerar den här porten förhindras HTTP-anrop till Logic Apps med begär ande utlösare. |
| Körnings historik för Logic app | Inkommande | 443 | Interna åtkomst slut punkter: <br>VirtualNetwork <p><p>Externa åtkomst slut punkter: <br>Internet <p><p>**Obs!** de här slut punkterna refererar till den slut punkts inställning som [valdes vid skapande av ISE](#create-environment). Mer information finns i [slut punkts åtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | IP-adressen för den dator från vilken du visar den logiska appens körnings historik. Även om du stänger eller blockerar den här porten hindrar dig inte från att Visa körnings historiken kan du inte Visa indata och utdata för varje steg i den här körnings historiken. |
| Anslutnings hantering | Utgående | 443 | VirtualNetwork  | AppService | |
| Publicera diagnostikloggar & mått | Utgående | 443 | VirtualNetwork  | AzureMonitor | |
| Kommunikation från Azure Traffic Manager | Inkommande | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps designer – dynamiska egenskaper | Inkommande | 454 | Se antecknings kolumnen för IP-adresser som ska tillåtas | VirtualNetwork | Begär Anden kommer från Logic Apps åtkomst slut punktens [inkommande](../logic-apps/logic-apps-limits-and-config.md#inbound) IP-adresser för den regionen. |
| Nätverks hälso kontroll | Inkommande | 454 | Se antecknings kolumnen för IP-adresser som ska tillåtas | VirtualNetwork | Begär Anden kommer från Logic Apps åtkomst slut punkten för både [inkommande](../logic-apps/logic-apps-limits-and-config.md#inbound) och [utgående](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-adresser för den regionen. |
| Beroende för App Service hantering | Inkommande | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Kopplings distribution | Inkommande | 454 | AzureConnectors | VirtualNetwork | Krävs för att distribuera och uppdatera anslutningar. Om du stänger eller blockerar den här porten kan ISE-distributioner Miss Missing och förhindrar anslutnings uppdateringar eller korrigeringar. |
| Distribution av kopplings princip | Inkommande | 3443 | Internet | VirtualNetwork | Krävs för att distribuera och uppdatera anslutningar. Om du stänger eller blockerar den här porten kan ISE-distributioner Miss Missing och förhindrar anslutnings uppdateringar eller korrigeringar. |
| Azure SQL-beroende | Utgående | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Utgående | 1886 | VirtualNetwork | AzureMonitor | För att publicera hälso status till Resource Health |
| API Management hanterings slut punkt | Inkommande | 3443 | API Management | VirtualNetwork | |
| Beroende från logg till Event Hub-princip och övervaknings agent | Utgående | 5672 | VirtualNetwork | EventHub | |
| Få åtkomst till Azure cache för Redis-instanser mellan roll instanser | Inkommande <br>Utgående | 6379-6383 | VirtualNetwork | VirtualNetwork | För att ISE ska fungera med Azure cache för Redis måste du också öppna de här [utgående och inkommande portarna som beskrivs i Azure cache för Redis vanliga frågor och svar](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Inkommande | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

Följ dessa steg om du vill skapa en integrerings tjänst miljö (ISE):

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**på huvud menyn i Azure.
I rutan Sök anger du "integration service Environment" som filter.

   ![Skapa ny resurs](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. I fönstret Skapa Integration Service Environment väljer du **skapa**.

   ![Välj "skapa"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Ange den här informationen för din miljö och välj sedan **Granska + skapa**, till exempel:

   ![Ange miljö information](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumerationsnamn*> | Azure-prenumerationen som ska användas för din miljö |
   | **Resursgrupp** | Ja | <*Azure-resurs-grupp-namn*> | Den Azure-resurs grupp där du vill skapa din miljö |
   | **Namn på integrerings tjänst miljö** | Ja | <*miljö-namn*> | Ditt ISE-namn, som endast får innehålla bokstäver, siffror, bindestreck (`-`), under streck (`_`) och punkter (`.`). |
   | **Plats** | Ja | <*Azure-datacenter-region*> | Azure Data Center-regionen där du distribuerar din miljö |
   | **SKU** | Ja | **Premium** eller **Developer (service avtal)** | ISE-SKU: n för att skapa och använda. För skillnader mellan dessa SKU: er, se [ISE SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Viktigt**: det här alternativet är endast tillgängligt vid skapande av ISE och kan inte ändras senare. |
   | **Ytterligare kapacitet** | Premium: <br>Ja <p><p>Utvecklare: <br>Inte tillämpligt | Premium: <br>0 till 10 <p><p>Utvecklare: <br>Inte tillämpligt | Antalet ytterligare bearbetnings enheter som ska användas för denna ISE-resurs. Information om hur du lägger till kapacitet när du har skapat finns i [lägga till ISE-kapacitet](#add-capacity) |
   | **Åtkomst slut punkt** | Ja | **Intern** eller **extern** | Den typ av åtkomst slut punkter som ska användas för din ISE, som avgör om begäran eller webhook-utlösare i Logic Apps i din ISE kan ta emot samtal utanför det virtuella nätverket. Slut punkts typen påverkar också åtkomst till indata och utdata i din Logic app kör historik. Mer information finns i [slut punkts åtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Viktigt**: det här alternativet är endast tillgängligt vid skapande av ISE och kan inte ändras senare. |
   | **Virtuellt nätverk** | Ja | <*Azure-Virtual-Network-name*> | Det virtuella Azure-nätverket där du vill mata in din miljö så att Logic Apps i den miljön kan komma åt ditt virtuella nätverk. Om du inte har ett nätverk [skapar du först ett virtuellt Azure-nätverk](../virtual-network/quick-create-portal.md). <p>**Viktigt**: du kan *bara* utföra den här inmatningen när du skapar din ISE. |
   | **Undernät** | Ja | <*undernät – resurs lista*> | En ISE kräver fyra *tomma* undernät för att skapa och distribuera resurser i din miljö. [Följ stegen i den här tabellen](#create-subnet)om du vill skapa varje undernät. |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   För att skapa och distribuera resurser i din miljö behöver din ISE fyra *tomma* undernät som inte har delegerats till någon tjänst. Du *kan inte* ändra dessa under näts adresser när du har skapat din miljö.
   
   > [!IMPORTANT]
   > 
   > Under näts namn måste börja med antingen ett alfabetiskt tecken eller ett under streck (inga siffror) och använder inte dessa tecken: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.
   
   Dessutom måste varje undernät uppfylla följande krav:

   * Använder [CIDR-format (Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) och ett klass B-adressutrymme.

   * Använder minst en `/27` i adress utrymmet eftersom varje undernät måste ha *minst* 32 adresser som *minst.* Exempel:

     * `10.0.0.0/27` har 32 adresser eftersom 2<sup>(32-27)</sup> är 2<sup>5</sup> eller 32.

     * `10.0.0.0/24` har 256 adresser eftersom 2<sup>(32-24)</sup> är 2<sup>8</sup> eller 256.

     * `10.0.0.0/28` har bara 16 adresser och är för liten eftersom 2<sup>(32-28)</sup> är 2<sup>4</sup> eller 16.

     Mer information om hur du beräknar adresser finns i [IPv4 CIDR-block](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md)måste du [skapa en](../virtual-network/manage-route-table.md) routningstabell med följande väg och länka tabellen till varje undernät som används av din ISE:

     **Namn**: <*route-Name*><br>
     **Adressprefix**: 0.0.0.0/0<br>
     **Nästa hopp**: Internet

   1. I listan **undernät** väljer du **Hantera under näts konfiguration**.

      ![Hantera under näts konfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. I fönstret **undernät** väljer du **undernät**.

      ![Lägga till undernät](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Ange den här informationen i fönstret **Lägg till undernät** .

      * **Namn**: namnet på under nätet
      * **Adress intervall (CIDR-block)** : under nätets intervall i det virtuella nätverket och i CIDR-format

      ![Lägg till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. När du är klar väljer du **OK**.

   1. Upprepa de här stegen för tre fler undernät.

      > [!NOTE]
      > Om de undernät du försöker skapa inte är giltiga, visar Azure Portal ett meddelande, men blockerar inte ditt förlopp.

   Mer information om hur du skapar undernät finns i [lägga till ett undernät för virtuellt nätverk](../virtual-network/virtual-network-manage-subnet.md).

1. När Azure har verifierat din ISE-information väljer du **skapa**, till exempel:

   ![När verifieringen är klar väljer du "skapa"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, men det *kan* ta upp till två timmar innan processen har slutförts. För att kontrol lera distributions status går du till Azure-verktygsfältet och väljer aviserings ikonen, som öppnar fönstret meddelanden.

   ![Kontrol lera distributions status](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen har slutförts visas följande meddelande i Azure:

   ![Distributionen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Annars följer du Azure Portal anvisningarna för fel sökning av distribution.

   > [!NOTE]
   > Om distributionen Miss lyckas eller om du tar bort din ISE kan Azure ta upp till en timme innan du släpper upp dina undernät. Den här fördröjningen innebär att du kan behöva vänta innan du återanvänder dessa undernät i en annan ISE.
   >
   > Om du tar bort det virtuella nätverket tar Azure vanligt vis upp till två timmar innan du frigör dina undernät, men den här åtgärden kan ta längre tid. 
   > Se till att inga resurser fortfarande är anslutna när du tar bort virtuella nätverk. 
   > Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Om du vill visa din miljö väljer du **gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.

1. Information om hur du kontrollerar nätverks hälsan för din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Om du vill börja skapa Logi Kap par och andra artefakter i din ISE, se [lägga till artefakter i integrerings tjänst miljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Lägg till ISE-kapacitet

Premium ISE-bas enheten har fast kapacitet, så om du behöver mer data flöde kan du lägga till fler skalnings enheter, antingen under skapandet eller efteråt. Du kan Autoskala baserat på prestanda mått eller baserat på ett antal ytterligare bearbetnings enheter. Om du väljer automatisk skalning baserat på mått kan du välja mellan olika kriterier och ange tröskelvärdena för att uppfylla villkoren. Developer SKU: n inkluderar inte möjligheten att lägga till skalnings enheter.

1. Leta upp din ISE i Azure Portal.

1. Om du vill granska användnings-och prestanda mått för din ISE väljer du **Översikt**på din ISES huvud meny.

   ![Visa användning för ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Om du vill konfigurera automatisk skalning väljer du **skala ut**under **Inställningar**. På fliken **Konfigurera** väljer du **Aktivera autoskalning**.

   ![Aktivera automatisk skalning](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Ange ett namn för inställningen för **autoskalning inställnings namn**.

1. I **standard** -avsnittet väljer du antingen **skala baserat på ett mått** eller **skala till ett angivet instans antal**.

   * Om du väljer instans-baserad anger du antalet bearbetnings enheter mellan 0 och 10.

   * Följ dessa steg om du väljer Metric-baserad:

     1. I avsnittet **regler** väljer du **Lägg till en regel**.

     1. I fönstret **skalnings regel** ställer du in kriterier och åtgärder som ska vidtas när regeln utlöses.

     1. När du är klar väljer du **Lägg till**.

1. Spara ändringarna när du är klar med inställningarna för autoskalning.

## <a name="next-steps"></a>Nästa steg

* [Lägga till artefakter i integrerings tjänst miljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Kontrol lera nätverks hälsan för integrerings tjänst miljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Läs mer om [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [Virtual Network-integrering för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
