---
title: Konfigurera nätverket Prestandaövervakaren för Azure ExpressRoute-kretsar | Microsoft Docs
description: Konfigurera molnbaserade nätverksövervakning (NPM) för Azure ExpressRoute-kretsar. Detta omfattar övervakning över privata ExpressRoute-peering och Microsoft-peering.
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
ms.openlocfilehash: 47f219b7319e4d2bbadf03954f7bd7f6f39da3b4
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128987"
---
# <a name="configure-network-performance-monitor-for-expressroute"></a>Konfigurera övervakare av nätverksprestanda för ExpressRoute

Network Performance Monitor (NPM) är en molnbaserad nätverk övervakningslösning som övervakar anslutning mellan Azure-molndistributioner och lokala platser (avdelningskontor, etc.). NPM är en del av logganalys. NPM erbjuder ett tillägg för ExpressRoute där du kan övervaka nätverkets prestanda över ExpressRoute-kretsar som är konfigurerade för att använda privat peering eller Microsoft-peering. När du konfigurerar NPM för ExpressRoute kan du identifiera problem för att identifiera och eliminera. Den här tjänsten är också tillgänglig för Azure Government-molnet.

Du kan:

* Övervaka förluster eller fördröjningar mellan olika Vnet och Ställ in aviseringar

* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket

* Felsökning av nätverksproblem med tillfälliga och tidpunkt i som är svåra att replikera

* Att fastställa ett visst segment i nätverket som ansvarar för försämrade prestanda

* Hämta genomströmning per virtuellt nätverk (om du har agenter som installerats i varje virtuellt nätverk)

* Se ExpressRoute systemtillståndet från en tidigare punkt i tiden

## <a name="workflow"></a>Arbetsflöde

Övervakning agenter är installerade på flera servrar, både lokalt och i Azure. Agenterna kommunicera med varandra, men inte skickar data, de skicka paket för TCP-handskakningen. Kommunikation mellan agenterna kan Azure för att mappa nätverkstopologi och sökvägen trafiken kan ta.

1. Skapa en arbetsyta för NPM. Det här är samma som en OMS-arbetsyta.
2. Installera och konfigurera programvaruagenter: 
    * Installera övervakning agenter på lokala servrar och virtuella Azure-datorer (för att privat peering).
    * Konfigurera inställningarna för övervakning agenten servrarna så att övervakningsagenter kommunicera. (Öppna portar i brandväggen, osv.)
3. Konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta övervakning agenten installeras på virtuella Azure-datorer kan kommunicera med lokal övervakning agenter.
4. Konfigurera övervakning: automatiskt identifiera och hantera vilka nätverk som visas i NPM.

Om du redan använder Network Performance Monitor för att övervaka andra objekt eller tjänster och du redan har arbetsytan i något av regionerna som stöds, kan du hoppa över steg 1 och 2 och börjar din konfiguration med steg3.

## <a name="configure"></a>Steg 1: Skapa en arbetsyta

Skapa en arbetsyta i den prenumeration som har Vnet-länk till ExpressRoute circuit(s).

