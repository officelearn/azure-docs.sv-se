---
title: Anslut kontroll punkts data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter kontroll punkts data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: ea9cb7997f86c10bb09c01dfc345aaeeef46708a
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679343"
---
# <a name="connect-your-check-point-appliance"></a>Anslut Check Point-enheten

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel till valfri Check Point-apparat genom att spara loggfilerna som syslog-CEF. Integrationen med Azure Sentinel gör det möjligt för dig att enkelt köra analyser och frågor i logg fils data från Check Point. Mer information om hur Azure Sentinel matar in CEF-data finns i [ansluta CEF-enheter](connect-common-event-format.md).

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>Steg 1: Anslut Check Point-apparaten med en agent

Om du vill ansluta din Check Point-installation till Azure Sentinel måste du distribuera en agent på en dedikerad dator (VM eller lokal) för att stödja kommunikationen mellan-apparaten och Azure-kontroll. 

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

> [!NOTE]
> Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. 

Om du vill se ett nätverks diagram över båda alternativen, se [Anslut data källor](connect-data-sources.md).

### <a name="deploy-the-agent"></a>Distribuera agenten
1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **Check Point** och sedan **Öppna kopplings sida**. 

1. Under **Hämta och installera syslog**-agenten väljer du dator typ, antingen Azure eller lokalt. 
1. På skärmen **virtuella datorer** som öppnas väljer du den dator som du vill använda och klickar på **Anslut**.
1. Om du väljer **Hämta och installera agent för virtuella Azure Linux-datorer**väljer du datorn och klickar på **Anslut**. Om du väljer **Hämta och installera agent för virtuella datorer som inte använder Azure Linux**kör du skriptet under **Ladda ned och integrera agent för Linux**på skärmen **Direct agent** .
 
1. Ange om syslog-daemonen ska vara **rsyslog. d** eller **syslog-ng**på anslutnings skärmen under **Konfigurera och vidarebefordra syslog**. 
1. Kopiera dessa kommandon och kör dem på din apparat:
     - Om du har valt rsyslog. d:
            
       1. Instruera syslog-demon att lyssna på local_4 och till "Check Point" och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
           
       2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.
           
       1. Starta om syslog-daemon`sudo service rsyslog restart`
             
    - Om du valde syslog-ng:

         1. Instruera syslog-demon att lyssna på local_4 och till "Check Point" och skicka syslog-meddelanden till Azure Sentinel-agenten med port 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
         2. Hämta och installera den [security_events-konfigurationsfil](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar syslog-agenten att lyssna på port 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Var {0} ska ersättas med arbets ytans GUID.

        3. Starta om syslog-daemon`sudo service syslog-ng restart`
2. Starta om syslog-agenten med följande kommando:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Bekräfta att det inte finns några fel i agent loggen genom att köra det här kommandot:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>Steg 2: Vidarebefordra check punkts loggar till syslog-agenten

Konfigurera Check Point-enheten för att vidarebefordra syslog-meddelanden i CEF-format till din Azure-arbetsyta via syslog-agenten.

1. Gå till [Check Point log export](https://aka.ms/asi-syslog-checkpoint-forwarding).
2. Rulla ned till **grundläggande distribution** och följ anvisningarna för att konfigurera anslutningen med hjälp av följande rikt linjer:
   - Ställ in **syslog-porten** på **514** eller porten som du har angett för agenten.
     - Ersätt **namnet** och **mål serverns IP-adress** i CLI med syslog-AGENTens namn och IP-adress.
     - Ange formatet till **CEF**.
3. Om du använder version R 77.30 eller R 80.10 rullar du upp till **installationer** och följer anvisningarna för att installera en logg exportör för din version.
 
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

4. Se till att köra följande kommandon:
  
   - Om du använder syslog-ng kör du dessa kommandon (Observera att det startar om syslog-agenten):

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Starta om syslog-daemon:`sudo service syslog-ng restart`

   - Om du använder rsyslog kör du dessa kommandon (Observera att det startar om syslog-agenten): 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Starta om syslog-daemon:`sudo service rsyslog restart`




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter kontroll punkts apparater till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

