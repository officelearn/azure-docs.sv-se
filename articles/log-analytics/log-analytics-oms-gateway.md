---
title: Ansluta datorer med hjälp av OMS-Gateway | Microsoft Docs
description: Ansluta dina enheter och datorer som övervakas av Operations Manager till OMS-Gateway för att skicka data till Azure Automation och Log Analytics-tjänsten när de inte har tillgång till internet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Ansluta datorer utan Internetåtkomst med hjälp av OMS-Gateway
Det här dokumentet beskriver hur du konfigurerar kommunikation med Azure Automation och Log Analytics med hjälp av OMS-Gateway när direkt anslutna datorer eller datorer som övervakas av Operations Manager har inte Internetåtkomst. OMS-Gateway är en vanlig HTTP-proxy som stöder HTTP tunnlar med hjälp av kommandot HTTP-anslutning. Den kan samla in data och skicka den till Azure Automation och logganalys uppdrag datorer utan Internetanslutning.  

OMS-gatewayen stöder:

* Azure Automation Hybrid Runbook Worker  
* Windows-datorer med Microsoft Monitoring Agent direkt ansluten till logganalys-arbetsytan
* Linux-datorer med OMS-Agent för Linux direkt ansluten till logganalys-arbetsytan  
* System Center Operations Manager 2012 SP1 med UR7, Operations Manager 2012 R2 UR3, Operations Manager 2016 och hanteringsgruppen för Operations Manager version 1801 integrerad med logganalys  

Om din IT-säkerhetsprinciper Tillåt inte vissa datorer i nätverket för att ansluta till internet (till exempel kassaenheter eller servrar som stöder IT-tjänster), men du behöver ansluta dem till Azure Automation eller Log Analytics för att hantera och övervaka dem, de kan konfigureras för att kommunicera direkt med OMS-Gateway.

 Om dessa datorer konfigureras med OMS-agent för att ansluta direkt till logganalys-arbetsytan, alla datorer i stället att kommunicera med OMS-Gateway. OMS-Gateway överför data från agenter till tjänsten direkt. Den analysera inte data när den är i överföringen.

När en hanteringsgrupp för Operations Manager är integrerat med logganalys kan hanteringsservrar konfigureras för att ansluta till OMS-Gateway för att ta emot information om konfiguration och skicka insamlade data. Operations Manager-agenter skickar vissa data, till exempel Operations Manager-aviseringar, configuration assessment, instansutrymme och kapacitetsinformation till hanteringsservern. Andra stora volymer data, till exempel IIS loggar information om programprestanda och säkerhetshändelser, skickas direkt till OMS-Gateway.  

Om du har en eller flera Operations Manager-Gateway-servrar som har distribuerats i en DMZ eller andra isolerat nätverk för att övervaka ej betrodda datorer kan inte kan de kommunicera med en OMS-Gateway. Operations Manager-Gateway-servrar kan endast rapportera till en hanteringsserver. 

När en hanteringsgrupp för Operations Manager har konfigurerats för att kommunicera med en OMS-Gateway distribueras automatiskt proxy konfigurationsinformation till alla agenthanterade datorer som konfigurerats för att samla in data för Log Analytics, även om inställningen är tom.    

För att tillhandahålla hög tillgänglighet för direktanslutna eller Operations Management-grupper som kommunicerar med logganalys via OMS-Gateway, kan du använda Utjämning av nätverksbelastning att dirigera och distribuerar trafik över flera OMS Gateway-servrar. Om en gateway-servern kraschar, dirigeras trafiken till en annan tillgänglig nod.  

Vi rekommenderar att du installerar OMS-agenten på datorn som kör programmet OMS-Gateway för att övervaka den och analysera prestanda eller händelse. Dessutom kan agenten OMS-gatewayen identifiera slutpunkter för tjänsten som behöver kommunicera med.

Alla agenter måste ha en nätverksanslutning till deras gateway så att de automatiskt kan överföra data till och från gatewayen. Vi rekommenderar inte att du installerar en gateway på en domänkontrollant.

Följande diagram visar dataflöde från direkt agenter för Azure Automation och Log Analytics använder gateway-servern. Proxykonfiguration för agent måste använda samma port som används i OMS-Gateway för att kommunicera med tjänsten.  

