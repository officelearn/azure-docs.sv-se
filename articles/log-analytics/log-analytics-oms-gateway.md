---
title: Ansluta datorer till OMS med OMS-gatewayen | Microsoft Docs
description: "Ansluta din OMS-hanterade enheter och datorer som övervakas av Operations Manager till OMS-Gateway för att skicka data till OMS-tjänsten när de inte har tillgång till Internet."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: magoedte;banders
ms.openlocfilehash: 16d79f02bffeb3db22a0190822d4304d3a1de73b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="connect-computers-without-internet-access-to-oms-using-the-oms-gateway"></a>Ansluta datorer utan Internetanslutning till OMS med OMS-Gateway

Det här dokumentet beskriver hur din OMS-hanterad och System Center Operations Manager övervakade datorer kan skicka data till OMS-tjänsten när de inte har tillgång till Internet. OMS-gatewayen, som är en vanlig HTTP-proxy som stöder HTTP-tunnel använder kommandot HTTP ansluta kan samla in data och skicka den till OMS-tjänsten för deras räkning.  

OMS-gatewayen stöder:

* Azure Automation Hybrid Runbook Worker  
* Windows-datorer med Microsoft Monitoring Agent direkt ansluten till en OMS-arbetsyta
* Linux-datorer med OMS-Agent för Linux anslutna direkt till en OMS-arbetsyta  
* System Center Operations Manager 2012 SP1 med UR7, Operations Manager 2012 R2 UR3 eller Operations Manager 2016 hanteringsgruppen integrerad med OMS.  

Om din IT-säkerhetsprinciper tillåter inte datorer i nätverket för att ansluta till Internet, till exempel försäljning (POS) enheter eller servrar som stöder IT-tjänster, men du behöver ansluta dem till OMS att hantera och övervaka dem, kan de konfigureras för att kommunicera direkt med OMS-Gateway för att ta emot konfigurationen och vidarebefordra data åt.  Om dessa datorer är konfigurerade med OMS-agent för att ansluta direkt till en OMS-arbetsyta, alla datorer i stället att kommunicera med OMS-Gateway.  Gatewayen överför data från agenter till OMS direkt, analyserar data under överföring inte.

När en hanteringsgrupp för Operations Manager är integrerat med OMS kan-hanteringsservrar konfigureras för att ansluta till OMS-Gateway för att ta emot information om konfiguration och skicka insamlade data beroende på lösningen som du har aktiverat.  Operations Manager-agenter skickar vissa data som Operations Manager-aviseringar, configuration assessment, instansutrymme och kapacitet data till hanteringsservern. Andra stora volymer data, till exempel IIS-loggar, prestanda och säkerhetshändelser skickas direkt till OMS-Gateway.  Om du har en eller flera Operations Manager-Gateway-servrar som distribuerats i en DMZ eller andra isolerat nätverk för att övervaka obetrodda system, kan inte den kommunicera med en OMS-Gateway.  Operations Manager-Gateway-servrar kan endast rapportera till en hanteringsserver.  När en hanteringsgrupp för Operations Manager har konfigurerats för att kommunicera med en OMS-Gateway distribueras automatiskt proxy konfigurationsinformation till alla agenthanterade datorer som konfigurerats för att samla in data för Log Analytics, även om inställningen är tom.    

Att tillhandahålla hög tillgänglighet för direkt ansluten eller Operations Management-grupper som kommunicerar med OMS via gatewayen, du kan använda Utjämning av nätverksbelastning att omdirigera och distribuerar trafik över flera gateway-servrar.  Om en gateway-servern kraschar, dirigeras trafiken till en annan tillgänglig nod.  

Vi rekommenderar att du installerar OMS-agenten på datorn som kör programmet OMS-Gateway för att övervaka OMS-Gateway och analysera prestanda eller händelse. Dessutom kan agenten OMS-gatewayen identifiera slutpunkter för tjänsten som behöver kommunicera med.

Varje agent måste ha en nätverksanslutning till dess gateway så att agenterna kan överföra data till och från gatewayen automatiskt. Gatewayen har installerats på en domänkontrollant rekommenderas inte.

Följande diagram visar dataflöde från direkt agenter OMS använder gateway-servern.  Agenter måste ha sina proxykonfiguration matchar samma port OMS-gatewayen har konfigurerats för att kommunicera med till OMS.  

