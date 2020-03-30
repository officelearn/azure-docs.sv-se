---
title: 'Azure ExpressRoute: Konfigurera NPM för kretsar'
description: Konfigurera molnbaserad nätverksövervakning (NPM) för Azure ExpressRoute-kretsar. Detta omfattar övervakning över ExpressRoute privat peering och Microsoft peering.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 01/25/2019
ms.author: cherylmc
ms.openlocfilehash: 54fa3dcbfbbcb3153f81407a9bc9b52511405390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076595"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurera övervakare av nätverksprestanda för ExpressRoute

Den här artikeln hjälper dig att konfigurera ett nätverksprestandaövervakare för att övervaka ExpressRoute. Övervakare av nätverksprestanda är en molnbaserad nätverksövervakningslösning som övervakar anslutningen mellan Azures molndistributioner och lokala platser (kontor etc.). Övervakaren av nätverksprestanda är en del av Azure Monitor-loggarna. Övervakning av nätverksprestanda är ett tillägg i ExpressRoute som låter dig övervaka nätverkets prestanda över ExpressRoute-kretsar som är konfigurerade att använda privat peering eller Microsoft-peering. När du konfigurerar övervakning av nätverksprestanda för ExpressRoute, kan du hitta nätverksproblem som ska identifieras och elimineras. Den här tjänsten är också tillgänglig för Azure Government-molnet.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Du kan:

* Övervaka förlust och svarstid i olika virtuella nätverk och ställa in aviseringar

* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket

* Felsöka tillfälliga och punkt-i-tid nätverksproblem som är svåra att replikera

* Hjälp till att fastställa ett specifikt segment i nätverket som ansvarar för försämrad prestanda

* Hämta dataflöde per virtuellt nätverk (Om du har agenter installerade i varje virtuellt nätverk)

* Se ExpressRoute-systemets tillstånd från en tidigare tidpunkt

## <a name="workflow"></a><a name="workflow"></a>Arbetsflöde

Övervakningsagenter installeras på flera servrar, både lokalt och i Azure. Agenterna kommunicerar med varandra, men skickar inte data, de skickar TCP-handskakningspaket. Kommunikationen mellan agenterna gör att Azure kan mappa nätverkstopologin och sökvägen som trafiken kan ta.

1. Skapa en NPM-arbetsyta. Det här är samma som en Log Analytics-arbetsyta.
2. Installera och konfigurera programvaruagenter. (Om du bara vill övervaka över Microsoft Peering behöver du inte installera och konfigurera programvaruagenter.): 
    * Installera övervakningsagenter på de lokala servrarna och virtuella Azure-datorer (för privat peering).
    * Konfigurera inställningar på övervakningsagentservrarna så att övervakningsagenterna kan kommunicera. (Öppna brandväggsportar, etc.)
3. Konfigurera NSG-regler (Network Security Group) så att övervakningsagenten som är installerad på virtuella Azure-datorer kan kommunicera med lokala övervakningsagenter.
4. Konfigurera övervakning: Identifiera automatiskt och hantera vilka nätverk som är synliga i NPM.

Om du redan använder Network Performance Monitor för att övervaka andra objekt eller tjänster, och du redan har arbetsyta i något av de regioner som stöds, kan du hoppa över steg 1 och steg 2 och börja konfigurera med steg 3.

## <a name="step-1-create-a-workspace"></a><a name="configure"></a>Steg 1: Skapa en arbetsyta

Skapa en arbetsyta i prenumerationen som har VNets-länken till ExpressRoute-kretsarna.

