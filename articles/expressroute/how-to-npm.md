---
title: Konfigurera Övervakare av nätverksprestanda för Azure ExpressRoute-kretsar | Microsoft Docs
description: Konfigurera molnbaserade nätverksövervakning (NPM) för Azure ExpressRoute-kretsar. Detta omfattar övervakning över ExpressRoute privat peering och Microsofts peering.
documentationcenter: na
services: expressroute
author: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: 93bafb739c35b81796186dc845f7933a82e1675b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968126"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurera övervakare av nätverksprestanda för ExpressRoute

Övervakare för nätverk-prestanda (NPM) är en molnbaserad lösning för nätverksövervakning som övervakar anslutning mellan distributioner av Azure-molnet och lokala platser (avdelningskontor, osv.). NPM är en del av Log Analytics. NPM erbjuder ett tillägg för ExpressRoute som låter dig övervaka nätverkets prestanda över ExpressRoute-kretsar som är konfigurerade för att använda privat peering eller Microsoft-peering. När du konfigurerar NPM för ExpressRoute, kan du identifiera nätverksproblem för att identifiera och eliminera. Den här tjänsten är också tillgängligt för Azure Government-molnet.

Du kan:

* Övervaka dataförluster och svarstider mellan olika virtuella nätverk och Ställ in aviseringar

* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket

* Felsök tillfälliga och point-in-time nätverksproblem som är svåra att replikera

* Att fastställa ett visst segment i nätverket som ligger bakom den försämrade prestandan

* Beräkna dataflöde per virtuellt nätverk (om du har agenter som installerats i varje virtuellt nätverk)

* Se ExpressRoute systemtillståndet från en tidigare tidpunkt i tid

## <a name="workflow"></a>Arbetsflöde

Övervakningsagenterna installeras på flera servrar, både lokalt och i Azure. Agenterna kommunicerar med varandra, men inte skickar data kan de skicka TCP handshake-paket. Kommunikation mellan agenterna kan Azure för att mappa nätverkets topologi och sökvägen kan ta en trafiken.

1. Skapa en arbetsyta för NPM. Det här är samma som en Log Analytics-arbetsyta.
2. Installera och konfigurera programvaruagenter: 
    * Installera övervakningsagenter på lokala servrar och virtuella Azure-datorer (för privat peering).
    * Konfigurera inställningar på övervakningsservrar agent för att tillåta övervakningsagenterna att kommunicera. (Öppna portar i brandväggen, osv.)
3. Konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta övervakningsagenten installeras på Azure virtuella datorer ska kunna kommunicera med en lokal övervakning av agenter.
4. Konfigurera övervakning: automatiskt upptäcka och hantera vilka nätverk som är synliga i NPM.

Om du redan använder Övervakare av nätverksprestanda för att övervaka andra objekt eller tjänster och du redan har arbetsytan i en av regionerna som stöds, kan du hoppa över steg 1 och steg 2 och börjar din konfiguration med steg3.

## <a name="configure"></a>Steg 1: Skapa en arbetsyta

Skapa en arbetsyta i den prenumeration som har virtuella nätverk-länk till ExpressRoute-kretsar.

