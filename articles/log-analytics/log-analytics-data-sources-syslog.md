---
title: Samla in och analysera Syslog-meddelanden i OMS Log Analytics | Microsoft Docs
description: Syslog är en händelse loggning protokoll som är gemensamma för Linux. Den här artikeln beskriver hur du konfigurerar samling Syslog-meddelanden i logganalys och information om poster skapas i OMS-databasen.
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
ms.author: magoedte;bwren
ms.component: na
ms.openlocfilehash: 1e7806e802f9b1dda16a9d5d477783663d03d416
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131794"
---
# <a name="syslog-data-sources-in-log-analytics"></a>Syslog-datakällor i logganalys
Syslog är en händelse loggning protokoll som är gemensamma för Linux.  Program skickar meddelanden som kan lagras på den lokala datorn eller levereras till en Syslog-insamlare.  När OMS-Agent för Linux installeras konfigurerar den lokala Syslog-daemon för att vidarebefordra meddelanden till agenten.  Agenten skickar sedan meddelandet till logganalys där motsvarande post har skapats i OMS-databasen.  

> [!NOTE]
> Log Analytics har stöd för meddelanden som skickas av rsyslog eller syslog-ng där rsyslog är standard daemon samling. Standard-daemon för syslog på version 5 Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog händelseinsamling. Samla in syslog-data från den här versionen av dessa distributioner av [rsyslog daemon](http://rsyslog.com) ska installeras och konfigureras för att ersätta sysklog.
>
>

![Syslog-samling](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Konfigurera Syslog
OMS-Agent för Linux endast samlar in händelser med verksamhet och allvarlighetsgraderna som anges i dess konfiguration.  Du kan konfigurera Syslog via Azure-portalen eller genom att hantera konfigurationsfiler på Linux-agenter.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurera Syslog i Azure-portalen
Konfigurera Syslog från den [Data-menyn i avancerade inställningar för Log Analytics](log-analytics-data-sources.md#configuring-data-sources).  Den här konfigurationen levereras till konfigurationsfilen på varje Linux-agenten.

Du kan lägga till en ny funktion genom att skriva dess namn och klicka på **+**.  Endast meddelanden med de valda allvarlighetsgraderna kommer att samlas in för varje anläggning.  Kontrollera allvarlighetsgraderna för viss-funktion som du vill samla in.  Du kan inte ange några ytterligare kriterier för att filtrera meddelanden.

![Konfigurera Syslog](media/log-analytics-data-sources-syslog/configure.png)

Som standard pushas alla konfigurationsändringar automatiskt till alla agenter.  Om du vill konfigurera Syslog manuellt på varje Linux-agenten och avmarkera sedan kryssrutan *Använd konfigurationen nedan för Mina Linux-datorer*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurera Syslog på Linux-agent
När den [OMS-agenten är installerad på en Linux-klient](log-analytics-linux-agents.md), installeras en standard syslog-konfigurationsfil som definierar anläggning och allvarlighetsgrad för meddelanden som samlas in.  Du kan ändra den här filen om du vill ändra konfigurationen.  Konfigurationsfilen är olika beroende på daemon för Syslog som klienten har installerats.

> [!NOTE]
> Om du redigerar syslog-konfigurationen, måste du starta om syslog-daemon för att ändringarna ska börja gälla.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurationsfilen för rsyslog finns på **/etc/rsyslog.d/95-omsagent.conf**.  Standardinnehållet visas nedan.  Detta samlar in syslog-meddelanden skickas från den lokala agenten för alla verksamhet med en nivå av varning eller högre.

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

Du kan ta bort en funktion genom att ta bort delen av konfigurationsfilen.  Du kan begränsa de allvarlighetsgraderna som samlas in för en viss funktion genom att ändra den lokal transaktion.  Om du vill begränsa användare möjlighet att meddelanden med en allvarlighetsgrad för fel eller högre ändrar du till exempel den raden i konfigurationsfilen på följande:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
Konfigurationsfilen för syslog-ng är plats **/etc/syslog-ng/syslog-ng.conf**.  Standardinnehållet visas nedan.  Detta samlar in syslog-meddelanden skickas från den lokala agenten för alla resurser och alla grader.   

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

Du kan ta bort en funktion genom att ta bort delen av konfigurationsfilen.  Du kan begränsa de allvarlighetsgraderna som samlas in för en viss funktion genom att ta bort dem från listan.  Om du vill begränsa användare möjlighet att bara aviseringen och viktiga meddelanden ändrar du till exempel det avsnittet av konfigurationsfilen till följande:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Samla in data från ytterligare Syslog-portar
OMS-agenten lyssnar efter Syslog-meddelanden på den lokala klienten på port 25224.  När agenten har installerats kan en syslog-standardkonfiguration appliceras och finns på följande plats:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Du kan ändra portnumret genom att skapa två konfigurationsfiler: en FluentD .config-fil och en rsyslog-eller-syslog-ng fil beroende på daemon för Syslog som du har installerat.  

* Konfigurationsfilen FluentD ska vara en ny fil i: `/etc/opt/microsoft/omsagent/conf/omsagent.d` och ersätta värdet i den **port** post med din anpassade portnummer.

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

* För rsyslog, bör du skapa en ny konfigurationsfil finns på: `/etc/rsyslog.d/` och Ersätt värdet % SYSLOG_PORT % med dina egna portnummer.  

    > [!NOTE]
    > Om du ändrar det här värdet i konfigurationsfilen `95-omsagent.conf`, skrivs den över när agenten använder en standardkonfiguration.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Syslog-ng config ska ändras genom att kopiera exempelkonfiguration nedan och lägga till anpassade ändrade inställningar i slutet av konfigurationsfilen syslog-ng.conf finns i `/etc/syslog-ng/`.  Gör **inte** använda Standardetiketten **% WORKSPACE_ID % _oms** eller **% WORKSPACE_ID_OMS**, definiera en anpassad etikett för att skilja på dina ändringar.  

    > [!NOTE]
    > Om du ändrar standardvärdena i konfigurationsfilen skrivs de över när agenten använder en standardkonfiguration.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

När du har slutfört ändringarna, Syslog och OMS-agent måste-tjänsten startas om för att säkerställa att konfigurationsändringarna börjar gälla.   

## <a name="syslog-record-properties"></a>Egenskaper för Syslog-post
Syslog-poster har en typ av **Syslog** och ha egenskaper i följande tabell.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Dator |Dator som händelsen samlats in från. |
| Anläggningen |Definierar en del av systemet som genererade meddelandet. |
| HostIP |IP-adress i systemet som skickar meddelandet. |
| Värdnamn |Namnet på system som skickar meddelandet. |
| SeverityLevel |Allvarlighetsgrad för händelsen. |
| SyslogMessage |Texten i meddelandet. |
| ProcessID |ID för den process som genererade meddelandet. |
| EventTime |Datum och tid då händelsen skapades. |

## <a name="log-queries-with-syslog-records"></a>Log-frågor med Syslog-poster
Följande tabell innehåller olika exempel på loggen frågor som hämtar Syslog-poster.

| Fråga | Beskrivning |
|:--- |:--- |
| Syslog |Alla systemloggar. |
| Syslog &#124; där SeverityLevel == ”error” |Alla Syslog-poster med allvarlighetsgraden fel. |
| Syslog &#124; sammanfatta AggregatedValue = count() per dator |Antal Syslog poster per dator. |
| Syslog &#124; sammanfatta AggregatedValue = count() av anläggningen |Antal Syslog poster efter anläggning. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att analysera data som samlas in från datakällor och lösningar.
* Använd [anpassade fält](log-analytics-custom-fields.md) att tolka data från syslog-poster till enskilda fält.
* [Konfigurera Linux-agenter](log-analytics-linux-agents.md) att samla in andra typer av data.
