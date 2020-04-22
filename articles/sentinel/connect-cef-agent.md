---
title: Distribuera logg vidarebefordraren för att ansluta CEF-data till Azure Sentinel | Microsoft-dokument
description: Lär dig hur du distribuerar agenten för att ansluta CEF-data till Azure Sentinel.
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731645"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Steg 1: Distribuera logg vidarebefordraren


I det här steget ska du ange och konfigurera Linux-datorn som vidarebefordrar loggarna från din säkerhetslösning till din Azure Sentinel-arbetsyta. Den här datorn kan vara en fysisk eller virtuell dator i din lokala miljö, en Virtuell Azure-dator eller en virtuell dator i ett annat moln. Med hjälp av länken kör du ett skript på den angivna datorn som utför följande uppgifter:
- Installerar Log Analytics-agenten för Linux (kallas även OMS-agenten) och konfigurerar den för följande ändamål:
    - lyssna efter CEF-meddelanden från den inbyggda Linux Syslog demonen på TCP-port 25226
    - skicka meddelandena säkert via TLS till din Azure Sentinel-arbetsyta, där de tolkas och berikas

- Konfigurerar den inbyggda Linux Syslog-demonen (rsyslog.d/syslog-ng) för följande ändamål:
    - lyssna efter Syslog-meddelanden från dina säkerhetslösningar på TCP-port 514
    - vidarebefordra endast de meddelanden som identifieras som FSE till Log Analytics-agenten på localhost med TCP-port 25226
 
## <a name="prerequisites"></a>Krav

- Du måste ha förhöjda behörigheter (sudo) på din utsedda Linux-dator.
- Du måste ha python installerat på Linux-datorn.<br>Använd `python -version` kommandot för att kontrollera.
- Linux-datorn får inte vara ansluten till några Azure-arbetsytor innan du installerar Log Analytics-agenten.

## <a name="run-the-deployment-script"></a>Kör distributionsskriptet
 
1. Klicka på **Datakopplingar på navigeringsmenyn**i Azure Sentinel . Klicka på panelen **Gemensamt händelseformat (CEF)** i listan över kopplingar och sedan knappen **Öppna kopplingssidan** längst ned till höger. 

1. Under **1.2 Installera FSE-insamlaren på Linux-datorn,** kopiera länken under **Kör följande skript för att installera och tillämpa FSE-insamlaren,** eller från texten nedan:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Kontrollera att du inte får några felmeddelanden eller varningsmeddelanden när skriptet körs.

Fortsätt till [STEG 2: Konfigurera säkerhetslösningen för att vidarebefordra CEF-meddelanden](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Distributionsskript förklaras

Följande är en kommando-för-kommando-beskrivning av åtgärderna i distributionsskriptet.

Välj en syslog daemon för att se rätt beskrivning.

# <a name="rsyslog-daemon"></a>[rsyslog demon](#tab/rsyslog)

1. **Hämtar och installerar Log Analytics-agenten:**

    - Hämtar installationsskriptet för Log Analytics (OMS) Linux-agent<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installerar Log Analytics-agenten<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurera Syslog-demonen:**

    1. Öppnar port 514 för TCP-kommunikation `/etc/rsyslog.conf`med hjälp av konfigurationsfilen syslog .

    1. Konfigurerar demonen för att vidarebefordra CEF-meddelanden till Log Analytics-agenten på TCP-port 25226 genom att infoga en särskild konfigurationsfil `security-config-omsagent.conf` i syslog daemon-katalogen `/etc/rsyslog.d/`.

        Innehållet i `security-config-omsagent.conf` filen:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **Starta om Syslog-demonen**

    `service rsyslog restart`

1. **Ställa in konfigurationen för Log Analytics-agenten för att lyssna på port 25226 och vidarebefordra CEF-meddelanden till Azure Sentinel**

    1. Hämtar konfigurationen från Log Analytics-agenten GitHub-databasen<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Startar om Log Analytics-agenten<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng demon](#tab/syslogng)

1. **Hämtar och installerar Log Analytics-agenten:**

    - Hämtar installationsskriptet för Log Analytics (OMS) Linux-agent<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installerar Log Analytics-agenten<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurera Syslog-demonen:**

    1. Öppnar port 514 för TCP-kommunikation `/etc/syslog-ng/syslog-ng.conf`med hjälp av konfigurationsfilen syslog .

    1. Konfigurerar demonen för att vidarebefordra CEF-meddelanden till Log Analytics-agenten på TCP-port 25226 genom att infoga en särskild konfigurationsfil `security-config-omsagent.conf` i syslog daemon-katalogen `/etc/syslog-ng/conf.d/`.

        Innehållet i `security-config-omsagent.conf` filen:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **Starta om Syslog-demonen**

    `service syslog-ng restart`

1. **Ställa in konfigurationen för Log Analytics-agenten för att lyssna på port 25226 och vidarebefordra CEF-meddelanden till Azure Sentinel**

    1. Hämtar konfigurationen från Log Analytics-agenten GitHub-databasen<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Startar om Log Analytics-agenten<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du distribuerar Log Analytics-agenten för att ansluta CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

