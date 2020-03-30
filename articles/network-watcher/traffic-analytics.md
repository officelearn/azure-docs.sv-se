---
title: Azure-trafikanalys | Microsoft-dokument
description: Lär dig hur du analyserar flödesloggar för Azure-nätverkssäkerhetsgrupper med trafikanalyser.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: 83164a615cacc067e5f1ea6a1dd6ce0f0fd9d540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298856"
---
# <a name="traffic-analytics"></a>Trafikanalys

Traffic Analytics är en molnbaserad lösning som ger insyn i användar- och programaktivitet i molnnätverk. Trafikanalys analyserar NSG-flödesloggar (Network Watcher Network Security Group) för att ge insikter om trafikflödet i ditt Azure-moln. Med trafikanalys kan du:

- Visualisera nätverksaktivitet över dina Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot mot och skydda nätverket, med information som öppna portar, program som försöker komma åt internet och virtuella datorer (VM) som ansluter till oseriösa nätverk.
- Förstå trafikflödesmönster i Azure-regioner och internet för att optimera nätverksdistributionen för prestanda och kapacitet.
- Identifiera nätverksdefigureringar som leder till misslyckade anslutningar i nätverket.

> [!NOTE]
> Traffic Analytics stöder nu insamling av NSG-flödesloggar med en högre frekvens på 10 minuter

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Varför trafikanalys?

Det är viktigt att övervaka, hantera och känna till ditt eget nätverk för kompromisslös säkerhet, efterlevnad och prestanda. Att känna till din egen miljö är av största vikt för att skydda och optimera den. Du behöver ofta känna till det aktuella tillståndet för nätverket, vem som ansluter, var de ansluter från, vilka portar som är öppna för internet, förväntat nätverksbeteende, oregelbundet nätverksbeteende och plötsliga trafikökningar.

Molnnätverk skiljer sig från lokala företagsnätverk, där du har netflow eller motsvarande protokollfunktioner routrar och växlar, som ger möjlighet att samla in IP-nätverkstrafik när den kommer in i eller avslutar ett nätverksgränssnitt. Genom att analysera trafikflödesdata kan du skapa en analys av nätverkstrafikflöde och volym.

Virtuella Azure-nätverk har NSG-flödesloggar som ger dig information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp som är associerad med enskilda nätverksgränssnitt, virtuella datorer eller undernät. Genom att analysera råa NSG-flödesloggar och infoga information om säkerhet, topologi och geografi kan trafikanalys ge dig insikter i trafikflödet i din miljö. Traffic Analytics tillhandahåller information som de flesta kommunicerande värdar, de flesta kommunicerande programprotokoll, de flesta samtalande värdpar, tillåten/blockerad trafik, inkommande/utgående trafik, öppna Internetportar, de flesta blockeringsregler, trafikdistribution per Azure-datacenter, virtuellt nätverk, undernät eller otillåtna nätverk.

## <a name="key-components"></a>Nyckelkomponenter

