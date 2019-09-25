---
title: Anslut Cisco-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Cisco-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6218d3a53df38e938b7d7805c45facd30f850105
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240148"
---
# <a name="connect-your-cisco-asa-appliance"></a>Anslut Cisco ASA-enheten 



Du kan ansluta Azure Sentinel till valfri Cisco ASA-enhet. Cisco ASA är inbyggt integrerat med Azure Sentinel för data inmatning, så att även om Cisco-installationen inte sparar loggar som CEF, matar Azure Sentinel in dem på samma sätt som den hanterar CEF-loggar. Integrationen med Azure Sentinel gör det möjligt för dig att enkelt köra analyser och frågor i logg fils data från Cisco ASA. 

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>Steg 1: Ansluta Cisco ASA-enheten med en agent

Om du vill ansluta Cisco ASA-enheten till Azure Sentinel måste du distribuera en agent på en dedikerad dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure-kontroll. 

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

> [!NOTE]
> Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. 

Om du vill se ett nätverks diagram över båda alternativen, se [Anslut data källor](connect-data-sources.md).

### <a name="deploy-the-agent-on-your-machine"></a>Distribuera agenten på din dator

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer sedan **Cisco ASA** och sedan **Öppna kopplings sida**. 

1. Under **Hämta och installera syslog-agenten**väljer du dator typ, antingen Azure eller lokalt. 
1. På skärmen **virtuella datorer** som öppnas väljer du den dator som du vill använda och klickar på **Anslut**.
1. Om du väljer **Hämta och installera agent för virtuella Azure Linux-datorer**väljer du datorn och klickar på **Anslut**. Om du väljer **Hämta och installera agent för virtuella datorer som inte använder Azure Linux**kör du skriptet under **Ladda ned och integrera agent för Linux**på skärmen **Direct agent** .
1. Ange om syslog-daemonen ska vara **rsyslog. d** eller **syslog-ng**på anslutnings skärmen under **Konfigurera och vidarebefordra syslog**. 
1. Kopiera dessa kommandon och kör dem på din apparat:
    - Om du har valt rsyslog. d:
              
       1. Instruera syslog-daemon att lyssna på local_4 och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.
            
      1. Starta om syslog-daemon`sudo service rsyslog restart`
             
    - Om du valde syslog-ng:

        1. Instruera syslog-daemon att lyssna på local_4 och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
        2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.

        3. Starta om syslog-daemon`sudo service syslog-ng restart`
1. Starta om syslog-agenten med följande kommando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Bekräfta att det inte finns några fel i agent loggen genom att köra det här kommandot:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>Steg 2: Vidarebefordra Cisco ASA-loggar till syslog-agenten

Cisco ASA stöder inte CEF, så loggarna skickas som syslog och Azure Sentinel-agenten vet hur de kan parsas som om de är CEF-loggar. Konfigurera Cisco ASA för att vidarebefordra syslog-meddelanden till Azure-arbetsytan via syslog-agenten:

Gå till [Skicka syslog-meddelanden till en extern Syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)och följ anvisningarna för att konfigurera anslutningen. Använd följande parametrar när du uppmanas till det:
- Ange **port** till 514 eller porten som du anger i agenten.
- Ange **syslog_ip** som IP-adress för agenten.
- Ange **loggnings funktion** för den funktion som du har angett i agenten. Som standard ställer agenten in funktionen på 4.

Om du vill använda det relevanta schemat i Log Analytics för Cisco-händelserna söker `CommonSecurityLog`du efter.

## <a name="step-3-validate-connectivity"></a>Steg 3: Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 

1. Se till att du använder rätt funktion. Funktionen måste vara densamma i din installation och i Azure Sentinel. Du kan kontrol lera vilken lokal fil du använder i Azure Sentinel och ändra den i filen `security-config-omsagent.conf`. 

2. Se till att dina loggar får rätt port i syslog-agenten. Kör det här kommandot på datorn för syslog-agenten: `tcpdump -A -ni any  port 514 -vv`Det här kommandot visar de loggar som strömmas från enheten till syslog-datorn. Se till att loggarna tas emot från käll installationen på rätt port och till höger.

3. Se till att de loggar du skickar följer [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. På den dator som kör syslog-agenten kontrollerar du att portarna 514, 25226 är öppna och lyssnar med kommandot `netstat -a -n:`. Mer information om hur du använder det här kommandot finns i [netstat (8) – Linux man-sidan](https://linux.die.net/man/8/netstat). Om den lyssnar korrekt visas följande:

   ![Azure Sentinel-portar](./media/connect-cef/ports.png) 

5. Kontrol lera att daemon är inställt på att lyssna på port 514, där du skickar loggarna.
    - För rsyslog:<br>Kontrol lera att filen `/etc/rsyslog.conf` innehåller den här konfigurationen:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Mer information finns i [imudp: UDP syslog-ingångs modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) och [imtcp: TCP syslog-indatamängd](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - För syslog-ng:<br>Kontrol lera att filen `/etc/syslog-ng/syslog-ng.conf` innehåller den här konfigurationen:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Mer information finns i [syslog-ng öppen källkod Edition 3,16-administrations guide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Kontrol lera att det finns kommunikation mellan syslog-daemon och agenten. Kör det här kommandot på datorn för syslog-agenten: `tcpdump -A -ni any  port 25226 -vv`Det här kommandot visar de loggar som strömmas från enheten till syslog-datorn. Se till att loggarna också tas emot på agenten.

6. Om båda de här kommandona har fått lyckade resultat kontrollerar du Log Analytics för att se om dina loggar kommer in. Alla händelser som strömmas från dessa apparater visas i rå form i Log Analytics `CommonSecurityLog` under typ.

7. Om du vill kontrol lera om det finns fel eller om loggarna inte kommer `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`, kan du leta i. Om det står fel i logg format, går du till `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` och tittar på filen `security_events.conf`och kontrollerar att dina loggar matchar regex-formatet som visas i den här filen.

8. Se till att standard storleken för syslog-meddelanden är begränsad till 2048 byte (2 KB). Om loggarna är för långa uppdaterar du security_events. conf med det här kommandot:`message_length_limit 4096`




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Cisco ASA-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