1. I [Azure-portalen](https://portal.azure.com)väljer du den prenumeration som har VNET-datorerna peered till din ExpressRoute-krets. Sök sedan i listan över tjänster på **Marketplace** efter "Network Performance Monitor". Öppna sidan **Nätverksprestandaövervakare** i returen.

   >[!NOTE]
   >Du kan skapa en ny arbetsyta eller använda en befintlig arbetsyta. Om du vill använda en befintlig arbetsyta måste du se till att arbetsytan har migrerats till det nya frågespråket. [Mer information...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Längst ned på huvudsidan **för Nätverksprestandaövervakaren** klickar du på **Skapa** för att öppna **Nätverksprestandaövervakaren - Skapa en ny lösningssida.** Klicka på **Logganalysarbetsyta – välj en arbetsyta** för att öppna sidan Arbetsytor. Klicka på **+ Skapa ny arbetsyta** för att öppna sidan Arbetsyta.
3. På **arbetsytan Logganalys** väljer du **Skapa ny**och konfigurerar sedan följande inställningar:

   * Log Analytics Workspace - Skriv ett namn på arbetsytan.
   * Prenumeration – Om du har flera prenumerationer väljer du den du vill associera med den nya arbetsytan.
   * Resursgrupp - Skapa en resursgrupp eller använd en befintlig.
   * Plats - Den här platsen används för att ange platsen för lagringskontot som används för agentanslutningsloggarna.
   * Prisnivå - Välj prisnivå.
  
     >[!NOTE]
     >ExpressRoute-kretsen kan vara var som helst i världen. Det behöver inte vara i samma region som arbetsytan.
     >
  
     ![arbetsyta](./media/how-to-npm/4.png)<br><br>
4. Klicka på **OK** om du vill spara och distribuera inställningsmallen. När mallen har validerats klickar du på **Skapa** för att distribuera arbetsytan.
5. När arbetsytan har distribuerats navigerar du till den **NetworkMonitoring(name)-resurs** som du skapade. Validera inställningarna och klicka sedan på **Lösning kräver ytterligare konfiguration**.

   ![ytterligare konfiguration](./media/how-to-npm/5.png)

## <a name="step-2-install-and-configure-agents"></a><a name="agents"></a>Steg 2: Installera och konfigurera agenter

### <a name="21-download-the-agent-setup-file"></a><a name="download"></a>2.1: Hämta agentinställningsfilen

1. Gå till fliken **Vanliga inställningar på** sidan Konfiguration av **nätverksprestandaövervakaren** för din resurs. Klicka på agenten som motsvarar serverns processor från avsnittet **Installera logganalysagenter** och hämta installationsfilen.
2. Kopiera sedan **arbetsyte-ID:et** och **primärnyckeln** till Anteckningar.
3. Hämta Powershell-skriptet från avsnittet **Konfigurera logganalysagenter för övervakning med TCP-protokoll.** PowerShell-skriptet hjälper dig att öppna den relevanta brandväggsporten för TCP-transaktionerna.

   ![PowerShell-skript](./media/how-to-npm/7.png)

### <a name="22-install-a-monitoring-agent-on-each-monitoring-server-on-each-vnet-that-you-want-to-monitor"></a><a name="installagent"></a>2.2: Installera en övervakningsagent på varje övervakningsserver (på varje virtuella nätverk som du vill övervaka)

Vi rekommenderar att du installerar minst två agenter på varje sida av ExpressRoute-anslutningen för redundans (till exempel lokala, Azure VNETs). Agenten måste vara installerad på en Windows Server (2008 SP1 eller senare). Övervakning av ExpressRoute-kretsar med Windows Desktop OS och Linux OS stöds inte. Gör så här för att installera agenter:
   
  >[!NOTE]
  >Agenter som skickas av SCOM (inkluderar [MMA)](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)kanske inte konsekvent kan identifiera sin plats om de finns i Azure. Vi rekommenderar att du inte använder dessa agenter i Azure VNETs för att övervaka ExpressRoute.
  >

1. Kör **installationsprogrammet** för att installera agenten på varje server som du vill använda för att övervaka ExpressRoute. Servern som du använder för övervakning kan antingen vara en virtuell dator eller lokal och måste ha tillgång till Internet. Du måste installera minst en lokal agent och en agent i varje nätverkssegment som du vill övervaka i Azure.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **Licensvillkor** läser du licensen och klickar sedan på **Jag godkänner**.
4. På sidan **Målmapp** ändrar eller behåller du standardinstallationsmappen och klickar sedan på **Nästa**.
5. På sidan **Alternativ för agentinställningar** kan du välja att ansluta agenten till Azure Monitor-loggar eller Operations Manager. Du kan också lämna alternativen tomma om du vill konfigurera agenten senare. När du har gjort dina val klickar du på **Nästa**.

   * Om du väljer att ansluta till **Azure Log Analytics**klistrar du in **arbetsyte-ID** och **arbetsytanyckel** (primärnyckel) som du kopierade till Anteckningar i föregående avsnitt. Klicka sedan på **Nästa**.

     ![ID och nyckel](./media/how-to-npm/8.png)
   * Om du väljer att ansluta till **Operations Manager**skriver du **namn, hanteringsgruppsnamn,** **hanteringsserver**och **hanteringsserverporten**på sidan Konfiguration av **hanteringsgrupp.** Klicka sedan på **Nästa**.

     ![Operations Manager](./media/how-to-npm/9.png)
   * På sidan **Agent Action-konto** väljer du antingen kontot **Lokalt system** eller **Domän- eller Lokalt datorkonto**. Klicka sedan på **Nästa**.

     ![Konto](./media/how-to-npm/10.png)
6. På sidan **Klar att installera** granskar du dina val och klickar sedan på **Installera**.
7. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.
8. När du är klar visas Microsoft Monitoring Agent på Kontrollpanelen. Du kan granska din konfiguration där och kontrollera att agenten är ansluten till Azure Monitor-loggar. När agenten är ansluten visas ett meddelande som anger: **Microsoft Monitoring Agent har anslutit till Tjänsten Microsoft Operations Management Suite**.

9. Upprepa proceduren för varje VNET som du behöver övervaka.

### <a name="23-configure-proxy-settings-optional"></a><a name="proxy"></a>2.3: Konfigurera proxyinställningar (valfritt)

Om du använder en webbproxy för att ansluta till Internet använder du följande steg för att konfigurera proxyinställningar för Microsoft Monitoring Agent. Utför dessa steg för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. Om så är fallet läser [du Så här konfigurerar du proxyinställningar för Microsoft Monitoring Agent med ett skript](../log-analytics/log-analytics-windows-agent.md).

Så här konfigurerar du proxyinställningar för Microsoft Monitoring Agent med hjälp av Kontrollpanelen:

1. Öppna **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.
4. Välj **Använd en proxyserver** och skriv URL-adressen och portnumret, om något behövs. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

   ![proxy](./media/how-to-npm/11.png)

### <a name="24-verify-agent-connectivity"></a><a name="verifyagent"></a>2.4: Verifiera agentanslutning

Du kan enkelt kontrollera om dina agenter kommunicerar.

1. Öppna **Kontrollpanelen**på en server med övervakningsagenten .
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Azure Log Analytics.**
4. I kolumnen **Status** bör du se att agenten har anslutits till Azure Monitor-loggar.

   ![status](./media/how-to-npm/12.png)

### <a name="25-open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>2.5: Öppna brandväggsportarna på övervakningsagentservrarna

Om du vill använda TCP-protokollet måste du öppna brandväggsportar för att säkerställa att övervakningsagenterna kan kommunicera.

Du kan köra ett PowerShell-skript för att skapa registernycklarna som krävs av Nätverksprestandaövervakaren. Det här skriptet skapar också windows-brandväggens regler så att övervakningsagenter kan skapa TCP-anslutningar med varandra. Registernycklarna som skapas av skriptet anger om felsökningsloggarna ska loggas och sökvägen för loggfilen. Den definierar också agenten TCP-port som används för kommunikation. Värdena för dessa nycklar ställs automatiskt in av skriptet. Du bör inte ändra dessa nycklar manuellt.

Port 8084 öppnas som standard. Du kan använda en anpassad port genom att ange parametern 'portNumber' till skriptet. Om du gör det måste du dock ange samma port för alla servrar där du kör skriptet.

>[!NOTE]
>PowerShell-skriptet "EnableRules" konfigurerar windowsbrandväggsregler endast på den server där skriptet körs. Om du har en nätverksbrandvägg bör du se till att den tillåter trafik som är avsedd för TCP-porten som används av Network Performance Monitor.
>
>

Öppna ett PowerShell-fönster med administratörsbehörighet på agentservrarna. Kör [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-skriptet (som du hämtade tidigare). Använd inga parametrar.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="step-3-configure-network-security-group-rules"></a><a name="opennsg"></a>Steg 3: Konfigurera regler för nätverkssäkerhetsgrupper

Om du vill övervaka agentservrar som finns i Azure måste du konfigurera NSG-regler (Network Security Group) så att TCP-trafik tillåts på en port som används av NPM för syntetiska transaktioner. Standardporten är 8084. Detta gör att en övervakningsagent installerad på en Azure VM för att kommunicera med en lokal övervakningsagent.

Mer information om NSG finns i [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Kontrollera att du har installerat agenterna (både den lokala serveragenten och Azure-serveragenten) och har kört PowerShell-skriptet innan du fortsätter med det här steget.
>

## <a name="step-4-discover-peering-connections"></a><a name="setupmonitor"></a>Steg 4: Upptäck peering-anslutningar

1. Navigera till översiktspanelen För nätverksprestandaövervakaren genom att gå till sidan **Alla resurser** och klicka sedan på den vitlistade NPM-arbetsytan.

   ![npm arbetsyta](./media/how-to-npm/npm.png)
2. Klicka på översiktspanelen **För nätverksprestandaövervakaren** om du vill visa instrumentpanelen. Instrumentpanelen innehåller en ExpressRoute-sida som visar att ExpressRoute är i ett "okonfigurerat tillstånd". Klicka på **Funktionsinställningar** för att öppna konfigurationssidan för nätverksprestandaövervakaren.

   ![inställningar för funktionen](./media/how-to-npm/npm2.png)
3. På konfigurationssidan navigerar du till fliken "ExpressRoute Peerings", som finns på den vänstra sidopanelen. Klicka sedan på **Upptäck nu**.

   ![Upptäck](./media/how-to-npm/13.png)
4. När identifieringen är klar visas en lista med följande objekt:
   * Alla Microsoft-peering-anslutningar i ExpressRoute-kretsar som är associerade med den här prenumerationen.
   * Alla privata peering-anslutningar som ansluter till de virtuella nätverk som är associerade med den här prenumerationen.
            
## <a name="step-5-configure-monitors"></a><a name="configmonitor"></a>Steg 5: Konfigurera bildskärmar

I det här avsnittet konfigurerar du övervakarna. Följ stegen för den typ av peering som du vill övervaka: **privat peering**eller **Microsoft-peering**.

### <a name="private-peering"></a>Privat peering

För privat peering, när identifieringen är klar, ser du regler för unika **kretsnamn** och **VNet-namn**. Inledningsvis är dessa regler inaktiverade.

![regler](./media/how-to-npm/14.png)

1. Markera kryssrutan **Övervaka den här peering-kryssrutan.**
2. Markera kryssrutan **Aktivera hälsoövervakning för den här peeringen**.
3. Välj övervakningsförhållanden. Du kan ange anpassade tröskelvärden för att generera hälsohändelser genom att skriva tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverks-/undernätverksparet genereras en hälsohändelse.
4. Klicka på knappen ON-PREM AGENTS **Add Agents** om du vill lägga till de lokala servrar som du vill övervaka den privata peering-anslutningen från. Se till att du bara väljer agenter som har anslutning till Microsoft-tjänstens slutpunkt som du angav i avsnittet för steg 2. De lokala agenterna måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutningen.
5. Spara inställningarna.
6. När du har aktiverat reglerna och valt de värden och agenter som du vill övervaka väntar det cirka 30-60 minuter på att värdena ska börja fyllas och **ExpressRoute Monitoring-panelerna** blir tillgängliga.

### <a name="microsoft-peering"></a>Microsoft-peering

För Microsoft-peering klickar du på de Microsoft-peering-anslutningar som du vill övervaka och konfigurerar inställningarna.

1. Markera kryssrutan **Övervaka den här peering-kryssrutan.** 
2. (Valfritt) Du kan ändra slutpunkten för tjänsten För Mål. Som standard väljer NPM en Microsoft-tjänstslutpunkt som mål. NPM övervakar anslutningen från lokala servrar till den här målslutpunkten via ExpressRoute. 
    * Om du vill ändra den här målslutpunkten klickar du på **länken (redigera)** under **Mål:** och väljer en annan slutpunkt för Microsoft-tjänstmål i listan med webbadresser.
      ![redigera mål](./media/how-to-npm/edit_target.png)<br>

    * Du kan använda en anpassad URL eller IP-adress. Det här alternativet är särskilt relevant om du använder Microsoft-peering för att upprätta en anslutning till Azure PaaS-tjänster, till exempel Azure Storage, SQL-databaser och webbplatser som erbjuds på offentliga IP-adresser. Det gör du genom att klicka på länken **(Använd anpassad URL eller IP-adress i stället)** längst ned i URL-listan och ange sedan den offentliga slutpunkten för din Azure PaaS-tjänst som är ansluten via ExpressRoute Microsoft-peering.
    ![anpassad URL](./media/how-to-npm/custom_url.png)<br>

    * Om du använder de här valfria inställningarna kontrollerar du att endast Microsoft-tjänstens slutpunkt är markerad här. Slutpunkten måste vara ansluten till ExpressRoute och nås av lokala agenter.
3. Markera kryssrutan **Aktivera hälsoövervakning för den här peeringen**.
4. Välj övervakningsförhållanden. Du kan ange anpassade tröskelvärden för att generera hälsohändelser genom att skriva tröskelvärden. När värdet för villkoret överskrider det valda tröskelvärdet för det valda nätverks-/undernätverksparet genereras en hälsohändelse.
5. Klicka på knappen ON-PREM AGENTS **Add Agents** om du vill lägga till de lokala servrar som du vill övervaka Microsoft-peering-anslutningen från. Se till att du bara väljer agenter som har anslutning till de Microsoft-tjänstslutpunkter som du angav i avsnittet för steg 2. De lokala agenterna måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutningen.
6. Spara inställningarna.
7. När du har aktiverat reglerna och valt de värden och agenter som du vill övervaka väntar det cirka 30-60 minuter på att värdena ska börja fyllas och **ExpressRoute Monitoring-panelerna** blir tillgängliga.

## <a name="step-6-view-monitoring-tiles"></a><a name="explore"></a>Steg 6: Visa övervakningspaneler

När du ser övervakningspanelerna övervakas dina ExpressRoute-kretsar och anslutningsresurser av NPM. Du kan klicka på Microsoft Peering-panelen för att öka detaljnivån för hälsotillståndet för Microsoft Peering-anslutningar.

![övervaka plattor](./media/how-to-npm/15.png)

### <a name="network-performance-monitor-page"></a><a name="dashboard"></a>Sidan Övervakare av nätverksprestanda

NPM-sidan innehåller en sida för ExpressRoute som visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peerings.

![Instrumentpanel](./media/how-to-npm/dashboard.png)

### <a name="list-of-circuits"></a><a name="circuits"></a>Lista över kretsar

Om du vill visa en lista över alla övervakade ExpressRoute-kretsar klickar du på panelen **ExpressRoute-kretsar.** Du kan välja en krets och visa dess hälsotillstånd, trenddiagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja ett anpassat tidsfönster för att rita diagrammen. Du kan dra musen över ett område i diagrammet för att zooma in och se detaljerade datapunkter.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend-of-loss-latency-and-throughput"></a><a name="trend"></a>Trend av förlust, latens och dataflöde

Diagrammen för bandbredd, svarstid och förlust är interaktiva. Du kan zooma in i alla delar av dessa diagram med hjälp av muskontroller. Du kan också se bandbredd, svarstid och förlustdata för andra intervall genom att klicka på **Datum/tid**, som finns under knappen Åtgärder längst upp till vänster.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings-list"></a><a name="peerings"></a>Peerings lista

Om du vill visa en lista över alla anslutningar till virtuella nätverk via privat peering klickar du på panelen **Privata peerings** på instrumentpanelen. Här kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trenddiagram för paketförlust, bandbreddsutnyttjande och svarstid.

![kretslista](./media/how-to-npm/peerings.png)

### <a name="nodes-view"></a><a name="nodes"></a>Vyn Noder

Om du vill visa en lista över alla länkar mellan lokala noder och Azure VMs/Microsoft-tjänstslutpunkter för den valda ExpressRoute-peering-anslutningen klickar du på **Visa nodlänkar**. Du kan visa hälsostatus för varje länk, samt trenden för förlust och svarstid som är associerad med dem.

![vyn noder](./media/how-to-npm/nodes.png)

### <a name="circuit-topology"></a><a name="topology"></a>Kretstopologi

Om du vill visa kretstopologin klickar du på panelen **Topologi.** Detta tar dig till topologin vyn för den valda kretsen eller peering. Topologidiagrammet innehåller svarstiden för varje segment i nätverket. Varje lager 3 hop representeras av en nod i diagrammet. Klicka på ett hopp avslöjar mer information om hopp.

Du kan öka synlighetsnivån så att den inkluderar lokala hopp genom att flytta skjutreglaget under **Filter**. Om du flyttar skjutreglaget åt vänster eller höger ökar/minskar antalet hopp i topologidiagrammet. Svarstiden för varje segment är synlig, vilket möjliggör snabbare isolering av segment med hög latens i nätverket.

![filter](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi vy av en krets

I den här vyn visas VNet-anslutningar.
![detaljerad topologi](./media/how-to-npm/17.png)
