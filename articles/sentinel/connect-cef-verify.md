---
title: Verifiera anslutningen till Azure Sentinel | Microsoft Docs
description: Verifiera anslutningen till din säkerhetslösning för att se till att CEF-meddelanden vidarebefordras till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 07a6b84569fe0356267440e38b31ac738b2659d6
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260839"
---
# <a name="step-3-validate-connectivity"></a>STEG 3: verifiera anslutningen

När du har distribuerat din logg vidarebefordrare (i steg 1) och konfigurerat din säkerhetslösning för att skicka CEF-meddelanden (i steg 2) följer du dessa anvisningar för att kontrol lera anslutningen mellan din säkerhets lösning och Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Du måste ha förhöjd behörighet (sudo) på logg vidarebefordraren.

- Du måste ha python installerat på logg vidarebefordraren.<br>
Använd `python –version` kommandot för att kontrol lera.

## <a name="how-to-validate-connectivity"></a>Så här verifierar du anslutningen

1. Öppna **loggar**på navigerings menyn i Azure Sentinel. Kör en fråga med **CommonSecurityLog** -schemat för att se om du får loggar från säkerhets lösningen.<br>
Tänk på att det kan ta ungefär 20 minuter tills loggarna börjar visas i **Log Analytics**. 

1. Om du inte ser några resultat från frågan kontrollerar du att händelser genereras från din säkerhetslösning, eller försöker att generera några, och kontrollerar att de vidarebefordras till den syslog forwarder-dator som du har angett. 

1. Kör följande skript på logg vidarebefordraren för att kontrol lera anslutningen mellan säkerhets lösningen, logg vidarebefordraren och Azure Sentinel. Det här skriptet kontrollerar att daemon lyssnar på rätt portar, att vidarebefordran har kon figurer ATS korrekt och att ingenting blockerar kommunikationen mellan daemonen och den Log Analytics agenten. Den skickar även de blå meddelandena "TestCommonEventFormat" för att kontrol lera anslutning från slut punkt till slut punkt. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Förklaring av validerings skript

Verifierings skriptet utför följande kontroller:

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. Kontrollerar att filen<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    finns och är giltig.

1. Kontrollerar att filen innehåller följande text:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Kontrollerar om det finns några säkerhets förbättringar på datorn som kan blockera nätverks trafik (till exempel en värd brand vägg).

1. Kontrollerar att syslog-daemonen (rsyslog) är korrekt konfigurerad för att skicka meddelanden som identifieras som CEF (med ett regex) till Log Analytics agent på TCP-port 25226:

    - Konfigurations fil:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF"|"ASA"
            *.* @@127.0.0.1:25226

1. Kontrollerar att syslog-daemon tar emot data på port 514

1. Kontrollerar att de nödvändiga anslutningarna har upprättats: TCP 514 för att ta emot data, TCP 25226 för intern kommunikation mellan syslog-daemon och Log Analytics agent

1. Skickar blå data till port 514 på localhost. Dessa data bör vara synliga på Azure Sentinel-arbetsytan genom att köra följande fråga:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng-daemon](#tab/syslogng)

1. Kontrollerar att filen<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    finns och är giltig.

1. Kontrollerar att filen innehåller följande text:

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. Kontrollerar om det finns några säkerhets förbättringar på datorn som kan blockera nätverks trafik (till exempel en värd brand vägg).

1. Kontrollerar att syslog-demon (syslog-ng) är korrekt konfigurerad för att skicka meddelanden som identifieras som CEF (med ett regex) till Log Analytics agent på TCP-port 25226:

    - Konfigurations fil:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Kontrollerar att syslog-daemon tar emot data på port 514

1. Kontrollerar att de nödvändiga anslutningarna har upprättats: TCP 514 för att ta emot data, TCP 25226 för intern kommunikation mellan syslog-daemon och Log Analytics agent

1. Skickar blå data till port 514 på localhost. Dessa data bör vara synliga på Azure Sentinel-arbetsytan genom att köra följande fråga:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

