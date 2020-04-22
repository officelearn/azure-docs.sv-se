---
title: Validera anslutning till Azure Sentinel | Microsoft-dokument
description: Verifiera anslutningen av din säkerhetslösning för att se till att FSE-meddelanden vidarebefordras till Azure Sentinel.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731819"
---
# <a name="step-3-validate-connectivity"></a>STEG 3: Validera anslutningen

När du har distribuerat logg vidarebefordraren (i steg 1) och konfigurerat din säkerhetslösning för att skicka den FSE-meddelanden (i steg 2), följer du dessa instruktioner för att verifiera anslutningen mellan din säkerhetslösning och Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Du måste ha förhöjda behörigheter (sudo) på din logg vidarebefordrare maskin.

- Du måste ha Python installerat på logg vidarebefordrarens dator.<br>
Använd `python –version` kommandot för att kontrollera.

## <a name="how-to-validate-connectivity"></a>Så här validerar du anslutningen

1. Öppna Loggar på **navigeringsmenyn i**Azure Sentinel . Kör en fråga med **CommonSecurityLog-schemat** för att se om du tar emot loggar från säkerhetslösningen.<br>
Tänk på att det kan ta cirka 20 minuter innan loggarna börjar visas i **Log Analytics**. 

1. Om du inte ser några resultat från frågan kontrollerar du att händelser genereras från säkerhetslösningen eller försöker generera vissa och verifiera att de vidarebefordras till syslog-vidarebefordrarens dator som du har angett. 

1. Kör följande skript på logg vidarebefordraren för att kontrollera anslutningen mellan din säkerhetslösning, logg vidarebefordraren och Azure Sentinel. Det här skriptet kontrollerar att demonen lyssnar på rätt portar, att vidarebefordran är korrekt konfigurerad och att ingenting blockerar kommunikationen mellan demonen och Log Analytics-agenten. Det skickar också falska meddelanden "TestCommonEventFormat" för att kontrollera end-to-end-anslutning. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Valideringsskript förklaras

Valideringsskriptet utför följande kontroller:

# <a name="rsyslog-daemon"></a>[rsyslog demon](#tab/rsyslog)

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

1. Kontrollerar om det finns några säkerhetsförbättringar på datorn som kan blockera nätverkstrafik (till exempel en värdbrandvägg).

1. Kontrollerar att syslog-demonen (rsyslog) är korrekt konfigurerad för att skicka meddelanden som den identifierar som FSE (med hjälp av en regex) till Log Analytics-agenten i TCP-port 25226:

    - Konfigurationsfil:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Kontrollerar att syslog-demonen tar emot data om port 514

1. Kontrollerar att nödvändiga anslutningar har upprättats: tcp 514 för att ta emot data, tcp 25226 för intern kommunikation mellan syslog daemon och Log Analytics-agenten

1. Skickar MOCK-data till port 514 på localhost. Dessa data bör kunna observeras i Azure Sentinel-arbetsytan genom att köra följande fråga:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng demon](#tab/syslogng)

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

1. Kontrollerar om det finns några säkerhetsförbättringar på datorn som kan blockera nätverkstrafik (till exempel en värdbrandvägg).

1. Kontrollerar att syslog-demonen (syslog-ng) är korrekt konfigurerad för att skicka meddelanden som den identifierar som FSE (med hjälp av en regex) till Log Analytics-agenten i TCP-port 25226:

    - Konfigurationsfil:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Kontrollerar att syslog-demonen tar emot data om port 514

1. Kontrollerar att nödvändiga anslutningar har upprättats: tcp 514 för att ta emot data, tcp 25226 för intern kommunikation mellan syslog daemon och Log Analytics-agenten

1. Skickar MOCK-data till port 514 på localhost. Dessa data bör kunna observeras i Azure Sentinel-arbetsytan genom att köra följande fråga:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

