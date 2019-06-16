---
title: Ansluta CEF-data till Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Lär dig hur du ansluter CEF-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 8e711c0586ce63d4293e2fb0914bbe884b55971f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389952"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ansluta din externa lösning med hjälp av Common Event Format

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel med en extern lösning som hjälper dig att spara loggfiler i Syslog. Om din installation kan du spara loggarna som Syslog Common Event Format (CEF), kan integrering med Azure Sentinel du enkelt köra analyser och frågor över data.

> [!NOTE] 
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="how-it-works"></a>Hur det fungerar

Anslutningen mellan Sentinel-Azure och din CEF-installation sker i tre steg:

1. Du måste ange dessa värden så att enheten som skickar nödvändiga loggar i rätt format för Azure Sentinel Syslog-agenten på installationen. Du kan ändra dessa parametrar i din installation så länge som du också ändra dem i Syslog-daemon på Sentinel-Azure-agenten.
    - Protocol = UDP
    - Port = 514
    - Anläggning = lokal 4
    - Format = CEF
2. Syslog-agenten samlar in information och skickar dem på ett säkert sätt till Log Analytics, där den parsas och utökad.
3. Agenten lagrar data i Log Analytics-arbetsytan så att den kan ta emot efter behov med analytics, Korrelations-regler och instrumentpaneler.

> [!NOTE]
> Agenten kan samla in loggar från flera källor, men de måste installeras på dedikerad proxy-datorn.

## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>Steg 1: Ansluta till din CEF-installation via dedikerade virtuella Azure-datorer

Du måste distribuera en agent på en dedikerad Linux-dator (VM eller lokalt) för kommunikationen mellan enheten och Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution baseras på Resource Manager-mallar och kan bara användas om den dedikerade Linux-datorn är en ny virtuell dator som du skapar i Azure.

 ![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Du kan också distribuera agenten manuellt på en befintlig Azure-dator på en virtuell dator i ett annat moln eller på en lokal dator. 

 ![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Distribuera agenten i Azure


1. Sentinel-Azure-portalen klickar du på **datakopplingar** och väljer du typen av installation. 

1. Under **Linux Syslog-agentkonfiguration**:
   - Välj **automatisk distribution** om du vill skapa en ny dator som är förinstallerade med agenten Sentinel-Azure och innehåller alla konfiguration behövs, enligt beskrivningen ovan. Välj **automatisk distribution** och klicka på **agenten för automatisk distribution**. Detta tar dig till sidan för en dedikerad Linux VM som automatiskt ansluter till din arbetsyta. Den virtuella datorn är en **standard D2s v3 (2 virtuella processorer, 8 GB minne)** och har en offentlig IP-adress.
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
            
            1. Starta om syslog-daemon `sudo service rsyslog restart`<br> Mer information finns i den [rsyslog-dokumentation](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Om du har valt syslog-ng:

              1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Där {0} ska ersättas med din arbetsyta GUID.

              3. Starta om syslog-daemon `sudo service syslog-ng restart` <br>Mer information finns i den [syslog-ng-dokumentation](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

 Om du vill använda relevanta schemat i Log Analytics för CEF-händelser, söka efter `CommonSecurityLog`.


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
  
 Om du vill använda relevanta schemat i Log Analytics för CEF-händelser, söka efter `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Steg 2: Vidarebefordra Common Event Format (CEF) loggar till Syslog-agent

Ange din säkerhetslösning för att skicka Syslog-meddelanden i CEF-format till Syslog-agenten. Kontrollera att du använder samma parametrar som visas i din agentkonfiguration. Det här är vanligtvis:

- Port 514
- Anläggning local4

## <a name="step-3-validate-connectivity"></a>Steg 3: Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 

1. Kontrollera att du använder rätt anläggningen. Funktionen måste vara samma i din installation och Sentinel-Azure. Du kan kontrollera vilken anläggning-fil som du använder i Azure Sentinel och göra ändringar i filen `security-config-omsagent.conf`. 

2. Se till att dina loggar får till rätt port i Syslog-agenten. Kör följande kommando på agentdatorn Syslog: `tcpdump -A -ni any  port 514 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Kontrollera att loggarna tas emot från käll-installationen på rätt port och rätt resurs.

3. Se till att loggarna du skickar följer [RFC 5424](https://tools.ietf.org/html/rfc542).

4. På den dator som kör Syslog-agenten, se till att dessa portar 514, 25226 är öppna och lyssna, med hjälp av kommandot `netstat -a -n:`. Mer information om hur du använder det här kommandot finns i [netstat(8) - Linux man sidan](https://linux.die.net/man/8/netstat). Om den lyssnar på rätt sätt, visas följande:

   ![Azure Sentinel-portar](./media/connect-cef/ports.png) 

5. Kontrollera daemon anges att lyssna på port 514, där du skickar loggarna.
    - För rsyslog:<br>Kontrollera att filen `/etc/rsyslog.conf` innehåller den här konfigurationen:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Mer information finns i [imudp: Indata för UDP Syslogmodulen](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) och [imtcp: Indata för Syslog-modulen för TCP](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - För syslog-ng:<br>Kontrollera att filen `/etc/syslog-ng/syslog-ng.conf` innehåller den här konfigurationen:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Mer information finns i [imudp: UDP Syslogmodulen till indata] (Mer information finns i den [syslog-ng öppen källkod Edition 3,16 - Administrationsguide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Kontrollera att det finns kommunikation mellan Syslog-daemon och agenten. Kör följande kommando på agentdatorn Syslog: `tcpdump -A -ni any  port 25226 -vv` Det här kommandot visar de loggar som strömmas från enheten till den Syslog-datorn. Kontrollera att loggarna också tas emot på agenten.

6. Om båda dessa kommandon angetts lyckade resultat och kontrollera Log Analytics för att se om dina loggar inkommer. Alla händelser som strömmas direkt från dessa enheter visas i obearbetat format i Log Analytics under `CommonSecurityLog` typen.

7. Kontrollera om det finns fel eller om loggarna som inte kommer se i `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Om det står att det finns fel i formatet matchningsfel, går du till `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` och titta på filen `security_events.conf`och se till att dina loggar matchar regex-format som du ser i den här filen.

8. Se till att din standardstorlek för Syslog-meddelande är begränsat till 2 048 byte (2KB). Om loggarna är för långt, uppdatera security_events.conf med hjälp av det här kommandot: `message_length_limit 4096`


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF installationer till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