1. I den [Azure-portalen](https://portal.azure.com), Välj den prenumeration som har Vnet peerkoppla till ExpressRoute-krets. Sök sedan efter listan över tjänster i den **Marketplace** för 'Network Performance Monitor'. Återkommer, klicka för att öppna den **Network Performance Monitor** sidan.

   >[!NOTE]
   >Du kan skapa en ny arbetsyta eller använda en befintlig arbetsyta. Om du vill använda en befintlig arbetsyta måste du se till att arbetsytan har migrerats till det nya språket i fråga. [Mer information...](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-upgrade)
   >

   ![portal](.\media\how-to-npm\3.png)<br><br>
2. Längst ned i huvudsakliga **Network Performance Monitor** klickar du på **skapa** att öppna **Network Performance Monitor - skapa nya lösningen** sidan. Klicka på **OMS-arbetsyta - Välj en arbetsyta** att öppna sidan arbetsytor. Klicka på **+ Skapa ny arbetsyta** att öppna sidan arbetsytan.
3. På den **OMS-arbetsytan** väljer **Skapa nytt**, konfigurera följande inställningar:

  * OMS-arbetsyta - ange ett namn för din arbetsyta.
  * Prenumeration – om du har flera prenumerationer, Välj den du vill koppla till det nya arbetsområdet.
  * Resursgrupp – skapa en resursgrupp eller Använd en befintlig.
  * Plats – den här platsen används för att ange platsen för lagringskontot som används för agenten anslutning loggarna.
  * Prisnivån - Välj prisnivå.
  
    >[!NOTE]
    >ExpressRoute-kretsen kan finnas var som helst i världen. Det behöver inte finnas i samma region som arbetsytan.
    >
  
    ![arbetsyta](.\media\how-to-npm\4.png)<br><br>
4. Klicka på **OK** att spara och distribuera inställningar för mallen. När mallen validerar klickar du på **skapa** att distribuera på arbetsytan.
5. Efter att arbetsytan har distribuerats, navigera till den **NetworkMonitoring(name)** resurs som du skapade. Verifiera inställningarna och klicka sedan på **lösningen kräver ytterligare konfiguration**.

   ![ytterligare konfigurering](.\media\how-to-npm\5.png)

## <a name="agents"></a>Steg 2: Installera och konfigurera agenter

### <a name="download"></a>2.1: ladda ned installationsfilen agent

1. Gå till den **gemensamma inställningar för** för den **nätverkskonfigurationen Performance Monitor** sidan för din resurs. Klickar du på agenten som motsvarar serverns processor från den **installera OMS-agenter** avsnittet och hämta installationsfilen.
2. Kopiera den **arbetsyte-ID** och **primärnyckel** till anteckningar.
3. Från den **konfigurera OMS-agenter för övervakning med hjälp av TCP-protokollet** avsnittet måste du ladda ned Powershell-skript. PowerShell-skriptet kan du öppna den relevanta brandväggsporten för TCP-transaktioner.

  ![PowerShell-skript](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: Installera en övervakningsagent på varje övervakning server (för varje virtuella nätverk som du vill övervaka)

Vi rekommenderar att du installerar minst två agenter på varje sida av ExpressRoute-anslutning för redundans (till exempel lokalt, virtuella Azure-nätverk). Agenten måste installeras på en Windows-Server (2008 SP1 eller senare). Övervakning med hjälp av Windows Desktop OS- och Linux-Operativsystemdatorn ExpressRoute-kretsar stöds inte. Använd följande steg för att installera agenter:
   
  >[!NOTE]
  >Agenter pushas av SCOM (omfattar [MMA](https://technet.microsoft.com/library/dn465154(v=sc.12).aspx)) kanske inte kan identifiera deras plats konsekvent om de finns i Azure. Vi rekommenderar att du inte använder de här agenterna i virtuella Azure-nätverk för att övervaka ExpressRoute.
  >

1. Kör **installationsprogrammet** att installera agenten på varje server som du vill använda för att övervaka ExpressRoute. Den server du använder för att övervaka kan antingen vara en virtuell dator eller lokalt och måste ha tillgång till Internet. Du måste installera minst en agent lokalt och en agent på varje nätverkssegment som du vill övervaka i Azure.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På den **licensvillkoren** sidan, Läs licensvillkoren och klicka sedan på **jag accepterar**.
4. På den **målmappen** , ändra eller behålla standardinstallationsmappen, och klickar sedan på **nästa**.
5. På den **installationsalternativ för Agent** sidan kan du ansluta agenten till Azure logganalys eller Operations Manager. Eller lämna alternativen tomt om du vill konfigurera agenten senare. När du har gjort dina selection(s) klickar du på **nästa**.

  * Om du väljer att ansluta till **Azure logganalys**, klistra in den **arbetsyte-ID** och **Arbetsytenyckel** (primärnyckel) som du kopierade i anteckningar i föregående avsnitt. Klicka sedan på **Nästa**.

    ![ID och nyckel](.\media\how-to-npm\8.png)
  * Om du vill ansluta till **Operations Manager**på den **Hanteringsgruppkonfiguration** anger du den **Hanteringsgruppnamn**, **Management Server** , och **Hanteringsserverporten**. Klicka sedan på **Nästa**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * På den **Agentåtgärdskontot** och välj någon av **lokalt System** konto, eller **domän eller lokalt datorkonto**. Klicka sedan på **Nästa**.

    ![Konto](.\media\how-to-npm\10.png)
6. På den **klar att installera** sidan Granska dina val och klicka sedan på **installera**.
7. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.
8. När du är klar visas Microsoft Monitoring Agent på Kontrollpanelen. Du kan granska konfigurationen av det och kontrollera att agenten är ansluten till Azure logganalys (OMS). När du är ansluten, agenten visas ett meddelande om: **i Microsoft Monitoring Agent har lyckats ansluta till tjänsten Microsoft Operations Management Suite**.

9. Upprepa proceduren för varje VNET som du behöver övervakas.

### <a name="proxy"></a>2.3: konfigurera proxyinställningar (valfritt)

Om du använder en webbproxy för att få åtkomst till Internet, kan du använda följande steg för att konfigurera proxyinställningar för Microsoft Monitoring Agent. Utför de här stegen för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så fall, se [konfigurera proxyinställningar för Microsoft Monitoring Agent använder ett skript för](../log-analytics/log-analytics-windows-agent.md).

Konfigurera proxyinställningar för Microsoft Monitoring Agent via Kontrollpanelen:

1. Öppna den **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.
4. Välj **använder en proxyserver** och ange URL och portnummer, om en sådan krävs. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: Verifiera agent

Du kan enkelt kontrollera om dina agenter kommunicerar.

1. Öppna på en server med en övervakningsagent på **Kontrollpanelen**.
2. Öppna den **Microsoft Monitoring Agent**.
3. Klicka på den **Azure logganalys** fliken.
4. I den **Status** kolumn, bör du se att agenten har ansluten till logganalys.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: öppna portar i brandväggen på övervakning agenten servrarna

Om du vill använda TCP-protokollet måste du öppna portar i brandväggen för att säkerställa att övervakning agenterna kan kommunicera.

Du kan köra ett PowerShell-skript för att skapa registernycklar som krävs av Network Performance Monitor. Det här skriptet skapar även Windows-brandväggsreglerna för att tillåta övervakning agenter för att skapa TCP-anslutningar med varandra. Registernycklar som skapats av skriptet ange om du vill logga debug-loggar och sökvägen för filen loggar. Den definierar även agent TCP-port som används för kommunikation. Skriptet anges automatiskt värden för nycklarna. Du bör inte ändra dessa nycklar manuellt.

Port 8084 öppnas som standard. Du kan använda en anpassad port genom att ange parametern portNumber om du till skriptet. Om du gör det måste du ange samma port för alla servrar som du kör skriptet.

>[!NOTE]
>'EnableRules' PowerShell-skriptet konfigurerar Windows-brandväggsreglerna endast på den server där skriptet körs. Om du har en brandvägg för nätverk, bör du kontrollera att den tillåter trafik för TCP-porten som används av Network Performance Monitor.
>
>

Öppna ett PowerShell-fönster med administrativ behörighet på agentservrar. Kör den [EnableRules](https://aka.ms/npmpowershellscript) PowerShell-skript (som du hämtade tidigare). Använd inte några parametrar.

![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Steg 3: Konfigurera regler för nätverkssäkerhetsgrupper

Om du vill övervaka agentservrar som finns i Azure måste du konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på en port som används av NPM för syntetiska transaktioner. Standardporten är 8084. Detta gör att en övervakning agenten är installerad på en Azure-VM för att kommunicera med en lokal övervakningsagent.

Mer information om NSG finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Kontrollera att du har installerat agenterna (server-agenten lokalt och Azure-server-agent) och har kört PowerShell-skriptet innan du fortsätter med det här steget.
>

## <a name="setupmonitor"></a>Steg 4: Identifiera peering anslutningar

1. Navigera till översiktsikon Network Performance Monitor genom att gå till den **alla resurser** sidan och klicka på listan över godkända NPM arbetsytan.

  ![npm-arbetsytan](.\media\how-to-npm\npm.png)
2. Klicka på den **Network Performance Monitor** översiktsikon att öppna den på instrumentpanelen. Instrumentpanelen innehåller en ExpressRoute-sida, som visar att ExpressRoute är i ett okonfigurerat tillstånd. Klicka på **installationsprogram** att öppna sidan Network Performance Monitor-konfiguration.

  ![Installationsprogram för](.\media\how-to-npm\npm2.png)
3. Gå till fliken 'ExpressRoute Peerings' på panelen vänster på konfigurationssidan. Klicka sedan på **identifiera nu**.

  ![upptäck](.\media\how-to-npm\13.png)
4. När identifieringen har slutförts visas en lista som innehåller följande:
  * Alla anslutningar Microsoft peering i ExpressRoute-circuit(s) som är associerade med den här prenumerationen.
  * Alla privata peering anslutningar som ansluter till Vnet som är associerade med den här prenumerationen.
            
## <a name="configmonitor"></a>Steg 5: Konfigurera bildskärmar

I det här avsnittet kan du konfigurera övervakarna. Följ stegen för typ av peering som du vill övervaka: **privat peering**, eller **Microsoft-peering**.

### <a name="private-peering"></a>Privat peering

För att privat peering när identifieringen har slutförts visas kommer regler för unika **krets namnet** och **VNet namn**. Inledningsvis har reglerna inaktiverats.

![regler](.\media\how-to-npm\14.png)

1. Kontrollera den **övervaka den här peering** kryssrutan.
2. Markera kryssrutan **aktivera övervakning av hälsotillstånd för den här peering**.
3. Välj villkor som övervakning. Du kan ange anpassade tröskelvärden för att generera hälsa händelser genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverk/undernätverk paret, skapas en hälsohändelse.
4. Klicka på lokal agenter **lägga till agenter** för att lägga till de lokala servrar som du vill övervaka privat peering-anslutning. Se till att du endast välja agenter som har nätverksanslutning till slutpunkten för Microsoft-tjänst som du angav i avsnittet för steg 2. Lokal-agenter måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutningen.
5. Spara inställningarna.
6. När du aktiverar regler och välja värden och agenter som du vill övervaka, är en Vänta ungefär 30 till 60 minuter för värden att börja fylla och **ExpressRoute övervakning** paneler ska bli tillgänglig.

### <a name="microsoft-peering"></a>Microsoft-peering

På Microsoft peering anslutningarna som du vill övervaka och konfigurera inställningar för Microsoft-peering.

1. Kontrollera den **övervaka den här peering** kryssrutan. 
2. (Valfritt) Du kan ändra mål Microsoft tjänsteslutpunkt. Som standard väljer NPM en Microsoft-tjänstslutpunkt som mål. NPM övervakar anslutningen mellan din lokala servrar och slutpunkten målet via ExpressRoute. 
    * Om du vill ändra den här slutpunkten mål klickar du på den **(redigera)** länken under **mål:**, och välj en annan Microsoft mål tjänstslutpunkten från listan över URL: er.
      ![Redigera mål](.\media\how-to-npm\edit_target.png)<br>

    * Du kan använda en anpassad URL eller IP-adress. Det här alternativet är särskilt relevant om du använder Microsoft-peering för att upprätta en anslutning till Azure PaaS-tjänster, till exempel Azure Storage, SQL-databaser och webbplatser som erbjuds på offentliga IP-adresser. Det gör du genom att klicka på länken **(Använd anpassad URL eller IP-adress i stället)** längst ned i listan över URL: en anger offentliga slutpunkten för din Azure PaaS-tjänst som är ansluten via Microsoft ExpressRoute-peering.
    ![Anpassad URL](.\media\how-to-npm\custom_url.png)<br>

    * Om du använder de här valfria inställningarna kan du kontrollera att endast Microsoft tjänstslutpunkten valt här. Slutpunkten måste vara ansluten till ExpressRoute och kan nås av lokala agenter.
3. Markera kryssrutan **aktivera övervakning av hälsotillstånd för den här peering**.
4. Välj villkor som övervakning. Du kan ange anpassade tröskelvärden för att generera hälsa händelser genom att skriva tröskelvärden. När värdet för villkoret går över dess valda tröskelvärdet för det valda nätverk/undernätverk paret, skapas en hälsohändelse.
5. Klicka på lokal agenter **lägga till agenter** för att lägga till de lokala servrar som du vill övervaka Microsoft peering-anslutning. Se till att du endast välja agenter som har en anslutning till tjänsten Microsoft-slutpunkter som du angav i avsnittet för steg 2. Lokal-agenter måste kunna nå slutpunkten med hjälp av ExpressRoute-anslutningen.
6. Spara inställningarna.
7. När du aktiverar regler och välja värden och agenter som du vill övervaka, är en Vänta ungefär 30 till 60 minuter för värden att börja fylla och **ExpressRoute övervakning** paneler ska bli tillgänglig.

## <a name="explore"></a>Steg 6: Visa övervakning paneler

När du ser övervakning paneler övervakas dina ExpressRoute-kretsar och anslutningen resurser av NPM. Du kan klicka på ikonen för Microsoft-Peering och öka detaljnivån på hälsotillståndet för Microsoft-Peering-anslutningar.

![övervakning av paneler](.\media\how-to-npm\15.png)

### <a name="dashboard"></a>Sidan för nätverk Prestandaövervakaren

Sidan NPM innehåller en sida för ExpressRoute som visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peerkopplingar.

![Instrumentpanel](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Lista över kretsar

Visa en lista över alla övervakade ExpressRoute-kretsar klickar du på den **ExpressRoute-kretsar** panelen. Du kan välja en krets och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja en anpassad tidsfönstret för att rita upp diagram. Du kan dra muspekaren över ett område på diagrammet för att zooma in och visa detaljerade datapunkter.

![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Trend för dataförlust, svarstid och genomströmning

Diagram för bandbredd, svarstid och dataförlust är interaktiva. Du kan zooma in en del av dessa diagram med hjälp av musen kontroller. Du kan också se bandbredd, svarstid och förlust av data för andra intervall genom att klicka på **tidsvärdet**, som finns under åtgärder-knappen längst upp till vänster.

![Trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Peerkopplingar lista

Visa en lista över alla anslutningar till virtuella nätverk via privat peering, klicka på **privata Peerings** panelen på instrumentpanelen. Här, kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid.

![kretsen lista](.\media\how-to-npm\peerings.png)

### <a name="nodes"></a>Visa noder

Visa en lista över alla länkar mellan lokala noder och Azure virtuella datorer/Microsoft slutpunkter för den valda peering ExpressRoute-anslutningen, klicka **visa nodlänkar**. Du kan visa hälsostatus för varje länk samt trend förluster eller fördröjningar associerade.

![Visa noder](.\media\how-to-npm\nodes.png)

### <a name="topology"></a>Kretsen topologi

Om du vill visa krets topologi, klickar du på den **topologi** panelen. Då kommer du till vyn topologin för den valda krets eller peering. Diagram över topologi ger svarstid för varje segment i nätverket. Varje nivå 3-hopp representeras av en nod i diagrammet. Klicka på ett hopp visar mer information om hopp.

Du kan öka synlighet att inkludera lokalt hopp genom att flytta skjutreglaget nedan **filter**. Flytta skjutreglaget till vänster eller höger, ökar/minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment visas, vilket gör att för snabbare isolering av fördröjningar segment i nätverket.

![filter](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi vy av en krets

Den här vyn visar VNet-anslutningar.
![Detaljerad topologi](.\media\how-to-npm\17.png)