![direkt agentkommunikation med OMS-diagram](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Följande diagram visar dataflöde från en hanteringsgrupp för Operations Manager till OMS.   

![Operations Manager-kommunikation med OMS-diagram](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Krav

När du ställer in en dator för att köra OMS-Gateway, måste datorn ha följande:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET framework 4.5
* Minst en 4 kärnor och 8 GB minne 

### <a name="language-availability"></a>Tillgängliga språk

OMS-gatewayen är tillgänglig på följande språk:

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
- Koreanska
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska (internationell)

### <a name="supported-encryption-protocols"></a>Krypteringsprotokoll som stöds
OMS-Gateway har endast stöd för Transport Layer Security (TLS) 1.0, 1.1 och 1.2.  Det stöder inte Secure Sockets Layer (SSL).

## <a name="download-the-oms-gateway"></a>Hämta OMS-Gateway

Det finns tre sätt att få den senaste versionen av filen OMS Gateway Setup.

1. Ladda ned från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Hämta från OMS-portalen.  När du loggar in på OMS-arbetsytan går du till **inställningar** > **anslutna källor** > **Windows-servrar** och på **hämta OMS Gateway**.

3. Ladda ned från den [Azure-portalen](https://portal.azure.com).  När du loggar in:  

   1. Bläddra i listan över tjänster och välj sedan **logganalys**.  
   2. Välj en arbetsyta.
   3. I arbetsytan-bladet under **allmänna**, klickar du på **Snabbstart**.
   4. Under **Välj en datakälla för att ansluta till arbetsytan**, klickar du på **datorer**.
   5. I den **direkt Agent** bladet, klickar du på **hämta OMS Gateway**.<br><br> ![Hämta OMS-Gateway](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Installera OMS-Gateway

Utför följande steg för att installera en gateway.  Om du har installerat en tidigare version kallades *Log Analytics vidarebefordrare*, kommer att uppgraderas till den här versionen.  

1. Dubbelklicka på målmappen, **OMS Gateway.msi**.
2. På sidan **Välkommen** klickar du på **Nästa**.<br><br> ![Installationsguiden för gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. På den **licensavtalet** väljer **jag accepterar villkoren i licensavtalet** du godkänner LICENSAVTALET och klicka sedan på **nästa**.
4. På den **Port och proxy adress** sidan:
   1. Ange TCP-portnummer som ska användas för gatewayen. Installationsprogrammet konfigurerar en inkommande regel med det här portnumret på Windows-brandväggen.  Standardvärdet är 8080.
      Portnumret giltigt intervall är 1-65535. Om indata inte hamnar i det här intervallet, visas ett felmeddelande.
   2. Alternativt servern där gatewayen har installerats behöver för att kommunicera via en proxyserver, skriver du proxyadress där gateway ska anslutas. Till exempel `http://myorgname.corp.contoso.com:80`.  Om det är tomt, försöker gatewayen ansluter direkt till Internet.  Om proxyservern kräver autentisering, ange ett användarnamn och lösenord.<br><br> ![Proxykonfiguration för gateway-guiden](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Klicka på **Nästa**.
5. Om du inte har Microsoft Update har aktiverats visas sidan Microsoft Update där du kan välja att aktivera den. Gör ett val och klicka sedan på **nästa**. I annat fall Fortsätt till nästa steg.
6. På den **målmappen** kan antingen lämna standardmappen C:\Program Files\OMS Gateway eller ange platsen där du vill installera gateway och klicka sedan på **nästa**.
7. På den **redo att installera** klickar du på **installera**. User Account Control visas begärande behörighet att installera. I så fall, klickar du på **Ja**.
8. När installationen är klar klickar du på **Slutför**. Du kan kontrollera att tjänsten körs genom att öppna snapin-modulen services.msc och kontrollera att **OMS Gateway** visas i listan över tjänster och status är **kör**.<br><br> ![Tjänster – OMS-Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurera Utjämning av nätverksbelastning 
Du kan konfigurera en gateway för hög tillgänglighet med hjälp av Utjämning av nätverksbelastning (NLB) med hjälp av antingen Microsoft NLB (Utjämning av nätverksbelastning) eller maskinvarubaserad belastningsutjämnare.  Belastningsutjämnaren hanterar trafik genom att omdirigera begärda anslutningar från OMS-Agent eller Operations Manager-hanteringsservrar över dess noder. Om en Gateway-servern kraschar, hämtar trafiken dirigeras till andra noder.

Om du vill lära dig mer om att skapa och distribuera en Windows Server 2016 Utjämning av nätverksbelastning kluster, se [Utjämning av nätverksbelastning](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Följande steg beskriver hur du konfigurerar en Microsoft Utjämning av nätverksbelastning kluster.  

1.  Logga in på Windows server som är medlem i NLB-kluster med ett administratörskonto.  
2.  Öppna Hanteraren för Utjämning av nätverksbelastning i Serverhanteraren, klicka på **verktyg**, och klicka sedan på **hanteraren**.
3. Högerklicka på klustrets IP-adress för att ansluta en OMS-Gateway-servern med Microsoft Monitoring Agent installerad, och klicka sedan på **Lägg till värd i klustret**.<br><br> ![Nätverket läsa in belastningsutjämning Manager – Lägg till värd i klustret](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Ange IP-adressen för gateway-servern som du vill ansluta till.<br><br> ![Utjämning av nätverksbelastning – Lägg till värd i klustret: ansluta](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Konfigurera OMS-agent och Operations Manager-hanteringsgruppen
Följande avsnitt innehåller anvisningar om hur du konfigurerar direktanslutna OMS agenter, en Operations Manager-hanteringsgrupp eller Azure Automation Hybrid Runbook Workers med OMS-Gateway för att kommunicera med OMS.  

Information om krav och anvisningar om hur du installerar OMS-agent på Windows-datorer ansluta direkt till OMS finns [ansluta Windows-datorer till OMS](log-analytics-windows-agent.md) eller Linux-datorer finns i [ansluta Linux-datorer till OMS](log-analytics-linux-agents.md). 

### <a name="configuring-the-oms-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Konfigurera OMS-agent och Operations Manager om du vill använda OMS-Gateway som en proxyserver

### <a name="configure-standalone-oms-agent"></a>Konfigurera fristående OMS-agent
Se [konfigurera proxy-och brandväggsinställningarna med Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) information om hur du konfigurerar en agent om du vill använda en proxyserver som i det här fallet är gatewayen.  Om du har distribuerat flera gateway-servrar bakom en belastningsutjämnare för nätverk är OMS-agent proxykonfiguration virtuella IP-adressen för Utjämning av nätverksbelastning:<br><br> ![Microsoft Monitoring agentegenskaper – proxyinställningar](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager - Använd samma proxyserver för alla agenter
Du kan konfigurera Operations Manager för att lägga till gateway-servern.  Operations Manager-proxykonfigurationen tillämpas automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.

Om du vill använda en Gateway för att stödja Operations Manager, måste du ha:

* Microsoft Monitoring Agent (agentversion – **8.0.10900.0** och senare) installerat på Gateway-servern och konfigurerat för OMS-arbetsytor du vill kommunicera.
* Gatewayen måste ha Internetanslutning eller vara ansluten till en proxyserver som tillåter.

> [!NOTE]
> Om du inte anger ett värde för gatewayen pushas tomma värden till alla agenter.


1. Öppna Operations Manager-konsolen och under **Operations Management Suite**, klickar du på **anslutning** och klicka sedan på **konfigurera proxyservern**.<br><br> ![Operations Manager – Konfigurera proxyserver](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Välj **använder en proxyserver för att få åtkomst till Operations Management Suite** och ange IP-adressen för OMS Gateway-servern eller den virtuella IP-adressen i NLB-klustret. Se till att du börjar med den `http://` prefix.<br><br> ![Operations Manager – proxyserveradress](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Klicka på **Slutför**. Operations Manager-servern är ansluten till din OMS-arbetsyta.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Konfigurera Operations Manager - specifik agenter använder för proxyserver
För stora eller komplexa miljöer, kan du bara vill specifika servrar (eller grupper) att använda OMS Gateway-servern.  Du kan inte uppdatera Operations Manager-agenten för dessa servrar direkt när det här värdet skrivs över av det globala värdet för hanteringsgruppen.  I stället måste du åsidosätta den regel som används för att skicka dessa värden.

> [!NOTE] 
> Samma konfiguration teknik kan användas för att tillåta användning av flera OMS Gateway-servrar i din miljö.  Du kan till exempel kräva specifika OMS Gateway-servrar som anges på grundval av per region.

1. Öppna Operations Manager-konsolen och välj den **redigering** arbetsytan.  
2. I arbetsytan redigering väljer **regler** och klicka på den **omfång** i Operations Manager-verktygsfältet. Om den här knappen inte är tillgänglig, kontrollera att du har ett objekt och inte en mapp markerad i övervakningsfönstret. Den **omfång för Hanteringspaketsobjekt** dialogrutan visar en lista med vanliga riktade klasser, grupper eller objekt. 
3. Typen **Hälsotjänsten** i den **leta efter** fältet och markera den i listan.  Klicka på **OK**.  
4. Sök efter regeln **Advisor Proxy inställningen regeln** och klicka på i verktygsfältet Operations-konsolen **åsidosätter** och peka sedan på **åsidosätta Rule\For ett specifikt objekt i klassen: Hälsotjänsten** och välj ett specifikt objekt i listan.  Du kan också kan du skapa en anpassad grupp som innehåller hälsotillstånd serviceobjektet av de servrar som du vill tillämpa den här åsidosättningen och tillämpa sedan åsidosättningen till gruppen.
5. I den **egenskaper för åsidosättning** dialogrutan, klicka om du vill markera i den **åsidosätta** bredvid den **WebProxyAddress** parameter.  I den **åsidosättningsvärde** anger du URL: en OMS-Gateway-server för att du startar med den `http://` prefix.
   >[!NOTE]
   > Du behöver inte aktivera regeln eftersom den redan hanteras automatiskt med en åsidosättning finns i Microsoft System Center Advisor säker referens Override management pack målobjekt för Microsoft System Center Advisor övervakning servergruppen.
   > 
6. Välj antingen ett management pack från den **väljer målhanteringspaket** listan eller skapa ett nytt oförseglat hanteringspaket genom att klicka på **ny**. 
7. När du är klar med ändringarna klickar du på **OK**. 

### <a name="configure-for-automation-hybrid-workers"></a>Konfigurera för automation-hybrider
Om du har Automation Hybrid Runbook Worker-arbeten i din miljö, ange följande manuella, tillfälligt lösningar för att konfigurera gatewayen för att stödja dem.

Du behöver veta Azure-regionen där Automation-kontot finns i följande steg. Att hitta platsen:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj Azure Automation-tjänsten.
3. Välj lämplig Azure Automation-kontot.
4. Visa dess region under **plats**.<br><br> ![Azure-portalen – platsen för Automation-konto](./media/log-analytics-oms-gateway/location.png)  

Använd följande tabeller för att identifiera URL: en för varje plats:

**Jobbet runtime data service-URL: er**

| **Plats** | **URL: EN** |
| --- | --- |
| Norra centrala USA |ncus jobruntimedata-produktprenumeration-su1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net |
| Östra USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Kanada central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net |
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japan |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australien |ase-jobruntimedata-prod-su1.azure-automation.net |

**URL: er till Agent**

| **Plats** | **URL: EN** |
| --- | --- |
| Norra centrala USA |ncus agentservice-produktprenumeration-1.azure-automation.net |
| Västra Europa |Vi-agentservice-produktprenumeration-1.azure-automation.net |
| Södra centrala USA |scus agentservice-produktprenumeration-1.azure-automation.net |
| Östra USA 2 |eus2 agentservice-produktprenumeration-1.azure-automation.net |
| Kanada central |CC-agentservice-produktprenumeration-1.azure-automation.net |
| Norra Europa |ne agentservice-produktprenumeration-1.azure-automation.net |
| Sydostasien |SEA-agentservice-produktprenumeration-1.azure-automation.net |
| Indien, centrala |CID agentservice-produktprenumeration-1.azure-automation.net |
| Japan |jpe agentservice-produktprenumeration-1.azure-automation.net |
| Australien |ase agentservice-produktprenumeration-1.azure-automation.net |

Om datorn är registrerad som en Hybrid Runbook Worker automatiskt för korrigering med lösningen för hantering av uppdateringar, gör du följande:

1. Lägga till jobbet Körningsdata service-URL: er i listan med tillåtna värden på OMS-Gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Starta om tjänsten OMS-Gateway med hjälp av följande PowerShell-cmdlet:`Restart-Service OMSGatewayService`

Om datorn är publicerat till Azure Automation med hjälp av cmdleten Hybrid Runbook Worker-registrering, gör du följande:

1. Lägg till URL: en agent-tjänsten registrering i listan med tillåtna värden på OMS-Gateway. Exempel: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Lägga till jobbet Körningsdata service-URL: er i listan med tillåtna värden på OMS-Gateway. Exempel: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Starta om tjänsten OMS-Gateway.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbar PowerShell-cmdlets
Cmdlets kan hjälpa dig att utföra uppgifter som behövs för att uppdatera konfigurationsinställningar för OMS-Gateway. Innan du använder dem, måste du:

1. Installera OMS-Gateway (MSI).
2. Öppna ett PowerShell-konsolfönster.
3. Ange följande kommando för att importera modulen:`Import-Module OMSGateway`
4. Om inga fel uppstod i föregående steg, modulen har importerats och cmdlets kan användas. Typ`Get-Module OMSGateway`
5. När du gör ändringar med hjälp av cmdlets, se till att du startar om tjänsten Gateway.

Om du får ett fel i steg 3 kan importera inte modulen. Felet kan uppstå när PowerShell är det går inte att hitta modulen. Du hittar i en Gateway-installationssökvägen: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet:** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen för tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Nyckel (krävs) <br> Värde |Ändringar av konfigurationen för tjänsten |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen till proxy för vidarebefordran (överordnad) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösenord |Anger relay (överordnad) proxy-adress (och autentiseringsuppgifter) |1. Ange en relay-proxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ange en relay-proxy som inte kräver autentisering:`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Avmarkera relay proxyinställning:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar för närvarande tillåtna värden (endast lokalt konfigurerade tillåtna värden inte inkluderas automatiskt hämtade tillåtna värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (krävs) |Lägger till värden i listan över tillåtna |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (krävs) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klientcertifikatet för listan över tillåtna |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klienten certifikatets ämne från listan över tillåtna |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar för närvarande tillåtna klienten certifikatämnen (endast lokalt konfigurerade tillåts ämnen, inkluderas inte automatiskt hämtade tillåtna ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning
Du måste också har OMS-agenten installerad för att samla in händelser som loggats av gateway.<br><br> ![Loggboken – OMS Gateway logg](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS Gateway händelse-ID och beskrivningar**

I följande tabell visas de händelse-ID och beskrivningar för OMS Gateway logghändelser.

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla fel som inte har ett specifikt ID |
| 401 |Felaktig konfiguration. Till exempel: listenPort = ”text” i stället för ett heltal |
| 402 |Undantag vid tolkning TLS handshake-meddelanden |
| 403 |Fel för nätverk. Till exempel: Det går inte att ansluta till målservern |
| 100 |Allmän information |
| 101 |Tjänsten har startats |
| 102 |Tjänsten har stoppats |
| 103 |Tog emot ett HTTP-ansluta kommando från klient |
| 104 |Inte ett HTTP-ansluta kommando |
| 105 |Målservern är inte i listan över tillåtna eller målporten är inte säker port (443) <br> <br> Se till att agenten MMA på Gateway-servern och agenterna kommunicera med gatewayen är ansluten till samma logganalys-arbetsytan. |
| 105 |FEL TcpConnection – ogiltig klientcertifikat: CN = Gateway <br><br> Se till att: <br>    <br> &#149; Du använder en Gateway med versionsnumret 1.0.395.0 eller större. <br> &#149; MMA agenten på Gateway-servern och agenterna kommunicera med gatewayen är anslutna till samma logganalys-arbetsytan. |
| 106 |OMS-Gateway har endast stöd för TLS 1.0, TLS 1.1 och 1.2.  Det stöder inte SSL. För alla stöds inte TLS/SSL-protokollversion genererar OMS Gateway händelse-ID 106.|
| 107 |TLS-sessionen har verifierats |

**Prestandaräknare för att samla in**

Följande tabell visar de tillgängliga prestandaräknarna för OMS-Gateway. Du kan lägga till räknare med Prestandaövervakaren.

| **Namn** | **Beskrivning** |
| --- | --- |
| OMS Gateway/aktiv klientanslutning |Antal aktiva klientnätverksanslutningar (TCP) |
| OMS Gateway/Felräkning |Antal fel |
| OMS-Gateway/ansluten klient |Antal anslutna klienter |
| OMS Gateway-avvisande antal |Antal nekanden på grund av eventuella verifieringsfel för TLS |

![Prestandaräknare OMS-Gateway](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Få hjälp
När du är inloggad på Azure-portalen, skapar du en begäran om du behöver hjälp med OMS-Gateway eller andra Azure-tjänst eller funktion i en tjänst.
Begära hjälp, klicka på symbolen frågetecken i det övre högra hörnet av portalen och klicka sedan på **ny supportbegäran**. Slutför formuläret nya support.

![Ny supportbegäran](./media/log-analytics-oms-gateway/support.png)

Du kan också lämna feedback om OMS eller logganalys på den [Microsoft Azure Feedbackforum](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Nästa steg
* [Lägg till datakällor](log-analytics-data-sources.md) samla in data från den anslutna källor i logganalys-arbetsytan och lagra den i logganalys-databasen.
