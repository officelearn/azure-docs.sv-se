---
title: Anslut Palo-nätverks data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter Palo-nätverks data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 5860e1b1b817985aafd95f6f63d39553489482b9
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679222"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>Anslut din Palo-nätverks-enhet

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel till valfri Palo-enhet med-nätverk genom att spara loggfilerna som syslog-CEF (common Error format). Integrationen med Azure Sentinel gör det möjligt för dig att enkelt köra analyser och frågor i logg fils data från Palo-nätverk. Mer information om hur Azure Sentinel matar in CEF-data finns i [ansluta CEF-enheter](connect-common-event-format.md).

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>Steg 1: Anslut din Palo-enhet med en agent

Om du vill ansluta din Palo-enhet till Azure Sentinel måste du distribuera en agent på en dedikerad dator (VM eller lokalt) för att stödja kommunikationen mellan-apparaten och Azure-kontroll. 

Alternativt kan du distribuera agenten manuellt på en befintlig virtuell Azure-dator, på en virtuell dator i ett annat moln eller på en lokal dator.

> [!NOTE]
> Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. 

Om du vill se ett nätverks diagram över båda alternativen, se [Anslut data källor](connect-data-sources.md#agent-options).


### <a name="deploy-the-agent"></a>Distribuera agenten 

1. Klicka på **data kopplingar** på Azure Sentinel-portalen och välj **Palo-nätverk** och **Öppna sedan kopplings sidan**. 

1. Under **Hämta och installera syslog**-agenten väljer du dator typ, antingen Azure eller lokalt. 
1. På skärmen **virtuella datorer** som öppnas väljer du den dator som du vill använda och klickar på **Anslut**.
1. Om du väljer **Hämta och installera agent för virtuella Azure Linux-datorer**väljer du datorn och klickar på **Anslut**. Om du väljer **Hämta och installera agent för virtuella datorer som inte använder Azure Linux**kör du skriptet under **Ladda ned och integrera agent för Linux**på skärmen **Direct agent** .      1. Ange om syslog-daemonen ska vara **rsyslog. d** eller **syslog-ng**på anslutnings skärmen under **Konfigurera och vidarebefordra syslog**. 
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


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Steg 2: Vidarebefordra Palo-nätverk loggar till syslog-agenten

Konfigurera Palo-nätverk för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten:
1.  Gå till [konfigurations guider för common Event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) och ladda ned PDF-filen för din installations typ. Följ alla instruktioner i guiden för att konfigurera Palo-enheter för att samla in CEF-händelser. 

1.  Gå till [Konfigurera Syslog-övervakning](https://aka.ms/asi-syslog-paloalto-forwarding) och följ steg 2 och 3 för att konfigurera CEF-händelse vidarebefordran från din Palo-enhets enhets nätverk till Azure Sentinel.

    1. Se till att ställa in **syslog-serverns format** på **BSD**.
    1. Se till att du ställer in **funktions numret** till samma värde som du angav i syslog-agenten.

       > [!NOTE]
       > Kopierings-och Inklistrings åtgärderna från PDF-filen kan ändra texten och infoga slumpmässiga tecken. Undvik detta genom att kopiera texten till en redigerare och ta bort eventuella tecken som kan bryta logg formatet innan du klistrar in det, som du kan se i det här exemplet.
 
        ![CEF text kopierings problem](./media/connect-cef/paloalto-text-prob1.png)

2. Om du vill använda det relevanta schemat i Log Analytics för Palo-nätverks händelser söker du efter **CommonSecurityLog**.

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
I det här dokumentet har du lärt dig hur du ansluter Palo-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

