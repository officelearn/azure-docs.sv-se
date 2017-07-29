---
title: "Ansluta säkerhetsprodukter till säkerhets- och granskningslösningen i Operations Management Suite (OMS) | Microsoft Docs"
description: "Det här dokumentet beskriver hur du ansluter dina säkerhetsprodukter till säkerhets- och granskningslösningen i Operations Management Suite med hjälp av Common Event Format."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 46eee484-e078-4bad-8c89-c88a3508f6aa
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: f0a512fb0684b1db25353bf4bbd35bdf2a07e1b2
ms.openlocfilehash: 5cb5c6b6cc047974013df59d615d4f094d8d60a4
ms.contentlocale: sv-se
ms.lasthandoff: 12/06/2016

---
# <a name="connecting-your-security-products-to-the-operations-management-suite-oms-security-and-audit-solution"></a>Ansluta säkerhetsprodukter till säkerhets- och granskningslösningen i Operations Management Suite (OMS) 
Det här dokumentet beskriver hur du ansluter dina säkerhetsprodukter till säkerhets- och granskningslösningen i OMS. Följande källor stöds:

- CEF-händelser (Common Event Format)
- Cisco ASA-händelser


## <a name="what-is-cef"></a>Vad är CEF?
Common Event Format (CEF) är ett branschstandardformat ovanpå Syslog-meddelanden som används av många säkerhetsleverantörer för att händelser ska kunna samverka mellan olika plattformar. Säkerhets- och granskningslösningen i OMS stöder datainmatning med hjälp av CEF så att du kan ansluta dina säkerhetsprodukter till OMS-säkerhetslösningen. 

Genom att ansluta din datakälla till OMS kan du dra nytta av följande funktioner som ingår i den här plattformen:

- Sökning och korrelation
- Granskning
- Varning
- Hotinformation
- Anmärkningsvärda problem

## <a name="collection-of-security-solution-logs"></a>Insamling av loggar för säkerhetslösningen

OMS-säkerhetslösningen har stöd för insamling av loggar via CEF över Syslogs- och [Cisco ASA](https://blogs.technet.microsoft.com/msoms/2016/08/25/add-your-cisco-asa-logs-to-oms-security/)-loggar. I det här exemplet är källan (datorn som genererar loggarna) en Linux-dator som kör syslog-ng-daemon och målet är OMS-säkerhetslösningen. Du måste förbereda Linux-datorn genom att utföra följande uppgifter:

- Ladda ned OMS-agenten för Linux, version 1.2.0-25 eller senare.
- Följ stegen i **snabbinstallationsguiden** från [den här artikeln](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux) för att installera och publicera agenten på din arbetsyta.

Normalt installeras agenten på en annan dator än den där loggarna genereras. När loggarna ska vidarebefordras till agentdatorn krävs vanligtvis följande steg:

- Konfigurera loggningsprodukten eller loggningsdatorn så att den vidarebefordrar nödvändiga händelser till syslog-daemon (rsyslog eller syslog-ng) på agentdatorn.
- Aktivera syslog-daemon på agentdatorn så att meddelanden kan tas emot från ett fjärrsystem.

På agentdatorn måste händelserna skickas från syslog-daemon till den lokala UDP-porten 25226. Agenten lyssnar efter inkommande händelser på den här porten. Följande är ett exempel på en konfiguration som skickar alla händelser från det lokala systemet till agenten (du kan ändra konfigurationen så att den passar dina lokala inställningar):

1. Öppna terminalfönstret och gå till katalogen */etc/syslog-ng /* 
2. Skapa en ny fil *security-config-omsagent.conf* och lägg till följande innehåll: OMS_facility = local4
    
    filter f_local4_oms { facility(local4); };

    destination security_oms { tcp("127.0.0.1" port(25226)); };

    log { source(src); filter(f_local4_oms); destination(security_oms); };
    
3. Hämta filen *security_events.conf* och placera den i */etc/opt/microsoft/omsagent/conf/omsagent.d/* på OMS-agentdatorn.
4. Skriv kommandot nedan för att starta om syslog-daemon:  *För syslog-ng kör du:*
    
    ```
    sudo service rsyslog restart
    ```

    *För rsyslog kör du:*
    
    ```
    /etc/init.d/syslog-ng restart
    ```
5. Skriv kommandot nedan för att starta om OMS-agenten:

    *För syslog-ng kör du:*
    
    ```
    sudo service omsagent restart
    ```

    *För rsyslog kör du:*
    
    ```
    systemctl restart omsagent
    ```
6. Skriv kommandot nedan och granska resultatet för att bekräfta att det inte finns några fel i OMS-agentloggen:

    ``` 
    tail /var/opt/microsoft/omsagent/log/omsagent.log
    ```

## <a name="reviewing-collected-security-events"></a>Gå igenom de insamlade säkerhetshändelserna

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

När konfigurationen är klar börjar säkerhetshändelsen att matas in av OMS-säkerhetslösningen. Du kan visualisera dessa händelser genom att öppna Loggsökning, skriva kommandot *Type=CommonSecurityLog* i sökfältet och trycka på Retur. Följande exempel visar resultatet av det här kommandot. Observera att i det här fallet har säkerhetsloggar från flera leverantörer redan matats in i OMS-säkerhetslösningen:
   
![Utvärdering av säkerhetsbaslinjen i säkerhets- och granskningslösningen i OMS](./media/oms-security-connect-products/oms-security-connect-products-fig1.png)

Du kan förfina sökningen för en enskild leverantör, till exempel för att visualisera Cisco-onlineloggar, genom att skriva: *Type=CommonSecurityLog DeviceVendor=Cisco*. ”CommonSecurityLog” har fördefinierade fält för ett eventuellt CEF-huvud, inklusive de grundläggande tilläggen, medan andra tillägg, oavsett om det rör sig om ett ”anpassat tillägg” eller inte, infogas i fältet ”AdditionalExtensions”. Du kan använda funktionen för anpassade fält för att hämta dedikerade fält. 

### <a name="accessing-computers-missing-baseline-assessment"></a>Kontrollera datorer utan utvärdering av säkerhetsbaslinje
OMS stöder baslinjeprofilen för domänmedlemmar i Windows Server 2008 R2 upp till Windows Server 2012 R2. Baslinjen för Windows Server 2016 är inte klar än och läggs till så fort den publiceras. Alla andra operativsystem som genomsöks med Utvärdering av säkerhetsbaslinje i säkerhets- och granskningslösningen i OMS visas i avsnittet **Datorer utan utvärdering av säkerhetsbaslinje**.

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig hur du ansluter din CEF-lösning till OMS. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)


