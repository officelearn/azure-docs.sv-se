---
title: Samla in och analysera Syslog-meddelanden i OMS Log Analytics | Microsoft Docs
description: Syslog är ett protokoll för loggning av händelse som är gemensamma för Linux. Den här artikeln beskriver hur du konfigurerar insamling av Syslog-meddelanden i Log Analytics och information för de poster som de skapar i OMS-databasen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 132935371a1b4f52892ae2fb956407079906e79a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004910"
---
# <a name="syslog-data-sources-in-log-analytics"></a>Syslog-datakällor i Log Analytics
Syslog är ett protokoll för loggning av händelse som är gemensamma för Linux.  Program skickar meddelanden som kan lagras på den lokala datorn eller levereras till en Syslog-insamlare.  När OMS-agenten för Linux installeras konfigurerar den lokala Syslog-daemon för att vidarebefordra meddelanden till agenten.  Agenten skickar sedan meddelandet till Log Analytics där en motsvarande post skapas i OMS-databasen.  

> [!NOTE]
> Log Analytics har stöd för insamling av meddelanden som skickas av rsyslog eller syslog-ng, där rsyslog är standard-daemon. Standard syslog-daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog-händelseinsamling. Samla in syslog-data från den här versionen av dessa distributioner kan de [rsyslog-daemon](http://rsyslog.com) ska installeras och konfigureras för att ersätta sysklog.
>
>

![Sysloginsamling](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Konfigurera Syslog
OMS-agenten för Linux endast samlar in händelser med anläggningar och allvarlighetsgrader som anges i dess konfiguration.  Du kan konfigurera Syslog via Azure portal eller genom att hantera konfigurationsfiler på Linux-agenter.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurera Syslog i Azure portal
Konfigurera Syslog från den [Data-menyn i Log Analytics avancerade inställningar](log-analytics-data-sources.md#configuring-data-sources).  Den här konfigurationen levereras till konfigurationsfilen på varje Linux-agent.

Du kan lägga till en ny resurs genom att skriva dess namn och klicka på **+**.  Endast meddelanden med de valda allvarlighetsgraderna kommer att samlas in för varje resurs.  Kontrollera allvarlighetsgraderna för en viss tjänst du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera meddelanden.

![Konfigurera Syslog](media/log-analytics-data-sources-syslog/configure.png)

Som standard skickas automatiskt alla konfigurationsändringar till alla agenter.  Om du vill konfigurera Syslog manuellt på varje Linux-agent och avmarkerar sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurera Syslog på Linux-agenten
När den [OMS-agenten är installerad på en Linux-klient](log-analytics-quick-collect-linux-computer.md), installeras en standard syslog-konfigurationsfil som definierar anläggning och allvarlighetsgraden för de meddelanden som samlas in.  Du kan ändra den här filen om du vill ändra konfigurationen.  Konfigurationsfilen är olika beroende på Syslog-daemon som klienten har installerats.

> [!NOTE]
> Om du redigerar systemloggkonfigurationen, måste du starta om syslog-daemon för att ändringarna ska börja gälla.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurationsfilen för rsyslog finns på **/etc/rsyslog.d/95-omsagent.conf**.  Standardinnehållet visas nedan.  Detta samlar in syslog-meddelanden som skickas från den lokala agenten för alla anläggningar med en nivå med varning eller högre.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Du kan ta bort en resurs genom att ta bort delen av konfigurationsfilen.  Du kan begränsa allvarlighetsgraderna som samlas in för en viss anläggning genom att ändra den anläggning posten.  Till exempel om du vill begränsa användare möjlighet att meddelanden med en allvarlighetsgrad för fel eller senare ändrar du raden av konfigurationsfilen så här:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
Konfigurationsfilen för syslog-ng är plats **/etc/syslog-ng/syslog-ng.conf**.  Standardinnehållet visas nedan.  Detta samlar in syslog-meddelanden som skickas från den lokala agenten för alla anläggningar och alla allvarlighetsgrader.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Du kan ta bort en resurs genom att ta bort delen av konfigurationsfilen.  Du kan begränsa allvarlighetsgraderna som samlas in för en viss anläggning genom att ta bort dem från listan.  Till exempel för att begränsa användare möjlighet att bara aviseringen och viktiga meddelanden, ändrar du avsnittet om att konfigurationsfilen så här:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Samla in data från ytterligare Syslog-portar
OMS-agenten lyssnar efter Syslog-meddelanden på den lokala klienten på port 25224.  När agenten är installerad, är en standardkonfiguration för syslog tillämpas och finns på följande plats:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Du kan ändra portnumret genom att skapa två konfigurationsfiler: en FluentD-konfigurationsfilen och en rsyslog-eller-syslog-ng-fil beroende på Syslog-daemon som du har installerat.  

* FluentD-konfigurationsfilen ska vara en ny fil som finns på: `/etc/opt/microsoft/omsagent/conf/omsagent.d` och Ersätt värdet i den **port** post med din anpassat portnummer.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* För rsyslog, bör du skapa en ny konfigurationsfil som finns på: `/etc/rsyslog.d/` och Ersätt värdet % SYSLOG_PORT % med din anpassat portnummer.  

    > [!NOTE]
    > Om du ändrar detta värde i konfigurationsfilen `95-omsagent.conf`, den kommer att skrivas över när agenten gäller en standardkonfiguration.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Syslog-ng-config ska ändras genom att kopiera exempelkonfiguration som visas nedan och lägga till anpassade ändrade inställningar i slutet av konfigurationsfilen syslog-ng.conf finns i `/etc/syslog-ng/`.  Gör **inte** använda Standardetiketten **% WORKSPACE_ID % _oms** eller **% WORKSPACE_ID_OMS**, definiera en anpassad etikett för att skilja dina ändringar.  

    > [!NOTE]
    > Om du ändrar standardvärdena i konfigurationsfilen, de kommer att skrivas över när agenten gäller en standardkonfiguration.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

Tjänsten måste startas om för att se till att konfigurationsändringarna träder i kraft när du har slutfört ändringarna, Syslog och OMS-agenten.   

## <a name="syslog-record-properties"></a>Egenskaper för Syslog-post
Syslog-poster har en typ av **Syslog** och har egenskaperna i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Dator |Datorn där händelsen har samlats in från. |
| Anläggning |Definierar en del av systemet som genererade meddelandet. |
| HostIP |IP-adressen för det system som meddelandet skickades. |
| Värdnamn |Namnet på system som meddelandet skickades. |
| SeverityLevel |Allvarlighetsgrad för händelsen. |
| SyslogMessage |Meddelandets text. |
| ProcessID |ID för den process som genererade meddelandet. |
| eventTime |Datum och tid då händelsen skapades. |

## <a name="log-queries-with-syslog-records"></a>Loggfrågor med Syslog-poster
I följande tabell innehåller olika exempel på loggfrågor som hämtar Syslog-poster.

| Söka i data | Beskrivning |
|:--- |:--- |
| Syslog |Alla Syslog-poster. |
| Syslog &#124; där SeverityLevel == ”error” |Alla Syslog-poster med allvarlighetsgrad för fel. |
| Syslog &#124; sammanfatta AggregatedValue = antal() efter dator |Räkna Syslog-poster per dator. |
| Syslog &#124; sammanfatta AggregatedValue = antal() efter lokal |Räkna Syslog-poster efter anläggning. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](log-analytics-queries.md) att analysera data som samlas in från datakällor och lösningar.
* Använd [anpassade fält](log-analytics-custom-fields.md) att parsa data från syslog-poster i enskilda fält.
* [Konfigurera Linux-agenter](log-analytics-quick-collect-linux-computer.md) att samla in andra typer av data.
