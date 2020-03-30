---
title: Ansluta datorer med hjälp av Log Analytics-gatewayen | Microsoft-dokument
description: Anslut dina enheter och Operations Manager-övervakade datorer med hjälp av Log Analytics-gatewayen för att skicka data till Azure Automation och Log Analytics-tjänsten när de inte har tillgång till internet.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/24/2019
ms.openlocfilehash: a92e96a835f24ac54fa55b05086a35b9a91d609e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298341"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Ansluta datorer utan internetåtkomst med hjälp av Log Analytics-gatewayen i Azure Monitor

>[!NOTE]
>När OMS (Microsoft Operations Management Suite) övergår till Microsoft Azure Monitor ändras terminologin. Den här artikeln refererar till OMS Gateway som Azure Log Analytics-gatewayen. 
>

I den här artikeln beskrivs hur du konfigurerar kommunikation med Azure Automation och Azure Monitor med hjälp av Log Analytics-gatewayen när datorer som är direkt anslutna eller som övervakas av Operations Manager inte har någon internetåtkomst. 

Log Analytics-gatewayen är en HTTP-vidareproxy som stöder HTTP-tunnel med kommandot HTTP CONNECT. Den här gatewayen skickar data till Azure Automation och en Log Analytics-arbetsyta i Azure Monitor för de datorer som inte direkt kan ansluta till internet. 

Log Analytics-gatewayen stöder:

* Rapportera upp till samma Log Analytics-arbetsytor som konfigurerats på varje agent bakom den och som är konfigurerade med Azure Automation Hybrid Runbook Workers.  
* Windows-datorer där Microsoft Monitoring Agent är direkt ansluten till en Log Analytics-arbetsyta i Azure Monitor.
* Linux-datorer där en Log Analytics-agent för Linux är direkt ansluten till en Log Analytics-arbetsyta i Azure Monitor.  
* System Center Operations Manager 2012 SP1 med UR7, Operations Manager 2012 R2 med UR3 eller en ledningsgrupp i Operations Manager 2016 eller senare som är integrerad med Log Analytics.  

Vissa IT-säkerhetsprinciper tillåter inte internetanslutning för nätverksdatorer. Dessa datorer som inte är anslutna kan till exempel vara kassaenheter eller servrar som stöder IT-tjänster. Om du vill ansluta dessa enheter till Azure Automation eller en Log Analytics-arbetsyta så att du kan hantera och övervaka dem konfigurerar du dem så att de kommunicerar direkt med Log Analytics-gatewayen. Log Analytics-gatewayen kan ta emot konfigurationsinformation och vidarebefordra data för deras räkning. Om datorerna är konfigurerade med Log Analytics-agenten för att ansluta direkt till en Log Analytics-arbetsyta kommunicerar datorerna i stället med Log Analytics-gatewayen.  

Log Analytics-gatewayen överför data från agenterna till tjänsten direkt. Den analyserar inte någon av de data som transiteras och gatewayen cachelagrar inte data när den förlorar anslutningen till tjänsten. När gatewayen inte kan kommunicera med tjänsten fortsätter agenten att köra och köar insamlade data på disken på den övervakade datorn. När anslutningen återställs skickar agenten cachelagrade data som samlats in till Azure Monitor.

När en Hanteringsgrupp för Operations Manager är integrerad med Log Analytics kan hanteringsservrarna konfigureras för att ansluta till Log Analytics-gatewayen för att ta emot konfigurationsinformation och skicka insamlade data, beroende på vilken lösning du har aktiverat .  Operations Manager-agenter skickar vissa data till hanteringsservern. Agenter kan till exempel skicka Operations Manager-aviseringar, konfigurationsbedömningsdata, instansutrymmesdata och kapacitetsdata. Andra högvolymsdata, till exempel IIS-loggar (Internet Information Services), prestandadata och säkerhetshändelser, skickas direkt till Log Analytics-gatewayen. 

Om en eller flera Operations Manager Gateway-servrar distribueras för att övervaka ej betrodda system i ett perimeternätverk eller ett isolerat nätverk, kan dessa servrar inte kommunicera med en Log Analytics-gateway.  Operations Manager Gateway-servrar kan bara rapportera till en hanteringsserver.  När en Hanteringsgrupp för Operations Manager är konfigurerad för att kommunicera med Log Analytics-gatewayen distribueras proxykonfigurationsinformationen automatiskt till alla agenthanterade datorer som är konfigurerade för att samla in loggdata för Azure Monitor. även om inställningen är tom.

