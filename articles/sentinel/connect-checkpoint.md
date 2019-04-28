---
title: Anslut Check Point data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Check Point data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 664b09a8ad0cb7d06019281869e390a465637c00
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120934"
---
# <a name="connect-your-check-point-appliance"></a>Ansluta din Check Point-installation

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Sentinel-Azure till någon Check Point-installation för genom att spara loggfilerna som Syslog CEF. Integrering med Azure Sentinel kan du enkelt köra analyser och frågor över loggfilsdata från Check Point. Mer information om hur Azure Sentinel matar in data som CEF Se [ansluta CEF installationer](connect-common-event-format.md).

> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>Steg 1: Ansluta din Check Point-installation med hjälp av en agent

För att ansluta din Check Point-installation till Azure Sentinel, måste du distribuera en agent på en dedikerad dator (VM eller lokal) för att stödja kommunikation mellan enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är endast tillgänglig om den dedikerade datorn är en ny virtuell dator som du skapar i Azure. 

Du kan också distribuera agenten manuellt på en befintlig Azure-dator på en virtuell dator i ett annat moln eller på en lokal dator.

Ett nätverksdiagram för båda alternativen finns i [ansluta datakällor](connect-data-sources.md).

### <a name="deploy-the-agent-in-azure"></a>Distribuera agenten i Azure

1. Sentinel-Azure-portalen klickar du på **datakopplingar** och väljer du typen av installation. 

