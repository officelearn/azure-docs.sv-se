---
title: Anslut CEF-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter CEF-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2019
ms.author: rkarlin
ms.openlocfilehash: 28def73926294a025d70844e535a0856153ae30a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611931"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ansluta din externa lösning med hjälp av vanligt händelse format

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel med en extern lösning som gör att du kan spara loggfiler i syslog. Om din enhet gör det möjligt att spara loggar som ett syslog-CEF (common Event format) kan du enkelt köra analyser och frågor över data med hjälp av integreringen med Azure Sentinel.

> [!NOTE] 
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="how-it-works"></a>Hur det fungerar

Anslutningen mellan Azure Sentinel och din CEF-apparat sker i tre steg:

1. På installationen måste du ange dessa värden så att installations programmet skickar nödvändiga loggar i det format som krävs till Azure Sentinel syslog-agenten, baserat på Microsoft Monitoring Agent. Du kan ändra dessa parametrar i din installation så länge du också ändrar dem i syslog-daemonen på Azure Sentinel-agenten.
    - Protokoll = UDP
    - Port = 514
    - Facility = Local4
    - Format = CEF
2. Syslog-agenten samlar in data och skickar dem säkert till Log Analytics, där det är parsat och berikat.
3. Agenten lagrar data i en Log Analytics arbets yta så att den kan frågas vid behov, med hjälp av analys, korrelations regler och instrument paneler.

> [!NOTE]
> Agenten kan samla in loggar från flera källor, men måste installeras på en särskild dator.


 ![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator. 

 ![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Säkerhetsöverväganden

Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. Du kan använda följande instruktioner för att förbättra datorns säkerhets konfiguration:  [säker virtuell dator i Azure](../virtual-machines/linux/security-policy.md), [metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md).

Om du vill använda TLS-kommunikation mellan säkerhets lösningen och syslog-datorn måste du konfigurera syslog-daemon (rsyslog eller syslog-ng) för att kommunicera i TLS: [Kryptera syslog-trafik med TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [kryptera logg meddelanden med TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Steg 1: Konfigurera din syslog-VM

Du måste distribuera en agent på en särskild Linux-dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure Sentinel. 

> [!NOTE]
> Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. 


1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **vanligt händelse format (CEF)** och **öppnar sedan kopplings sidan**. 

1. Under **Hämta och installera syslog**-agenten väljer du dator typ, antingen Azure eller lokalt. 
1. På skärmen **virtuella datorer** som öppnas väljer du den dator som du vill använda och klickar på **Anslut**.
1. Om du väljer **Hämta och installera agent för virtuella Azure Linux-datorer**väljer du datorn och klickar på **Anslut**. Om du väljer **Hämta och installera agent för virtuella datorer som inte använder Azure Linux**kör du skriptet under **Ladda ned och integrera agent för Linux**på skärmen **Direct agent** .
1. På skärmen CEF-anslutning under **Konfigurera och vidarebefordra syslog**anger du om ditt syslog-daemon är **rsyslog. d** eller **syslog-ng**. 
1. Kopiera dessa kommandon och kör dem på din apparat:
    - Om du har valt rsyslog. d:
              
       1. Instruera syslog-daemon att lyssna på local_4 och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.
            
       1. Starta om syslog-daemon`sudo service rsyslog restart`<br> Mer information finns i rsyslog- [dokumentationen](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
    - Om du valde syslog-ng:
       1. Instruera syslog-daemon att lyssna på local_4 och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.

        3. Starta om syslog-daemon`sudo service syslog-ng restart` <br>Mer information finns i [syslog-ng-dokumentationen](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
1. Starta om syslog-agenten med följande kommando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Bekräfta att det inte finns några fel i agent loggen genom att köra det här kommandot:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Om du vill använda det relevanta schemat i Log Analytics för CEF-händelserna söker `CommonSecurityLog`du efter.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Steg 2: Vidarebefordra CEF-loggar (common Event format) till syslog-agenten

Ange din säkerhets lösning för att skicka syslog-meddelanden i CEF-format till syslog-agenten. Se till att du använder samma parametrar som visas i agent konfigurationen. Detta är vanligt vis:

- Port 514
- Lokal local4

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
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