Om du vill ge hög tillgänglighet för direkt anslutna eller Operations Management-grupper som kommunicerar med en Log Analytics-arbetsyta via gatewayen använder du NLB (Network Load Balancing) för att omdirigera och distribuera trafik över flera gatewayservrar. På så sätt omdirigeras trafiken till en annan tillgänglig nod om en gateway-server går ned.  

Datorn som kör Log Analytics-gatewayen kräver att Log Analytics Windows-agenten identifierar de tjänstslutpunkter som gatewayen behöver kommunicera med. Agenten måste också styra gatewayen för att rapportera till samma arbetsytor som agenterna eller Operations Manager-hanteringsgruppen bakom gatewayen är konfigurerade med. Med den här konfigurationen kan gatewayen och agenten kommunicera med sin tilldelade arbetsyta.

En gateway kan vara multihomed till upp till fyra arbetsytor. Det här är det totala antalet arbetsytor som en Windows-agent stöder.  

Varje agent måste ha nätverksanslutning till gatewayen så att agenter automatiskt kan överföra data till och från gatewayen. Undvik att installera gatewayen på en domänkontrollant. Linux-datorer som finns bakom en gateway-server kan inte använda [installationsmetoden för omslagsskript](agent-linux.md#install-the-agent-using-wrapper-script) för att installera Log Analytics-agenten för Linux. Agenten måste hämtas manuellt, kopieras till datorn och installeras manuellt eftersom gatewayen endast stöder kommunikation med Azure-tjänster som nämndes tidigare.

Följande diagram visar data som flödar från direkta agenter, via gatewayen, till Azure Automation och Log Analytics. Agentproxykonfigurationen måste matcha porten som Log Analytics-gatewayen har konfigurerats med.  

![Diagram över direktagentkommunikation med tjänster](./media/gateway/oms-omsgateway-agentdirectconnect.png)

I följande diagram visas dataflöde från en Hanteringsgrupp för Operations Manager till Log Analytics.   

![Diagram över Operations Manager-kommunikation med Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurera systemet

Datorer som har utsetts för att köra Log Analytics-gatewayen måste ha följande konfiguration:

* Windows 10, Windows 8.1 eller Windows 7
* Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 eller Windows Server 2008
* Microsoft .NET Framework 4.5
* Minst en 4-kärnig processor och 8 GB minne 
* En [Log Analytics-agent för Windows](agent-windows.md) som är konfigurerad för att rapportera till samma arbetsyta som de agenter som kommunicerar via gatewayen

### <a name="language-availability"></a>Språktillgänglighet

Log Analytics-gatewayen är tillgänglig på följande språk:

- Kinesiska (förenklad)
- Kinesiska (traditionell)
- Tjeckiska
- Nederländska
- Svenska
- Franska
- Tyska
- Ungerska
- Italienska
- Japanska
- Koreansk
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska (Internationellt)

### <a name="supported-encryption-protocols"></a>Krypteringsprotokoll som stöds

Log Analytics-gatewayen stöder endast Transport Layer Security (TLS) 1.0, 1.1 och 1.2.  Det stöder inte SSL (Secure Sockets Layer).  Om du vill säkerställa säkerheten för data under överföring till Log Analytics konfigurerar du gatewayen så att den använder minst TLS 1.2. Äldre versioner av TLS eller SSL är sårbara. Även om de för närvarande tillåter bakåtkompatibilitet, undvika att använda dem.  

Mer information finns i [Skicka data på ett säkert sätt med TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Antal agentanslutningar som stöds

Följande tabell visar ungefär hur många agenter som kan kommunicera med en gateway-server. Supporten baseras på agenter som laddar upp cirka 200 kB data var sjätte sekund. För varje agent som testas är datavolymen ca 2,7 GB per dag.

|Gateway |Agenter som stöds (ungefärlig)|  
|--------|----------------------------------|  
|CPU: Intel Xeon-processor E5-2660 v3 \@ 2,6 GHz 2 kärnor<br> Minne: 4 GB<br> Nätverkets bandbredd: 1 Gbit/s| 600|  
|CPU: Intel Xeon-processor E5-2660 v3 \@ 2,6 GHz 4 kärnor<br> Minne: 8 GB<br> Nätverkets bandbredd: 1 Gbit/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Ladda ned Log Analytics-gatewayen

Hämta den senaste versionen av installationsfilen för Log Analytics gateway från antingen Microsoft Download Center ([Download Link](https://go.microsoft.com/fwlink/?linkid=837444)) eller Azure-portalen.

Så här hämtar du Log Analytics-gatewayen från Azure-portalen:

1. Bläddra i listan över tjänster och välj sedan **Logga Analytics**. 
1. Välj en arbetsyta.
1. Välj **Snabbstart**under **Allmänt**i bladet på arbetsytan . 
1. Under **Välj en datakälla för anslutning till arbetsytan**väljer du **Datorer**.
1. I bladet **Direct Agent** väljer du **Hämta logganalysgateway**.
 
   ![Skärmbild av stegen för att hämta Log Analytics-gatewayen](./media/gateway/download-gateway.png)

eller 

1. Välj **Avancerade inställningar**under **Inställningar**i bladet på arbetsytan .
1. Gå till **Anslutna källor** > **Windows-servrar** och välj **Hämta logganalysgateway**.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Installera Log Analytics-gateway med installationsguiden

Så här installerar du en gateway med installationsguiden. 

1. Dubbelklicka på **Log Analytics gateway.msi**i målmappen .
1. På sidan **Välkommen** klickar du på **Nästa**.

   ![Skärmbild av välkomstsidan i guiden Konfigurera gateway](./media/gateway/gateway-wizard01.png)

1. På sidan **Licensavtal** väljer **du Jag godkänner villkoren i licensavtalet** för att godkänna licensvillkoren för Programvara från Microsoft och välj sedan **Nästa**.
1. På sidan **Port- och proxyadress:**

   a. Ange det TCP-portnummer som ska användas för gatewayen. Installationsprogrammet använder det här portnumret för att konfigurera en inkommande regel i Windows-brandväggen.  Standardvärdet är 8080.
      Det giltiga intervallet för portnumret är 1 till 65535. Om indata inte hamnar i det här intervallet visas ett felmeddelande.

   b. Om servern där gatewayen är installerad måste kommunicera via en proxy anger du proxyadressen där gatewayen behöver ansluta. Ange till exempel `http://myorgname.corp.contoso.com:80`.  Om du lämnar det här fältet tomt försöker gatewayen ansluta till internet direkt.  Om proxyservern kräver autentisering anger du ett användarnamn och lösenord.

   c. Välj **Nästa**.

   ![Skärmbild av konfigurationen för gatewayproxyen](./media/gateway/gateway-wizard02.png)

1. Om du inte har aktiverat Microsoft Update visas sidan Microsoft Update och du kan välja att aktivera den. Gör en markering och välj sedan **Nästa**. Annars fortsätter du till nästa steg.
1. Lämna standardmappen C:\Program Files\OMS Gateway på sidan **Målmapp** eller ange den plats där du vill installera gatewayen. Välj sedan **Nästa**.
1. På sidan **Klar att installera** väljer du **Installera**. Om User Account Control begär behörighet att installera väljer du **Ja**.
1. När installationen är klar väljer du **Slutför**. Om du vill kontrollera att tjänsten körs öppnar du snapin-modulen services.msc och kontrollerar att **OMS Gateway** visas i listan över tjänster och att dess status **körs**.

   ![Skärmbild av lokala tjänster som visar att OMS Gateway körs](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Installera log analytics-gatewayen med kommandoraden

Den nedladdade filen för gatewayen är ett Windows Installer-paket som stöder tyst installation från kommandoraden eller annan automatiserad metod. Om du inte är bekant med standardkommandoradsalternativen för Windows Installer läser du [Kommandoradsalternativ](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
I följande tabell visas de parametrar som stöds av installationen.

|Parametrar| Anteckningar|
|----------|------| 
|Portnummer | TCP-portnummer för gateway att lyssna på |
|Proxy | IP-adress för proxyserver |
|INSTALLDIR | Fullständigt kvalificerad sökväg för att ange installationskatalog för gateway-programfiler |
|USERNAME | Användar-ID för att autentisera med proxyserver |
|Lösenord | Lösenord för användar-ID för att autentisera med proxy |
|LicenseAccepted | Ange värdet **1** för att verifiera att du accepterar licensavtal |
|HASAUTH (HASAUTH) | Ange ett värde **på 1** när parametrar för ANVÄNDARNAMN/LÖSENORD anges |
|HASPROXY (PÅ) | Ange ett värde **på 1** när IP-adress för **PROXY-parameter** anges |

Om du vill installera gatewayen tyst och konfigurera den med en specifik proxyadress skriver du följande portnummer:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

Med kommandoradsalternativet /qn döljs installationen/qb under tyst installation.  

Om du behöver ange autentiseringsuppgifter för att autentisera med proxyn skriver du följande:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Efter installationen kan du bekräfta att inställningarna accepteras (exklusive användarnamn och lösenord) med hjälp av följande PowerShell-cmdletar:

- **Get-OMSGatewayConfig** – Returnerar TCP-porten som gatewayen är konfigurerad för att lyssna på.
- **Get-OMSGatewayRelayProxy** – Returnerar IP-adressen för den proxyserver som du konfigurerat den för att kommunicera med.

## <a name="configure-network-load-balancing"></a>Konfigurera utjämning av nätverksbelastning

Du kan konfigurera gatewayen för hög tillgänglighet med hjälp av NLB (Network Load Balancing) med antingen Microsoft [Network Load Balancing (NLB),](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing) [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)eller maskinvarubaserade belastningsutjämnare. Belastningsutjämnaren hanterar trafik genom att omdirigera de begärda anslutningarna från Log Analytics-agenterna eller Hanteringsservrarna i Operations Manager över noderna. Om en Gateway-server går ner omdirigeras trafiken till andra noder.

### <a name="microsoft-network-load-balancing"></a>Microsoft-nätverksbelastningsutjämning

Mer information om hur du utformar och distribuerar ett kluster för utjämning av nätverksbelastning i Windows Server 2016 finns i [Utjämning av nätverksbelastning](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). I följande steg beskrivs hur du konfigurerar ett Microsoft-kluster för utjämning av nätverksbelastning.  

1. Logga in på Den Windows-server som är medlem i NLB-klustret med ett administrativt konto.  
2. Öppna Hanteraren för utjämning av nätverksbelastning i Serverhanteraren, klicka på **Verktyg**och klicka sedan på **Hanteraren för utjämning av nätverksbelastning**.
3. Om du vill ansluta en Log Analytics-gatewayserver med Microsoft Monitoring Agent installerad högerklickar du på klustrets IP-adress och klickar sedan på **Lägg till värd i kluster**. 

    ![Hanteraren för utjämning av nätverksbelastning – Lägg till värd i klustret](./media/gateway/nlb02.png)
 
4. Ange IP-adressen för den gateway-server som du vill ansluta. 

    ![Hanteraren för utjämning av nätverksbelastning – Lägg till värd i klustret: Anslut](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer

Mer information om hur du utformar och distribuerar en Azure Load Balancer finns i [Vad är Azure Load Balancer?](../../load-balancer/load-balancer-overview.md). Om du vill distribuera en grundläggande belastningsutjämnare följer du stegen i den här [snabbstarten](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) exklusive de steg som beskrivs i avsnittet **Skapa serverdelsservrar**.   

> [!NOTE]
> Konfigurera Azure Load Balancer med hjälp av **Basic SKU**, kräver att Azure virtuella datorer tillhör en tillgänglighetsuppsättning. Mer information om tillgänglighetsuppsättningar finns [i Hantera tillgängligheten för virtuella Windows-datorer i Azure](../../virtual-machines/windows/manage-availability.md). Information om hur du lägger till befintliga virtuella datorer i en tillgänglighetsuppsättning läser du [Ange tillgänglighetsuppsättning](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)för Azure Resource Manager VM .
> 

När belastningsutjämnaren har skapats måste en servergrupp skapas som distribuerar trafik till en eller flera gatewayservrar. Följ stegen som beskrivs i avsnittet snabbstartsartikel [Skapa resurser för belastningsutjämnaren](../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

>[!NOTE]
>När hälsoavsökningen konfigureras bör den konfigureras för att använda TCP-porten på gateway-servern. Hälsoavsökningen lägger dynamiskt till eller tar bort gatewayservrarna från belastningsutjämnarrotationen baserat på deras svar på hälsokontroller. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurera hanteringsgruppen Log Analytics-agent och Operations Manager

I det här avsnittet får du se hur du konfigurerar direkt anslutna Log Analytics-agenter, en Hanteringsgrupp för Operations Manager eller Azure Automation Hybrid Runbook Workers med Log Analytics-gatewayen för att kommunicera med Azure Automation eller Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurera en fristående Log Analytics-agent

När du konfigurerar Log Analytics-agenten ersätter du proxyservervärdet med IP-adressen för Log Analytics gateway-servern och dess portnummer. Om du har distribuerat flera gatewayservrar bakom en belastningsutjämnare är proxykonfigurationen för Log Analytics-agenten den virtuella IP-adressen för belastningsutjämnaren.  

>[!NOTE]
>Information om hur du installerar Log Analytics-agenten på gatewayen och Windows-datorer som ansluter direkt till Log Analytics finns [i Ansluta Windows-datorer till Log Analytics-tjänsten i Azure](agent-windows.md). Information om hur du ansluter Linux-datorer finns i [Ansluta Linux-datorer till Azure Monitor](agent-linux.md). 
>

När du har installerat agenten på gateway-servern konfigurerar du den så att den rapporterar till de arbetsyte- eller arbetsyteagenter som kommunicerar med gatewayen. Om Log Analytics Windows-agenten inte är installerad på gatewayen skrivs händelse 300 till OMS Gateway-händelseloggen, vilket anger att agenten måste installeras. Om agenten är installerad men inte konfigurerad för att rapportera till samma arbetsyta som agenterna som kommunicerar via den, skrivs händelse 105 till samma logg, vilket indikerar att agenten på gatewayen måste konfigureras för att rapportera till samma arbetsyta som de agenter som kommunicera med gatewayen.

När du har slutfört konfigurationen startar du om **OMS Gateway-tjänsten** för att tillämpa ändringarna. Annars kommer gatewayen att avvisa agenter som försöker kommunicera med Log Analytics och kommer att rapportera händelse 105 i OMS Gateway-händelseloggen. Detta händer också när du lägger till eller tar bort en arbetsyta från agentkonfigurationen på gateway-servern.

Information om Automation Hybrid Runbook Worker finns [i Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager, där alla agenter använder samma proxyserver

Proxykonfigurationen för Operations Manager tillämpas automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.  

Om du vill använda OMS Gateway för att stödja Operations Manager måste du ha:

* Microsoft Monitoring Agent (version 8.0.10900.0 eller senare) installerad på OMS Gateway-servern och konfigurerad med samma Log Analytics-arbetsytor som hanteringsgruppen är konfigurerad att rapportera till.
* Internetanslutning. Alternativt måste OMS Gateway vara ansluten till en proxyserver som är ansluten till internet.

> [!NOTE]
> Om du inte anger något värde för gatewayen, trycks tomma värden till alla agenter.
>

Om hanteringsgruppen för Operations Manager registrerar sig med en Log Analytics-arbetsyta för första gången visas inte alternativet att ange proxykonfigurationen för hanteringsgruppen i driftkonsolen. Det här alternativet är endast tillgängligt om hanteringsgruppen har registrerats hos tjänsten.  

Om du vill konfigurera integrering uppdaterar du systemproxykonfigurationen med Netsh på det system där du kör driftkonsolen och på alla hanteringsservrar i hanteringsgruppen. Följ de här stegen:

1. Öppna en upphöjd kommandotolk:

   a. Välj **Start** och ange **cmd**.  

   b. Högerklicka på **Kommandotolken** och välj **Kör som administratör**.  

1. Ange följande kommando:

   `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört integreringen med `netsh winhttp reset proxy`Log Analytics tar du bort ändringen genom att köra . Använd sedan alternativet Konfigurera proxyserver i **driftkonsolen** för att ange gatewayservern Log Analytics. 

1. Välj **Anslutning**under **Operations Management Suite**på Operations Manager-konsolen och välj sedan Konfigurera **proxyserver**.

   ![Skärmbild av Operations Manager som visar valet Konfigurera proxyserver](./media/gateway/scom01.png)

1. Välj **Använd en proxyserver för att komma åt Operations Management Suite** och ange sedan IP-adressen för Log Analytics gateway-servern eller den virtuella IP-adressen för belastningsutjämnaren. Var noga med att `http://`börja med prefixet .

   ![Skärmbild av Operations Manager som visar proxyserveradressen](./media/gateway/scom02.png)

1. Välj **Slutför**. Hanteringsgruppen för Operations Manager är nu konfigurerad för att kommunicera via gateway-servern till Log Analytics-tjänsten.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurera Operations Manager, där specifika agenter använder en proxyserver

För stora eller komplexa miljöer kanske du bara vill att specifika servrar (eller grupper) ska använda Log Analytics gateway-servern.  För dessa servrar kan du inte uppdatera Operations Manager-agenten direkt eftersom det här värdet skrivs över av det globala värdet för hanteringsgruppen.  Åsidosätt i stället regeln som används för att skicka dessa värden.  

> [!NOTE] 
> Använd den här konfigurationstekniken om du vill tillåta flera Log Analytics-gatewayservrar i din miljö.  Du kan till exempel kräva att specifika Log Analytics-gatewayservrar anges på regional basis.
>

Så här konfigurerar du specifika servrar eller grupper så att log Analytics-gatewayservern används: 

1. Öppna Operations Manager-konsolen och välj arbetsytan **Redigering.**  
1. Välj **Regler**på arbetsytan Författande. 
1. Välj knappen **Scope** i verktygsfältet OperationsHanteraren. Om den här knappen inte är tillgänglig kontrollerar du att du har markerat ett objekt, inte en mapp, i **övervakningsfönstret.** I dialogrutan **Scope Management Pack Objects** visas en lista över vanliga målklasser, grupper eller objekt. 
1. I fältet **Sök efter** anger du **Hälsotjänst** och väljer den i listan. Välj **OK**.  
1. Sök efter **proxyinställningsregel**för rådgivare . 
1. I verktygsfältet Operations Manager väljer du **Åsidosättningar** och pekar sedan **på Åsidosätta regeln\För ett visst objekt i klassen: Hälsotjänsten** och välj ett objekt i listan.  Eller skapa en anpassad grupp som innehåller hälsotjänstobjektet för de servrar som du vill använda den här åsidosättningen på. Använd sedan åsidosättningen på din anpassade grupp.
1. Lägg till en bock i kolumnen **Åsidosätt** bredvid parametern **WebProxyAddress** i dialogrutan **Åsidosättegenskaper.**  Ange URL:en för log Analytics-gateway-servern i fältet **Åsidosättningsvärde.** Var noga med att `http://`börja med prefixet .  

    >[!NOTE]
    > Du behöver inte aktivera regeln. Det hanteras redan automatiskt med en åsidosättning i hanteringspaketet för säker referens åsidosättning av Microsoft System Center Advisor som är inriktat på Microsoft System Center Advisor Monitoring Server Group.
    > 

1. Välj ett hanteringspaket i listan **Välj målhanteringspaket** eller skapa ett nytt oförseglade hanteringspaket genom att välja **Nytt**. 
1. Välj **OK** när du är klar. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurera för Automation Hybrid Runbook Arbetare

Om du har Automation Hybrid Runbook Workers i din miljö följer du dessa steg för att konfigurera gatewayen för att stödja arbetarna.

Se avsnittet [Konfigurera nätverket](../../automation/automation-hybrid-runbook-worker.md#network-planning) i automatiseringsdokumentationen för att hitta URL:en för varje region.

Om datorn är registrerad som hybridkörboksarbetare automatiskt, till exempel om lösningen för uppdateringshantering är aktiverad för en eller flera virtuella datorer, gör du så här:

1. Lägg till jobbkörningsdatatjänst-URL:erna i listan Tillåtna värdvärden i Log Analytics-gatewayen. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics med hjälp av följande PowerShell-cmdlet:`Restart-Service OMSGatewayService`

Om datorn är ansluten till Azure Automation med hjälp av registrerings-cmdleten hybridkörningsarbetare gör du så här:

1. Lägg till url:en för registrering av agenttjänsten i listan Tillåtna värdvärden i Log Analytics-gatewayen. Exempel: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Lägg till jobbkörningsdatatjänst-URL:erna i listan Tillåtna värdvärden i Log Analytics-gatewayen. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdlets

Du kan använda cmdletar för att slutföra uppgifterna för att uppdatera konfigurationsinställningarna för Log Analytics.You can use cmdlets to complete the tasks to update the Log Analytics gateway's configuration settings. Innan du använder cmdlets, se till att:

1. Installera Log Analytics-gatewayen (Microsoft Windows Installer).
1. Öppna ett PowerShell-konsolfönster.
1. Importera modulen genom att skriva det här kommandot:`Import-Module OMSGateway`
1. Om inget fel uppstod i föregående steg har modulen importerats och cmdlets kan användas. Ange `Get-Module OMSGateway`
1. När du har använt cmdlets för att göra ändringar startar du om TJÄNSTEN OMS Gateway.

Ett fel i steg 3 innebär att modulen inte importerades. Felet kan uppstå när PowerShell inte kan hitta modulen. Du hittar modulen i installationssökvägen för OMS Gateway: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen av tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Nyckel (obligatoriskt) <br> Värde |Ändrar tjänstens konfiguration |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen till relay (uppströms) proxy |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösenord (säker sträng) |Anger adressen (och autentiseringsuppgifter) för relay (uppströms) proxy |1. Ställ in en reläproxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ställ in en relay proxy som inte behöver autentisering:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Rensa reläproxyinställningen:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar den för närvarande tillåtna värden (endast den lokalt konfigurerade tillåtna värden, inte automatiskt nedladdade tillåtna värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (obligatoriskt) |Lägger till värden i den tillåtna listan |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (obligatoriskt) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klientcertifikatet som omfattas av den tillåtna listan |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klientcertifikatämnet från den tillåtna listan |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar de för närvarande tillåtna klientcertifikatämnena (endast lokalt konfigurerade tillåtna ämnen, inte automatiskt hämtade tillåtna ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning

Om du vill samla in händelser som loggas av gatewayen bör du ha Log Analytics-agenten installerad.

![Skärmbild av loggboken i loggen Analytics gateway-logg](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Händelse-ID:er och beskrivningar av logganalysgateway

I följande tabell visas händelse-ID:er och beskrivningar för logganalysgatewaylogghändelser.

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla programfel som inte har något specifikt ID. |
| 401 |Fel konfiguration. Lyssna till exempelPort = "text" i stället för ett heltal. |
| 402 |Undantag vid tolkning av TLS-handskakningsmeddelanden. |
| 403 |Nätverksfel. Det går till exempel inte att ansluta till målservern. |
| 100 |Allmän information. |
| 101 |Tjänsten har startat. |
| 102 |Tjänsten har stoppats. |
| 103 |Ett HTTP CONNECT-kommando togs emot från klienten. |
| 104 |Inte ett HTTP CONNECT-kommando. |
| 105 |Målservern finns inte i listan över tillåtna mål, eller målporten är inte säker (443). <br> <br> Kontrollera att MMA-agenten på din OMS Gateway-server och de agenter som kommunicerar med OMS Gateway är anslutna till samma Log Analytics-arbetsyta. |
| 105 |FEL TcpConnection – Ogiltigt klientcertifikat: CN=Gateway. <br><br> Se till att du använder OMS Gateway version 1.0.395.0 eller senare. Se också till att MMA-agenten på din OMS Gateway-server och de agenter som kommunicerar med OMS Gateway är anslutna till samma Log Analytics-arbetsyta. |
| 106 |TLS/SSL-protokollversion som inte stöds.<br><br> Log Analytics-gatewayen stöder endast TLS 1.0, TLS 1.1 och 1.2. Det stöder inte SSL.|
| 107 |TLS-sessionen har verifierats. |

### <a name="performance-counters-to-collect"></a>Prestandaräknare att samla in

I följande tabell visas de prestandaräknare som är tillgängliga för Log Analytics-gatewayen. Använd Prestandaövervakaren för att lägga till räknarna.

| **Namn** | **Beskrivning** |
| --- | --- |
| Logganalys gateway/aktiv klientanslutning |Antal TCP-anslutningar (Active Client Network) |
| Logga Analytics Gateway/felräkning |Antal fel |
| Logganalys gateway/ansluten klient |Antal anslutna klienter |
| Antal loggar Analytics Gateway/avslag |Antal avslag på grund av något TLS-valideringsfel |

![Skärmbild av Gateway-gränssnitt för Log Analytics som visar prestandaräknare](./media/gateway/counters.png)

## <a name="assistance"></a>Hjälp

När du är inloggad på Azure-portalen kan du få hjälp med Log Analytics-gatewayen eller någon annan Azure-tjänst eller -funktion.
Om du vill ha hjälp väljer du frågetecknet i det övre högra hörnet av portalen och väljer **Ny supportbegäran**. Fyll sedan i det nya formuläret för supportbegäran.

![Skärmbild av en ny supportbegäran](./media/gateway/support.png)

## <a name="next-steps"></a>Nästa steg

[Lägg till datakällor](../../azure-monitor/platform/agent-data-sources.md) för att samla in data från anslutna källor och lagra data på logganalysarbetsytan.
