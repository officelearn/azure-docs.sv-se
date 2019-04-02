---
title: Azure trafikanalys | Microsoft Docs
description: Lär dig hur du analyserar Azure-nätverk flödesloggar för nätverkssäkerhetsgruppen med trafikanalys.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;jdial
ms.openlocfilehash: ac4351bd2e125c922cb3044c1d06298b3ad6de97
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805065"
---
# <a name="traffic-analytics"></a>Trafikanalys

Trafikanalys är en molnbaserad lösning som ger insyn i användar- och aktiviteter i molnnätverk. Trafikanalys analyserar flödesloggar för Network Watcher network security group (NSG) för att ge insikter om trafikflöden i Azure-molnet. Med trafikanalys kan du:

- Visualisera nätverksaktivitet för alla Azure-prenumerationer och identifiera aktiva punkter.
- Identifiera säkerhetshot till och skydda ditt nätverk, med information, till exempel öppna portar, program som försöker Internetåtkomst och virtuella datorer (VM) som ansluter till obehöriga nätverk.
- Förstå trafikflödesmönster mellan Azure-regioner och internet för att optimera din nätverksdistribution för prestanda och kapacitet.
- Identifiera nätverket felkonfigurationer som leder till misslyckade anslutningar i nätverket.

## <a name="why-traffic-analytics"></a>Varför traffic analytics?

Det är viktigt att övervaka, hantera och vet ditt eget nätverk för kompromisslös säkerhet, kompatibilitet och prestanda. Är av yttersta vikt att skydda och optimera den att känna till din egen miljö. Du behöver ofta veta det aktuella tillståndet för nätverket som ansluter, där de ansluter från, vilka portar är öppna till internet, förväntade nätverksproblem oregelbunden nätverksproblem, och plötsliga ökningar trafik.

Molnnätverk skiljer sig från den lokala företagets nätverk, där du har netflow eller motsvarande protokollet kompatibla routrar och växlar, som erbjuder möjligheten att samla in IP-nätverkstrafik när den anländer till eller lämnar ett nätverksgränssnitt. Du kan skapa en analys av flödet i nätverkstrafiken och volym genom att analysera trafikdata för flödet.

Azure-nätverk har NSG-flödesloggar som ger dig information om inkommande och utgående IP-trafik via en Nätverkssäkerhetsgrupp som är kopplade till enskilda nätverkskort, virtuella datorer eller undernät. Genom att analysera raw NSG-flödesloggar och lägga till intelligens av säkerhet, topologi och geografi, trafik ger analytics dig insikter om trafikflöden i din miljö. Trafikanalys ger dig information som mest kommunicerar värdar, mest kommunicerar programprotokoll, mest konverserande värdepar, tillåtna/blockerade trafik, inkommande/utgående trafik, öppna internet-portar, mest blockerande regler, trafik distribution per Azure-datacenter, virtuellt nätverk, undernät, eller obehöriga nätverk.

## <a name="key-components"></a>Nyckelkomponenter