1. I den [Azure-portalen](https://portal.azure.com), Välj den prenumeration som har de virtuella nätverken peer-kopplas till ExpressRoute-kretsen. Sök sedan i listan över tjänster i den **Marketplace** för ”Network Performance Monitor”. I returnering, klickar du på för att öppna den **Övervakare av nätverksprestanda** sidan.

   >[!NOTE]
   >Du kan skapa en ny arbetsyta eller använda en befintlig arbetsyta. Om du vill använda en befintlig arbetsyta måste du se till att arbetsytan har migrerats till det nya frågespråket. [Mer information...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](./media/how-to-npm/3.png)<br><br>
2. Längst ned i huvudsakliga **Övervakare av nätverksprestanda** klickar du på **skapa** att öppna **Network Performance Monitor - Skapa ny lösning** sidan. Klicka på **Log Analytics-arbetsyta – Välj en arbetsyta** att öppna sidan arbetsytor. Klicka på **+ Skapa ny arbetsyta** att öppna sidan för arbetsytan.
3. På den **Log Analytics-arbetsytan** väljer **Skapa ny**, konfigurera följande inställningar:

  * Log Analytics-arbetsyta – ange ett namn för din arbetsyta.
  * Prenumeration – om du har flera prenumerationer väljer du den du vill associera med den nya arbetsytan.
  * Resursgrupp – skapa en resursgrupp eller Använd en befintlig.
  * Plats – den här platsen används för att ange platsen för det lagringskonto som används för agentloggarna som anslutningen.
  * Prisnivå – Välj prisnivå.
  
    >[!NOTE]
    >ExpressRoute-kretsen kan finnas var som helst i världen. Det behöver inte finnas i samma region som arbetsytan.
    >
  
    ![arbetsyta](./media/how-to-npm/4.png)<br><br>
4. Klicka på **OK** att spara och distribuera inställningar för mallen. När mallen verifierar klickar du på **skapa** att distribuera arbetsytan.
5. När arbetsytan har distribuerats, går du till den **NetworkMonitoring(name)** resurs som du skapade. Verifiera inställningarna och klicka sedan på **ytterligare konfiguration krävs för lösningen**.

   ![ytterligare konfigurering](./media/how-to-npm/5.png)

## <a name="agents"></a>Steg 2: Installera och konfigurera agenter

### <a name="download"></a>2.1: ladda ned installationsfilen för agenten

1. Gå till den **gemensamma inställningar för** fliken den **konfiguration av övervakaren av nätverksprestanda** för din resurs. Klickar du på agenten som motsvarar din server processor från den **installera Log Analytics-agenter** avsnittet och ladda ned installationsfilen.
2. Kopiera den **arbetsyte-ID** och **primärnyckel** till anteckningar.
3. Från den **konfigurera Log Analytics-agenter för övervakning med TCP-protokoll** avsnittet, ladda ned Powershell-skriptet. PowerShell-skriptet kan du öppna den relevanta brandväggsporten för TCP-transaktioner.

  ![PowerShell-skript](./media/how-to-npm/7.png)

### <a name="installagent"></a>2.2: Installera en övervakningsagent på varje Övervakningsservern (på varje virtuellt nätverk som du vill övervaka)

Vi rekommenderar att du installerar minst två agenter på båda sidor av ExpressRoute-anslutning för redundans (exempelvis lokala, virtuella Azure-nätverk). Agenten måste installeras på en Windows-Server (2008 SP1 eller senare). Övervakning av ExpressRoute-kretsar som använder Windows Desktop OS och Linux-operativsystem stöds inte. Använd följande steg för att installera agenter:
   
  >[!NOTE]
  >Agenter som skickas av SCOM (innehåller [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) kanske inte kan identifiera deras plats konsekvent om de finns i Azure. Vi rekommenderar att du inte använder de här agenterna i virtuella Azure-nätverk för att övervaka ExpressRoute.
  >

1. Kör **installationsprogrammet** att installera agenten på varje server som du vill använda för att övervaka ExpressRoute. Den server du använder för att övervaka kan antingen vara en virtuell dator eller på plats och måste ha Internetåtkomst. Du måste installera minst en agenten lokalt och en agent på varje nätverkssegment som du vill övervaka i Azure.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På den **licensvillkoren** sidan, läser du licensen och klicka sedan på **jag accepterar**.
4. På den **målmapp** sidan, ändra eller Behåll standardinstallationsmappen och klickar sedan på **nästa**.
5. På den **installationsalternativ för Agent** sidan som du kan välja att ansluta agenten till Azure Log Analytics eller Operations Manager. Eller så kan du lämna alternativen tomt om du vill konfigurera agenten senare. När du har gjort dina val klickar du på **nästa**.

  * Om du väljer att ansluta till **Azure Log Analytics**, klistra in den **arbetsyte-ID** och **Arbetsytenyckel** (primärnyckel) som du kopierade till anteckningar i föregående avsnitt. Klicka sedan på **Nästa**.

    ![ID och nyckel](./media/how-to-npm/8.png)
  * Om du väljer att ansluta till **Operations Manager**på den **Hanteringsgruppkonfiguration** anger du den **Hanteringsgruppnamn**, **Management Server** , och **Hanteringsserverport**. Klicka sedan på **Nästa**.

    ![Operations Manager](./media/how-to-npm/9.png)
  * På den **Agentåtgärdskontot** väljer du antingen den **lokalt System** konto, eller **domän eller lokalt datorkonto**. Klicka sedan på **Nästa**.

    ![Konto](./media/how-to-npm/10.png)
6. På den **klar att installera** sidan, kontrollerar du valen och klickar sedan på **installera**.
7. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.
8. När du är klar visas Microsoft Monitoring Agent på Kontrollpanelen. Du kan granska konfigurationen där och kontrollera att agenten är ansluten till Azure Log Analytics. När du är ansluten, agenten visar ett meddelande om: **The Microsoft Monitoring Agent har anslutits till tjänsten Microsoft Operations Management Suite**.

9. Upprepa proceduren för varje virtuellt nätverk som du behöver som ska övervakas.

### <a name="proxy"></a>2.3: konfigurera proxyinställningar (valfritt)

Om du använder en webbproxy för att få åtkomst till Internet, kan du använda följande steg för att konfigurera proxyinställningar för Microsoft Monitoring Agent. Utföra dessa steg för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så, fall Se [konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript](../log-analytics/log-analytics-windows-agent.md).

Konfigurera proxyinställningar för Microsoft Monitoring Agent med Kontrollpanelen:

1. Öppna den **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.
4. Välj **använder en proxyserver** och anger du URL och portnummer, om en sådan krävs. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

  ![Proxy](./media/how-to-npm/11.png)

### <a name="verifyagent"></a>2.4: Verifiera agentanslutning

Du kan enkelt kontrollera om agenterna kommunicerar.

1. På en server med övervakningsagenten, öppna den **Kontrollpanelen**.
2. Öppna den **Microsoft Monitoring Agent**.
3. Klicka på den **Azure Log Analytics** fliken.
4. I den **Status** kolumn, bör du se att agenten har ansluten till Log Analytics.

  ![status](./media/how-to-npm/12.png)

### <a name="firewall"></a>2.5: Öppna brandväggsportar på övervakningsservrar för agenten

För att använda TCP-protokollet, måste du öppna portar i brandväggen för att säkerställa att övervakningsagenterna kan kommunicera.

Du kan köra ett PowerShell-skript för att skapa de registernycklar som krävs av Network Performance Monitor. Det här skriptet skapar även Windows-brandväggsregler som tillåter övervakning av agenter för att skapa TCP-anslutningar med varandra. De registernycklar som skapats av skriptet anger du om du vill logga debug-loggarna och sökvägen för filen loggar. Den definierar även agent-TCP-port som används för kommunikation. Värdena för dessa nycklar anges automatiskt av skriptet. Du bör inte ändra dessa nycklar manuellt.

Som standard öppnas port 8084. Du kan använda en anpassad port genom att ange parametern portNumber om du till skriptet. Om du gör det måste du ange samma port för alla servrar där du kör skriptet.

>[!NOTE]
>'EnableRules' PowerShell-skriptet konfigurerar regler för Windows-brandväggen endast på den server där skriptet körs. Om du har en brandvägg, bör du se till att den tillåter trafik till TCP-port som används av Övervakare av nätverksprestanda.
>
>

Öppna ett PowerShell-fönster med administratörsbehörighet på agentservrar. Kör den [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-skript (som du hämtade tidigare). Använd inte några parametrar.

![PowerShell_Script](./media/how-to-npm/script.png)

## <a name="opennsg"></a>Steg 3: Konfigurera regler för nätverkssäkerhetsgrupper

Om du vill övervaka agentservrar som finns i Azure måste du konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på en port som används av NPM för syntetiska transaktioner. Standardporten är 8084. På så sätt kan en övervakningsagent som är installerad på en virtuell Azure-dator ska kunna kommunicera med en lokal övervakningsagent.

Läs mer om NSG [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Se till att du har installerat agenterna (både den lokala server-agenten och Azure server-agenten) och har kört PowerShell-skriptet innan du fortsätter med det här steget.
>

## <a name="setupmonitor"></a>Steg 4: Identifiera peering-anslutningar

1. Gå till översiktspanelen Övervakare av nätverksprestanda genom att gå till den **alla resurser** sidan och klicka på listan över godkända NPM-arbetsytan.

  ![npm-arbetsyta](./media/how-to-npm/npm.png)
2. Klicka på den **Övervakare av nätverksprestanda** panelen för att ta fram på instrumentpanelen. Instrumentpanelen innehåller en ExpressRoute-sida som visar att ExpressRoute är i ett okonfigurerat tillstånd. Klicka på **Funktionsinstallation** att öppna konfigurationssidan för Övervakare av nätverksprestanda.

  ![funktionsinstallation](./media/how-to-npm/npm2.png)
3. Gå till fliken ExpressRoute-Peerings på panelen till vänster på konfigurationssidan. Klicka sedan på **identifiera nu**.

  ![upptäck](./media/how-to-npm/13.png)
4. När identifieringen har slutförts visas en lista som innehåller följande objekt:
  * Alla anslutningar som Microsoft peering i ExpressRoute-kretsar som är associerade med den här prenumerationen.
  * Alla privata peering-anslutningarna som ansluter till de virtuella nätverken som är associerade med den här prenumerationen.
            
## <a name="configmonitor"></a>Steg 5: Konfigurera Övervakare

I det här avsnittet konfigurerar du övervakarna. Följ stegen för typ av peering som du vill övervaka: **privat peering**, eller **Microsoft-peering**.

### <a name="private-peering"></a>Privat peering

För privat peering när identifieringen har slutförts visas kommer regler för unika **Kretsnamn** och **namn på virtuellt nätverk**. Inledningsvis är är dessa regler inaktiverade.

![regler](./media/how-to-npm/14.png)

1. Kontrollera den **övervaka denna peering** kryssrutan.
2. Markera kryssrutan **aktivera övervakning av hälsotillstånd för denna peering**.
3. Välj villkor som övervakning. Du kan ange anpassade tröskelvärden för att generera health-händelser genom att skriva tröskelvärdena. När värdet för villkoret går över det valda tröskelvärdet för den valda nätverk/undernätverk-par, genereras en hälsotillståndshändelse.
4. Klicka på LOKALT AGENTERNA **Lägg till agenter** för att lägga till de lokala servrar som du vill övervaka privata peering-anslutningen. Se till att du bara väljer agenter som har nätverksanslutning till slutpunkten för Microsoft-tjänst som du angav i avsnittet för steg 2. Lokala agenter måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutning.
5. Spara inställningarna.
6. När du aktiverar reglerna och välja värden och agenter som du vill övervaka, det finns en Vänta ungefär 30 – 60 minuter för värden för att börja fylla och **ExpressRoute-övervakning** paneler ska bli tillgänglig.

### <a name="microsoft-peering"></a>Microsoft-peering

På Microsoft peering-anslutningarna som du vill övervaka och konfigurera inställningar för Microsoft-peering.

1. Kontrollera den **övervaka denna peering** kryssrutan. 
2. (Valfritt) Du kan ändra mål Microsoft tjänsteslutpunkt. Som standard väljer NPM en Microsoft-tjänstslutpunkt som mål. NPM övervakar anslutningen mellan dina lokala servrar och mål slutpunkten via ExpressRoute. 
    * Om du vill ändra den här slutpunkten för målet klickar du på den **(redigera)** länka **mål:**, och välj en annan Microsoft target tjänstslutpunkt i listan över URL: er.
      ![Redigera mål](./media/how-to-npm/edit_target.png)<br>

    * Du kan använda en anpassad URL eller IP-adress. Det här alternativet är särskilt relevant om du använder Microsoft-peering för att upprätta en anslutning till Azure PaaS-tjänster, till exempel Azure Storage, SQL-databaser och webbplatser som erbjuds på offentliga IP-adresser. Gör detta genom att klicka på länken **(Använd anpassad URL eller IP-adress istället)** längst ned i URL-listan sedan ange den offentliga slutpunkten för din Azure PaaS-tjänst som är anslutna via ExpressRoute Microsoft-peering.
    ![Anpassad URL](./media/how-to-npm/custom_url.png)<br>

    * Om du använder dessa valfria inställningar, se till att endast Microsoft-tjänstslutpunkt väljs här. Slutpunkten måste vara anslutna till ExpressRoute och kan nås av den lokala agenter.
3. Markera kryssrutan **aktivera övervakning av hälsotillstånd för denna peering**.
4. Välj villkor som övervakning. Du kan ange anpassade tröskelvärden för att generera health-händelser genom att skriva tröskelvärdena. När värdet för villkoret går över det valda tröskelvärdet för den valda nätverk/undernätverk-par, genereras en hälsotillståndshändelse.
5. Klicka på LOKALT AGENTERNA **Lägg till agenter** för att lägga till de lokala servrar som du vill övervaka Microsoft-peering-anslutningen. Se till att du bara väljer agenter som har anslutning till Microsoft-tjänstslutpunkter som du angav i avsnittet för steg 2. Lokala agenter måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutning.
6. Spara inställningarna.
7. När du aktiverar reglerna och välja värden och agenter som du vill övervaka, det finns en Vänta ungefär 30 – 60 minuter för värden för att börja fylla och **ExpressRoute-övervakning** paneler ska bli tillgänglig.

## <a name="explore"></a>Steg 6: Visa övervakning paneler

När du ser övervakning panelerna övervakas din ExpressRoute-kretsar och anslutningsresurser av NPM. Du kan klicka på panelen för Microsoft-Peering att öka detaljnivån för hälsotillståndet för Microsoft-Peering-anslutningar.

![övervakning av paneler](./media/how-to-npm/15.png)

### <a name="dashboard"></a>Network Performance Monitor-sidan

NPM-sidan innehåller en sida för ExpressRoute som visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peer-kopplingar.

![Instrumentpanel](./media/how-to-npm/dashboard.png)

### <a name="circuits"></a>Lista över kretsar

Visa en lista över alla ExpressRoute-kretsar som övervakas, klickar du på den **ExpressRoute-kretsar** panelen. Du kan välja en krets och visa dess hälsotillstånd, trenddiagram för paketförlust, nyttjandet av bandbredd och latens. Diagrammen är interaktiva. Du kan välja ett anpassat tidsintervall för diagrammen. Du kan dra musen över ett område i diagrammet för att zooma in och se detaljerade datapunkter.

![circuit_list](./media/how-to-npm/circuits.png)

#### <a name="trend"></a>Trend över förlust, svarstid och dataflöde

Bandbredd, svarstid och förlust diagram är interaktiva. Du kan zooma in en del av dessa diagram med hjälp av musen kontroller. Du kan också se bandbredd, svarstid och förlust av data för andra intervall genom att klicka på **datum/tid**, som finns under knappen åtgärder i det övre vänstra hörnet.

![Trend](./media/how-to-npm/16.png)

### <a name="peerings"></a>Peerkopplingar lista

Visa en lista över alla anslutningar till virtuella nätverk över privat peering, genom att klicka på den **privat Peerings** panelen på instrumentpanelen. Här kan du kan välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trenddiagram för paketförlust, nyttjandet av bandbredd och latens.

![kretsen lista](./media/how-to-npm/peerings.png)

### <a name="nodes"></a>Visa noder

Visa en lista över alla länkar mellan lokala noder och Azure virtuella datorer/Microsoft Tjänsteslutpunkter för den valda ExpressRoute-peering-anslutningen, genom att klicka på **visa nodlänkar**. Du kan visa hälsotillståndet för varje länk, samt trenden för förlust och fördröjning som är kopplade till standardrisknivåer.

![Visa noder](./media/how-to-npm/nodes.png)

### <a name="topology"></a>Kretsen topologi

Om du vill visa krets topologi, klickar du på den **topologi** panelen. Då kommer du till vyn topologin för den valda krets eller -peering. Diagram över topologi ger svarstiden för varje segment i nätverket. Varje nivå 3-hopp representeras av en nod i diagrammet. När du klickar på ett hopp visar mer information om hoppet.

Du kan öka hur mycket av att inkludera den lokala hopp genom att flytta skjutreglaget nedan **filter**. Flytta skjutreglaget till vänster eller höger, ökar/minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment är synliga, vilket gör att för snabbare isolering med lång svarstid segment i nätverket.

![filter](./media/how-to-npm/topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi vy av en krets

Den här vyn visar VNet-anslutningar.
![Detaljerad topologi](./media/how-to-npm/17.png)