![direkt agentkommunikation med services-diagram](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

Följande diagram visar dataflöde från en Operations Manager-hanteringsgrupp till logganalys.   

![Operations Manager-kommunikation med logganalys diagram](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>Förutsättningar

När du anger en dator för att köra OMS-Gateway, se till att följande komponenter:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Minst en 4 kärnor och 8 GB minne 

### <a name="language-availability"></a>Tillgängliga språk

OMS-gatewayen är tillgänglig på följande språk:

- Kinesiska (förenklad)
- Kinesiska (traditionell)
- tjeckiska
- Nederländska
- Svenska
- Franska
- Tyska
- ungerska
- Italienska
- Japanska
- Koreanska
- Polska
- Portugisiska (Brasilien)
- Portugisiska (Portugal)
- Ryska
- Spanska (internationell)

### <a name="supported-encryption-protocols"></a>Krypteringsprotokoll som stöds
OMS-Gateway stöder endast Transport Layer Security (TLS) 1.0, 1.1 och 1.2.  Det stöder inte Secure Sockets Layer (SSL).

### <a name="supported-number-of-agent-connections"></a>Stöds antalet anslutningar som agenten
Följande tabell visar stöds antalet agenter som kommunicerar med en gateway-server. Det här stödet baseras på agenter och överför ungefär 200 KB data var 6: e sekund. Datavolym per agent testas är cirka 2.7 GB per dag.

|Gateway |Det ungefärliga antalet agenter som stöds|  
|--------|----------------------------------|  
|-CPU: Intel XEON CPU E5 2660 v3 @ 2.6 GHz 2 kärnor<br> -Minne: 4 GB<br> -Nätverksbandbredd: 1 Gbit/s| 600|  
|-CPU: Intel XEON CPU E5 2660 v3 @ 2.6 GHz 4 kärnor<br> -Minne: 8 GB<br> -Nätverksbandbredd: 1 Gbit/s| 1000|  

## <a name="download-the-oms-gateway"></a>Hämta OMS-Gateway

Det finns två sätt att få den senaste versionen av installationsfilen OMS-Gateway.

1. Ladda ner det från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443).

2. Ladda ned det från Azure-portalen. När du loggar in på Azure-portalen kan du utföra följande åtgärder:  

   1. Bläddra i listan över tjänster och välj sedan **logganalys**.  
   2. Välj en arbetsyta.
   3. I arbetsytan-bladet under **allmänna**väljer **Snabbstart**.
   4. Under **Välj en datakälla för att ansluta till arbetsytan**väljer **datorer**.
   5. I den **direkt Agent** fönstret väljer **hämta OMS Gateway**.
   
    ![Hämta OMS-Gateway](./media/log-analytics-oms-gateway/download-gateway.png)

--ELLER-- 

   1. I arbetsytan-bladet under **inställningar**väljer **avancerade inställningar**.
   2. Välj **anslutna källor** > **Windows-servrar**. Välj sedan **hämta OMS Gateway**.

## <a name="install-the-oms-gateway"></a>Installera OMS-Gateway

Gör följande om du vill installera en gateway. Om du har installerat den tidigare versionen kallades *Log Analytics vidarebefordrare*, kommer att uppgraderas till den här versionen.  

1. Välj i målmappen, **OMS Gateway.msi**.
2. På sidan **Välkommen** klickar du på **Nästa**.

 ![Installationsguiden för gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. På den **licensavtalet** väljer **jag accepterar villkoren i licensavtalet**. Välj sedan **Nästa**.

4. På den **Port och proxy adress** sidan:

   a. Ange TCP-portnummer som ska användas för gatewayen. Installationsprogrammet konfigurerar en inkommande regel med det här portnumret på Windows-brandväggen. Standardvärdet är 8080. Portnumret giltigt intervall är 1 till 65535. Om indata inte hamnar i det här intervallet, visas ett felmeddelande.

   b. Alternativt servern där gatewayen har installerats behöver för att kommunicera via en proxyserver, skriver du proxyn som gatewayen ska anslutas. Ett exempel är `http://myorgname.corp.contoso.com:80`som visas i följande skärmbild. Om du lämnar fältet tomt försöker gatewayen ansluter direkt till internet.  Om proxyservern kräver autentisering, ange ett användarnamn och lösenord.
   
    ![Proxykonfiguration för gateway-guiden](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Välj **Nästa**.

5. Om du inte har Microsoft Update har aktiverats, visas sidan Microsoft Update och du kan välja att aktivera den. Gör ett val och välj sedan **nästa**. I annat fall Fortsätt till nästa steg.

6. På den **målmappen** sida, lämnar du antingen standardmappen som **C:\Program Files\OMS Gateway** eller ange platsen där du vill installera OMS-gateway. Välj sedan **Nästa**.

7. På den **redo att installera** väljer **installera**. User Account Control visas begärande behörighet att installera. Om du får en förfrågan om tillåtelse väljer **Ja**.

8. När installationen är klar, Välj **Slutför**. Du kan kontrollera att tjänsten körs genom att öppna snapin-modulen services.msc att se till att **OMS Gateway** visas i listan över tjänster, och att statusen är **kör**.

    ![Tjänster – OMS-Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Konfigurera Utjämning av nätverksbelastning 
Du kan konfigurera en gateway för hög tillgänglighet med hjälp av Utjämning av nätverksbelastning (NLB). Använd Microsoft Utjämning av nätverksbelastning eller maskinvarubaserad belastningsutjämnare.  Belastningsutjämnaren hanterar trafik genom att omdirigera begärda anslutningar från OMS-Agent eller Operations Manager-hanteringsservrar över dess noder. Om en gateway-servern kraschar, hämtar trafiken dirigeras till andra noder.

Om du vill lära dig mer om att skapa och distribuera en Windows Server 2016 Utjämning av nätverksbelastning kluster, se [Utjämning av nätverksbelastning](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Följande steg beskriver hur du konfigurerar en Microsoft Utjämning av nätverksbelastning kluster.  

1.  Logga in med ett administratörskonto till Windows server som är medlem i NLB-klustret.  

2.  I Serverhanteraren Öppna Hanteraren för Utjämning av nätverksbelastning. Välj **verktyg**, och välj sedan **hanteraren**.

3. Högerklicka på klustrets IP-adress för att ansluta en OMS-Gateway-servern med Microsoft Monitoring Agent installerad, och välj sedan **Lägg till värd i klustret**.

 ![Nätverket läsa in belastningsutjämning Manager--Lägg till värd i klustret](./media/log-analytics-oms-gateway/nlb02.png)

4. Ange IP-adressen för gateway-servern som du vill ansluta till.

    ![Utjämning av nätverksbelastning – Lägg till värd i klustret: ansluta](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Konfigurera OMS-agenten och Operations Manager-hanteringsgruppen
De följande avsnitten i den här artikeln innehåller anvisningar om hur du konfigurerar direktanslutna OMS-Agent, en Operations Manager-hanteringsgrupp eller Azure Automation Hybrid Runbook workers med OMS-Gateway för att kommunicera med Azure Automation eller logganalys.  

Information om krav och steg för att installera OMS-agent på Windows-datorer som ansluter direkt till Log Analytics finns [samla in data från datorer i din miljö med logganalys](log-analytics-concept-hybrid.md#prerequisites).

 Linux-datorer finns i [ansluta Linux-datorer till logganalys](log-analytics-quick-collect-linux-computer.md). Information som rör Automation Hybrid Runbook Worker finns [distribuera Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Konfigurera fristående OMS-agent
Information om hur du konfigurerar en agent om du vill använda en proxyserver (som i det här fallet är gatewayen) finns i [samla in data från datorer i din miljö med logganalys](log-analytics-concept-hybrid.md#prerequisites). Om du har distribuerat flera gateway-servrar bakom en belastningsutjämnare för nätverk är OMS-agent proxykonfiguration virtuella IP-adressen för Utjämning av nätverksbelastning:

![Microsoft Monitoring Agent-egenskaper – proxyinställningar](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Konfigurera Operations Manager: alla agenter Använd samma proxyserver
Du kan konfigurera Operations Manager för att lägga till gateway-servern.  Operations Manager-proxykonfigurationen tillämpas automatiskt på alla agenter som rapporterar till Operations Manager, även om inställningen är tom.  

Om du vill använda OMS-Gateway för att stödja Operations Manager, måste följande komponenter vara på plats:

* Microsoft Monitoring Agent (agent-version **8.0.10900.0** eller senare) installerat på gateway-servern och konfigurerat för logganalys-arbetsytor du vill kommunicera.

* En gateway som har Internetanslutning eller en anslutning till en proxyserver som tillåter.

> [!NOTE]
> Om du inte anger ett värde för gatewayen pushas tomma värden till alla agenter.
> 

Om din Operations Manager-hanteringsgrupp som registrerar med logganalys-arbetsytan inte alternativet för att ange proxykonfiguration för hanteringsgruppen i Operations-konsolen. 

Hanteringsgruppen har har registrerats med tjänsten innan det här alternativet är tillgängligt. Uppdatera system proxykonfigurationen med hjälp av Netsh på samma system där du kör driftkonsolen och alla hanteringsservrar i hanteringsgruppen.

1. Öppna en kommandotolk med förhöjd behörighet.

    a. Gå till **starta**. Skriv **cmd**.
    
    b. Högerklicka på **kommandotolk**. Välj sedan **kör som administratör**.
    
2. Ange följande kommando och välj sedan **ange**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört integrationen med Log Analytics kan du ta bort ändringen genom att köra `netsh winhttp reset proxy`. Använd sedan den **konfigurera proxyservern** alternativet i Operations-konsolen för att ange OMS Gateway-servern. 

1. Öppna Operations Manager-konsolen. Under **Operations Management Suite**väljer **anslutning**. Välj sedan **konfigurera proxyservern**.

    ![Operations Manager--Konfigurera proxyserver](./media/log-analytics-oms-gateway/scom01.png)

2. Välj **använder en proxyserver för att få åtkomst till Operations Management Suite**. Skriv sedan IP-adressen för OMS Gateway-servern eller den virtuella IP-adressen i NLB-klustret. Se till att du börjar med den `http://` prefix.

    ![Operations Manager--proxyserveradress](./media/log-analytics-oms-gateway/scom02.png)

3. Välj **Slutför**. Hanteringsgruppen för Operations Manager har nu konfigurerats för att kommunicera via gateway-servern till Log Analytics-tjänsten.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Konfigurera Operations Manager: specifika agenter använder för proxyserver
För stora eller komplexa miljöer kanske du bara vill specifika servrar (eller grupper) att använda OMS Gateway-servern. För dessa servrar, du kan inte uppdatera Operations Manager-agenten direkt, eftersom detta värde åsidosätts av det globala värdet för hanteringsgruppen. I stället kan du åsidosätta den regel som används för att skicka dessa värden.  

> [!NOTE] 
> Samma konfiguration teknik kan användas för att tillåta användning av flera OMS Gateway-servrar i din miljö. Du kan till exempel kräva specifika OMS Gateway-servrar som anges på grundval av per region.
>  

1. Öppna Operations Manager-konsolen och välj sedan den **redigering** arbetsytan.

2. I arbetsytan redigering väljer **regler**. Välj sedan den **omfång** i Operations Manager-verktygsfältet. Om den här knappen inte är tillgänglig, kontrollera att du har ett objekt, inte en mapp markerad i den **övervakning** fönstret. Den **omfång för Hanteringspaketsobjekt** dialogrutan visar en lista med vanliga riktade klasser, grupper eller objekt. 

3. i den **leta efter** anger **Hälsotjänsten**. Markera den i listan. Välj **OK**.  

4. Sök efter regeln **Advisor Proxy inställningen regeln**. I verktygsfältet Operations-konsolen väljer du **åsidosätter**. Välj sedan **åsidosätta Rule\For ett specifikt objekt i klassen: Hälsotjänsten**. 

5. Välj sedan ett specifikt objekt i listan. 

6. (Valfritt) Skapa en anpassad grupp som innehåller hälsotillstånd serviceobjektet servrar som du vill tillämpa den här åsidosättningen. Du kan sedan koppla åsidosättningen till gruppen.

7. I den **egenskaper för åsidosättning** dialogrutan, klicka om du vill markera i den **åsidosätta** bredvid den **WebProxyAddress** parameter.  I den **åsidosättningsvärde** , ange Webbadressen till OMS-Gateway-servern att säkerställa att den börjar med den `http://` prefix.  

    >[!NOTE]
    > Du behöver inte aktivera regeln. Den hanteras redan automatiskt med en åsidosättning som har i Microsoft System Center Advisor säker referens Override management pack som riktar in sig Microsoft System Center Advisor övervakning servergruppen.
    >   

8. Välj antingen ett management pack från den **väljer målhanteringspaket** listan eller skapa ett nytt oförseglat hanteringspaket genom att välja **ny**. 

9. När du är klar med ändringarna markerar **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Konfigurera OMS-Gateway för Automation Hybrid Runbook Worker
Om du har Automation Hybrid Runbook Worker-arbeten i din miljö, ange följande manuella, tillfälligt lösningar för att konfigurera OMS-Gateway för att stödja dem.

Du behöver veta Azure-regionen där Automation-kontot finns i följande steg. Vidta följande steg för att hitta platsen:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj Azure Automation-tjänsten.
3. Välj lämplig Azure Automation-kontot.
4. Under **plats**, Visa region för kontot.

    ![Azure-portalen – platsen för Automation-konto](./media/log-analytics-oms-gateway/location.png)  

Använd följande tabeller för att identifiera URL: en för varje plats:

**Jobbet runtime data service-URL: er**

| **Plats** | **URL** |
| --- | --- |
| Norra centrala USA |ncus-jobruntimedata-prod-su1.azure-automation.net |
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

| **Plats** | **URL** |
| --- | --- |
| Norra centrala USA |ncus-agentservice-prod-1.azure-automation.net |
| Västra Europa |we-agentservice-prod-1.azure-automation.net |
| Södra centrala USA |scus-agentservice-prod-1.azure-automation.net |
| Östra USA 2 |eus2-agentservice-prod-1.azure-automation.net |
| Kanada central |cc-agentservice-prod-1.azure-automation.net |
| Norra Europa |ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-agentservice-prod-1.azure-automation.net |
| Indien, centrala |cid-agentservice-prod-1.azure-automation.net |
| Japan |jpe-agentservice-prod-1.azure-automation.net |
| Australien |ase-agentservice-prod-1.azure-automation.net |

Om datorn är automatiskt registrerad som en Hybrid Runbook Worker för korrigering med lösningen för hantering av uppdateringar, gör du följande:

1. Lägga till jobbet Körningsdata service-URL: er i listan med tillåtna värden på OMS-Gateway. Skriv till exempel följande: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Starta om tjänsten OMS-Gateway med hjälp av följande PowerShell-cmdlet: `Restart-Service OMSGatewayService`

Om du inbyggd Azure Automation datorn med hjälp av cmdleten Hybrid Runbook Worker registrering följer de här stegen:

1. Lägg till URL: en agent-tjänsten registrering i listan med tillåtna värden på OMS-Gateway. Skriv till exempel följande: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Lägga till jobbet Körningsdata service-URL: er i listan med tillåtna värden på OMS-Gateway. Skriv till exempel följande: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Starta om tjänsten OMS-Gateway:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Användbar PowerShell-cmdlets
Cmdlets kan hjälpa dig att utföra uppgifter som är nödvändiga för att uppdatera konfigurationsinställningarna för OMS-Gateway. Innan du använder dem, måste du vidta följande åtgärder:

1. Installera OMS-Gateway (MSI).
2. Öppna ett PowerShell-konsolfönster.
3. Importera modulen genom att skriva följande kommando: `Import-Module OMSGateway`.
4. Om inga fel uppstod i föregående steg, modulen har importerats och cmdlets kan användas. Skriv `Get-Module OMSGateway`.
5. När du gör ändringar med hjälp av cmdlets, se till att du startar om tjänsten OMS-Gateway.

Om du får ett fel i steg 3 kan importera inte modulen. Felet kan uppstå när PowerShell är det går inte att hitta modulen. Du hittar i OMS-Gateway-installationssökvägen: *C:\Program Files\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet:** | **Parametrar** | **Beskrivning** | **Exempel** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Nyckel |Hämtar konfigurationen för tjänsten |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Nyckel (krävs) <br> Värde |Ändringar av konfigurationen för tjänsten |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Hämtar adressen till proxy som relay (överordnad) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adress<br> Användarnamn<br> Lösenord |Anger relay (överordnad) proxy-adress (och autentiseringsuppgifter) |1. Ange en relay-proxy och autentiseringsuppgifter:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Ange en relay-proxy som inte kräver autentisering: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Avmarkera relay proxyinställning:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Hämtar för närvarande tillåtna värden (endast lokalt konfigurerade tillåts värd; inkluderas inte automatiskt hämtade tillåtna värdar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Värd (krävs) |Lägger till värden i listan över tillåtna |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Värd (krävs) |Tar bort värden från listan över tillåtna |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Lägger till klientcertifikatet för listan över tillåtna |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Ämne (krävs) |Tar bort klienten certifikatets ämne från listan över tillåtna |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Hämtar för närvarande tillåtna klienten certifikatämnen (endast lokalt konfigurerade tillåts ämnen, inkluderas inte automatiskt hämtade tillåtna ämnen) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Felsökning
Du måste också har OMS-agenten installerad för att samla in händelser som loggas av gateway.

![Loggboken – OMS Gateway logg](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS Gateway händelse-ID och beskrivningar**

Följande tabell visar händelse-ID och beskrivningar för händelser OMS-Gateway:

| **ID** | **Beskrivning** |
| --- | --- |
| 400 |Alla fel som inte har ett visst-ID. |
| 401 |Felaktig konfiguration. Till exempel: listenPort = ”text” i stället för ett heltal. |
| 402 |Undantag vid tolkning TLS handshake-meddelanden. |
| 403 |Fel för nätverk. Till exempel: Det går inte att ansluta till målservern. |
| 100 |Allmän information. |
| 101 |Tjänsten har startats. |
| 102 |Tjänsten har stoppats. |
| 103 |Tog emot ett HTTP-ansluta kommando från klienten. |
| 104 |Inte ett HTTP-ansluta kommando. |
| 105 |Målservern är inte i listan över tillåtna eller målporten är inte säker port (443). <br> <br> Se till att agenten MMA på Gateway-servern och agenterna som kommunicerar med Gateway är ansluten till samma logganalys-arbetsytan. |
| 105 |FEL TcpConnection – ogiltig klientcertifikat: CN = Gateway <br><br> Se till att: <br>    <br> -Du använder en Gateway med versionsnumret 1.0.395.0 eller större. <br> -MMA agenten på Gateway-servern och agenterna som kommunicerar med gatewayen som är anslutna till samma logganalys-arbetsytan. |
| 106 |OMS-Gateway har endast stöd för TLS 1.0, TLS 1.1 och 1.2.  Det stöder inte SSL. För alla stöds inte TLS/SSL-protokollversion genererar OMS Gateway händelse-ID 106.|
| 107 |TLS-sessionen har verifierats. |

**Prestandaräknare för att samla in**

I följande tabell visas de prestandaräknare som är tillgängliga för OMS-Gateway. Du kan lägga till räknare genom att använda Prestandaövervakaren.

| **Namn** | **Beskrivning** |
| --- | --- |
| OMS Gateway/aktiv klientanslutning |Antal aktiva klientnätverksanslutningar (TCP) |
| OMS Gateway/Felräkning |Antal fel |
| OMS-Gateway/ansluten klient |Antal anslutna klienter |
| OMS Gateway-avvisande antal |Antal nekanden på grund av eventuella verifieringsfel för TLS |

![Prestandaräknare OMS-Gateway](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Få hjälp
När du är inloggad på Azure-portalen, skapar du en begäran om du behöver hjälp med OMS-Gateway eller andra Azure-tjänst eller funktion i en tjänst.

Välj symbolen frågetecken i det övre högra hörnet av portalen för att begära hjälp. Välj sedan **ny supportbegäran**. Slutför formuläret nya support.

![Ny supportbegäran](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Nästa steg
[Lägg till datakällor](log-analytics-data-sources.md) samla in data från dina anslutna källor och lagra den i logganalys-arbetsytan.