- **Nätverkssäkerhetsgrupp (NSG)**: Innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till en Azure-nätverk. Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager). Mer information finns i [översikt över Network security group](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network flödesloggar för nätverkssäkerhetsgruppen (NSG)**: Kan du visa information om ingående och utgående IP-trafik via en nätverkssäkerhetsgrupp. NSG flow loggarna skrivs i json-format och visa utgående och inkommande flöden på basis av per regel, NIC flödet som gäller för fem-tuppel information om flödet (källa/mål-IP-adress, källa/mål port och protokoll) och om trafiken tilläts eller inte. Läs mer om NSG-flödesloggar [NSG-flödesloggar](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: En Azure-tjänst som samlar in övervakningsdata och lagrar data i ett centrallager. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API i Azure. När data har samlats in är de tillgängliga för avisering, analys och export. Övervaka som network monitor och trafik prestandaanalys skapas med hjälp av Azure Monitor-loggar som grund. Mer information finns i [Azure Monitor loggar](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Log Analytics-arbetsyta**: En instans av Azure Monitor-loggar, där de data som hör till ett Azure-konto lagras. Läs mer om Log Analytics-arbetsytor, [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: En regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå i Azure. Du kan stänga NSG-flödesloggar och inaktivera med Network Watcher. Mer information finns i [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Så här fungerar trafikanalys

Trafikanalys undersöker raw NSG-flödesloggar och samlar in minskade loggarna genom att sammanställa vanliga flöden mellan samma käll-IP-adress, målets IP-adress, målport och protokoll. Till exempel värddator 1 (IP-adress: 10.10.10.10) kommunicera värddator 2 (IP-adress: 10.10.20.10), 100 gånger under en period av 1 timme med port (till exempel 80) och protokoll (till exempel http). Minskad loggen har en post som värddator 1 och värddator 2 förmedlas 100 gånger under en period på 1 timme som använder port *80* och protokoll *HTTP*, i stället för att 100 poster. Minskad loggar är förbättrad med geografisk plats, säkerhet och topologiinformation och lagras sedan i Log Analytics-arbetsytan. Följande bild visar dataflödet:

![Dataflödet för bearbetning av NSG-flödesloggar](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regioner som stöds

Du kan använda trafikanalys för NSG: er i någon av de följande regionerna som stöds:

* Centrala Kanada
* Västra centrala USA
* Östra USA
* USA, östra 2
* Norra centrala USA
* Södra centrala USA
* Centrala USA
* Västra USA
* Västra USA 2
* Frankrike, centrala
* Västra Europa
* Norra Europa
* Södra Brasilien
* Storbritannien, västra
* Storbritannien, södra
* Östra Australien
* Sydöstra Australien
* Östasien
* Sydostasien
* Sydkorea, centrala
* Indien, centrala
* Södra Indien
* Östra Japan 
* Västra Japan
* Virginia (USA-förvaltad region)

Log Analytics-arbetsytan måste finnas i följande regioner:
* Centrala Kanada
* Västra centrala USA
* Västra USA 2
* Östra USA
* Frankrike, centrala
* Västra Europa
* Storbritannien, södra
* Sydöstra Australien
* Sydostasien
* Sydkorea, centrala
* Indien, centrala
* Östra Japan
* Virginia (USA-förvaltad region)

## <a name="prerequisites"></a>Förutsättningar

### <a name="user-access-requirements"></a>Krav för åtkomst av användare

Ditt konto måste vara medlem i någon av följande Azure [inbyggda roller](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Distributionsmodell   | Roll                   |
|---------          |---------               |
|Resource Manager   | Ägare                  |
|                   | Deltagare            |
|                   | Läsare                 |
|                   | Nätverksdeltagare    |

Om ditt konto inte har tilldelats till en av de inbyggda rollerna, så måste de tilldelas en [anpassad roll](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) som har tilldelats följande åtgärder på prenumerationsnivå:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Information om hur du kontrollerar användarbehörigheter finns i [Traffic analytics vanliga frågor och svar](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Aktivera Network Watcher

Om du vill analysera trafik, måste du ha en befintlig nätverksbevakaren eller [aktivera en network watcher](network-watcher-create.md) i varje region där NSG: er som du vill analysera trafik, för. Trafikanalys kan aktiveras för NSG: er som finns i någon av de [regioner som stöds](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Omregistrera provider för nätverksresurser

Innan du kan använda trafikanalys, måste du omregistrera din provider för nätverksresurser. Klicka på **prova** i kodrutan följande för att öppna Azure Cloud Shell. Cloud Shell loggar du in på din Azure-prenumeration. När Cloud Shell är öppet, anger du följande kommando för att registrera nätverksresursprovidern:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Välj en grupp

Du måste ha en nätverkssäkerhetsgrupp för att logga flöden för innan du aktiverar NSG flow loggning. Om du inte har en nätverkssäkerhetsgrupp kan se [skapa en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md#create-a-network-security-group) att skapa en.

På vänster sida av Azure-portalen, väljer **övervakaren**, sedan **nätverksbevakaren**, och välj sedan **NSG-flödesloggar**. Välj den nätverkssäkerhetsgrupp som du vill aktivera en NSG flöde du vill ha, enligt följande bild:

![Val av NSG: er som kräver aktivering av NSG flödeslogg](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Om du försöker aktivera trafikanalys för NSG: N som finns i alla regioner utom den [regioner som stöds](#supported-regions), felmeddelandet ”hittades inte”.

## <a name="enable-flow-log-settings"></a>Aktivera flöde logginställningar

Innan du aktiverar logginställningar för flödet, måste du utföra följande uppgifter:

Registrera Azure Insights-providern, om den inte redan är registrerad för din prenumeration:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Om du inte redan har ett Azure Storage-konto för att lagra NSG flödet loggar in, måste du skapa ett lagringskonto. Du kan skapa ett lagringskonto med kommandot nedan. Ersätt innan du kör kommandot `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt i alla Azure-platser, mellan 3 och 24 tecken långt, med hjälp av endast siffror och gemener. Du kan också ändra resursgruppens namn, om det behövs.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Välj följande alternativ som visas på bild:

1. Välj *på* för **Status**
2. Välj ett befintligt lagringskonto för att lagra flödesloggar i. Om du vill lagra data alltid ange värdet till *0*. Du använder Azure Storage-avgifter för storage-konto.
3. Ange **kvarhållning** för hur många dagar som du vill lagra data för.
4. Välj *på* för **Traffic Analytics-Status**.
5. Välj en befintlig Log Analytics-arbetsyta eller välj **Skapa ny arbetsyta** att skapa en ny. En Log Analytics-arbetsyta används av trafikanalys för att lagra de aggregerade och indexerade data som används för att generera analyser. Om du väljer en befintlig arbetsyta måste finnas i något av regionerna som stöds och har uppgraderats till det nya frågespråket. Om du inte vill uppgradera en befintlig arbetsyta eller inte har en arbetsyta i en region som stöds, kan du skapa en ny. Läs mer om frågespråk [Azure Monitor loggar uppgraderingen till ny loggsökning](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Log Analytics-arbetsytan som är värd för analyslösning trafik och NSG: erna behöver inte finnas i samma region. Du kan till exempel ha trafikanalys i en arbetsyta i regionen Europa, västra, medan du kan ha NSG: er i östra USA och västra USA. Flera NSG: er kan konfigureras på samma arbetsyta.
6. Välj **Spara**.

    ![Valet av lagringskonto, Log Analytics-arbetsyta och aktivering av trafikanalys](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Upprepa föregående steg för alla andra NSG: er som du vill aktivera trafikanalys för. Data från flödesloggar skickas till arbetsytan, så se till att lokala lagar och bestämmelser i ditt land tillåter lagring av data i den region där arbetsytan finns.

Du kan också konfigurera traffic analytics med hjälp av den [Set-AzureRmNetworkWatcherConfigFlowLog](/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog) PowerShell-cmdlet i AzureRm PowerShell-Modulversion 6.2.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="view-traffic-analytics"></a>Visa trafikanalys

På vänster sida av portalen, väljer **alla tjänster**, ange sedan *övervakaren* i den **Filter** box. När **övervakaren** visas i sökresultatet väljer du det. Om du vill börja utforska trafikanalys och dess funktioner, Välj **nätverksbevakaren**, sedan **trafikanalys**.

![Åtkomst till instrumentpanelen för trafikanalys](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Instrumentpanelen kan ta upp till 30 minuter att visas första gången eftersom trafikanalys måste först aggregera tillräckligt med data att härleda meningsfulla insikter innan det kan generera rapporter.

## <a name="usage-scenarios"></a>Användningsscenarier

Några av de insikter som du kanske vill få när konfigurationen är slutförd trafikanalys är följande:

### <a name="find-traffic-hotspots"></a>Hitta trafik-anslutningar

**Titta efter**

- Vilka värdar, undernät och virtuella nätverk är skicka eller ta emot de flesta trafik, gå igenom maximala skadlig trafik och blockerar betydande flöden?
    - Kontrollera jämförande diagram för värden, undernät och virtuellt nätverk. Förstå vilka värdar, undernät och virtuella nätverk skicka eller ta emot de mest trafik kan hjälpa dig att identifiera de värdar som bearbetar mest trafik, och om trafikfördelning görs korrekt.
    - Du kan utvärdera om mängden trafik som är lämpligt för en värd. Är mängden trafik normalt eller den värda ytterligare undersökning?
- Hur mycket inkommande/utgående trafik finns det?
    -   Förväntas värden får mer inkommande trafik än utgående eller vice versa?
- Statistik över blockerad trafik.
    - Varför en värd blockerar en betydande mängd ofarlig trafik? Detta kräver ytterligare undersökning och förmodligen optimering av konfigurationen
- Statistik över skadlig trafik för tillåtna/blockerade
  - Varför får en värd skadlig trafik och varför det är tillåtet att flöden från skadliga källa? Detta kräver ytterligare undersökning och förmodligen optimering av konfigurationen.

    Välj **se alla**under **värden**, enligt följande bild:

    ![Instrumentpanelen visar värden med de flesta trafik-information](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Följande bild visar tiden trender för de översta fem prata värdarna och flow-relaterad information (tillåtna – inkommande/utgående och Nekade - inkommande/utgående flöden) för en värd:

    ![Fem främsta pratar med de flesta värden trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Titta efter**

- Vilka är de mest konverserande värdepar?
    - Förväntat beteende som klient- eller backend-kommunikation eller oregelbunden beteenden, som backend-Internettrafik.
- Statistik över tillåtna/blockerade trafik
    - Varför en värd att tillåta eller blockera betydande trafikvolym
- Oftast används programprotokoll bland de flesta konverserande värdepar:
    - Dessa program är tillåtna i det här nätverket?
    - Är program korrekt konfigurerade? Använder de rätt protokoll för kommunikation? Välj **se alla** under **frekventa konversationen**, som visas i följande bild:

        ![Förkonfigurerad vanligaste konversationen instrumentpanel](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Följande bild visar tiden trender för de fem främsta konversationerna och flow-relaterade informationen som tillåtna respektive Nekade inkommande och utgående flöden för en konversation-par:

    ![Fem främsta trafikintensiva konversationen information och trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Titta efter**

- Vilka protokoll används mest i din miljö och vilka konverserande värdepar använder mest programprotokoll?
    - Dessa program är tillåtna i det här nätverket?
    - Är program korrekt konfigurerade? Använder de rätt protokoll för kommunikation? Förväntat beteende är vanliga portar, till exempel 80 och 443. För standard-kommunikation om ovanliga portar visas, krävs en konfigurationsändring. Välj **se alla** under **programporten**, på följande bild:

        ![Förkonfigurerad främsta programprotokoll instrumentpanel](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Följande bilder visar tiden trender för de fem främsta L7-protokollen och flow-relaterad information (till exempel tillåtna respektive nekade flöden) för en L7-protokoll:

    ![Fem viktigaste layer 7 protokoll information och trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Flow-information för protokoll i loggsökning](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Titta efter**

- Kapacitetsutnyttjande trender för en VPN-gateway i din miljö.
    - Varje VPN SKU kan en viss mängd bandbredd. Är underutnyttjade VPN-gatewayer?
    - Din gateway ansluter till kapacitet? Bör du uppgradera till nästa högre SKU?
- Vilka är de mest konverserande värdarna via vilken VPN-gateway, via vilken port?
    - Är det här mönstret normalt? Välj **se alla** under **VPN-gateway**, enligt följande bild:

        ![Instrumentpanelen visar främsta aktiva VPN-anslutningar](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Följande bild visar tiden trender för kapacitetsanvändning för Azure VPN Gateway och flow-relaterade data (till exempel tillåtna flöden och portar):

    ![VPN-gateway trend och flow information om diskanvändning](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualisera trafikfördelning efter geografi

**Titta efter**

- Fördelning av trafik per datacenter, till exempel främsta källor för trafik till ett datacenter, övre falsk nätverk konversation med datacenter och top konversation programprotokoll.
  - Om du upptäcker högre belastning på ett datacenter, kan du planera för effektiv trafikfördelning.
  - Om falskt nätverk konversation i datacentret, korrigera NSG-regler som blockerar dem.

    Välj **Visa karta** under **miljön**, enligt följande bild:

    ![Instrumentpanelen listvy med fördelning av trafik](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geo-kartan visar menyfliksområdet längst upp för val av parametrar, till exempel datacenter (distribuerat/Nej-distribution/aktiv/inaktiv/Trafikanalysaktiverade/trafikanalys inte aktiverad) och länder som bidrar till Benign/skadlig trafik till aktivt distribution:

    ![GEO-kartvyn visar aktiv distribution](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geo-kartan visar fördelning av trafik till ett datacenter från andra länder och kontinenter kommunikation till den i blå (ofarlig trafik) och röd (skadlig trafik) färgas rader:

    ![GEO-kartvyn visar fördelning av trafik i länder och kontinenter](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Flow-information för fördelning av trafik i loggsökning](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualisera fördelning av trafik med virtuella nätverk

**Titta efter**

- Fördelning av trafik per virtuellt nätverk, topologi, främsta källor för trafik till det virtuella nätverket, övre falsk nätverk konversation till det virtuella nätverket och top konversation programprotokoll.
  - Att veta vilket virtuellt nätverk konversation till vilket virtuellt nätverk. Om konversationen inte förväntas, kan det åtgärdas.
  - Om falskt nätverk konversation med ett virtuellt nätverk, kan du korrigera NSG-regler för att blockera obehörig-nätverk.
 
    Välj **visa VNets** under **miljön**, enligt följande bild:

    ![Instrumentpanelen visar fördelning i virtuella nätverk](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Den virtuella nätverkstopologin visar menyfliksområdet längst upp för val av parametrar som ett virtuellt nätverk (Inter vnet-anslutningar/Active/Inactive), externa anslutningar, aktiva flöden och skadliga flöden i det virtuella nätverket.
- Du kan filtrera den virtuella nätverkstopologin baserat på prenumerationer, arbetsytor, resursgrupper och tidsintervall. Ytterligare filter som hjälper dig att förstå flödet är: Flow typ (mellan virtuella nätverk, IntraVNET osv), Flow riktning (inkommande, utgående), Flow Status (tillåtna, blockerad) virtuella nätverk (målgrupper och ansluten), anslutningstyp (Peering eller Gateway - P2S och S2S) och NSG. Du kan använda dessa filter för att fokusera på virtuella nätverk som du vill undersöka i detalj.
- Den virtuella nätverkstopologin visar fördelning av trafik till ett virtuellt nätverk för flöden (tillåten/blockerad/inkommande/utgående/Benign/skadlig), protokoll och nätverkssäkerhetsgrupper, till exempel:

    ![Virtuella nätverkets topologi som visar information om distribution och flödet av trafik](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtuella nätverkets topologi med översta nivån och mer filter](./media/traffic-analytics/virtual-network-filters.png)

    ![Flow-information för fördelning av trafik i virtuella nätverk i loggsökning](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Titta efter**

- Trafik distribution per undernät, topologi, främsta källor för trafik till undernätet, övre falsk nätverk konversation till undernätet och top konversation programprotokoll.
    - Att veta vilka undernät konversation till vilket undernät. Om du ser ett oväntat konversationer, kan du korrigera din konfiguration.
    - Om falskt nätverk konversation med ett undernät, kan du åtgärda det genom att konfigurera NSG-regler för att blockera obehörig-nätverk.
- Undernät topologin visar menyfliksområdet längst upp för val av parametrar, t.ex Active/Inactive undernät, externa anslutningar, aktiva flöden och skadliga flöden för undernätet.
- Undernät topologin visar fördelning av trafik till ett virtuellt nätverk för flöden (tillåten/blockerad/inkommande/utgående/Benign/skadlig), protokoll och NSG: er, till exempel:

    ![Nättopologi visar fördelning av trafik till ett virtuellt nätverksundernät för flöden](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Titta efter**

Fördelning av trafik per Programgateway och belastningsutjämnare, topologi, främsta källor för trafiken, övre obehöriga nätverk konversation till Application gateway & belastningsutjämnare och top konversation programprotokoll. 
    
 - Att veta vilka undernät konversation som Application gateway eller belastningsutjämnare. Om du upptäcker ett oväntat konversationer, kan du korrigera din konfiguration.
 - Om falskt nätverk konversation med ett Application gateway, belastningsutjämnare, kan du åtgärda det genom att konfigurera NSG-regler för att blockera obehörig-nätverk. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visa portar och virtuella datorer tar emot trafik från internet

**Titta efter**

- Vilka öppna portar konversation via internet?
  - Om det oväntade portar finns öppna kan du korrigera konfigurationen:

    ![Instrumentpanelen visar portarna ta emot och skickar trafik till internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Information om Azure-målportar och värdar](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Titta efter**

Har du skadlig trafik i din miljö? Där den härstammar från? Där är den är avsedd för?

![Skadlig trafik flödar detalj i loggsökning](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualisera trender i NSG/NSG-regler träffar

**Titta efter**

- Vilka NSG/NSG-regler har de flesta träffar i jämförande diagram med flöden distribution?
- Vad är de främsta käll- och konversationspar per NSG/NSG-regler?

    ![Förkonfigurerad NSG instrumentpanel når statistik](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Följande bilder visar tiden trender för träffar för nätverkssäkerhetsgruppregler och källa-mål flow-information för en grupp:

  - Upptäck vilka NSG: er och NSG regler passerar skadliga flöden och som är den främsta skadliga IP-adresser åtkomst till din molnmiljö
  - Identifiera vilka NSG/NSG-regler att tillåta/blockering av betydande nätverkstrafik
  - Välj upp filter för detaljerad granskning av en NSG eller NSG regler

    ![Visar tid trender för träffar för regel och övre NSG-regler](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Främsta NSG-regler statistik information i loggsökningar](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

Om du vill få svar på vanliga frågor och svar, se [Traffic analytics vanliga frågor och svar](traffic-analytics-faq.md).
