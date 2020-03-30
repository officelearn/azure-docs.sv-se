---
title: Samla in och analysera Syslog-meddelanden i Azure Monitor | Microsoft-dokument
description: Syslog är ett händelseloggningsprotokoll som är gemensamt för Linux. I den här artikeln beskrivs hur du konfigurerar insamling av Syslog-meddelanden i Logganalys och information om de poster de skapar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274728"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog-datakällor i Azure Monitor
Syslog är ett händelseloggningsprotokoll som är gemensamt för Linux. Program skickar meddelanden som kan lagras på den lokala datorn eller levereras till en Syslog-insamlare. När Log Analytics-agenten för Linux är installerad konfigureras den lokala Syslog-demonen för att vidarebefordra meddelanden till agenten. Agenten skickar sedan meddelandet till Azure Monitor där en motsvarande post skapas.  

> [!NOTE]
> Azure Monitor stöder insamling av meddelanden som skickas av rsyslog eller syslog-ng, där rsyslog är standarddemonen. Standardsystemlog daemon på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux version (sysklog) stöds inte för syslog händelsesamling. För att samla in syslog data från den här versionen av dessa distributioner, bör [rsyslog demon](http://rsyslog.com) installeras och konfigureras för att ersätta sysklog.
>
>

![Syslog-samling](media/data-sources-syslog/overview.png)

Följande anläggningar stöds med Syslog-samlaren:

* Kern
* användare
* e-post
* Daemon
* Auth
* syslog
* Lpr
* news
* Uucp
* Cron
* authpriv (på andra)
* ftp
* local0-local7

Konfigurera [en datakälla för anpassade loggar](data-sources-custom-logs.md) i Azure Monitor för alla andra anläggningar.
 
## <a name="configuring-syslog"></a>Konfigurera Syslog
Log Analytics-agenten för Linux samlar bara in händelser med de anläggningar och allvarlighetsgrader som anges i dess konfiguration. Du kan konfigurera Syslog via Azure-portalen eller genom att hantera konfigurationsfiler på dina Linux-agenter.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurera Syslog i Azure-portalen
Konfigurera Syslog från [datamenyn i Avancerade inställningar](agent-data-sources.md#configuring-data-sources). Den här konfigurationen levereras till konfigurationsfilen på varje Linux-agent.

Du kan lägga till en ny anläggning genom att först välja alternativet **Använd nedan för konfigurationen på mina datorer** och sedan skriva in dess namn och klicka på . **+** För varje anläggning samlas endast meddelanden med de valda allvarlighetsgraderna in.  Kontrollera allvarlighetsgraden för den aktuella anläggningen som du vill samla in. Du kan inte ange några ytterligare villkor för att filtrera meddelanden.

![Konfigurera Syslog](media/data-sources-syslog/configure.png)

Som standard skjuts alla konfigurationsändringar automatiskt till alla agenter. Om du vill konfigurera Syslog manuellt på varje Linux-agent avmarkerar du rutan *Använd under konfigurationen på mina datorer*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurera Syslog på Linux-agent
När [Log Analytics-agenten är installerad på en Linux-klient](../../azure-monitor/learn/quick-collect-linux-computer.md)installeras en standardkonfigurationsfil för syslog som definierar möjligheten och allvarlighetsgraden för de meddelanden som samlas in. Du kan ändra den här filen om du vill ändra konfigurationen. Konfigurationsfilen är olika beroende på den Syslog-demon som klienten har installerat.

> [!NOTE]
> Om du redigerar syslog-konfigurationen måste du starta om syslog-demonen för att ändringarna ska börja gälla.
>
>

#### <a name="rsyslog"></a>Rsyslog
Konfigurationsfilen för rsyslog finns på **/etc/rsyslog.d/95-omsagent.conf**. Dess standardinnehåll visas nedan. Detta samlar syslog meddelanden som skickas från den lokala agenten för alla anläggningar med en varningsnivå eller högre.

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

Du kan ta bort en anläggning genom att ta bort dess avsnitt i konfigurationsfilen. Du kan begränsa de allvarlighetsgrader som samlas in för en viss anläggning genom att ändra den facilitetens post. Om du till exempel vill begränsa användarfunktionen till meddelanden med fel allvarlighetsgrad eller högre ändrar du den raden i konfigurationsfilen till följande:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
Konfigurationsfilen för syslog-ng är plats på **/etc/syslog-ng/syslog-ng.conf**.  Dess standardinnehåll visas nedan. Detta samlar syslog meddelanden som skickas från den lokala agenten för alla anläggningar och alla allvarlighetsgrader.   

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

Du kan ta bort en anläggning genom att ta bort dess avsnitt i konfigurationsfilen. Du kan begränsa de allvarlighetsgrader som samlas in för en viss anläggning genom att ta bort dem från listan.  Om du till exempel vill begränsa användarfunktionen till att bara avisera och kritiska meddelanden ändrar du det avsnittet i konfigurationsfilen till följande:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Samla in data från ytterligare Syslog-portar
Log Analytics-agenten lyssnar efter Syslog-meddelanden på den lokala klienten i port 25224.  När agenten installeras tillämpas en standardkonfiguration för syslog och hittas på följande plats:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

Du kan ändra portnumret genom att skapa två konfigurationsfiler: en FluentD-config-fil och en rsyslog-or-syslog-ng-fil beroende på vilken Syslog-demon du har installerat.  

* Filen FluentD-konfiguration ska vara en ny `/etc/opt/microsoft/omsagent/conf/omsagent.d` fil som finns i: och ersätta värdet i **portposten** med ditt anpassade portnummer.

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

* För rsyslog bör du skapa en ny `/etc/rsyslog.d/` konfigurationsfil som finns i: och ersätta värdet %SYSLOG_PORT% med ditt anpassade portnummer.  

    > [!NOTE]
    > Om du ändrar det `95-omsagent.conf`här värdet i konfigurationsfilen skrivs det över när agenten tillämpar en standardkonfiguration.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* Syslog-ng config bör ändras genom att kopiera exempelkonfigurationen som visas nedan och lägga till anpassade ändrade inställningar `/etc/syslog-ng/`i slutet av konfigurationsfilen syslog-ng.conf i . Använd **inte** standardetiketten **%WORKSPACE_ID%_oms** eller **%WORKSPACE_ID_OMS**, definiera en anpassad etikett för att skilja ändringarna.  

    > [!NOTE]
    > Om du ändrar standardvärdena i konfigurationsfilen skrivs de över när agenten tillämpar en standardkonfiguration.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

När ändringarna har slutförts måste Syslog- och Log Analytics-agenttjänsten startas om för att säkerställa att konfigurationsändringarna börjar gälla.   

## <a name="syslog-record-properties"></a>Egenskaper för Syslog-post
Syslog-poster har en typ av **Syslog** och har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Dator som händelsen samlades in från. |
| Anläggning |Definierar den del av systemet som genererade meddelandet. |
| Hostip |IP-adressen för det system som skickar meddelandet. |
| Värdnamn |Namnet på det system som skickar meddelandet. |
| AllvarlighetsgradNivå |Allvarlighetsgrad för händelsen. |
| SyslogMessage |Texten i meddelandet. |
| ProcessID |ID för processen som genererade meddelandet. |
| EventTime (EventTime) |Datum och tid då händelsen genererades. |

## <a name="log-queries-with-syslog-records"></a>Logga frågor med Syslog-poster
Följande tabell innehåller olika exempel på loggfrågor som hämtar Syslog-poster.

| Söka i data | Beskrivning |
|:--- |:--- |
| Syslog |Alla Syslogs. |
| Syslog &#124; där allvarlighetsgradLevel == "fel" |Alla Syslog-poster med fel allvarlighetsgrad. |
| Syslog &#124; sammanfatta AggregatedValue = count() efter dator |Antal Syslog-poster efter dator. |
| Syslog &#124; sammanfatta AggregatedValue = count() efter anläggning |Antal Syslog-poster efter anläggning. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../../azure-monitor/log-query/log-query-overview.md) för att analysera data som samlas in från datakällor och lösningar.
* Använd [anpassade fält](../../azure-monitor/platform/custom-fields.md) för att tolka data från syslog-poster i enskilda fält.
* [Konfigurera Linux-agenter](../../azure-monitor/learn/quick-collect-linux-computer.md) för att samla in andra typer av data.
