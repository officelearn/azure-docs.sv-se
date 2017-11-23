---
title: "Konfigurera nätverket Prestandaövervakaren för Azure ExpressRoute-kretsar (förhandsversion) | Microsoft Docs"
description: "Konfigurera NPM för Azure ExpressRoute-kretsar. (Förhandsversion)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 9e459a42a9fd7caedfa255a7baf51273eef2265a
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Konfigurera nätverket Prestandaövervakaren för ExpressRoute (förhandsgranskning)

Network Performance Monitor (NPM) är en molnbaserad nätverk övervakningslösning som övervakar anslutning mellan Azure-molndistributioner och lokala platser (avdelningskontor, etc.). NPM är en del av Microsoft Operations Management Suite (OMS). NPM erbjuder ett tillägg för ExpressRoute där du kan övervaka nätverkets prestanda över ExpressRoute-kretsar som är konfigurerade för att använda privat Peering. När du konfigurerar NPM för ExpressRoute kan du identifiera problem för att identifiera och eliminera.

Du kan:

* Övervaka förluster eller fördröjningar mellan olika Vnet och Ställ in aviseringar

* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket

* Felsökning av nätverksproblem med tillfälliga och tidpunkt i som är svåra att replikera

* Att fastställa ett visst segment i nätverket som ansvarar för försämrade prestanda

* Hämta genomströmning per virtuellt nätverk (om du har agenter som installerats i varje virtuellt nätverk)

* Se ExpressRoute systemtillståndet från en tidigare punkt i tiden

## <a name="regions"></a>Regioner som stöds

Du kan övervaka ExpressRoute-kretsar i alla delar av världen med hjälp av en arbetsyta som ligger på något av följande områden:

* Västra Europa 
* Östra USA 
* Sydostasien 

## <a name="workflow"></a>Arbetsflöde

Övervakning agenter är installerade på flera servrar, både lokalt och i Azure. Agenterna kommunicera med varandra, men inte skickar data, de skicka paket för TCP-handskakningen. Kommunikation mellan agenterna kan Azure för att mappa nätverkstopologi och sökvägen trafiken kan ta.

