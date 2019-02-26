---
title: Ansluta datorer med hjälp av Log Analytics-gateway | Microsoft Docs
description: Anslut dina enheter och datorer som övervakas av Operations Manager med hjälp av Log Analytics-gateway för att skicka data till Azure Automation och Log Analytics-tjänsten när de inte har Internetåtkomst.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 41ffd7229383f1006bb846f975aeccf83256032a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807736"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway"></a>Ansluta datorer utan Internetåtkomst med hjälp av Log Analytics-gateway

>[!NOTE]
>Eftersom Microsoft Operations Management Suite (OMS) övergår till Microsoft Azure Monitor, ändrar terminologi. Den här artikeln refererar till OMS-Gateway som en Azure Log Analytics-gateway. 
>

Den här artikeln beskriver hur du konfigurerar kommunikationen med Azure Automation och Log Analytics med hjälp av Log Analytics-gateway när datorer som är anslutna direkt eller som övervakas av Operations Manager har ingen Internetåtkomst. 

Log Analytics-gateway är en vanlig HTTP-proxy som har stöd för HTTP-tunnel med hjälp av kommandot HTTP ansluta. Den här gatewayen kan samla in data och skicka den till Azure Automation och Log Analytics för de datorer som inte är anslutna till internet.  

Log Analytics-gateway har stöd för:

* Rapporterar upp till samma fyra Log Analytics arbetsyta-agenter som ligger bakom den och som har konfigurerats med Azure Automation Hybrid Runbook Worker.  
* Windows-datorer där Microsoft Monitoring Agent är direkt ansluten till en Log Analytics-arbetsyta.
* Linux-datorer där en Log Analytics-agenten för Linux är direkt ansluten till en Log Analytics-arbetsyta.  
* System Center Operations Manager 2012 SP1 med UR7 eller Operations Manager 2012 R2 med UR3 en hanteringsgrupp i Operations Manager 2016 eller senare som är integrerat med Log Analytics.  

Vissa IT-säkerhetspolicyer Tillåt inte internet-anslutning för datorer i nätverket. Datorerna är anslutna kan vara av försäljning (POS) enheter eller servrar som stöder IT-tjänster, till exempel. För att ansluta enheterna till Azure Automation eller Log Analytics så att du kan hantera och övervaka konfigurera dem, dem för att kommunicera direkt med Log Analytics-gateway. Log Analytics-gateway kan ta emot konfigurationer och vidarebefordra data å deras vägnar. Om datorerna som har konfigurerats med Log Analytics-agenten för att ansluta direkt till en Log Analytics-arbetsyta, på datorer i stället att kommunicera med Log Analytics-gateway.  

Log Analytics-gateway överför data från agenter till tjänsten direkt. Det inte att analysera data under överföring.

När en hanteringsgrupp för Operations Manager är integrerat med Log Analytics kan-hanteringsservrar konfigureras för att ansluta till Log Analytics-gatewayen till mottagning av konfigurationsinformation och skicka insamlade data, beroende på lösningen som du har aktiverat .  Operations Manager-agenter skickar vissa data till hanteringsservern. Agenter kan till exempel skicka aviseringar, konfigurationsbedömningsdata, utrymmesinstansdata och kapacitetsdata i Operations Manager. Andra stora volymer data, till exempel Internet Information Services (IIS)-loggar, prestandadata och säkerhetshändelser, skickas direkt till Log Analytics-gateway. 

Om en eller flera Operations Manager Gateway-servrar distribueras för att övervaka obetrodd system i ett perimeternätverk eller ett isolerat nätverk, kommer dessa servrar kan inte kommunicera med en Log Analytics-gateway.  Operations Manager Gateway-servrar kan rapportera endast till en hanteringsserver.  När en Operations Manager-hanteringsgrupp konfigureras för att kommunicera med Log Analytics-gateway, distribueras konfigurationsinformation proxy automatiskt till alla agenthanterade datorer som konfigureras för att samla in data för Log Analytics, även Om inställningen är tom.    

