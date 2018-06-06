---
title: Azure-trafik analytics | Microsoft Docs
description: Lär dig att analysera Azure-nätverk grupp flödet säkerhetsloggar med trafik analytics.
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
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: f7603d56a56e45771fa170c05ec62a0725ba367a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715830"
---
# <a name="traffic-analytics"></a>Trafikanalys

Trafik analytics är en molnbaserad lösning som ger inblick i användar- och programaktivitet i molnet nätverk. Trafik analytics analyserar Nätverksbevakaren nätverk grupp (NSG) flöde säkerhetsloggar för att ge insikter om trafikflöde i Azure-molnet. Med trafik analytics kan du:

- Visualisera nätverksaktivitet över dina Azure-prenumerationer och identifiera anslutningar.
- Identifiera säkerhetshot till och skydda ditt nätverk, med information, till exempel öppna portar, program försöker tillgång till internet och virtuella datorer (VM) som ansluter till obehöriga nätverk.
- Förstå trafikmönster flödet i Azure-regioner och internet för att optimera din nätverksdistribution för prestanda och kapacitet.
- Identifiera nätverket felaktiga konfigurationer leda till misslyckade anslutningar i nätverket.

## <a name="why-traffic-analytics"></a>Varför trafik analytics?

Är det viktigt att övervaka, hantera och känner till ditt eget nätverk för kompromisslös säkerhet, efterlevnad och prestanda. Känna till din egen miljö är av avgörande betydelse för att skydda och optimera den. Du behöver ofta veta det aktuella tillståndet för nätverket som ansluter, vilka portar är öppna för internet, är förväntat nätverk, oregelbundna nätverksproblem och plötslig stiger i trafik.

Molnet nätverk skiljer sig från lokala företagets nätverk, där du har Netflow eller motsvarande protokollet kompatibla routrar och växlar, som gör möjligheten att samla in IP-nätverkstrafik när den anländer till eller lämnar ett nätverksgränssnitt. Du kan skapa en analys av flödet i nätverkstrafiken och volymen genom att analysera trafikinformation flödet.

Virtuella Azure-nätverk har NSG flödet loggarna, vilket ger dig information om ingång och utgång IP-trafik via en Nätverkssäkerhetsgrupp som är kopplade till enskilda nätverksgränssnitt, virtuella datorer eller undernät. Genom att analysera NSG flödet loggarna och infoga intelligence säkerhet, topologi och geografi, trafik kan analytics ge dig insikter om trafikflöde i din miljö. Trafik Analytics innehåller information som mest kommunicerande värdar, mest kommunicerande programprotokoll, mest konversation värdepar, tillåtna/blockerade trafik, inkommande/utgående trafik, öppna internet-portar, mest blockerande regler, trafik distributionsplatser per Azure-datacenter, virtuella nätverk, undernät, eller obehöriga nätverk.

## <a name="key-components"></a>Nyckelkomponenter 