1. Skapa en arbetsyta för NPM i något av de [regioner som stöds](#regions).
2. Installera och konfigurera programvaruagenter: 
    * Installera övervakning agenter på lokala servrar och virtuella Azure-datorer.
    * Konfigurera inställningarna för övervakning agenten servrarna så att övervakningsagenter kommunicera. (Öppna portar i brandväggen, osv.)
3. Konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta övervakning agenten installeras på virtuella Azure-datorer kan kommunicera med lokal övervakning agenter.
4. Begäran om att godkända NPM-arbetsytan.
5. Konfigurera övervakning: automatiskt identifiera och hantera vilka nätverk som visas i NPM.

Om du redan använder Network Performance Monitor för att övervaka andra objekt eller tjänster och du redan har arbetsytan i något av regionerna som stöds, kan du hoppa över steg 1 och 2 och börjar din konfiguration med steg3.

## <a name="configure"></a>Steg 1: Skapa en arbetsyta

1. I den [Azure-portalen](https://portal.azure.com), Sök i listan över tjänster i den **Marketplace** för 'Network Performance Monitor'. Återkommer, klicka för att öppna den **Network Performance Monitor** sidan.

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. Längst ned i huvudsakliga **Network Performance Monitor** klickar du på **skapa** att öppna **Network Performance Monitor - skapa nya lösningen** sidan. Klicka på **OMS-arbetsyta - Välj en arbetsyta** att öppna sidan arbetsytor. Klicka på **+ Skapa ny arbetsyta** att öppna sidan arbetsytan.
3. På den **OMS-arbetsytan** väljer **Skapa nytt** och konfigurera följande inställningar:

  * OMS-arbetsyta - ange ett namn för din arbetsyta.
  * Prenumeration – om du har flera prenumerationer, Välj den du vill koppla till det nya arbetsområdet.
  * Resursgrupp – skapa en resursgrupp eller Använd en befintlig.
  * Plats – du måste välja en [stöds region](#regions).
  * Prisnivån - väljer lediga
  
  >[!NOTE]
  >ExpressRoute-kretsen kan finnas var som helst i världen och behöver inte finnas i samma region som arbetsytan.
  >


  ![Arbetsytan](.\media\how-to-npm\4.png)<br><br>
4. Klicka på **OK** att spara och distribuera inställningar för mallen. När mallen validerar klickar du på **skapa** att distribuera på arbetsytan.
5. Efter att arbetsytan har distribuerats, navigera till den **NetworkMonitoring(name)** resurs som du skapade. Verifiera inställningarna och klicka sedan på **lösningen kräver ytterligare konfiguration**.

  ![ytterligare konfigurering](.\media\how-to-npm\5.png)
6. På den **Välkommen till Network Performance Monitor** väljer **används TCP för syntetiska transaktioner**, klicka på **skicka**. TCP-transaktioner används bara för att se och bryta anslutningen. Inga data skickas över dessa TCP-anslutningar.

  ![TCP för syntetiska transaktioner](.\media\how-to-npm\6.png)

## <a name="agents"></a>Steg 2: Installera och konfigurera agenter

### <a name="download"></a>2.1: ladda ned installationsfilen agent

1. På den **nätverk prestanda Monitor-konfiguration – TCP installationssidan** för din resurs i den **installera OMS-agenter** klickar du på agenten som motsvarar serverns processor och ladda ned den installationsfilen.

  >[!NOTE]
  >Agenten måste installeras på en Windows-Server (2008 SP1 eller senare). Övervakning av ExpressRoute-kretsar med hjälp av Windows Desktop OS- och Linux-operativsystem stöds inte. 
  >
  >
2. Kopiera den **arbetsyte-ID** och **primärnyckel** till anteckningar.
3. I den **konfigurera agenter** avsnittet måste du ladda ned Powershell-skript. PowerShell-skriptet kan du öppna den relevanta brandväggsporten för TCP-transaktioner.

  ![PowerShell-skript](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: Installera en övervakningsagent på varje server som övervakning

Vi rekommenderar att du installerar minst två agenter på varje sida av ExpressRoute-anslutning (d.v.s. lokalt, Azure Vnet) för redundans. Använd följande steg för att installera agenter:

1. Kör **installationsprogrammet** att installera agenten på varje server som du vill använda för att övervaka ExpressRoute. Den server du använder för att övervaka kan antingen vara en virtuell dator eller lokalt och måste ha tillgång till Internet. Du måste installera minst en agent lokalt och en agent på varje nätverkssegment som du vill övervaka i Azure.
2. På den **Välkommen** klickar du på **nästa**.
3. På den **licensvillkoren** sidan, Läs licensvillkoren och klicka sedan på **jag accepterar**.
4. På den **målmappen** , ändra eller behålla standardinstallationsmappen, och klickar sedan på **nästa**.
5. På den **installationsalternativ för Agent** sidan kan du ansluta agenten till Azure logganalys (OMS) eller Operations Manager. Eller lämna alternativen tomt om du vill konfigurera agenten senare. När du har gjort dina selection(s) klickar du på **nästa**.

  * Om du väljer att ansluta till **Azure logganalys (OMS)**, klistra in den **arbetsyte-ID** och **Arbetsytenyckel** (primärnyckel) som du kopierade i anteckningar i föregående avsnitt. Klicka sedan på **Nästa**.

    ![ID och nyckel](.\media\how-to-npm\8.png)
  * Om du vill ansluta till **Operations Manager**på den **Hanteringsgruppkonfiguration** anger du den **Hanteringsgruppnamn**, **Management Server** , och **Hanteringsserverporten**. Klicka sedan på **Nästa**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * På den **Agentåtgärdskontot** och välj någon av **lokalt System** konto, eller **domän eller lokalt datorkonto**. Klicka sedan på **Nästa**.

    ![Konto](.\media\how-to-npm\10.png)
6. På den **klar att installera** sidan Granska dina val och klicka sedan på **installera**.
7. På den **konfigurationen slutförts** klickar du på **Slutför**.
8. När du är klar visas Microsoft Monitoring Agent på Kontrollpanelen. Du kan granska konfigurationen av det och kontrollera att agenten är ansluten till Operational Insights (OMS). När du är ansluten till OMS agenten visas ett meddelande om: **i Microsoft Monitoring Agent har lyckats ansluta till tjänsten Microsoft Operations Management Suite**.

### <a name="proxy"></a>2.3: konfigurera proxyinställningar (valfritt)

Om du använder en webbproxy för att få åtkomst till Internet, kan du använda följande steg för att konfigurera proxyinställningar för Microsoft Monitoring Agent. Utför de här stegen för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så fall, se [konfigurera proxyinställningar för Microsoft Monitoring Agent använder ett skript för](../log-analytics/log-analytics-windows-agents.md#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

Konfigurera proxyinställningar för Microsoft Monitoring Agent via Kontrollpanelen:

1. Öppna den **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.
4. Välj **använder en proxyserver** och ange URL och portnummer, om en sådan krävs. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.

  ![Proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: Verifiera agent

Du kan enkelt kontrollera om dina agenter kommunicerar.

1. Öppna på en server med en övervakningsagent på **Kontrollpanelen**.
2. Öppna den **Microsoft Monitoring Agent**.
3. Klicka på den **Azure logganalys (OMS)** fliken.
4. I den **Status** kolumn, bör du se att agenten har ansluten till Operations Management Suite-tjänsten.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: öppna portar i brandväggen på övervakning agenten servrarna

Om du vill använda TCP-protokollet måste du öppna portar i brandväggen för att säkerställa att övervakning agenterna kan kommunicera.

Du kan köra ett PowerShell-skript som skapar de registernycklar som krävs av Prestandaövervakaren nätverk, samt skapa regler för Windows-brandväggen för att tillåta övervakning agenter för att skapa TCP-anslutningar med varandra. Registernycklar som skapats av skriptet ange om du vill logga debug-loggar och sökvägen för filen loggar. Den definierar även agent TCP-port som används för kommunikation. Värden för nycklarna anges automatiskt av skript, så du inte bör ändra dessa nycklar manuellt.

Port 8084 öppnas som standard. Du kan använda en anpassad port genom att ange parametern portNumber om du till skriptet. Om du gör det måste du ange samma port för alla servrar som du kör skriptet.

>[!NOTE]
>'EnableRules' PowerShell-skriptet konfigurerar Windows-brandväggsreglerna endast på den server där skriptet körs. Om du har en brandvägg för nätverk, bör du kontrollera att den tillåter trafik för TCP-porten som används av Network Performance Monitor.
>
>

Öppna ett PowerShell-fönster med administrativ behörighet på agentservrar. Kör den [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) PowerShell-skript (som du hämtade tidigare). Använd inte några parametrar.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Steg 3: Konfigurera regler för nätverkssäkerhetsgrupper

För monitoring agentservrar som finns i Azure måste du konfigurera regler för nätverkssäkerhetsgrupper (NSG) för att tillåta TCP-trafik på en port som används av NPM för syntetiska transaktioner. Standardporten är 8084. Detta gör att en övervakning agenten är installerad på Azure VM att kommunicera med en lokal övervakningsagent.

Mer information om NSG finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

## <a name="whitelist"></a>Steg 4: Begäran till godkända arbetsytan

>[!NOTE]
>Kontrollera att du har installerat agenterna (server-agenten lokalt och Azure-server-agent) och har kört PowerShell-skriptet innan du fortsätter med det här steget.
>
>

Innan du kan börja använda funktionen ExpressRoute övervakning av NPM, måste du ansöka om för att få din arbetsyta i listan över godkända. [Klicka här för att gå till sidan och fylla i formuläret](https://aka.ms/npmcohort). (Tips: du kanske vill öppna den här länken i ett nytt fönster eller flik). Vitlistning av processen kan ta minst en arbetsdag. När vitlistning har slutförts får du ett e-postmeddelande.

## <a name="setupmonitor"></a>Steg 5: Konfigurera NPM för ExpressRoute-övervakning

>[!WARNING]
>Inte fortsätta förrän ditt arbetsområde har godkända och du får ett e-postbekräftelse.
>
>

När du har fyllt i föregående avsnitt och kontrollera att du blivit godkända kan du konfigurera övervakning.

1. Navigera till översiktsikon Network Performance Monitor genom att gå till den **alla resurser** sidan och klicka på listan över godkända NPM arbetsytan.

  ![npm-arbetsytan](.\media\how-to-npm\npm.png)
2. Klicka på den **Network Performance Monitor** översiktsikon att öppna den på instrumentpanelen. Instrumentpanelen innehåller en ExpressRoute-sida, som visar att ExpressRoute är i ett okonfigurerat tillstånd. Klicka på **installationsprogram** att öppna sidan Network Performance Monitor-konfiguration.

  ![Installationsprogram för](.\media\how-to-npm\npm2.png)
3. Gå till fliken 'ExpressRoute Peerings' på panelen vänster på konfigurationssidan. Klicka på **identifiera nu**.

  ![Identifiera](.\media\how-to-npm\13.png)
4. När identifieringen har slutförts kan du se regler för unik krets och VNet namn. Inledningsvis har reglerna inaktiverats. Aktivera regler, och välj sedan övervakningsagenter och tröskelvärden.

  ![regler](.\media\how-to-npm\14.png)
5. När du aktiverar regler och välja värden och agenter som du vill övervaka, är en Vänta ungefär 30 till 60 minuter för värden att börja fylla och **ExpressRoute övervakning** paneler ska bli tillgänglig. När du ser övervakning paneler övervakas dina ExpressRoute-kretsar och anslutningen resurser av NPM.

  ![övervakning av paneler](.\media\how-to-npm\15.png)

## <a name="explore"></a>Steg 6: Visa övervakning paneler

### <a name="dashboard"></a>Sidan för nätverk Prestandaövervakaren

Sidan NPM innehåller en sida för ExpressRoute som visar en översikt över hälsotillståndet för ExpressRoute-kretsar och peerkopplingar.

  ![Instrumentpanel](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Kretsar lista

Om du vill se en lista över alla övervakade ExpressRoute-kretsar, klicka på den **ExpressRoute-kretsar** panelen. Du kan välja en krets och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid. Diagrammen är interaktiva. Du kan välja en anpassad tidsfönstret för att rita upp diagram. Du kan dra muspekaren över ett område på diagrammet för att zooma in och visa detaljerade datapunkter.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Trend för dataförlust, svarstid och genomströmning

Diagram för bandbredd, svarstid och dataförlust är interaktiva. Du kan zooma in en del av dessa diagram med hjälp av musen kontroller. Du kan också se bandbredd, svarstid och förlust av data för andra intervall genom att klicka på **tidsvärdet**, som finns under åtgärder-knappen längst upp till vänster.

![Trend](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Peerkopplingar lista

Klicka på den **privata Peerings** panel på instrumentpanelen visar en lista över alla anslutningar till virtuella nätverk via privat peering. Här, kan du välja en virtuell nätverksanslutning och visa dess hälsotillstånd, trend diagram för paketförlust, bandbreddsanvändning och svarstid.

  ![kretsen lista](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Kretsen topologi

Om du vill visa krets topologi, klicka på den **topologi** panelen. Då kommer du till vyn topologin för den valda krets eller peering. Diagram över topologi ger svarstid för varje segment i nätverket och varje nivå 3-hopp representeras av en nod i diagrammet. Klicka på ett hopp visar mer information om hopp.
Du kan öka synlighet att inkludera lokalt hopp genom att flytta skjutreglaget nedan **filter**. Flytta skjutreglaget till vänster eller höger, ökar/minskar antalet hopp i diagrammet topologi. Fördröjning mellan varje segment visas, vilket gör att för snabbare isolering av fördröjningar segment i nätverket.

![filter](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Detaljerad topologi vy av en krets

Den här vyn visar VNet-anslutningar.
![Detaljerad topologi](.\media\how-to-npm\17.png)