- **NSG (Network Security Group):** Innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till ett Virtuellt Azure-nätverk. Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager). Mer information finns i [Översikt över nätverkssäkerhetsgrupper](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **NSG-flödesloggar (Network Security Group):** Gör att du kan visa information om inkommande och utgående IP-trafik via en nätverkssäkerhetsgrupp. NSG-flödesloggar skrivs i json-format och visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för fem-tuppelinformation om flödet (källa/mål-IP-adress, källa/målport och protokoll) och om trafiken tilläts eller nekades. Mer information om NSG-flödesloggar finns i [NSG-flödesloggar](network-watcher-nsg-flow-logging-overview.md).
- **Logganalys:** En Azure-tjänst som samlar in övervakningsdata och lagrar data i en central databas. Dessa data kan innehålla händelser, prestandadata eller anpassade data som tillhandahålls via Azure API. När data har samlats in är de tillgängliga för avisering, analys och export. Övervakningsprogram som nätverksprestandaövervakare och trafikanalys skapas med Azure Monitor-loggar som grund. Mer information finns i [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics-arbetsyta:** En instans av Azure Monitor-loggar, där data som hör till ett Azure-konto lagras. Mer information om Log Analytics-arbetsytor finns i [Skapa en log analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: En regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksscenarionivå i Azure. Du kan aktivera och inaktivera NSG-flödesloggar med Network Watcher. Mer information finns i [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Så här fungerar trafikanalyser

Trafikanalys undersöker de råa NSG-flödesloggarna och fångar in reducerade loggar genom att samla gemensamma flöden mellan samma käll-IP-adress, mål-IP-adress, målport och protokoll. Host 1 (IP-adress: 10.10.10.10) kommunicerar till Host 2 (IP-adress: 10.10.20.10), 100 gånger under en period av 1 timme med port (till exempel 80) och protokoll (till exempel http). Den reducerade loggen har en post, som värd 1 & Host 2 kommunicerade 100 gånger under en period av 1 timme med port *80* och protokoll *HTTP*, i stället för att ha 100 poster. Reducerade loggar förbättras med information om geografi, säkerhet och topologi och lagras sedan på en Log Analytics-arbetsyta. Följande bild visar dataflödet:

![Dataflöde för bearbetning av NSG-flödesloggar](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Regioner som stöds: NSG 

Du kan använda trafikanalys för NSG:er i någon av följande regioner som stöds:

* Kanada, centrala
* USA, västra centrala
* USA, östra
* USA, östra 2
* USA, norra centrala
* USA, södra centrala
* USA, centrala
* USA, västra
* USA, västra 2
* Frankrike, centrala
* Europa, västra
* Europa, norra
* Brasilien, södra
* Storbritannien, västra
* Storbritannien, södra
* Australien, östra
* Australien, sydöstra
* Asien, östra
* Sydostasien
* Sydkorea, centrala
* Indien, centrala
* Indien, södra
* Japan, östra 
* Japan, västra
* US Gov, Virginia
* Kina Öst 2

## <a name="supported-regions-log-analytics-workspaces"></a>Regioner som stöds: Logganalysarbetsytor

Arbetsytan Log Analytics måste finnas i följande regioner:
* Kanada, centrala
* USA, västra centrala
* USA, östra
* USA, östra 2
* USA, norra centrala
* USA, södra centrala
* USA, centrala
* USA, västra
* USA, västra 2
* USA, centrala
* Frankrike, centrala
* Europa, västra
* Europa, norra
* Brasilien, södra
* Storbritannien, västra
* Storbritannien, södra
* Australien, östra
* Australien, sydöstra
* Asien, östra
* Sydostasien
* Sydkorea, centrala
* Indien, centrala
* Japan, östra
* US Gov, Virginia
* Kina Öst 2

## <a name="prerequisites"></a>Krav

### <a name="user-access-requirements"></a>Krav på användaråtkomst

Ditt konto måste vara medlem i någon av följande [azure-inbyggda roller:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

|Distributionsmodell   | Roll                   |
|---------          |---------               |
|Resource Manager   | Ägare                  |
|                   | Deltagare            |
|                   | Läsare                 |
|                   | Nätverksdeltagare    |

Om ditt konto inte har tilldelats någon av de inbyggda rollerna måste det tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som tilldelas följande åtgärder på prenumerationsnivå:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAdresser/läs"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"
- "Microsoft.Network/expressRouteCircuits/read"

Information om hur du kontrollerar behörigheter för användaråtkomst finns i [Vanliga frågor och svar om trafikanalys](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Aktivera Network Watcher

För att analysera trafiken måste du ha en befintlig nätverksbevakare eller [aktivera en nätverksbevakare](network-watcher-create.md) i varje region som du har NSGs som du vill analysera trafik för. Trafikanalys kan aktiveras för NSG-enheter som finns i någon av de [regioner som stöds](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Välj en nätverkssäkerhetsgrupp

Innan du aktiverar NSG-flödesloggning måste du ha en nätverkssäkerhetsgrupp att logga flöden för. Om du inte har någon nätverkssäkerhetsgrupp läser du [Skapa en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md#create-a-network-security-group) för att skapa en.

Gå till **Nätverksbevakaren**i Azure-portalen och välj sedan **NSG-flödesloggar**. Välj den nätverkssäkerhetsgrupp som du vill aktivera en NSG-flödeslogg för, som visas i följande bild:

![Val av NSG som kräver aktivering av NSG-flödeslogg](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Om du försöker aktivera trafikanalys för en NSG som finns i någon annan region än de [regioner som stöds](#supported-regions-nsg)visas felmeddelandet "Hittades inte".

## <a name="enable-flow-log-settings"></a>Aktivera inställningar för flödeslogg

Innan du aktiverar flödeslogginställningar måste du utföra följande uppgifter:

Registrera Azure Insights-providern om den inte redan är registrerad för din prenumeration:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Om du inte redan har ett Azure Storage-konto för att lagra NSG-flödesloggar i måste du skapa ett lagringskonto. Du kan skapa ett lagringskonto med kommandot som följer. Innan du kör `<replace-with-your-unique-storage-account-name>` kommandot, ersätt med ett namn som är unikt på alla Azure-platser, mellan 3-24 tecken i längd, med endast siffror och gemener. Du kan också ändra resursgruppsnamnet om det behövs.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Markera följande alternativ, som visas på bilden:

1. Välj *På* för **status**
2. Välj *version 2* för **flödesloggar version**. Version 2 innehåller flödessessionsstatistik (byte och paket)
3. Välj ett befintligt lagringskonto om du vill lagra flödesloggarna. Om du vill lagra data för alltid anger du värdet till *0*. Du ådrar dig Azure Storage-avgifter för lagringskontot. Kontrollera att lagringen inte har "Data Lake Storage Gen2 Hierarchical Namespace Enabled" inställd på true.
4. Ange **Kvarhållning** till det antal dagar som du vill lagra data för.
5. Välj *På* för **trafikanalysstatus**.
6. Välj bearbetningsintervall. Baserat på ditt val kommer flödesloggar att samlas in från lagringskontot och bearbetas av Traffic Analytics. Du kan välja bearbetningsintervall för varje 1 timme eller var 10:e minut. 
7. Välj en befintlig LOG Analytics -arbetsyta (OMS) eller välj **Skapa ny arbetsyta** för att skapa en ny. En Log Analytics-arbetsyta används av Traffic Analytics för att lagra de aggregerade och indexerade data som sedan används för att generera analyserna. Om du väljer en befintlig arbetsyta måste den finnas i en av de [regioner som stöds](#supported-regions-log-analytics-workspaces) och har uppgraderats till det nya frågespråket. Om du inte vill uppgradera en befintlig arbetsyta eller inte har en arbetsyta i en region som stöds skapar du en ny. Mer information om frågespråk finns i [Azure Log Analytics-uppgradering till ny loggsökning](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Arbetsytan för logganalys som är värd för trafikanalyslösningen och NSG:erna behöver inte vara i samma region. Du kan till exempel ha trafikanalyser på en arbetsyta i regionen Västeuropa, medan du kan ha NSG:er i östra USA och västra USA. Flera NSG kan konfigureras på samma arbetsyta.

8. Välj **Spara**.

    ![Val av lagringskonto, Log Analytics-arbetsyta och Traffic Analytics-aktivering](./media/traffic-analytics/ta-customprocessinginterval.png)

Upprepa föregående steg för andra NSG:er som du vill aktivera trafikanalys för. Data från flödesloggar skickas till arbetsytan, så se till att lokala lagar och förordningar i ditt land tillåter datalagring i den region där arbetsytan finns. Om du har angett olika bearbetningsintervall för olika NSG-grupper samlas data in med olika intervall. Till exempel: Du kan välja att aktivera bearbetningsintervall på 10 minuter för kritiska VNETs och 1 timme för icke-kritiska VNETs.

Du kan också konfigurera trafikanalys med [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell-cmdlet i Azure PowerShell. Kör `Get-Module -ListAvailable Az` för att hitta din installerade version. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

## <a name="view-traffic-analytics"></a>Visa trafikanalys

Om du vill visa Traffic Analytics söker du efter **Network Watcher** i sökfältet i portalen. En gång i Network Watcher, för att utforska trafikanalys och dess funktioner, välj **Traffic Analytics** på den vänstra menyn. 

![Komma åt instrumentpanelen för Traffic Analytics](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Instrumentpanelen kan ta upp till 30 minuter att visas första gången eftersom Traffic Analytics först måste aggregera tillräckligt med data för att den ska kunna härleda meningsfulla insikter innan den kan generera några rapporter.

## <a name="usage-scenarios"></a>Användningsscenarier

Några av de insikter du kanske vill få efter att Traffic Analytics har konfigurerats är fullständigt konfigurerade, är följande:

### <a name="find-traffic-hotspots"></a>Hitta trafikhotspots

**Titta efter**

- Vilka värdar, undernät och virtuella nätverk skickar eller tar emot mest trafik, passerar maximal skadlig trafik och blockerar betydande flöden?
    - Kontrollera jämförande diagram för värd, undernät och virtuellt nätverk. Om du förstår vilka värdar, undernät och virtuella nätverk som skickar eller tar emot mest trafik kan du identifiera de värdar som bearbetar mest trafik och om trafikdistributionen görs korrekt.
    - Du kan utvärdera om trafikvolymen är lämplig för en värd. Är volymen av trafik normalt beteende, eller förtjänar det ytterligare utredning?
- Hur mycket inkommande/utgående trafik finns det?
    -   Förväntas värden få mer inkommande trafik än utgående eller vice versa?
- Statistik över blockerad trafik.
    - Varför blockerar en värd en betydande mängd godartad trafik? Detta kräver ytterligare undersökning och förmodligen optimering av konfiguration
- Statistik över skadlig tillåten/blockerad trafik
  - Varför tar en värd emot skadlig trafik och varför flöden från skadlig källa är tillåtna? Detta kräver ytterligare undersökning och förmodligen optimering av konfigurationen.

    Välj **Visa alla**under **Värd**, som visas i följande bild:

    ![Instrumentpanel som visar värd med de flesta trafikinformationer](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Följande bild visar tidstrender för de fem bästa talande värdarna och flödesrelaterade detaljer (tillåtna – inkommande/utgående och nekade – inkommande/utgående flöden) för en värd:

    ![Topp fem mest talande värd trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Titta efter**

- Vilka är de mest konverserande värdparen?
    - Förväntat beteende som front-end eller back-end kommunikation eller oregelbundet beteende, som back-end internettrafik.
- Statistik över tillåten/blockerad trafik
    - Varför en värd tillåter eller blockerar betydande trafikvolym
- Vanligast förekommande programprotokoll bland de flesta samtalande värdpar:
    - Är dessa program tillåtna i det här nätverket?
    - Är programmen korrekt konfigurerade? Använder de lämpligt protokoll för kommunikation? Välj **Visa alla** under Frekvent **konversation**, som visas i följande bild:

        ![Instrumentpanel som visar de vanligaste konversationerna](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Följande bild visar tidstrender för de fem konversationerna och flödesrelaterade detaljer, till exempel tillåtna och nekade inkommande och utgående flöden för ett konversationspar:

    ![Topp fem pratsamma konversation detaljer och trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Titta efter**

- Vilket programprotokoll används mest i din miljö och vilka samtalar värdpar använder programprotokollet mest?
    - Är dessa program tillåtna i det här nätverket?
    - Är programmen korrekt konfigurerade? Använder de lämpligt protokoll för kommunikation? Förväntat beteende är vanliga portar som 80 och 443. För standardkommunikation, om ovanliga portar visas, kan de kräva en konfigurationsändring. Välj **Visa alla** under **Programport**, i följande bild:

        ![Instrumentpanel som visar de bästa programprotokollen](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Följande bilder visar tidstrender för de fem bästa L7-protokollen och de flödesrelaterade detaljerna (till exempel tillåtna och nekade flöden) för ett L7-protokoll:

    ![Detaljer och trend på fem lager i 7-skikt](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Flödesinformation för programprotokoll i loggsökning](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Titta efter**

- Kapacitetsutnyttjandetrender för en VPN-gateway i din miljö.
    - Varje VPN SKU tillåter en viss mängd bandbredd. Är VPN-gateways underutnyttjade?
    - Når dina gateways kapacitet? Ska du uppgradera till nästa högre SKU?
- Vilka är de mest samtalande värdarna, via vilken VPN-gateway, över vilken port?
    - Är det här mönstret normalt? Välj **Visa alla** under **VPN-gateway**, som visas i följande bild:

        ![Instrumentpanel som visar de bästa aktiva VPN-anslutningarna](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Följande bild visar tidstrender för kapacitetsutnyttjande av en Azure VPN Gateway och flödesrelaterade detaljer (t.ex. tillåtna flöden och portar):

    ![Trend- och flödesinformation för VPN-gateway](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualisera trafikfördelning efter geografi

**Titta efter**

- Trafikdistribution per datacenter, till exempel de främsta trafikkällorna till ett datacenter, de vanligaste oseriösa nätverken som samtalar med datacentret och de främsta samtalsprogramprotokollen.
  - Om du observerar mer belastning på ett datacenter kan du planera för effektiv trafikdistribution.
  - Om oseriösa nätverk samtalar i datacentret, korrigera sedan NSG regler för att blockera dem.

    Välj **Visa karta** under Din **miljö**, som visas i följande bild:

    ![Instrumentpanel som visar trafikdistribution](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geo-kartan visar det övre menyfliksområdet för val av parametrar som datacenter (Distribuerad/ingen distribution/Aktiv/Inaktiv/Trafikanalys Aktiverad/Trafikanalys inte aktiverad) och länder/regioner som bidrar godartad/skadlig trafik till den aktiva distributionen:

    ![Geo kartvy som visar aktiv distribution](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geo-kartan visar trafikfördelningen till ett datacenter från länder/regioner och kontinenter som kommunicerar med den i blått (Godartad trafik) och röda (skadliga trafikerar) färgade linjer:

    ![Geo kartvy som visar trafikdistribution till länder/regioner och kontinenter](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Flödesinformation för trafikdistribution i loggsökning](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualisera trafikdistribution via virtuella nätverk

**Titta efter**

- Trafikdistribution per virtuellt nätverk, topologi, de främsta trafikkällorna till det virtuella nätverket, de främsta oseriösa nätverken som samtalar med det virtuella nätverket och de främsta samtalande programprotokollen.
  - Att veta vilket virtuellt nätverk som samtalar till vilket virtuellt nätverk. Om konversationen inte förväntas kan den korrigeras.
  - Om oseriösa nätverk samtalar med ett virtuellt nätverk kan du korrigera NSG-regler för att blockera de oseriösa nätverken.
 
    Välj **Visa virtuella nätverk** under Din **miljö**, som visas i följande bild:

    ![Instrumentpanel som visar virtuell nätverksdistribution](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Den virtuella nätverkstopologin visar det övre menyfliksområdet för val av parametrar som ett virtuellt nätverk (Inter-virtuella nätverksanslutningar/Aktiva/inaktiva), externa anslutningar, aktiva flöden och skadliga flöden i det virtuella nätverket.
- Du kan filtrera topologin för virtuellt nätverk baserat på prenumerationer, arbetsytor, resursgrupper och tidsintervall. Ytterligare filter som hjälper dig att förstå flödet är: Flödestyp (InterVNet, IntraVNET och så vidare), Flödesriktning (inkommande, Utgående), Flödesstatus (tillåten, Blockerad), VNETs (Riktad och Ansluten), Anslutningstyp (Peering eller Gateway - P2S och S2S) och NSG. Använd dessa filter för att fokusera på virtuella nätverk som du vill undersöka i detalj.
- Den virtuella nätverkstopologin visar trafikdistributionen till ett virtuellt nätverk med avseende på flöden (Tillåtet/Blockerat/Inkommande/Utgående/Godartade/skadliga), programprotokoll och nätverkssäkerhetsgrupper, till exempel:

    ![Virtuell nätverkstopologi som visar trafikdistribution och flödesinformation](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtuell nätverkstopologi som visar den högsta nivån och fler filter](./media/traffic-analytics/virtual-network-filters.png)

    ![Flödesinformation för distribution av virtuell nätverkstrafik i loggsökning](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Titta efter**

- Trafikdistribution per undernät, topologi, de främsta trafikkällorna till undernätet, de vanligaste oseriösa nätverken som samtalar med undernätet och de vanligaste samtalsprogramprotokollen.
    - Att veta vilket undernät som samtalar till vilket undernät. Om du ser oväntade konversationer kan du korrigera konfigurationen.
    - Om oseriösa nätverk samtalar med ett undernät kan du korrigera det genom att konfigurera NSG-regler för att blockera de oseriösa nätverken.
- I topologin Undernät visas det övre menyfliksområdet för val av parametrar som Active/Inaktivt undernät, Externa anslutningar, Aktiva flöden och Skadliga flöden i undernätet.
- Undernätstopologin visar trafikdistributionen till ett virtuellt nätverk med avseende på flöden (tillåtet/blockerat/inkommande/utgående/godartade/skadliga), programprotokoll och NSG:er, till exempel:

    ![Nättopologi som visar trafikdistribution ett virtuellt nätverksundernät när det gäller flöden](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Titta efter**

Trafikdistribution per programgateway & belastningsutjämnare, topkällor för trafik, de viktigaste oseriösa nätverken som samtalar med application gateway-gatewayen & belastningsutjämnar och de främsta samtalande programprotokollen. 
    
 - Att veta vilket undernät som samtalar till vilken programgateway eller belastningsutjämnare. Om du observerar oväntade konversationer kan du korrigera konfigurationen.
 - Om oseriösa nätverk samtalar med en programgateway eller belastningsutjämnare kan du korrigera den genom att konfigurera NSG-regler för att blockera de oseriösa nätverken. 

    ![undernät-topologi-visa upp-trafik-distribution-till-en-application-gateway-subnet-med-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visa portar och virtuella datorer som tar emot trafik från internet

**Titta efter**

- Vilka öppna portar samtalar över internet?
  - Om oväntade portar hittas öppna kan du korrigera konfigurationen:

    ![Instrumentpanel som visar portar som tar emot och skickar trafik till internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Information om Azure-målportar och värdar](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Titta efter**

Har du skadlig trafik i din miljö? Var kommer det ifrån? Var är den avsedd?

![Information om skadlig trafik flödar i loggsökning](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualisera trenderna i NSG / NSG regler träffar

**Titta efter**

- Vilka NSG/NSG-regler har flest träffar i jämförande diagram med flödesfördelning?
- Vilka är de bästa käll- och målkonversationsparen per NSG/NSG-regler?

    ![Dashboard visar NSG träffar statistik](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Följande bilder visar tidstrender för träffar av NSG-regler och information om källmålsflöde för en nätverkssäkerhetsgrupp:

  - Snabbt upptäcka vilka NSGs och NSG regler korsar skadliga flöden och vilka som är de bästa skadliga IP-adresser som kommer åt din molnmiljö
  - Identifiera vilka NSG/NSG-regler som tillåter/blockerar betydande nättrafik
  - Välj toppfilter för detaljerad inspektion av NSG- eller NSG-regler

    ![Visa upp tidstrender för NSG-regelträffar och topp-NSG-regler](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Top NSG regler statistik detaljer i loggsökning](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Mer om du vill få svar på vanliga frågor och svar finns i [Vanliga frågor och svar om trafikanalys](traffic-analytics-faq.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du aktiverar flödesloggar finns i [Aktivera NSG-flödesloggning](network-watcher-nsg-flow-logging-portal.md).
- Information om hur du förstår schema och bearbetningsinformation för Traffic Analytics finns i [Trafikanalysschema](traffic-analytics-schema.md).