Att tillhandahålla hög tillgänglighet för direkt ansluten eller Operations Management-grupper som kommunicerar med Log Analytics via gatewayen, använda Utjämning av nätverksbelastning (NLB) för att omdirigera och distribuerar trafik över flera gateway-servrar. På så sätt kan om en gateway-servern slutar fungera kan dirigeras trafik till en annan tillgänglig nod.  

Den dator som kör Log Analytics-gatewayen kräver att Log Analytics-Windows-agenten att identifiera Tjänsteslutpunkter som gatewayen behöver kommunicera med. Agenten måste också att dirigera gateway till samma arbetsytor som agenter eller Operations Manager-hanteringsgrupp bakom gatewayen har konfigurerats med. Den här konfigurationen kan gatewayen och agenten kan kommunicera med sina tilldelade arbetsyta.

En gateway kan vara att upp till fyra arbetsytor. Det här är det totala antalet arbetsytor som har stöd för en Windows-agent.  

Varje agent måste ha nätverksanslutning till gatewayen så att agenterna kan automatiskt att överföra data till och från gatewayen. Undvik att installera gatewayen på en domänkontrollant.

Följande diagram visar data som flödar från direkta agenter, via gatewayen, till Azure Automation och Log Analytics. Proxykonfigurationen måste matcha den port som Log Analytics-gatewayen har konfigurerats med.  

