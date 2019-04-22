---
title: Anslut Cisco data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Cisco data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5bc57d448b8aa04b8cb6fb16000205fda8964150
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495710"
---
# <a name="connect-your-cisco-asa-appliance"></a>Ansluta din Cisco ASA-installation 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel till alla Cisco ASA-enhet. Cisco ASA är internt integrerat med Azure Sentinel för datainläsning, så att även om Cisco-installationen inte spara loggarna som CEF, Azure Sentinel matar in dem på samma sätt som den hanterar CEF-loggar. Integrering med Azure Sentinel kan du enkelt köra analyser och frågor över loggfilsdata från Cisco ASA. 

> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>Steg 1: Ansluta din Cisco ASA-installation med hjälp av en agent

För att ansluta din Cisco ASA-installation till Azure Sentinel, måste du distribuera en agent på en dedikerad dator (VM eller lokalt) för kommunikationen mellan enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är endast tillgänglig om den dedikerade datorn är en ny virtuell dator som du skapar i Azure. 

Du kan också distribuera agenten manuellt på en befintlig Azure-dator på en virtuell dator i ett annat moln eller på en lokal dator.

Ett nätverksdiagram för båda alternativen finns i [ansluta datakällor](connect-data-sources.md).

### <a name="deploy-the-agent-in-azure"></a>Distribuera agenten i Azure

1. Sentinel-Azure-portalen klickar du på **datakopplingar** och väljer du typen av installation. 

1. Under **Linux Syslog-agentkonfiguration**:
   - Välj **automatisk distribution** om du vill skapa en ny dator som är förinstallerade med agenten Sentinel-Azure och innehåller alla konfiguration behövs, enligt beskrivningen ovan. Välj **automatisk distribution** och klicka på **agenten för automatisk distribution**. Detta tar dig till sidan för en dedikerad virtuell dator som automatiskt ansluter till din arbetsyta, är. Den virtuella datorn är en **standard D2s v3 (2 virtuella processorer, 8 GB minne)** och har en offentlig IP-adress.
      1. I den **anpassad distribution** anger din information och välja ett användarnamn och ett lösenord och om du samtycker till villkoren kan du köpa den virtuella datorn.
      1. Konfigurera installationen för att skicka loggar med inställningar som anges i anslutningssidan. Använd de här inställningarna för allmän Common Event Format-anslutningen:
         - Protocol = UDP
         - Port = 514
         - Anläggning = lokal 4
         - Format = CEF
   - Välj **manuell distribution** om du vill använda en befintlig virtuell dator som den dedikerade Linux-datorn där Sentinel-Azure-agenten ska installeras. 
      1. Under **ladda ned och installera agenten Syslog**väljer **virtuella Linux-datorer**. 
      1. I den **virtuella datorer** skärmen som öppnas väljer du den dator som du vill använda och klicka på **Connect**.
      1. På skärmen anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**. 
      1. Kopiera dessa kommandon och köra dem på din installation:
          - Om du har valt rsyslog.d:
              
            1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
            
            1. Starta om syslog-daemon `sudo service rsyslog restart`
             
          - Om du har valt syslog-ng:

              1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.

              3. Starta om syslog-daemon `sudo service syslog-ng restart`
      2. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Distribuera agenten på en på lokala Linux-server

Om du inte använder Azure, distribuera manuellt Azure Sentinel-agenten ska köras på en dedikerad server för Linux.


1. Sentinel-Azure-portalen klickar du på **datakopplingar** och väljer du typen av installation.
1. Skapa en dedikerad Linux VM under **Linux Syslog-agentkonfiguration** väljer **manuell distribution**.
   1. Under **ladda ned och installera agenten Syslog**väljer **icke-Azure Linux-dator**. 
   1. I den **direktagent** skärm som öppnas väljer du **agenten för Linux** att ladda ned agenten eller kör detta kommando för att hämta den på din Linux-dator:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. På skärmen anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**. 
      1. Kopiera dessa kommandon och köra dem på din installation:
         - Om du har valt rsyslog:
           1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
           3. Starta om syslog-daemon `sudo service rsyslog restart`
         - Om du har valt syslog-ng:
            1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
            3. Starta om syslog-daemon `sudo service syslog-ng restart`
      1. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>Steg 2: Vidarebefordra Cisco ASA-loggar till Syslog-agent

Konfigurera Cisco ASA för att vidarebefordra Syslog-meddelanden till Azure-arbetsytan via Syslog-agenten:

Gå till [skicka Syslog-meddelanden till en extern Syslog-server](https://aka.ms/asi-syslog-cisco-forwarding), och följ anvisningarna för att upprätta anslutningen. Använd de här parametrarna när du uppmanas till detta:
- Ange **port** 514 eller den port som du anger i agenten.
- Ange **syslog_ip** till IP-adressen för agenten.
- Ange **loggningsfunktionen** till funktionen som du anger i agenten. Som standard anger agenten funktionen till 4.


## <a name="step-3-validate-connectivity"></a>Steg 3: Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 

1. Se till att dina loggar får till rätt port i Syslog-agenten. Kör det här kommandot agentdatorn Syslog: `tcpdump -A -ni any  port 514 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Det här kommandot visar de loggar som direktuppspelning från enheten till den Syslog-datorn. Kontrollera att loggarna tas emot från käll-installationen på rätt port och rätt resurs.
2. Kontrollera att det finns kommunikation mellan Syslog-daemon och agenten. Kör det här kommandot agentdatorn Syslog: `tcpdump -A -ni any  port 25226 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Kontrollera att loggarna också tas emot på agenten.
3. Om båda dessa kommandon angetts lyckade resultat och kontrollera Log Analytics för att se om dina loggar inkommer. Alla händelser som strömmas direkt från dessa enheter visas i obearbetat format i Log Analytics under `CommonSecurityLog` typen.
1. Om du vill kontrollera om det finns fel eller om loggarna som inte kommer att se ut `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Se till att din standardstorlek för Syslog-meddelande är begränsat till 2 048 byte (2KB). Om loggarna är för långt, uppdatera security_events.conf med hjälp av det här kommandot: `message_length_limit 4096`
6. Om du vill använda relevanta schemat i Log Analytics för Cisco-händelser, söka efter **CommonSecurityLog**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Cisco ASA-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

