---
title: Ansluta till virtuella Azure-nätverk med en ISE
description: Skapa en integrationstjänstmiljö (ISE) som kan komma åt virtuella Azure-nätverk (VNETs) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478880"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ansluta till virtuella Azure-nätverk från Azure Logic Apps med hjälp av en integrationstjänstmiljö (ISE)

I scenarier där dina logikappar och integrationskonton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md)skapar du en [ *integrationstjänstmiljö* (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) En ISE är en isolerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den "globala" Logic Apps-tjänsten för flera innehavare. Den här separationen minskar också all påverkan som andra Azure-klienter kan ha på dina appars prestanda. En ISE ger dig också dina egna statiska IP-adresser. Dessa IP-adresser är åtskilda från de statiska IP-adresser som delas av logikapparna i den offentliga tjänsten med flera innehavare.

När du skapar en ISE *injicerar* Azure det ISE i ditt virtuella Azure-nätverk, som sedan distribuerar Logic Apps-tjänsten till ditt virtuella nätverk. När du skapar en logikapp eller ett integrationskonto väljer du ISE som plats. Logikappen eller integrationskontot kan sedan komma åt resurser direkt, till exempel virtuella datorer, servrar, system och tjänster i det virtuella nätverket.

![Välj integrationstjänstmiljö](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> För att logikappar och integrationskonton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som sin plats.

En ISE har ökat gränserna för körningstid, lagringskvarhållning, dataflöde, HTTP-begärande- och svarstidsgränser, meddelandestorlekar och anpassade anslutningsbegäranden. Mer information finns i [Gränser och konfiguration för Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md). Mer information om ISE finns i [Åtkomst till virtuella Azure-nätverksresurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

I den här artikeln beskrivs hur du slutför dessa uppgifter med hjälp av Azure-portalen:

* Aktivera åtkomst för din ISE.
* Skapa din ISE.
* Lägg till extra kapacitet till din ISE.

Du kan också skapa en ISE med hjälp av [snabbstartsmallen för Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) eller med hjälp av REST-API:et för Logic Apps, inklusive att konfigurera kundhanterade nycklar:

* [Skapa en integrationstjänstmiljö (ISE) med hjälp av REST-API:et för Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md)
* [Konfigurera kundhanterade nycklar för att kryptera data i vila för ISE:er](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logikappar, inbyggda utlösare, inbyggda åtgärder och kopplingar som körs i ISE använder en prisplan som skiljer sig från den förbrukningsbaserade prisplanen. Mer information om hur prissättning och fakturering fungerar för ISE finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prispriser finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

* Ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du inte har ett virtuellt nätverk kan du läsa om hur du [skapar ett virtuellt Azure-nätverk](../virtual-network/quick-create-portal.md).

  * Det virtuella nätverket måste ha fyra *tomma* undernät för att skapa och distribuera resurser i ISE. Varje undernät stöder en annan Logic Apps-komponent som används i DIN ISE. Du kan skapa dessa undernät i förväg, eller så kan du vänta tills du skapar din ISE där du kan skapa undernät samtidigt. Läs mer om [krav på undernät](#create-subnet).

  * Undernätsnamn måste börja med antingen ett alfabebetade tecken eller `<` `>`ett `%` `&`understreck och kan inte använda dessa tecken: , , , , `\\`, , `?`, `/`. 
  
  * Om du vill distribuera ISE via en Azure Resource Manager-mall kontrollerar du först att du delegerar ett tomt undernät till Microsoft.Logic/integrationServiceEnvironment. Du behöver inte göra den här delegeringen när du distribuerar via Azure-portalen.

  * Kontrollera att ditt virtuella nätverk [ger åtkomst för din ISE](#enable-access) så att din ISE kan fungera korrekt och vara tillgänglig.

  * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md), som tillhandahåller en privat anslutning till Microsofts molntjänster som underlättas av anslutningsleverantören, måste du [skapa en vägtabell](../virtual-network/manage-route-table.md) som har följande väg och länka tabellen till varje undernät som används av DIN ISE:

    **Namn**: <*ruttnamn*><br>
    **Adressprefix**: 0.0.0.0/0<br>
    **Nästa hopp:** Internet

* Om du vill använda anpassade DNS-servrar för ditt virtuella Azure-nätverk [konfigurerar du dessa servrar genom att följa dessa steg](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) innan du distribuerar ISE till ditt virtuella nätverk. Mer information om hur du hanterar DNS-serverinställningar finns i [Skapa, ändra eller ta bort ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Om du ändrar DNS-serverns eller DNS-serverinställningarna måste du starta om ISE:et så att ISE kan ta upp ändringarna. Mer information finns i [Starta om ISE.](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Aktivera åtkomst för ISE

När du använder en ISE med ett virtuellt Azure-nätverk är ett vanligt installationsproblem att ha en eller flera blockerade portar. De kopplingar som du använder för att skapa anslutningar mellan ISE- och målsystemen kan också ha egna portkrav. Om du till exempel kommunicerar med ett FTP-system med hjälp av FTP-anslutningen måste porten som du använder på FTP-systemet vara tillgänglig, till exempel port 21 för att skicka kommandon.

Om du vill vara säker på att ISE är tillgängligt och att logikapparna i ise:et kan kommunicera över varje undernät i det virtuella nätverket [öppnar du portarna som beskrivs i den här tabellen för varje undernät](#network-ports-for-ise). Om några nödvändiga portar inte är tillgängliga fungerar inte ISE korrekt.

* Om du har flera ISE-instanser som behöver åtkomst till andra slutpunkter som har IP-begränsningar, distribuerar du en [Azure-brandvägg](../firewall/overview.md) eller en [virtuell nätverksinstallation](../virtual-network/virtual-networks-overview.md#filter-network-traffic) i ditt virtuella nätverk och dirigerar utgående trafik genom brandväggen eller den virtuella nätverksinstallationen. Du kan sedan [ställa in en enda, utgående, offentlig, statisk och förutsägbar IP-adress](connect-virtual-network-vnet-set-up-single-ip-address.md) som alla ISE-instanser i det virtuella nätverket kan använda för att kommunicera med målsystem. På så sätt behöver du inte ställa in ytterligare brandväggsöppningar på dessa målsystem för varje ISE.

   > [!NOTE]
   > Du kan använda den här metoden för en enda ISE när ditt scenario kräver att begränsa antalet IP-adresser som behöver åtkomst. Fundera över om de extra kostnaderna för brandväggen eller den virtuella nätverksinstallationen är meningsfulla för ditt scenario. Läs mer om [prissättning av Azure-brandväggar](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Om du har skapat ett nytt virtuellt Azure-nätverk och -undernät utan några begränsningar behöver du inte konfigurera [nätverkssäkerhetsgrupper (NSG)](../virtual-network/security-overview.md#network-security-groups) i det virtuella nätverket för att styra trafiken över undernät.

* I ett *befintligt virtuellt* nätverk kan du också ställa in NSG:er genom [att filtrera nätverkstrafik över undernät](../virtual-network/tutorial-filter-network-traffic.md). Om du vill gå den här vägen, eller om du redan använder NSG, se till att du [öppnar portarna i](#network-ports-for-ise) den här tabellen i det virtuella nätverket där du har NSGs eller vill ställa in NSG.

  > [!NOTE]
  > Om du använder [NSG-säkerhetsregler](../virtual-network/security-overview.md#security-rules)måste du använda *både* TCP- och UDP-protokollen. NSG-säkerhetsregler beskriver de portar som du måste öppna för IP-adresser som behöver åtkomst till dessa portar. Kontrollera att alla brandväggar, routrar eller andra objekt som finns mellan dessa slutpunkter också håller dessa portar tillgängliga för dessa IP-adresser.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Nätverksportar som används av ISE

I den här tabellen beskrivs portarna i det virtuella Azure-nätverket som din ISE använder och var dessa portar används. För att minska komplexiteten när du skapar säkerhetsregler representerar [tjänsttaggarna](../virtual-network/service-tags-overview.md) i tabellen grupper av IP-adressprefix för en viss Azure-tjänst.

> [!IMPORTANT]
> Källportar är tillfälliga, så se till `*` att du ställer in dem för alla regler. Där det noteras hänvisar interna ISE och externa ISE till [slutpunkten som har valts när ISE skapas](connect-virtual-network-vnet-isolated-environment.md#create-environment). Mer information finns i [Slutpunktsåtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Syfte | Riktning | Målportar | Servicetag för källa | Måltjänsttagg | Anteckningar |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Intersubnet-kommunikation inom ditt virtuella nätverk | Inkommande & utgående | * | Adressutrymmet för det virtuella nätverket som har ISE:s undernät | Adressutrymmet för det virtuella nätverket som har ISE:s undernät | Krävs för att trafiken ska kunna flöda *mellan* undernäten i det virtuella nätverket. <p><p>**Viktigt:** För trafik som ska flöda mellan *komponenterna* i varje undernät, se till att du öppnar alla portar i varje undernät. |
| Kommunikation till logikappen | Inkommande | 443 | Intern ISE: <br>VirtualNetwork <p><p>Extern ISE: <br>Internet <br>(se kolumnen **Anteckningar)** | VirtualNetwork | I stället **Internet** för att använda Internet-tjänsttaggen kan du ange käll-IP-adressen för den dator eller tjänst som anropar alla begärandeutlösare eller webhooks i logikappen. <p><p>**Viktigt:** Om du stänger eller blockerar den här porten förhindras HTTP-anrop till logikappar som har utlösare för begäran. |
| Historik för körning av logikapp | Inkommande | 443 | Intern ISE: <br>VirtualNetwork <p><p>Extern ISE: <br>Internet <br>(se kolumnen **Anteckningar)** | VirtualNetwork | I stället **Internet** för att använda Internet-tjänsttaggen kan du ange käll-IP-adressen för den dator eller tjänst där du vill visa logikappens körningshistorik. <p><p>**Viktigt:** Även om det inte hindrar dig från att visa körningshistoriken om du stänger eller blockerar den här porten, kan du inte visa indata och utdata för varje steg i körningshistoriken. |
| Logic Apps Designer - dynamiska egenskaper | Inkommande | 454 | LogicAppsManagement | VirtualNetwork | Begäranden kommer från Logic Apps-åtkomsten till [inkommande](../logic-apps/logic-apps-limits-and-config.md#inbound) IP-adresser för den regionen. |
| Anslutningsdistribution | Inkommande | 454 | AzureConnectors | VirtualNetwork | Krävs för att distribuera och uppdatera kopplingar. Om du stänger eller blockerar den här porten misslyckas ISE-distributioner och anslutningsuppdateringar eller korrigeringar förhindras. |
| Hälsokontroll av nätverk | Inkommande | 454 | LogicApps | VirtualNetwork | Begäranden kommer från slutpunkten för Logic Apps-åtkomst för både [inkommande](../logic-apps/logic-apps-limits-and-config.md#inbound) och [utgående](../logic-apps/logic-apps-limits-and-config.md#outbound) IP-adresser för den regionen. |
| Beroende av apptjänsthantering | Inkommande | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Kommunikation från Azure Traffic Manager | Inkommande | Intern ISE: 454 <p><p>Extern ISE: 443 | AzureTrafficManager | VirtualNetwork | |
| API Management - hanteringslutpunkt | Inkommande | 3443 | APIHanagement | VirtualNetwork | |
| Distribution av anslutningsprincip | Inkommande | 3443 | APIHanagement | VirtualNetwork | Krävs för att distribuera och uppdatera kopplingar. Om du stänger eller blockerar den här porten misslyckas ISE-distributioner och anslutningsuppdateringar eller korrigeringar förhindras. |
| Kommunikation från logikappen | Utgående | 80, 443 | VirtualNetwork | Varierar beroende på destination | Slutpunkterna för den externa tjänst som logikappen behöver kommunicera med. |
| Azure Active Directory | Utgående | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Hantering av anslutningar | Utgående | 443 | VirtualNetwork  | AppService | |
| Publicera diagnostikloggar & mått | Utgående | 443 | VirtualNetwork  | AzureMonitor (På andra) | |
| Azure Storage-beroende | Utgående | 80, 443, 445 | VirtualNetwork | Storage | |
| Azure SQL-beroende | Utgående | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Utgående | 1886 | VirtualNetwork | AzureMonitor (På andra) | Krävs för att publicera hälsostatus till Resurshälsa |
| Beroende från logg till händelsehubbprincip och övervakningsagent | Utgående | 5672 | VirtualNetwork | EventHub | |
| Komma åt Azure-cache för Redis-instanser mellan rollinstanser | Inkommande <br>Utgående | 6379 - 6383 | VirtualNetwork | VirtualNetwork | För att ISE ska fungera med Azure Cache för Redis måste du också öppna dessa [utgående och inkommande portar som beskrivs i vanliga frågor och svar om Azure Cache för Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Skapa din ISE

1. I [Azure-portalen](https://portal.azure.com)anger du `integration service environments` som filter i den viktigaste Sökrutan i Azure och väljer **Integrationstjänstmiljöer**.

   ![Hitta och välj "Integrationstjänstmiljöer"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Välj **Lägg till**i fönstret **Integrationstjänstmiljöer** .

   ![Hitta och välj "Integrationstjänstmiljöer"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Ange dessa uppgifter för din miljö och välj sedan **Granska + skapa,** till exempel:

   ![Ange miljöinformation](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Prenumeration** | Ja | <*Azure-prenumeration-namn*> | Azure-prenumerationen som ska användas för din miljö |
   | **Resursgrupp** | Ja | <*Azure-resource-group-namn*> | En ny eller befintlig Azure-resursgrupp där du vill skapa din miljö |
   | **Namn på integrationstjänstmiljö** | Ja | <*miljönamn*> | ISE-namnet, som bara kan innehålla bokstäver,`-`siffror, bindestreck ( ), understreck (`_`) och punkter (`.`). |
   | **Location** | Ja | <*Azure-datacenter-region*> | Azure datacenter region där att distribuera din miljö |
   | **Sku** | Ja | **Premium** eller **utvecklare (inget serviceavtal)** | ISE SKU att skapa och använda. Skillnader mellan dessa SKU:er finns i [ISE SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Viktigt:** Det här alternativet är endast tillgängligt när ISE skapas och kan inte ändras senare. |
   | **Ytterligare kapacitet** | Premium: <br>Ja <p><p>Författare: <br>Inte tillämpligt | Premium: <br>0 till 10 <p><p>Författare: <br>Inte tillämpligt | Antalet ytterligare bearbetningsenheter som ska användas för den här ISE-resursen. Mer om du vill lägga till kapacitet när du har skapat den finns [i Lägga till ISE-kapacitet](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Slutpunkt för Åtkomst** | Ja | **Interna** eller **externa** | Den typ av åtkomstslutpunkter som ska användas för ISE. Dessa slutpunkter avgör om begäran eller webhook-utlösare på logikappar i DIN ISE kan ta emot samtal utanför det virtuella nätverket. <p><p>Ditt val påverkar också hur du kan visa och komma åt indata och utdata i logikappens historik. Mer information finns i [ISE-slutpunktsåtkomst](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Viktigt:** Det här alternativet är endast tillgängligt när ISE skapas och kan inte ändras senare. |
   | **Virtuellt nätverk** | Ja | <*Azure-virtual-network-namn*> | Det virtuella Azure-nätverk där du vill injicera din miljö så att logikappar i den miljön kan komma åt ditt virtuella nätverk. Om du inte har ett nätverk [skapar du först ett virtuellt Azure-nätverk](../virtual-network/quick-create-portal.md). <p><p>**Viktigt:** Du kan *bara* utföra denna injektion när du skapar din ISE. |
   | **Undernät** | Ja | <*lista över nätresurser*> | En ISE kräver fyra *tomma* undernät för att skapa och distribuera resurser i din miljö. Om du vill skapa varje undernät [följer du stegen under den här tabellen](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Skapa undernät**

   För att skapa och distribuera resurser i din miljö behöver DIN ISE fyra *tomma* undernät som inte delegeras till någon tjänst. Varje undernät stöder en annan Logic Apps-komponent som används i DIN ISE. Du *kan inte* ändra dessa undernätsadresser när du har skapat din miljö. Varje undernät måste uppfylla dessa krav:

   * Har ett namn som börjar med ett alfabetiskt tecken eller ett understreck (inga `\\` `?`tal) och inte använder dessa tecken: `<`, `>`, `%`, `&`, , , `/`.

   * Använder [CIDR-formatet (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) och ett klass B-adressutrymme.

   * Använder minst `/27` en i adressutrymmet eftersom varje undernät kräver minst 32 adresser *.* Ett exempel:

     * `10.0.0.0/28`har bara 16 adresser och är för liten eftersom 2<sup>(32-28)</sup> är 2<sup>4</sup> eller 16.

     * `10.0.0.0/27`har 32 adresser eftersom 2<sup>(32-27)</sup> är 2<sup>5</sup> eller 32.

     * `10.0.0.0/24`har 256 adresser eftersom 2<sup>(32-24)</sup> är 2<sup>8</sup> eller 256. Fler adresser ger dock inga ytterligare fördelar.

     Mer information om hur du beräknar adresser finns i [IPv4 CIDR-block](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md)måste du [skapa en flödestabell](../virtual-network/manage-route-table.md) som har följande väg och länka tabellen till varje undernät som används av ISE:

     **Namn**: <*ruttnamn*><br>
     **Adressprefix**: 0.0.0.0/0<br>
     **Nästa hopp:** Internet

   1. Välj **Hantera undernätskonfiguration**under listan **Undernät** .

      ![Hantera undernätskonfiguration](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Välj **Undernät**i fönstret **Undernät** .

      ![Lägga till fyra tomma undernät](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Ange den här informationen i fönstret **Lägg till undernät.**

      * **Namn**: Namnet på ditt undernät
      * **Adressintervall (CIDR-block):** Undernätets område i ditt virtuella nätverk och i CIDR-format

      ![Lägga till information om undernät](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. När du är klar väljer du **Ok**.

   1. Upprepa dessa steg för ytterligare tre undernät.

      > [!NOTE]
      > Om de undernät du försöker skapa inte är giltiga visar Azure-portalen ett meddelande, men blockerar inte dina framsteg.

   Mer information om hur du skapar undernät finns i [Lägga till ett virtuellt nätverksundernät](../virtual-network/virtual-network-manage-subnet.md).

1. När Azure har validerat din ISE-information väljer du **Skapa**, till exempel:

   ![När valideringen har slutförts väljer du "Skapa"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure börjar distribuera din miljö, vilket vanligtvis tar inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrollera distributionsstatus väljer du ikonen meddelanden i verktygsfältet i Azure, som öppnar meddelandefönstret.

   ![Kontrollera distributionsstatus](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Om distributionen har slutförts visar Azure det här meddelandet:

   ![Distributionen lyckades](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Annars följer du Azure-portalens instruktioner för felsökning av distributionen.

   > [!NOTE]
   > Om distributionen misslyckas eller om du tar bort din ISE kan Det ta upp till en timme innan du släpper dina undernät. Den här fördröjningen innebär att du kanske måste vänta innan du återanvänder dessa undernät i en annan ISE.
   >
   > Om du tar bort det virtuella nätverket tar Azure vanligtvis upp till två timmar innan du släpper upp dina undernät, men den här åtgärden kan ta längre tid. 
   > När du tar bort virtuella nätverk kontrollerar du att inga resurser fortfarande är anslutna. 
   > Se [Ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Om du vill visa din miljö väljer du **Gå till resurs** om Azure inte automatiskt går till din miljö när distributionen är klar.

1. Mer om du vill kontrollera nätverkshälsan för DIN ISE finns i [Hantera din integrationstjänstmiljö](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Information om hur du börjar skapa logikappar och andra artefakter i ISE finns i [Lägga till resurser i integrationstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Hanterade ISE-kopplingar som blir tillgängliga när du har skapat ISE visas inte automatiskt i anslutningsväljaren på Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du [manuellt lägga till dessa kopplingar i ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) så att de visas i Logic App Designer.

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integrationstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Läs mer om [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md)
* Lär dig mer om [integrering av virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md)