![Diagram över direktagent kommunikation med tjänster](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Följande diagram visar dataflödet från en Operations Manager-hanteringsgrupp till Log Analytics.   

![Diagram över Operations Manager-kommunikation med Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Konfigurera ditt system

Datorer som har utsetts för att köra Log Analytics-gatewayen måste ha följande konfiguration:

* Windows 10, Windows 8.1 eller Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 eller Windows Server 2008
* Microsoft .NET Framework 4.5
* Minst ett 4-kärnor och 8 GB minne 
* En [Log Analytics-agenten för Windows](agent-windows.md) som är konfigurerad för att rapportera till samma arbetsyta som agenter som kommunicerar via gatewayen

### <a name="language-availability"></a>Tillgängliga språk

Log Analytics-gatewayen är tillgänglig på följande språk:

- Förenklad kinesiska
- Traditionell kinesiska
- Tjeckiska
- Nederländska
- Svenska
- Franska
- Tyska
- Ungerska
- Italienska
- Japanska
- Koreanska
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska (internationell)

### <a name="supported-encryption-protocols"></a>Krypteringsprotokoll som stöds
Log Analytics-gateway stöder endast Transport Layer Security (TLS) 1.0, 1.1 och 1.2.  Det stöder inte Secure Sockets Layer (SSL).  För att säkerställa säkerheten för data under överföring till Log Analytics, konfigurera gatewayen att använda minst TLS 1.2. Äldre versioner av TLS eller SSL är sårbara. Undvik att använda dem även om de för närvarande tillåter bakåtkompatibilitet.  

Mer information [skickar data på ett säkert sätt med hjälp av TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Tillåtna antalet agenten anslutningar
I följande tabell visar ungefär hur många agenter kan kommunicera med en gateway-server. Support är baserad på agenter som överför ungefär 200 KB data var 6: e sekund. För varje agent som testas står datavolym i ungefär 2,7 GB per dag.

|Gateway |Agenter som stöds (ungefärlig)|  
|--------|----------------------------------|  
|CPU: Intel Xeon Processor E5 2660 v3 \@ 2,6 GHz 2 kärnor<br> Minne: 4 GB<br> Nätverkets bandbredd: 1 Gbit/s| 600|  
|CPU: Intel Xeon Processor E5 2660 v3 \@ 2,6 GHz 4 kärnor<br> Minne: 8 GB<br> Nätverkets bandbredd: 1 Gbit/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Ladda ned Log Analytics-gatewayen

Hämta den senaste versionen av installationsfilen för Log Analytics-gateway från antingen [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) eller Azure-portalen.

Följ dessa steg för att få Log Analytics-gateway från Azure portal:

1. Bläddra i listan över tjänster och välj sedan **Log Analytics**. 
1. Välj en arbetsyta.
1. I bladet för arbetsytan under **Allmänt**väljer **Snabbstart**. 
1. Under **Välj en datakälla för att ansluta till arbetsytan**väljer **datorer**.
1. I den **Direktagent** bladet väljer **ladda ned Log Analytics gateway**.
 
   ![Skärmbild av stegen för att ladda ned Log Analytics-gatewayen](./media/gateway/download-gateway.png)

eller 

1. I bladet för arbetsytan under **inställningar**väljer **avancerade inställningar**.
1. Gå till **anslutna källor** > **Windows-servrar** och välj **ladda ned Log Analytics gateway**.

## <a name="install-the-log-analytics-gateway"></a>Installera Log Analytics-gateway

Följ dessa steg om du vill installera en gateway.  (Om du har installerat en tidigare version som kallas vidarebefordrare för Log Analytics, det kommer att uppgraderas till den här versionen.)

1. Målmappen, dubbelklicka på **Log Analytics gateway.msi**.
1. På sidan **Välkommen** klickar du på **Nästa**.

   ![Skärmbild av välkomstsidan i installationsguiden för Gateway](./media/gateway/gateway-wizard01.png)

1. På den **licensavtal** väljer **jag accepterar villkoren i licensavtalet** du godkänner licensvillkoren för programvara från Microsoft och välj sedan **nästa**.
1. På den **Port och proxy-adress** sidan:

   a. Ange TCP-portnumret som ska användas för gatewayen. Installationen använder det här portnumret för att konfigurera en regel för inkommande på Windows-brandväggen.  Standardvärdet är 8080.
      Det giltiga intervallet i portnumret är mellan 1 och 65535. Om indata inte hamnar i det här intervallet, visas ett felmeddelande.

   b. Om den server där gatewayen är installerad behöver kommunicera via en proxyserver, ange Proxyadressen där gatewayen måste ansluta. Ange till exempel `http://myorgname.corp.contoso.com:80`.  Om du lämnar fältet tomt försöker gatewayen ansluta direkt till internet.  Om proxyservern kräver autentisering anger du ett användarnamn och lösenord.

   c. Välj **Nästa**.

   ![Skärmbild av konfiguration för gateway-proxy](./media/gateway/gateway-wizard02.png)

1. Om du inte har Microsoft Update aktiverat, Microsoft Update-sidan visas och du kan välja att aktivera den. Gör ett val och väljer sedan **nästa**. I annat fall Fortsätt till nästa steg.
1. På den **målmapp** kan antingen lämna standardmappen C:\Program Files\OMS Gateway eller ange den plats där du vill installera gatewayen. Välj sedan **Nästa**.
1. På den **redo att installera** väljer **installera**. Om User Account Control begär behörighet att installera, Välj **Ja**.
1. När installationen är klar väljer du **Slutför**. Kontrollera att tjänsten körs genom att öppna snapin-modulen services.msc och kontrollera att **OMS-gatewayen** visas i listan över tjänster och att statusen är **kör**.

   ![Skärmbild av lokala tjänster som visar att OMS-gatewayen körs](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Konfigurera Utjämning av nätverksbelastning 
Konfigurera gatewayen för hög tillgänglighet genom att använda Utjämning av nätverksbelastning (NLB). Använda Microsoft Azure Load Balancer eller maskinvarubaserade belastningsutjämnare.  Belastningsutjämnaren hanterar trafik genom att omdirigera begärda anslutningarna från Log Analytics-agenter eller Operations Manager-hanteringsservrar för dess noder. Om en gateway-servern slutar fungera kan dirigeras trafiken till andra noder.

>[!NOTE]
>Om du vill lära dig att utforma och distribuera en Windows Server 2016 NLB-kluster, se [Utjämning av nätverksbelastning](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing). 
>

Följ dessa steg om du vill konfigurera en belastningsutjämnare för Microsoft-kluster:  

1. Använd ett administratörskonto för att logga in på Windows-Server som är medlem i klustret för belastningsutjämnaren.
1. I Serverhanteraren, öppnar **hanteraren för Utjämning av nätverksbelastning**väljer **verktyg**, och välj sedan **hanteraren för Utjämning av nätverksbelastning**.
1. Högerklicka på klustrets IP-adress för att ansluta en Log Analytics gateway-server som har Microsoft Monitoring Agent installerad, och klicka sedan på **Lägg till värddator till klustret**.

   ![Skärmbild av NLB-hanteraren, med lägga till värden till klustret är valt](./media/gateway/nlb02.png)

1. Ange IP-adressen för gateway-servern som du vill ansluta till.

   ![Skärmbild av NLB-hanteraren, som visar sidan Lägg till värd i klustret: Anslut](./media/gateway/nlb03.png)
    
## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurera Log Analytics-agenten och Operations Manager-hanteringsgrupp
I det här avsnittet visas hur du konfigurerar direktanslutna Log Analytics-agenter, en Operations Manager-hanteringsgrupp eller Azure Automation Hybrid Runbook Worker med Log Analytics-gatewayen att kommunicera med Azure Automation och Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Konfigurera en fristående Log Analytics-agenten
När du konfigurerar Log Analytics-agenten, ersätter du värdet för proxy-server med IP-adressen för Log Analytics gateway-servern och dess portnummer. Om du har distribuerat flera gateway-servrar bakom en NLB är proxykonfiguration för Log Analytics-agenten den virtuella IP-adressen för Utjämning av nätverksbelastning.  

>[!NOTE]
>Log Analytics-agenten på gateway- och Windows-datorer som ansluter direkt till Log Analytics Se [ansluta Windows-datorer till Log Analytics-tjänsten i Azure](agent-windows.md). Om du vill ansluta Linux-datorer, se [konfigurera en Log Analytics-agenten för Linux-datorer i en hybridmiljö](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

När du har installerat agenten på gateway-servern, konfigurera den för att rapportera till arbetsytan eller arbetsytan agenter som kommunicerar med gatewayen. Om Log Analytics-Windows-agenten inte installeras på gatewayen, skrivs händelse 300 till händelseloggen OMS-gatewayen, som anger att agenten måste installeras. Om agenten är installerad men inte konfigureras att rapportera till samma arbetsyta som agenter som kommunicerar via den, skrivs händelse 105 till samma logg, som anger att agenten på en gateway måste konfigureras att rapportera till samma arbetsyta som agenterna som co mmunicate med gatewayen.

När du har slutfört konfigurationen, starta om OMS-gatewayen för att tillämpa ändringarna. Annars avvisar gatewayen som försöker kommunicera med Log Analytics och rapporterar 105-händelse i händelseloggen för OMS-gatewayen. Detta inträffar även när du lägger till eller ta bort en arbetsyta från agentkonfigurationen på gateway-servern.   

Information som rör Automation Hybrid Runbook Worker finns i [automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager, där alla agenter använder samma proxyserver
Proxykonfiguration för Operations Manager används automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.  

Om du vill använda OMS-gatewayen för att stödja Operations Manager, måste du ha:

* Microsoft Monitoring Agent (version 8.0.10900.0 eller senare) installerat på OMS-Gateway-servern och konfigurerad med samma Log Analytics-arbetsytor att din hanteringsgrupp är konfigurerad att rapportera till.
* Ansluten till Internet. OMS-gatewayen måste vara ansluten till en proxyserver som är ansluten till internet.

> [!NOTE]
> Om du anger något värde för gatewayen, skickas tomma värden till alla agenter.
>

Om din Operations Manager-hanteringsgrupp som registrerar med en arbetsyta för Log Analytics för första gången, visas inte alternativet för att ange proxykonfigurationen för hanteringsgruppen i driftkonsolen.  Det här alternativet är endast tillgängligt om hanteringsgruppen har registrerats med tjänsten.  

Om du vill konfigurera integration, uppdatera proxykonfiguration system med hjälp av Netsh på systemet där du kör driftkonsolen och på alla hanteringsservrar i hanteringsgruppen. Följ de här stegen:

1. Öppna en upphöjd kommandotolk:

   a. Välj **starta** och ange **cmd**.  

   b. Högerklicka på **kommandotolk** och välj **kör som administratör**.  

1. Ange följande kommando:

   `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört integrationen med Log Analytics, ta bort ändringen genom att köra `netsh winhttp reset proxy`. Sedan i Operations-konsolen använder den **konfigurera proxyservern** alternativet för att ange Log Analytics gateway-servern. 

1. På Operations Manager-konsolen under **Operations Management Suite**väljer **anslutning**, och välj sedan **Konfigurera proxyserver**.

   ![Skärmbild av Operations Manager, som visar valet Konfigurera proxyserver](./media/gateway/scom01.png)

1. Välj **använder en proxyserver för att få åtkomst till Operations Management Suite** och sedan ange IP-adressen för Log Analytics gateway-servern eller den virtuella IP-adressen för Utjämning av nätverksbelastning. Var noga med att inledas med prefixet `http://`.

   ![Skärmbild av Operations Manager, som visar proxyn serveradress](./media/gateway/scom02.png)

1. Välj **Slutför**. Operations Manager-hanteringsgrupp har nu konfigurerats för att kommunicera via gateway-servern till Log Analytics-tjänsten.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Konfigurera Operations Manager, där specifika agenter använder en proxyserver
För stora och komplexa miljöer kanske du vill endast specifika servrar (eller grupper) att använda Log Analytics gateway-servern.  För dessa servrar, kan inte du uppdatera Operations Manager-agenten direkt eftersom det här värdet skrivs över med globalt värde för hanteringsgruppen.  I stället Åsidosätt regeln används för att skicka dessa värden.  

> [!NOTE] 
> Använda configuration metod om du vill tillåta för flera Log Analytics gateway-servrar i din miljö.  Du kan till exempel kräva specifika Log Analytics-gatewayservrar anges per region.
>

Konfigurera specifika servrar eller grupper använder Log Analytics gateway-servern: 

1. Öppna Operations Manager-konsolen och välj den **redigering** arbetsyta.  
1. I arbetsytan redigering väljer **regler**. 
1. Operations Manager-verktygsfältet och välj den **omfång** knappen. Om den här knappen inte är tillgänglig, kontrollera att du har valt ett objekt, inte en mapp i den **övervakning** fönstret. Den **omfång för Hanteringspaketobjekt** dialogrutan visar en lista över vanliga riktade klasser, grupper eller objekt. 
1. I den **leta efter** anger **Hälsotjänsten** och markera den i listan. Välj **OK**.  
1. Sök efter **Advisor Proxy inställningen regeln**. 
1. Operations Manager-verktygsfältet och välj **åsidosätter** och peka sedan på **åsidosätta Rule\For ett specifikt objekt i klassen: Tjänsten för hälsotillstånd** och välj ett objekt i listan.  Eller skapa en anpassad grupp som innehåller hälsotillstånd objektet av de servrar som du vill tillämpa den här åsidosättningen till. Därefter tillämpa åsidosättningen för din anpassade grupp.
1. I den **egenskaper för åsidosättning** dialogrutan lägger du till en bock i den **åsidosätta** kolumnen bredvid den **WebProxyAddress** parametern.  I den **åsidosättningsvärde** fältet, anger du Webbadressen till Log Analytics gateway-servern. Var noga med att inledas med prefixet `http://`.  

    >[!NOTE]
    > Du behöver inte aktivera regeln. Den har redan hanteras automatiskt med en åsidosättning i Microsoft System Center Advisor säker referens åsidosätta management pack som riktar in sig på Microsoft System Center Advisor övervakning av servergrupp.
    > 

1. Välj ett management pack från den **Välj målhanteringspaket** , eller skapa ett nytt oförseglat hanteringspaket genom att välja **New**. 
1. Välj **OK** när du är klar. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Konfigurera för Automation Hybrid Runbook Worker
Följ dessa steg för manuell, tillfälligt lösningar att konfigurera OMS-gatewayen för att stödja ”arbetarna” om du har Automation Hybrid Runbook Worker i din miljö.

Om du vill följa stegen i det här avsnittet, som du behöver veta den Azure-region där Automation-kontot finns. Hitta den platsen:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj Azure Automation-tjänsten.
1. Välj lämpligt Azure Automation-konto.
1. Visa dess region under **plats**.

   ![Skärmbild av platsen för Automation-kontot i Azure portal](./media/gateway/location.png)

Använd följande tabeller för att identifiera URL: en för varje plats.

**Runtime jobbdata tjänstens webbadresser**

| **Plats** | **URL** |
| --- | --- |
| Norra centrala USA |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centrala Kanada |cc-jobruntimedata-prod-su1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japan |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australien |ase-jobruntimedata-prod-su1.azure-automation.net |

**Agent-tjänstens URL**

| **Plats** | **URL** |
| --- | --- |
| Norra centrala USA |ncus-agentservice-prod-1.azure-automation.net |
| Västra Europa |we-agentservice-prod-1.azure-automation.net |
| Södra centrala USA |scus-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-agentservice-prod-1.azure-automation.net |
| Centrala Kanada |cc-agentservice-prod-1.azure-automation.net |
| Norra Europa |ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-agentservice-prod-1.azure-automation.net |
| Indien, centrala |cid-agentservice-prod-1.azure-automation.net |
| Japan |jpe-agentservice-prod-1.azure-automation.net |
| Australien |ase-agentservice-prod-1.azure-automation.net |

Om datorn är registrerad som en Hybrid Runbook Worker automatiskt, kan du använda lösningen för uppdateringshantering för att hantera uppdateringen. Följ de här stegen:

1. Lägg till Runtime jobbdata tjänstens webbadresser i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics med hjälp av följande PowerShell-cmdlet: `Restart-Service OMSGatewayService`

Om datorn är ansluten till Azure Automation med hjälp av cmdleten Hybrid Runbook Worker-registrering, gör du följande:

1. Lägg till Registreringswebbadress för agent-tjänsten i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Lägg till Runtime jobbdata tjänstens webbadresser i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdletar
Du kan använda cmdlets för att utföra uppgifter för att uppdatera konfigurationsinställningar för Log Analytics-gateway. Innan du använder cmdlets, måste du:

1. Installera Log Analytics-gateway (Microsoft Windows Installer).
1. Öppna ett PowerShell-konsolfönster.
1. Importera modulen genom att skriva följande kommando: `Import-Module OMSGateway`
1. Om inget fel inträffade i föregående steg, modulen har importerats och cmdletarna som kan användas. Ange `Get-Module OMSGateway`
1. När du använder cmdlets för att göra ändringar kan du starta om OMS-gatewayen.

Ett fel i steg 3 innebär att modulen inte har importerats. Felet kan inträffa när PowerShell det går inte att hitta modulen. Du kan hitta modulen i installationssökvägen OMS-gatewayen: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet:** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen av tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Key (krävs) <br> Värde |Ändrar konfigurationen av tjänsten |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen för relay (överordnad) proxy |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösenord |Anger relay (överordnad) proxy-adress (och autentiseringsuppgifter) |1. Ange ett relay-proxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ange en relay-proxy som inte behöver autentisering: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Avmarkera relay proxyinställning:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar för närvarande tillåtna värden (endast lokalt konfigurerade tillåtna värden hämtas inte automatiskt tillåts värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (krävs) |Lägger till värden i listan med tillåtna |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (krävs) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klientcertifikatet för listan över tillåtna |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klienten certifikatets ämne från listan över tillåtna |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar för närvarande tillåtna klienten certifikatämnen (endast lokalt konfigurerade tillåtna ämnen hämtas inte automatiskt tillåts ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning
Om du vill samla in händelser som loggats av gatewayen, bör du ha Log Analytics-agenten installerad.

![Skärmbild av Loggboken listan i gatewayloggen Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics gateway händelse-ID och beskrivningar

I följande tabell visar de händelse-ID och beskrivningar för Log Analytics gateway logghändelser.

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla programfel som har inga specifika-ID. |
| 401 |Felaktig konfiguration. Till exempel listenPort = ”text” i stället för ett heltal. |
| 402 |Undantag vid tolkning TLS handshake-meddelanden. |
| 403 |Nätverk-fel. Exempelvis kan ansluta inte till målservern. |
| 100 |Allmän information. |
| 101 |Tjänsten är igång. |
| 102 |Tjänsten har stoppats. |
| 103 |Ett HTTP-ansluta kommando togs emot från klienten. |
| 104 |Inte ett HTTP-CONNECT-kommando. |
| 105 |Målservern är inte i listan över tillåtna eller målport är inte säker (443). <br> <br> Se till att MMA-agenten på din OMS-Gateway-server och agenter som kommunicerar med OMS-gatewayen är ansluten till samma Log Analytics-arbetsytan. |
| 105 |FEL TcpConnection – ogiltigt klientcertifikat: CN=Gateway. <br><br> Se till att du använder OMS-gatewayen version 1.0.395.0 eller större. Se också till att MMA-agenten på din OMS-Gateway-server och agenter som kommunicerar med OMS-gatewayen är ansluten till samma Log Analytics-arbetsytan. |
| 106 |Versionen för stöds inte TLS/SSL-protokollet.<br><br> Log Analytics-gateway stöder endast TLS 1.0, TLS 1.1 och 1.2. Det stöder inte SSL.|
| 107 |TLS-sessionen har verifierats. |

### <a name="performance-counters-to-collect"></a>Prestandaräknare för att samla in

I följande tabell visas prestandaräknarna som är tillgängliga för Log Analytics-gateway. Använda Prestandaövervakaren för att lägga till räknare.

| **Namn** | **Beskrivning** |
| --- | --- |
| Log Analytics Gateway/aktiv-klientanslutning |Antal aktiva klientnätverksanslutningar (TCP) |
| Antal för log Analytics Gateway/fel |Antal fel |
| Log Analytics-gatewayen/ansluten klient |Antal anslutna klienter |
| Log Analytics Gateway/avvisande antal |Antal avvisningar på grund av eventuella verifieringsfel för TLS |

![Skärmbild av Log Analytics gatewaygränssnitt som visar prestandaräknare](./media/gateway/counters.png)

## <a name="assistance"></a>Få hjälp
När du har loggat in på Azure Portal, kan du få hjälp med Log Analytics-gateway eller andra Azure-tjänst eller funktion.
Om du behöver hjälp, väljer du frågetecknet i det övre högra hörnet av portalen och välj **ny supportbegäran**. Slutför formuläret för begäran av nytt stöd.

![Skärmbild av en ny supportbegäran](./media/gateway/support.png)

## <a name="next-steps"></a>Nästa steg
[Lägg till datakällor](../../azure-monitor/platform/agent-data-sources.md) att samla in data från anslutna källor och lagra data i Log Analytics-arbetsytan.