1. Under **Linux Syslog-agentkonfiguration**:
   - Välj **automatisk distribution** om du vill skapa en ny dator som är förinstallerade med agenten Sentinel-Azure och innehåller alla konfiguration behövs, enligt beskrivningen ovan. Välj **automatisk distribution** och klicka på **agenten för automatisk distribution**. Detta tar dig till sidan för en dedikerad virtuell dator som automatiskt ansluter till din arbetsyta. Den virtuella datorn är en **standard D2s v3 (2 virtuella processorer, 8 GB minne)** och har en offentlig IP-adress.
     1. I den **anpassad distribution** anger din information och välja ett användarnamn och ett lösenord och om du samtycker till villkoren kan du köpa den virtuella datorn.
      
        1. Kör följande kommandon på agentdatorn Syslog att se till att alla Check Point loggar mappas till Sentinel-Azure-agenten:
           - Om du använder Syslog-ng kör du följande kommandon (Observera att det startas Syslog-agenten):
            
                sudo bash - c ”printf” filtrera f_local4_oms {facility(local4);}; \ n mål security_oms {tcp (\"127.0.0.1\" port(25226));}; \n log {source(src); filter(f_local4_oms); destination(security_oms);}; \n\nfilter f_msg_oms {matchar (\"Check Point\" värde (\" MEDDELANDET\")); }; \n mål security_msg_oms {tcp (\"127.0.0.1\" port(25226));}; \n log {source(src); filter(f_msg_oms); destination(security_msg_oms);};' > /etc/syslog-ng/security-config-omsagent.conf ”

             Starta om Syslog-daemon: `sudo service syslog-ng restart`
           - Om du använder rsyslog kör du följande kommandon (Observera att det startas Syslog-agenten):
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             Starta om Syslog-daemon: `sudo service rsyslog restart`

   - Välj **manuell distribution** om du vill använda en befintlig virtuell dator som den dedikerade Linux-datorn där Sentinel-Azure-agenten ska installeras. 
      1. Under **ladda ned och installera agenten Syslog**väljer **virtuella Linux-datorer**. 
      1. I den **virtuella datorer** skärmen som öppnas väljer du den dator som du vill använda och klicka på **Connect**.
      1. På skärmen anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**. 
      1. Kopiera dessa kommandon och köra dem på din installation:
          - Om du har valt rsyslog.d:
              
            1. Berätta för Syslog-daemon att lyssna på anläggningen local_4 och till ”Check Point” och att skicka Syslog-meddelanden till Sentinel-Azure-agenten med porten 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
           
            1. Starta om syslog-daemon `sudo service rsyslog restart`
             
          - Om du har valt syslog-ng:

              1. Berätta för Syslog-daemon att lyssna på anläggningen local_4 och till ”Check Point” och att skicka Syslog-meddelanden till Sentinel-Azure-agenten med porten 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.

              3. Starta om syslog-daemon `sudo service syslog-ng restart`
      2. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Distribuera agenten på en på lokala Linux-server

Om du inte använder Azure, distribuera manuellt Azure Sentinel-agenten ska köras på en dedikerad server för Linux.

1. Skapa en dedikerad Linux VM under **Linux Syslog-agentkonfiguration** väljer **manuell distribution**.
   1. Under **ladda ned och installera agenten Syslog**väljer **icke-Azure Linux-dator**. 
   1. I den **direktagent** skärm som öppnas väljer du **agenten för Linux** att ladda ned agenten eller kör detta kommando för att hämta den på din Linux-dator:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. På skärmen anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**. 
      1. Kopiera dessa kommandon och köra dem på din installation:
         - Om du har valt **rsyslog**:
           1. Berätta för Syslog-daemon att lyssna på anläggningen local_4 och till ”Check Point” och att skicka Syslog-meddelanden till Sentinel-Azure-agenten med porten 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
           3. Starta om syslog-daemon `sudo service rsyslog restart`
         - Om du har valt **syslog-ng**:
            1. Berätta för Syslog-daemon att lyssna på anläggningen local_4 och till ”Check Point” och att skicka Syslog-meddelanden till Sentinel-Azure-agenten med porten 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.
            3. Starta om syslog-daemon `sudo service syslog-ng restart`
      1. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>Steg 2: Vanlig Check Point loggar till Syslog-agent

Konfigurera Check Point installationen för att vidarebefordra Syslog-meddelanden i CEF-format till Azure-arbetsytan via Syslog-agenten.

1. Gå till [Kontrollera Export av punkt](https://aka.ms/asi-syslog-checkpoint-forwarding).
2. Rulla ned till **grundläggande distribution** och följ anvisningarna för att upprätta anslutningen med hjälp av följande riktlinjer:
   - Ange den **Syslog-port** till **514** eller den port du ställt in på agenten.
     - Ersätt den **namn** och **mål-IP-adress** i CLI med namnet på den Syslog och IP-adress.
     - Ange format **CEF**.
3. Om du använder version R77.30 eller R80.10 rulla upp till **installationer** och följ anvisningarna för att installera en logg exportör för din version.
 
## <a name="step-3-validate-connectivity"></a>Steg 3: Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 

1. Se till att dina loggar får till rätt port i Syslog-agenten. Kör det här kommandot agentdatorn Syslog: `tcpdump -A -ni any  port 514 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Kontrollera att loggarna tas emot från käll-installationen på rätt port och rätt resurs.
2. Kontrollera att det finns kommunikation mellan Syslog-daemon och agenten. Kör det här kommandot agentdatorn Syslog: `tcpdump -A -ni any  port 25226 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Kontrollera att loggarna också tas emot på agenten.
3. Om båda dessa kommandon angetts lyckade resultat och kontrollera Log Analytics för att se om dina loggar inkommer. Alla händelser som strömmas direkt från dessa enheter visas i obearbetat format i Log Analytics under `CommonSecurityLog` typen.

4. Se till att köra dessa kommandon:
  
   - Om du använder Syslog-ng kör du följande kommandon (Observera att det startas Syslog-agenten):

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Starta om Syslog-daemon: `sudo service syslog-ng restart`

   - Om du använder rsyslog kör du följande kommandon (Observera att det startas Syslog-agenten): 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Starta om Syslog-daemon: `sudo service rsyslog restart`

1. Om du vill kontrollera om det finns fel eller om loggarna som inte kommer att se ut `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Se till att din standardstorlek för Syslog-meddelande är begränsat till 2 048 byte (2KB). Om loggarna är för långt, uppdatera security_events.conf med hjälp av det här kommandot: `message_length_limit 4096`



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Check Point installationer till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

