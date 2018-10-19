---
title: Ansluta datorer med hjälp av Log Analytics-gateway | Microsoft Docs
description: Anslut dina enheter och datorer som övervakas av Operations Manager med Log Analytics-gateway för att skicka data till Azure Automation och Log Analytics-tjänsten när de inte har Internetåtkomst.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 463af7fc77b1f8e7d58e0dc8acbfdad336301269
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404689"
---
# <a name="connect-computers-without-internet-access-using-the-log-analytics-gateway"></a>Ansluta datorer utan Internetåtkomst med Log Analytics-gateway
Det här dokumentet beskriver hur du konfigurerar kommunikationen med Azure Automation och Log Analytics med Log Analytics-gateway när det är direkt ansluten eller Operations Manager övervakade datorer har inte Internetåtkomst.  Log Analytics-gateway, som är en vanlig HTTP-proxy som har stöd för HTTP-tunnel med hjälp av kommandot HTTP ansluta, kan samla in data och skicka den till Azure Automation och Log Analytics för deras räkning.  

Log Analytics-gateway har stöd för:

* Azure Automation Hybrid Runbook Worker  
* Windows-datorer med Microsoft Monitoring Agent som är direkt anslutna till en Log Analytics-arbetsyta
* Linux-datorer med Log Analytics-agenten för Linux som är direkt anslutna till en Log Analytics-arbetsyta  
* System Center Operations Manager 2012 SP1 med UR7 och Operations Manager 2012 R2 UR3, Operations Manager 2016 och hanteringsgruppen för Operations Manager version 1801 integrerat med Log Analytics.  

Om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket för att ansluta till Internet, till exempel försäljning (POS) enheter eller servrar som stöder IT-tjänster, men du behöver att ansluta till Azure Automation och Log Analytics för att hantera och övervaka dem , de kan konfigureras för att kommunicera direkt med Log Analytics-gatewayen tar emot konfiguration och vidarebefordra data å deras vägnar.  Om dessa datorer är konfigurerade med Log Analytics-agenten för att ansluta direkt till en Log Analytics-arbetsyta, alla datorer i stället att kommunicera med Log Analytics-gateway.  Gatewayen överför data från agenter till tjänsten direkt, analysera data under överföring inte.

När en hanteringsgrupp för Operations Manager är integrerat med Log Analytics kan-hanteringsservrar konfigureras för att ansluta till Log Analytics-gatewayen till mottagning av konfigurationsinformation och skicka insamlade data, beroende på lösningen som du har aktiverat.  Operations Manager-agenter skickar vissa data som Operations Manager-aviseringar, konfigurationskontrollen, instansutrymmet och kapacitetsdata till hanteringsservern. Andra stora volymer data, till exempel IIS-loggar, prestanda och säkerhetshändelser skickas direkt till Log Analytics-gateway.  Om du har en eller flera Operations Manager Gateway-servrar som distribuerats i en DMZ eller andra isolerat nätverk för att övervaka obetrodd system kan inte kan den kommunicera med en Log Analytics-gateway.  Operations Manager Gateway-servrar kan endast rapportera till en hanteringsserver.  När en Operations Manager-hanteringsgrupp konfigureras för att kommunicera med Log Analytics-gateway, distribueras konfigurationsinformation proxy automatiskt till alla agenthanterade datorer som konfigureras för att samla in data för Log Analytics, även Om inställningen är tom.    

Att tillhandahålla hög tillgänglighet för direkt ansluten eller Operations-hanteringsgrupper som kommunicerar med Log Analytics via gatewayen, du kan använda Utjämning av nätverksbelastning att omdirigera och distribuera trafiken mellan flera gateway-servrar.  Om en gateway-servern slutar fungera kan dirigeras trafiken till en annan tillgänglig nod.  

Log Analytics-agenten måste finnas på den dator som kör Log Analytics-gateway att identifiera tjänstens slutpunkter som tjänsten behöver för att kommunicera med och övervaka Log Analytics-gateway för att analysera dess prestanda eller händelsedata.