- **Nätverkssäkerhetsgrupp (NSG)**: innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till ett virtuellt Azure-nätverk. Nätverkssäkerhetsgrupper kan kopplas till undernät, enskilda virtuella datorer (klassisk) eller enskilda nätverkskort (NIC) som är anslutna till virtuella datorer (Resource Manager). Mer information finns i [nätverk Säkerhetsöversikt för gruppen](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Nätverk säkerhetsloggar grupp (NSG) flöde**: gör att du kan visa information om ingående och utgående IP-trafik via en nätverkssäkerhetsgrupp. NSG flödet loggarna skrivs i json-format och visa utgående och inkommande flöden på grundval av per regel NIC flödet gäller för 5-tuppel information om flödet (källan/målet IP-adress, källan/målet port och protokoll), och om trafiken tilläts eller nekad. Mer information om flödet NSG-loggar finns [NSG flödet loggar](network-watcher-nsg-flow-logging-overview.md).
- **Logga Analytics**: Azure-tjänst som samlar in övervakningsdata och lagrar data i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data som tillhandahålls via Azure API. När data har samlats in är de tillgängliga för avisering, analys och export. Övervakning av program som nätverket performance monitor och trafik analytics är byggda med logganalys som grund. Mer information finns i [logga analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Logga analytics-arbetsyta**: en instans av logganalys data som hör till ett Azure-konto ska lagras. Mer information om log analytics arbetsytor finns [skapa logganalys-arbetsytan](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Nätverk Watcher**: en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på en nivå för scenariot i Azure. Du kan aktivera NSG flödet loggar och inaktivera med Nätverksbevakaren. Mer information finns i [Nätverksbevakaren](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Hur fungerar trafiken analytics 

Trafik analytics undersöks flödet av loggarna NSG och samlar in minskade loggar genom insamling av vanliga flöden mellan samma käll-IP-adress, mål-IP-adress, målport och protokoll. Till exempel värddator 1 (IP-adress: 10.10.10.10) kommunicera med värddator 2 (IP-adress: 10.10.20.10), 100 gånger under en period 1 timme med hjälp av port (till exempel 80) och protokoll (exempelvis http). Minskad loggen har en post som värddator 1 & värddator 2 kommunicerat 100 gånger under en period på 1 timme som använder port *80* och protokoll *HTTP*, i stället för med 100 poster. Minskad loggar utökats med geografi, säkerhet och topologiinformation och lagras sedan i en log analytics-arbetsyta. Följande bild visar dataflödet:

![Dataflöde för NSG flödet loggar bearbetning](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regioner som stöds

Trafik analytics är tillgängliga i förhandsversionen. Funktioner i förhandsversionen har inte samma grad av tillgänglighet och tillförlitlighet som funktioner i allmänhet versionen.  I förhandsversionen kan du använda trafik analytics för NSG: er i något av följande områden: Väst centrala USA, östra USA, östra USA 2, norra centrala USA, södra centrala USA, centrala USA, västra USA, västra USA 2, Västeuropa, Norra Europa, västra Storbritannien, Syd Storbritannien, Östra Australien , och Australien sydost. Log analytics-arbetsyta måste finnas i West centrala USA, östra USA, västra Europa, Östra Australien, sydost eller Syd Storbritannien region.

## <a name="prerequisites"></a>Förutsättningar

### <a name="enable-network-watcher"></a>Aktivera Network Watcher 

Om du vill analysera trafik, måste du ha en befintlig nätverksbevakaren eller [aktivera en nätverksbevakaren](network-watcher-create.md) trafik för varje region där NSG: er som du vill analysera. Trafik analytics kan aktiveras för NSG: er som finns i någon av de [regioner som stöds](#supported-regions).

### <a name="re-register-the-network-resource-provider"></a>Registrera resursen nätverksprovider 

Innan du kan använda trafik analytics under förhandsgranskningen, måste du registrera din nätverksresursprovidern. Klicka på **prova** i rutan följande kod för att öppna Azure Cloud-gränssnittet. Molnet Shell loggas du automatiskt in på Azure-prenumerationen. Ange följande kommando för att registrera nätverksresursprovidern när Cloud-gränssnittet är öppet:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>Välj en nätverkssäkerhetsgrupp 

Innan du aktiverar NSG flödet loggning måste du ha en nätverkssäkerhetsgrupp logga flöden. Om du inte har en nätverkssäkerhetsgrupp, se [skapar en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md#create-a-network-security-group) att skapa en.

På vänster sida av Azure portal, väljer **övervakaren**, sedan **nätverksbevakaren**, och välj sedan **NSG flödet loggar**. Välj nätverkssäkerhetsgruppen som du vill aktivera en NSG flödet logg för, enligt följande bild:

![Val av NSG: er som kräver aktivering av NSG flödet logg](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

Om du försöker aktivera trafik analytics för en NSG som finns i en region än den [regioner som stöds](#supported-regions), felmeddelande ”kunde inte hittas”. 

## <a name="enable-flow-log-settings"></a>Aktivera flödet logginställningar

Innan du aktiverar flödet Logginställningar, måste du utföra följande uppgifter:

Registrera providern Azure insikter om den inte redan är registrerad för din prenumeration:

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

Om du inte redan har ett Azure Storage-konto för att lagra NSG flödet loggar in, måste du skapa ett lagringskonto. Du kan skapa ett lagringskonto med det kommando som följer. Innan du kör kommandot, ersätter `<replace-with-your-unique-storage-account-name>` med ett namn som är unikt för alla Azure platser mellan 3 till 24 tecken, med endast siffror och gemener. Du kan också ändra resursgruppens namn, om det behövs.

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Välj följande alternativ som visas i bilden:

1. Välj *på* för **Status**
2. Välj ett befintligt lagringskonto för lagring av flödet loggar in. Om du vill lagra data alltid anger du värdet till *0*. Du betalar avgifter för Azure-lagring för lagringskontot.
3. Ange **kvarhållning** antal dagar som du vill lagra data för.
4. Välj *på* för **trafik Analytics Status**.
5. Välj en befintlig arbetsyta logganalys (OMS) eller välj **Skapa ny arbetsyta** att skapa en ny. Logganalys-arbetsytan används av trafik Analytics för att lagra de aggregerade och indexerade data som sedan används för att generera analytics. Om du väljer en befintlig arbetsyta, det måste finnas i något av de [regioner som stöds](#traffic-analytics-supported-regions) och har uppgraderats till det nya språket i fråga. Om du inte vill uppgradera en befintlig arbetsyta eller har inte en arbetsyta i en region som stöds, kan du skapa en ny. Mer information om frågespråk finns [Azure logganalys uppgradera till ny logg sökning](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    Log analytics-arbetsyta som värd för analytics-lösningen trafik och de NSG: er behöver inte finnas i samma region. Till exempel kanske du trafik analytics i en arbetsyta i regionen Västeuropa, medan du kan ha NSG: er i östra USA och västra USA. Flera NSG: er kan konfigureras i samma arbetsyta.
6. Välj **Spara**.

    ![Valet av lagringskonto, logganalys-arbetsytan och trafik Analytics aktivering](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

Upprepa de här stegen för alla andra NSG: er som du vill aktivera trafik analytics för. Data från flödet loggar skickas till arbetsytan, så se till att den lokala lagar och förordningar i ditt land tillåter lagring av data i den region där arbetsytan finns.

## <a name="view-traffic-analytics"></a>Visa trafik analytics

På vänster sida av portalen, väljer **alla tjänster**, ange *övervakaren* i den **Filter** rutan. När **övervakaren** visas i sökresultaten väljer den. Om du vill börja utforska trafik analyser och dess funktioner, Välj **nätverksbevakaren**, sedan **trafik Analytics (förhandsgranskning)**.

![Åtkomst till instrumentpanelen trafik Analytics](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Instrumentpanelen kan ta upp till 30 minuter innan den visas första gången eftersom trafiken Analytics måste först aggregera tillräckligt med data att härleda insikter som beskrivande, innan det kan generera rapporter.

## <a name="usage-scenarios"></a>Användningsscenarier

Några av de insikter som du kanske vill få när trafik Analytics konfigurerats helt, är följande:

### <a name="find-traffic-hotspots"></a>Hitta trafik surfpunkter

**Titta efter**

- Vilka värdar skicka och ta emot de mest trafik?
    - Förstå vilka värdar skicka eller ta emot de flesta trafik kan hjälpa dig att identifiera de värdar som bearbetar merparten av trafiken.
    - Du kan utvärdera om mängden trafik som är lämplig för en värd. Är mängden trafik som utgör normal beteende eller den ha ytterligare undersökning?
- Hur mycket inkommande/utgående trafik finns det?
    -   Förväntas värden för att ta emot fler inkommande trafik än utgående eller tvärtom
- Statistik över tillåtna/blockerade trafik.
    - Varför är en värd som tillåter eller blockerar en stor mängd trafik?

    Välj **mer**under **värdar med de flesta trafik**som visas i följande bild:

    ![Instrumentpanelen med värden med de flesta trafik information](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Följande bild visar tid trender för översta fem pratar värdarna och flödet-relaterad information (tillåtna – inkommande/utgående och Nekade - inkommande/utgående flöden) för en virtuell dator:

    ![Fem främsta prata med de flesta värden trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Titta efter**

- Vilka är de mest conversing värden paren?
    - Förväntat som front end-backend kommunikation eller oregelbunden beteende som backend-internet-trafik.
- Statistik över tillåtna/blockerade trafik
    - Varför en värd att tillåta eller blockera betydande trafikvolym
- De vanligaste programprotokoll bland de flesta conversing värdepar:
    - Dessa program är tillåtna i det här nätverket?
    - Konfigureras program korrekt? Använder de rätt protokoll för kommunikation? Välj **mer** under **mest frekventa konversationer**, vilket visas i följande bild:

        ![Instrumentpanelen med de vanligaste konversation](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Följande bild visar tid trender för översta fem konversationer och flödet-relaterad information som tillåten respektive nekad inkommande och utgående flöden paret konversation:

    ![TOP fem chatty konversation information och trend](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Titta efter**

- Vilka protokoll används mest i din miljö och vilka conversing värdepar använder mest programprotokollet?
    - Dessa program är tillåtna i det här nätverket?
    - Konfigureras program korrekt? Använder de rätt protokoll för kommunikation? Förväntat beteende är vanliga portar, till exempel 80 och 443. För standard-kommunikation om ovanliga portar visas behöva de en konfigurationsändring. Välj **mer** under **uppifrån programprotokoll**, i följande bild:

        ![Instrumentpanelen med högsta programprotokoll](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Följande bilder visar tidpunkt trender för säljares L7 protokoll och flödet-relaterad information (till exempel tillåten respektive nekad flöden) för en L7-protokollet:

    ![Fem layer 7 protokoll information och trend](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![Flöda information om protokoll i loggen Sök](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Titta efter**

- Kapacitetsutnyttjande trender för en VPN-gateway i din miljö.
    - Varje VPN SKU gör det möjligt för en viss mängd bandbredd. Är underutnyttjade VPN-gatewayer?
    - Din gateway ansluter till kapacitet? Bör du uppgradera till nästa högre SKU
- Vilka är de mest conversing värdarna via vilken VPN-gateway över vilken port?
    - Är det här mönstret normalt? Välj **mer** under **uppifrån aktiva VPN-anslutningar**som visas i följande bild:

        ![Instrumentpanelen med högsta aktiva VPN-anslutningar](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Följande bild visar tid trender för användningen av kapaciteten för en Azure VPN-Gateway och den flöde-relaterad informationen (t.ex tillåtna flöden och portar):

    ![VPN gateway trend och flödar information om bandanvändning](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualisera trafikfördelning efter geografi

**Titta efter**

- Trafikfördelning per datacenter, till exempel övre källor för trafik till en datacenter övre falska nätverk konversation med datacenter och upp konversation programprotokoll.
    - Om du upptäcker mer belastningen på ett datacenter, kan du planera för distribution av effektiv trafik.
    - Om falska nätverk konversation i datacentret, korrigera du NSG-regler som blockerar dem.

    Välj **Klicka här om du vill se live geomap** under **trafik distribution över Azure-Datacenter**som visas i följande bild:

  ![Instrumentpanel med trafikfördelning](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Geo-kartan visar översta menyfliksområdet för val av parametrar, till exempel datacenter (ingen/distribuerat-distribution/inaktiv/Active/trafik Analytics aktiverat/trafik Analytics inte aktiverad) och länder bidrar Benign/skadliga trafik till aktivt distribution:

    ![GEO-kartvyn med aktiv distribution](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Geo-kartan visar Trafikfördelningen till ett datacenter från andra länder och kontinenter kommunikation till den i blå (ofarlig trafik) och det röda (skadlig trafik) färgade rader:

    ![GEO-kartvyn med trafikfördelning länder och kontinenter](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Flöda information för trafik distributionsplats i loggen Sök](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualisera trafik distribution av virtuella nätverk

**Titta efter**

- Trafikfördelning per virtuellt nätverk, topologi, övre källor för trafik till det virtuella nätverket översta falska nätverk konversation till virtuella nätverk och upp konversation programprotokoll.
    - Att veta vilka virtuella nätverk konversation till vilka virtuella nätverket. Om konversationen inte förväntas, kan det åtgärdas.
    - Om falska nätverk konversation med ett virtuellt nätverk, kan du korrigera NSG-regler för att blockera falska nätverk.
 
    Välj **Klicka här om du vill se VNet trafik topologi** under **virtuella nätverk distribution**som visas i följande bild: 

    ![Instrumentpanelen med distribution av virtuellt nätverk](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Den virtuella nätverkstopologin visar översta menyfliksområdet för val av parametrar som en virtuella nätverkets (Inter virtuellt nätverk anslutningar/Active/Inactive), externa anslutningar, aktiva flöden och skadliga flöden för det virtuella nätverket.
- Den virtuella nätverkstopologin visar trafikfördelning till ett virtuellt nätverk med avseende på flöden (tillåtna/blockerade/inkommande/utgående/Benign/skadliga), protokoll och nätverkssäkerhetsgrupper, till exempel:

    ![Virtuella nätverkets topologi med information för distribution och flödet av trafik](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![Flöda information för virtuellt nätverk trafik distributionsplats i loggen Sök](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Titta efter**

- Trafikfördelning per undernät, topologi, övre källor för trafik till undernät, övre rouge nätverk konversation till undernätet och upp konversation programprotokoll.
    - Att veta vilka undernät konversation till vilka undernät. Om du ser oväntat konversationer kan du korrigera konfigurationen.
    - Om rouge nätverk konversation med ett undernät, kan du åtgärda det genom att konfigurera NSG-regler för att blockera falska nätverk, t.ex.
- Undernät topologin visar översta menyfliksområdet för val av parametrar, till exempel Active/Inactive undernät, externa anslutningar, aktiva flöden och skadliga flöden till undernätet.
- Undernät topologin visar trafikfördelning till ett virtuellt nätverk med avseende på flöden (tillåtna/blockerade/inkommande/utgående/Benign/skadliga), protokoll och NSG: er, till exempel:

    ![Undernät topologi med trafikfördelning ett undernät för virtuellt nätverk med avseende på flöden](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Visa portar och virtuella datorer som tar emot trafik från internet

**Titta efter**

- Vilka öppna portar konversation via internet?
    - Du kan korrigera konfigurationen om oväntat portar öppna:

        ![Instrumentpanelen med portarna ta emot och skicka trafik till internet](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Information om Azure målportar och värdar](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Titta efter**

Har du skadlig trafik i din miljö? Där den härstammar från? Där är den som är avsett att?

![Skadliga trafiken flödar information i loggen Sök](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>Visualisera trender i NSG regeln träffar

**Titta efter**

- Vilka NSG/regler som är mest träffar?
- Vad är de främsta käll- och konversation par per NSG?

    ![Instrumentpanelen med NSG träffar statistik](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Följande bilder visar tidpunkt trender för träffar NSG-regler och information om källan till målet flödet för en nätverkssäkerhetsgrupp:

    ![Med tiden trender för NSG regeln träffar och översta NSG-regler](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Övre NSG-regler statistik information i loggen Sök](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

För att få svar på vanliga frågor och svar, se [trafik analytics vanliga frågor och svar](traffic-analytics-faq.md).