Varje agent måste ha nätverksanslutning till sin gateway så att agenterna kan automatiskt att överföra data till och från gatewayen. Du bör inte installera gatewayen på en domänkontrollant.

Följande diagram visar dataflödet från direkta agenter till Azure Automation och Log Analytics med hjälp av gateway-servern.  Agenter måste ha sina proxykonfiguration som matchar samma port Log Analytics-gatewayen har konfigurerats för att kommunicera med tjänsten.  

![direkt-agentens kommunikation med tjänster diagram](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Följande diagram visar dataflödet från en Operations Manager-hanteringsgrupp till Log Analytics.   

![Operations Manager-kommunikation med Log Analytics-diagram](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Förutsättningar

När du anger att en dator för att köra Log Analytics-gateway, måste datorn ha följande:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET framework 4.5
* Minst en 4-kärnor och 8 GB minne 
* Log Analytics-agenten för Windows 

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
Log Analytics-gateway har endast stöd för Transport Layer Security (TLS) 1.0, 1.1 och 1.2.  Det har inte stöd för Secure Sockets Layer (SSL).  Om du vill se till att skydda data under överföringen till Log Analytics, rekommenderar vi starkt att du kan konfigurera gatewayen att använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**.  Mer information [skickar data på ett säkert sätt med hjälp av TLS 1.2](log-analytics-data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Tillåtna antalet agenten anslutningar
Följande tabell visar det tillåtna antalet agenter som kommunicerar med en gateway-server.  Det här stödet är baserad på agenter som överför ~ 200KB data var 6: e sekund. Datavolym per agent testas är ungefär 2,7 GB per dag.

|Gateway |Ungefärligt antal agenter som stöds|  
|--------|----------------------------------|  
|-CPU: Intel XEON v3 CPU E5 2660 \@ 2,6 GHz 2 kärnor<br> -Minne: 4 GB<br> -Nätverksbandbredd: 1 Gbit/s| 600|  
|-CPU: Intel XEON v3 CPU E5 2660 \@ 2,6 GHz 4 kärnor<br> -Minne: 8 GB<br> -Nätverksbandbredd: 1 Gbit/s| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Ladda ned Log Analytics-gatewayen

Det finns två sätt att hämta den senaste versionen av installationsfilen för Log Analytics-gateway.

1. Ladda ned från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

1. Ladda ned från Azure-portalen.  När du loggar in på Azure portal:  

   1. Bläddra i listan över tjänster och välj sedan **Log Analytics**.  
   1. Välj en arbetsyta.
   1. I bladet för din arbetsytan under **Allmänt**, klickar du på **Snabbstart**.
   1. Under **Välj en datakälla för att ansluta till arbetsytan**, klickar du på **datorer**.
   1. I den **Direktagent** bladet klickar du på **ladda ned Log Analytics gateway**.<br><br> ![ladda ned Log Analytics-gatewayen](./media/log-analytics-oms-gateway/download-gateway.png)

eller 

   1. I bladet för din arbetsytan under **inställningar**, klickar du på **avancerade inställningar**.
   1. Gå till **anslutna källor** > **Windows-servrar** och klicka på **ladda ned Log Analytics gateway**.

## <a name="install-the-log-analytics-gateway"></a>Installera Log Analytics-gateway

Utför följande steg för att installera en gateway.  Om du har installerat en tidigare version, tidigare kallad *vidarebefordrare för Log Analytics*, den kommer att uppgraderas till den här versionen.  

1. Målmappen, dubbelklicka på **Log Analytics gateway.msi**.
1. På sidan **Välkommen** klickar du på **Nästa**.<br><br> ![Installationsguiden för gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
1. På den **licensavtal** väljer **jag accepterar villkoren i licensavtalet** du godkänner LICENSAVTALET och klicka sedan på **nästa**.
1. På den **Port och proxy-adress** sidan:
   1. Ange TCP-portnumret som ska användas för gatewayen. Installationsprogrammet konfigurerar en inkommande regel med det här portnumret på Windows-brandväggen.  Standardvärdet är 8080.
      Det giltiga intervallet i portnumret är 1-65535. Om indata inte hamnar i det här intervallet, visas ett felmeddelande.
   1. Om du om servern där gatewayen är installerad behöver kommunicera via en proxyserver, ange Proxyadressen där gatewayen måste ansluta. Till exempel `http://myorgname.corp.contoso.com:80`.  Om inget anges, försöker gatewayen ska ansluta direkt till Internet.  Om proxyservern kräver autentisering anger du ett användarnamn och lösenord.<br><br> ![Proxykonfiguration för gateway-guiden](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   1. Klicka på **Nästa**.
1. Om du inte har Microsoft Update aktiverat visas Microsoft Update-sidan där du kan välja att aktivera den. Gör ett val och klicka sedan på **nästa**. I annat fall Fortsätt till nästa steg.
1. På den **målmapp** kan antingen lämna standardmappen C:\Program Files\OMS Gateway eller ange den plats där du vill installera gatewayen och klicka sedan på **nästa**.
1. På den **redo att installera** klickar du på **installera**. User Account Control visas begär behörighet att installera. I så, fall klickar du på **Ja**.
1. När installationen är klar klickar du på **Slutför**. Du kan kontrollera att tjänsten körs genom att öppna snapin-modulen services.msc och kontrollera att **Log Analytics gateway** visas i listan över tjänster och den status är **kör**.<br><br> ![Tjänster – Log Analytics-gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurera Utjämning av nätverksbelastning 
Du kan konfigurera gatewayen för hög tillgänglighet med hjälp av Utjämning av nätverksbelastning (NLB) med hjälp av Microsoft Network (Utjämning av nätverksbelastning) eller maskinvarubaserade belastningsutjämnare.  Belastningsutjämnaren hanterar trafik genom att omdirigera begärda anslutningarna från Log Analytics-agenter eller Operations Manager-hanteringsservrar för dess noder. Om en Gateway-servern slutar fungera kan omdirigeras trafiken till andra noder.

Om du vill lära dig att utforma och distribuera en Windows Server 2016 nätverksbelastningsutjämnande kluster, se [Utjämning av nätverksbelastning](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Följande steg beskriver hur du konfigurerar en Microsoft Utjämning av nätverksbelastning kluster.  

1. Logga in på den Windows-server som är medlem i NLB-kluster med ett administratörskonto.  
1. Öppna Hanteraren för Utjämning av nätverksbelastning i Serverhanteraren, klicka på **verktyg**, och klicka sedan på **hanteraren för Utjämning av nätverksbelastning**.
1. Högerklicka på klustrets IP-adress för att ansluta en Log Analytics gateway-servern med Microsoft Monitoring Agent installerad, och klicka sedan på **Lägg till värddator till klustret**.<br><br> ![Nätverket läsa in belastningsutjämning Manager – Lägg till värd i klustret](./media/log-analytics-oms-gateway/nlb02.png)<br> 
1. Ange IP-adressen för gateway-servern som du vill ansluta till.<br><br> ![Utjämning av nätverksbelastning – Lägg till värd i klustret: ansluta](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-log-analytics-agent-and-operations-manager-management-group"></a>Konfigurera Log Analytics-agenten och Operations Manager-hanteringsgrupp
Följande avsnitt innehåller anvisningar om hur du konfigurerar direktanslutna Log Analytics-agenter, en Operations Manager-hanteringsgrupp eller Azure Automation Hybrid Runbook Worker med Log Analytics-gatewayen att kommunicera med Azure Automation eller loggfil Analytics.  

### <a name="configure-standalone-log-analytics-agent"></a>Konfigurera fristående Log Analytics-agenten
Information om krav och anvisningar om hur du installerar Log Analytics-agenten på Windows-datorer ansluta direkt till Log Analytics finns i [ansluta Windows-datorer till Log Analytics](log-analytics-windows-agents.md) eller Linux-datorer finns i [ Anslut Linux-datorer till Log Analytics](log-analytics-quick-collect-linux-computer.md). På att ange en proxyserver när du konfigurerar agenten kan ersätta du värdet med IP-adressen för Log Analytics gateway-servern och dess portnummer.  Om du har distribuerat flera gateway-servrar bakom Utjämning av nätverksbelastning, är proxykonfiguration för Log Analytics-agenten den virtuella IP-adressen för Utjämning av nätverksbelastning.  

Information som rör Automation Hybrid Runbook Worker finns i [distribuera Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager – alla agenter Använd samma proxyserver
Du kan konfigurera Operations Manager för att lägga till gateway-servern.  Operations Manager-proxykonfiguration tillämpas automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.  

Om du vill använda gatewayen för att stödja Operations Manager, måste du ha:

* Microsoft Monitoring Agent (agent-version – **8.0.10900.0** eller senare) installerat på Gateway-servern och konfigurerats för en Log Analytics-arbetsytor som du vill kommunicera.
* Gatewayen måste ha Internetanslutning eller vara ansluten till en proxyserver som gör.

> [!NOTE]
> Om du inte anger ett värde för gatewayen, skickas tomma värden till alla agenter.
> 

Om det här är första gången registrerar din Operations Manager-hanteringsgrupp med en arbetsyta för Log Analytics finns inte alternativet för att ange proxykonfigurationen för hanteringsgruppen i driftkonsolen.  Hanteringsgruppen måste registrerats med tjänsten innan det här alternativet är tillgängligt.  Du behöver uppdatera systemproxykonfigurationen med Netsh på systemet som Operations-konsolen körs från för att konfigurera integration och alla hanteringsservrar i hanteringsgruppen.  

1. Öppna en upphöjd kommandotolk.
   a. Gå till **starta** och skriv **cmd**.
   b. Högerklicka på **kommandotolk** och välj Kör som administratör **.
1. Ange följande kommando och tryck på **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört integrationen med Log Analytics, kan du ta bort ändringen genom att köra `netsh winhttp reset proxy` och sedan använda den **konfigurera proxyservern** alternativet i Operations-konsolen för att ange Log Analytics gateway-servern. 

1. Öppna Operations Manager-konsolen och under **Operations Management Suite**, klickar du på **anslutning** och klicka sedan på **Konfigurera proxyserver**.<br><br> ![Operations Manager – Konfigurera proxyserver](./media/log-analytics-oms-gateway/scom01.png)<br> 
1. Välj **använder en proxyserver för att få åtkomst till Operations Management Suite** och sedan ange IP-adressen för Log Analytics gateway-servern eller den virtuella IP-adressen för Utjämning av nätverksbelastning. Se till att du börjar med den `http://` prefix.<br><br> ![Operations Manager – proxyserveradress](./media/log-analytics-oms-gateway/scom02.png)<br> 
1. Klicka på **Slutför**. Operations Manager-hanteringsgrupp har nu konfigurerats för att kommunicera via gateway-servern till Log Analytics-tjänsten.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurera Operations Manager - specifik agenter använder för proxyserver
För stora och komplexa miljöer vill du kanske bara specifika servrar (eller grupper) för att använda Log Analytics gateway-servern.  Du kan inte uppdatera Operations Manager-agenten för dessa servrar direkt när det här värdet skrivs över med globalt värde för hanteringsgruppen.  Du måste i stället Åsidosätt regeln används för att skicka dessa värden.  

> [!NOTE] 
> Samma konfiguration metod kan användas för att tillåta användning av flera Log Analytics gateway-servrar i din miljö.  Du kan till exempel kräva specifika Log Analytics gateway-servrar måste anges på basis av per region.
>  

1. Öppna Operations Manager-konsolen och välj den **redigering** arbetsyta.  
1. I arbetsytan redigering väljer **regler** och klicka på den **omfång** i Operations Manager-verktygsfältet. Om den här knappen inte är tillgänglig, kontrollera att du har ett objekt och inte en mapp markerad i övervakningsfönstret. Den **omfång för Hanteringspaketobjekt** dialogrutan visar en lista över vanliga riktade klasser, grupper eller objekt. 
1. Typ **Hälsotjänsten** i den **leta efter** fältet och markera den i listan.  Klicka på **OK**.  
1. Sök efter regeln **Advisor Proxy inställningen regeln** och klicka på i verktygsfältet för driftkonsolen, **åsidosätter** och peka sedan på **åsidosätta Rule\For ett specifikt objekt i klassen: tjänsten för hälsotillstånd**  och välja ett specifikt objekt i listan.  Du kan också skapa en anpassad grupp som innehåller hälsotillstånd objektet av de servrar som du vill tillämpa den här åsidosättningen och sedan använda åsidosättningen för gruppen.
1. I den **egenskaper för åsidosättning** dialogrutan, klicka för att markera kryssrutan i den **åsidosätta** kolumnen bredvid den **WebProxyAddress** parametern.  I den **åsidosättningsvärde** fältet, anger du Webbadressen till Log Analytics gateway server säkerställer att du börjar med den `http://` prefix.  

    >[!NOTE]
    > Du behöver inte aktivera regeln eftersom den redan hanteras automatiskt med en åsidosättning finns i Microsoft System Center Advisor säker referens åsidosätta management pack riktar in sig på Microsoft System Center Advisor övervakning av servergrupp.
    >   

1. Välj antingen ett hanteringspaket från den **Välj målhanteringspaket** eller skapa ett nytt oförseglat hanteringspaket genom att klicka på **New**. 
1. När du är klar med ändringarna klickar du på **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurera för automation hybrid Worker-arbeten
Om du har Automation Hybrid Runbook Worker i din miljö kan ange följande manuella, tillfälliga lösningar för att konfigurera gatewayen för att stödja dem.

Du behöver veta den Azure-region där Automation-kontot finns i följande steg. Att hitta platsen:

1. Logga in på [Azure Portal](https://portal.azure.com/).
1. Välj Azure Automation-tjänsten.
1. Välj lämpligt Azure Automation-konto.
1. Visa dess region under **plats**.<br><br> ![Azure-portalen – platsen för Automation](./media/log-analytics-oms-gateway/location.png)  

Använd följande tabeller för att identifiera URL: en för varje plats:

**Jobbet runtime data tjänstens webbadresser**

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

Om datorn registreras automatiskt som en Hybrid Runbook Worker för uppdatering med hjälp av lösningen för uppdateringshantering, följer du dessa steg:

1. Lägg till Runtime jobbdata tjänstens webbadresser i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics med hjälp av följande PowerShell-cmdlet: `Restart-Service OMSGatewayService`

Om datorn är implementerad i Azure Automation med hjälp av cmdleten Hybrid Runbook Worker-registrering, gör du följande:

1. Lägg till Registreringswebbadress för agent-tjänsten i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Lägg till Runtime jobbdata tjänstens webbadresser i listan tillåtna värden på Log Analytics-gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Starta om gatewaytjänsten Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbara PowerShell-cmdletar
Med hjälp av cmdlet: ar kan du utföra uppgifter som behövs för att uppdatera konfigurationsinställningar för Log Analytics-gateway. Innan du använder dem, måste du:

1. Installera Log Analytics-gateway (MSI).
1. Öppna ett PowerShell-konsolfönster.
1. Ange följande kommando för att importera modulen: `Import-Module OMSGateway`
1. Om inget fel inträffade i föregående steg, modulen har importerats och cmdletarna som kan användas. Typ `Get-Module OMSGateway`
1. När du gör ändringar med hjälp av cmdletar, kontrollerar du att starta om gatewaytjänsten.

Om du får ett felmeddelande i steg 3 importerades inte modulen. Felet kan inträffa när PowerShell är det går inte att hitta modulen. Du hittar i gatewayens installationssökväg: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet:** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen av tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Key (krävs) <br> Värde |Ändrar konfigurationen av tjänsten |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen för relay (överordnad) proxy |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösenord |Anger relay (överordnad) proxy-adress (och autentiseringsuppgifter) |1. Ange ett relay-proxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ange en relay-proxy som inte behöver autentisering: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Avmarkera relay proxyinställning:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar för närvarande tillåtna värden (endast lokalt konfigurerade tillåtna värden inte inkluderas automatiskt hämtade tillåtna värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (krävs) |Lägger till värden i listan med tillåtna |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (krävs) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klientcertifikatet för listan över tillåtna |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klienten certifikatets ämne från listan över tillåtna |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar för närvarande tillåtna klienten certifikatämnen (endast lokalt konfigurerade tillåtna ämnen, inkluderas inte automatiskt hämtade tillåtna ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning
Du måste också ha Log Analytics-agenten installerad för att samla in händelser som loggats av gatewayen.<br><br> ![Loggboken – Log Analytics gateway Log](./media/log-analytics-oms-gateway/event-viewer.png)

**Log Analytics gateway händelse-ID och beskrivningar**

I följande tabell visar de händelse-ID och beskrivningar för Log Analytics gateway logghändelser.

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla programfel som inte har ett specifikt ID |
| 401 |Felaktig konfiguration. Till exempel: listenPort = ”text” i stället för ett heltal |
| 402 |Undantag vid tolkning TLS handshake-meddelanden |
| 403 |Nätverk-fel. Till exempel: Det går inte att ansluta till målservern |
| 100 |Allmän information |
| 101 |Tjänsten har startat |
| 102 |Tjänsten har stoppats |
| 103 |Tog emot en HTTP-ansluta kommando från klient |
| 104 |Inte ett HTTP-ansluta kommando |
| 105 |Målservern är inte i listan över tillåtna eller målporten är inte säker port (443) <br> <br> Se till att MMA-agenten på din Gateway-server och agenter som kommunicerar med gatewayen är ansluten till samma Log Analytics-arbetsytan. |
| 105 |FEL TcpConnection – ogiltigt klientcertifikat: CN = Gateway <br><br> Se till att: <br>    <br> &#149;Du använder en Gateway med versionsnumret 1.0.395.0 eller större. <br> &#149;MMA-agenten på din Gateway-server och agenter som kommunicerar med Gateway är anslutna till samma Log Analytics-arbetsytan. |
| 106 |Log Analytics-gatewayen har endast stöd för TLS 1.0, TLS 1.1 och 1.2.  Det stöder inte SSL. För alla stöds inte TLS/SSL-protokollversion genererar Log Analytics gateway händelse-ID 106.|
| 107 |TLS-sessionen har verifierats |

**Prestandaräknare för att samla in**

I följande tabell visas prestandaräknarna som är tillgängliga för Log Analytics-gateway. Du kan lägga till räknare med Prestandaövervakaren.

| **Namn** | **Beskrivning** |
| --- | --- |
| Log Analytics Gateway/aktiv-klientanslutning |Antal aktiva klientnätverksanslutningar (TCP) |
| Antal för log Analytics Gateway/fel |Antal fel |
| Log Analytics-gatewayen/ansluten klient |Antal anslutna klienter |
| Log Analytics Gateway/avvisande antal |Antal avvisningar på grund av eventuella verifieringsfel för TLS |

![Logga Analytics gateway-prestandaräknare](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Få hjälp
När du har loggat in på Azure Portal, skapar du en begäran för att få hjälp med Log Analytics-gateway eller andra Azure-tjänst eller funktion i en tjänst.
Be om hjälp, klicka på frågetecknet symbolen i det övre högra hörnet i portalen och klicka sedan på **ny supportbegäran**. Slutför formuläret för begäran av nytt stöd.

![Ny supportbegäran](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Nästa steg
[Lägg till datakällor](log-analytics-data-sources.md) att samla in data från dina anslutna källor och lagra den i Log Analytics-arbetsytan.
