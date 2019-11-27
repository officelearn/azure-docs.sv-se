---
title: Felsöka Azure Log Analytics-agenten för Linux | Microsoft Docs
description: Beskriv symptom, orsaker och lösningar för de vanligaste problemen med Log Analytics-agenten för Linux i Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: ca0fcd3b68722d44fc285b2dff52b560c591d0be
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306544"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Så här felsöker du problem med Log Analytics-agenten för Linux 

Den här artikeln innehåller fel söknings fel som kan uppstå i Log Analytics-agenten för Linux i Azure Monitor och ger förslag på möjliga lösningar för att lösa dem.

Om ingen av de här stegen fungerar för dig finns också stöd för följande kanaler:

* Kunder med Premier Support-förmåner kan öppna en support förfrågan med [Premier](https://premier.microsoft.com/).
* Kunder med support avtal för Azure kan öppna en support förfrågan [i Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnostisera OMI-problem med [fel söknings guiden för OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Besök sidan Log Analytics feedback och granska skickade idéer och buggar [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) eller skicka en ny fil.  

## <a name="important-log-locations-and-log-collector-tool"></a>Viktiga loggfilernas placering och Logginsamlaren verktyg

 Fil | Sökväg
 ---- | -----
 Log Analytics-agenten för Linux-loggfil | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log agentkonfigurationsfilen för log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Vi rekommenderar att du använder våra insamlaren loggverktyget för att hämta viktiga loggar för felsökning eller innan du skickar in ett GitHub-ärende. Du kan läsa mer om verktyget och hur du kör det [här](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Viktiga konfigurationsfiler

 Kategori | Filsökväg
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` eller `/etc/rsyslog.conf` eller `/etc/rsyslog.d/95-omsagent.conf`
 Prestanda, Nagios, Zabbix, Log Analytics-utdata och allmän agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Ytterligare konfigurationer | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Att redigera konfigurationsfiler för prestanda räknare och syslog skrivs över om samlingen konfigureras från [Data-menyn Log Analytics avancerade inställningar](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) i Azure Portal för din arbets yta. Om du vill inaktivera konfigurationen för alla agenter inaktiverar du samling från Log Analytics **Avancerade inställningar** eller för en enskild agent kör följande:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Felkoder för klientinstallationer

| Felkod | Betydelse |
| --- | --- |
| NOT_DEFINED | Eftersom nödvändiga beroenden är installerade, installeras inte auoms auditd-plugin-programmet | Installation av auoms misslyckades, installera paketet auditd. |
| 2 | Ogiltigt alternativ har angetts för shell-paket. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning |
| 3 | Inget alternativ som angetts för shell-paket. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning. |
| 4 | Ogiltig pakettyp eller ogiltiga proxyinställningar. omsagent-*rpm*. sh-paket kan bara installeras på RPM-baserade system och omsagent-*deb*. sh-paket kan bara installeras på Debian-baserade system. Vi rekommenderar att du använder det universella installations programmet från den [senaste versionen](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Se även till att verifiera proxyinställningarna. |
| 5 | Shell-paket måste köras som rot eller så uppstod 403-fel returneras under publiceringen. Kör kommandot med hjälp av `sudo`. |
| 6 | Ogiltig paket arkitektur eller så uppstod fel 200 vid registrering. omsagent-*x64.sh-paket kan bara installeras på 64-bitars system, och omsagent-x86.sh-* paket kan bara installeras på 32-bitars system. Hämta rätt paket för din arkitektur från den [senaste versionen](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Det gick inte att installera OMS-paketet. Titta igenom kommandoutdata till rot-felet. |
| 19 | Det gick inte att installera OMI-paketet. Titta igenom kommandoutdata till rot-felet. |
| 20 | Det gick inte att installera SCX-paketet. Titta igenom kommandoutdata till rot-felet. |
| 21 | Det gick inte att installera providern kits. Titta igenom kommandoutdata till rot-felet. |
| 22 | Det gick inte att installera paketet tillsammans. Titta igenom kommandoutdata för rot-fel |
| 23 | SCX- eller OMI-paketet redan installerad. Använd `--upgrade` i stället för `--install` för att installera Shell-paketet. |
| 30 | Intern paket-fel. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 55 | Openssl-versionen stöds inte eller så går det inte att ansluta till Azure Monitor eller så är dpkg låst eller så saknas ett sväng program. |
| 61 | Saknas Python ctypes-bibliotek. Installera Python-bibliotek för ctypes eller paket (python-ctypes). |
| 62 | Saknas tar program, installera tar. |
| 63 | Program som saknas sed, installera sed. |
| 64 | Saknas curl-program, installera curl. |
| 65 | Saknas gpg-program, installera gpg. |

## <a name="onboarding-error-codes"></a>Onboarding-felkoder

| Felkod | Betydelse |
| --- | --- |
| 2 | Ogiltigt alternativ anges i omsadmin-skriptet. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 3 | Ogiltig konfiguration som anges i omsadmin-skriptet. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 4 | Ogiltig proxy som anges i omsadmin-skriptet. Verifiera proxyn och se vår [dokumentation för att använda en HTTP-proxy](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 HTTP-fel togs emot från Azure Monitor. Visa fullständiga utdata av skriptet omsadmin för information. |
| 6 | HTTP-fel som inte är 200 togs emot från Azure Monitor. Visa fullständiga utdata av skriptet omsadmin för information. |
| 7 | Det går inte att ansluta till Azure Monitor. Visa fullständiga utdata av skriptet omsadmin för information. |
| 8 | Fel vid registrering till Log Analytics-arbetsyta. Visa fullständiga utdata av skriptet omsadmin för information. |
| 30 | Internt skriptfel. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 31 | Fel vid generering agent-ID. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 32 | Fel vid generering av certifikat. Visa fullständiga utdata av skriptet omsadmin för information. |
| 33 | Fel vid generering av metaconfiguration för omsconfig. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 34 | Metaconfiguration generation skript finns inte. Försök att registrera igen med `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning
### <a name="oms-output-plugin-debug"></a>Felsökning för OMS utdata-plugin-programmet
 FluentD gör för plugin-programmet-specifika loggningsnivåer så att du kan ange olika loggningsnivåerna för indata och utdata. Om du vill ange en annan loggnings nivå för OMS-utdata redigerar du den allmänna agent konfigurationen på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 I OMS-utdata-plugin-programmet, innan konfigurations filen har slutförts, ändrar du `log_level`-egenskapen från `info` till `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Med fel söknings loggning kan du se batch-överföringar till Azure Monitor avgränsade med typ, antal data objekt och tids åtgång för att skicka:

*Exempel på fel söknings aktive rad logg:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Utförliga utdata
I stället för att använda OMS-utdata-plugin-programmet kan du också mata ut data objekt direkt till `stdout`, som visas i logg filen Log Analytics agent för Linux.

I Log Analytics allmän agent konfigurations fil på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, kommentera ut plugin-programmet för OMS-utdata genom att lägga till en `#` framför varje rad:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Under plugin-programmet för utdata tar du bort kommentaren till följande avsnitt genom att ta bort `#` framför varje rad:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: det går inte att ansluta via proxy till Azure Monitor

### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som anges under publiceringen var felaktig
* Azure Monitor-och Azure Automations tjänstens slut punkter är inte vit listas i ditt data Center 

### <a name="resolution"></a>Lösning
1. Återaktivering till Azure Monitor med Log Analytics-agenten för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. Den tillåter utförlig utmatning av agenten som ansluter via proxyservern till Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Granska [proxyinställningarna](agent-manage.md#update-proxy-settings) för att kontrol lera att du har konfigurerat agenten korrekt för att kommunicera via en proxyserver.    
* Dubbla kontroller att följande Azure Monitor slut punkter är vit listas:

    |Agentresurs| Portar | Riktning |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | Port 443| Inkommande och utgående |  
    |*.oms.opinsights.azure.com | Port 443| Inkommande och utgående |  
    |*.blob.core.windows.net | Port 443| Inkommande och utgående |  

    Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten för att använda Runbooks eller hanterings lösningar i din miljö, måste den ha åtkomst till port numret och de URL: er som beskrivs i [Konfigurera ditt nätverk för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Du får ett 403-fel vid försök att publicera

### <a name="probable-causes"></a>Troliga orsaker
* Datum och tid stämmer på Linux-Server 
* Arbetsyte-ID och Arbetsytenyckel används är inte rätt

### <a name="resolution"></a>Lösning

1. Kontrollera tiden på Linux-server med kommandot datum. Om tiden är +/-15 minuter efter den aktuella tiden, misslyckas onboarding. Till rätt detta Uppdatera datum och/eller tidszonen för din Linux-server. 
2. Kontrollera att du har installerat den senaste versionen av Log Analytics-agenten för Linux.  Den senaste versionen nu meddelar dig om tidsförskjutningsintervallet som orsakar onboarding felet.
3. Reonboard med rätt arbetsyte-ID och Arbetsytenyckel följa installationsinstruktionerna tidigare i den här artikeln.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Du ser ett 500 och 404-fel i loggfilen direkt efter integreringen
Det här är ett känt problem som uppstår vid första överföring av Linux-data till Log Analytics-arbetsytan. Detta påverkar inte data som skickas eller service-upplevelse.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: du ser omiagent med 100% processor

### <a name="probable-causes"></a>Troliga orsaker
En regression i NSS-PEM Package [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) orsakade ett allvarligt prestanda problem, men vi har sett upp mycket i RedHat/CentOS 7. x-distributioner. Mer information om det här problemet finns i följande dokumentation: bugg [1667121-prestanda regression i](https://bugzilla.redhat.com/show_bug.cgi?id=1667121)lib-sväng.

Prestanda relaterade buggar sker inte hela tiden och de är mycket svåra att återskapa. Om du upplever det här problemet med omiagent bör du använda skriptet omiHighCPUDiagnostics.sh som samlar in stack spårningen för omiagent när en viss tröskel överskrids.

1. Hämta skriptet <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Kör diagnostik i 24 timmar med 30% processor tröskel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstacken kommer att dumpas i omiagent_trace-filen, om du märker många NSS-funktions anrop följer du lösnings stegen nedan.

### <a name="resolution-step-by-step"></a>Lösning (steg för steg)

1. Uppgradera NSS-PEM-paketet till [v 1.0.3-5. el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Om NSS-PEM inte är tillgänglig för uppgradering (de används huvudsakligen på CentOS), nedgradera du sväng nedåt till 7.29.0-46. Om du av misstag kör "yum update" uppgraderas spiralen till 7.29.0-51 och problemet inträffar igen. <br/>
`sudo yum downgrade curl libcurl`

3. Starta om OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Du inte ser några data i Azure portal

### <a name="probable-causes"></a>Troliga orsaker

- Det gick inte att registrera till Azure Monitor
- Anslutningen till Azure Monitor blockeras
- Log Analytics-agenten för Linux-data som säkerhetskopieras

### <a name="resolution"></a>Lösning
1. Kontrol lera om onboarding-Azure Monitor lyckades genom att kontrol lera om följande fil finns: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Återpublicera med hjälp av `omsadmin.sh` kommando rads instruktioner
3. Om du använder en proxyserver, referera till proxy Lösningssteg som angavs tidigare.
4. I vissa fall när Log Analytics-agenten för Linux inte kan kommunicera med tjänsten data på agenten är i kö till den fullständiga buffertstorleken, vilket är 50 MB. Agenten ska startas om genom att köra följande kommando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Det här problemet löses i agenten version 1.1.0-28 och senare.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: Du ser inte vidarebefordrade Syslog-meddelanden 

### <a name="probable-causes"></a>Troliga orsaker
* Konfigurationen tillämpas på Linux-servern tillåter inte insamling av skickade anläggningar och/eller loggningsnivåer
* Syslog vidarebefordras inte korrekt till Linux-servern
* Hur många meddelanden som vidarebefordras per sekund behövs för för den grundläggande konfigurationen av Log Analytics-agenten för Linux för att hantera

### <a name="resolution"></a>Lösning
* Kontrollera konfigurationen i Log Analytics-arbetsytan för Syslog innehåller alla funktioner och rätt loggningsnivåer. Granska [Konfigurera syslog-samling i Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifiera de inbyggda daemonarna för syslog-meddelanden (`rsyslog``syslog-ng`) kan ta emot vidarebefordrade meddelanden
* Kontrollera brandväggsinställningarna för på Syslog-servern för att säkerställa att meddelanden inte blockeras
* Simulera ett syslog-meddelande för att Log Analytics med kommandot `logger`
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: Du får Errno adress redan används i omsagent loggfil
Om du ser `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` i omsagent. log.

### <a name="probable-causes"></a>Troliga orsaker
Det här felet indikerar att Linux-diagnostiktillägget (LAD) installeras sida vid sida med Log Analytics Linux VM-tillägget och den använder samma port för syslog-datainsamling som omsagent.

### <a name="resolution"></a>Lösning
1. Kör följande kommandon (Observera att 25224 är ett exempel och det är möjligt att i din miljö du ser ett annat portnummer som används av LAD) som rot:

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Du måste redigera rätt `rsyslogd`-eller `syslog_ng`s konfigurations fil och ändra den LAD som ska skrivas till port 25229.

2. Om den virtuella datorn körs `rsyslogd`är filen som ska ändras: `/etc/rsyslog.d/95-omsagent.conf` (om den finns, annars `/etc/rsyslog`). Om den virtuella datorn körs `syslog_ng`är filen som ska ändras: `/etc/syslog-ng/syslog-ng.conf`.
3. Starta om omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Starta om syslog-tjänsten.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: Du kan inte avinstallera omsagent alternativet Rensa

### <a name="probable-causes"></a>Troliga orsaker

* Linux-Diagnostiktillägget installeras
* Linux-Diagnostiktillägget har installeras och avinstalleras, men du fortfarande ser ett fel om omsagent som används av mdsd och kan inte tas bort.

### <a name="resolution"></a>Lösning
1. Avinstallera Linux-Diagnostiktillägget (LAD).
2. Ta bort filer för Linux-diagnostik från datorn om de finns på följande plats: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` och `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: Du kan inte se data Nagios data 

### <a name="probable-causes"></a>Troliga orsaker
* Omsagent användaren har inte behörighet att läsa från Nagios-loggfil
* Nagios käll- och filter har inte uncommented från omsagent.conf fil

### <a name="resolution"></a>Lösning
1. Lägg till omsagent-användare för att läsa från nagios-filen genom att följa dessa [anvisningar](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. I Log Analytics agent för allmän konfigurations fil för Linux på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`kontrollerar du att **både** nagios-källan och-filtret är avkommenterade.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: Du inte ser några data i Linux 

### <a name="probable-causes"></a>Troliga orsaker
* Det gick inte att registrera till Azure Monitor
* Anslutningen till Azure Monitor blockeras
* Virtuella datorn har startats om
* OMI-paketet har manuellt uppgraderats till en senare version jämfört med vad som har installerats med Log Analytics-agenten för Linux-paket
* DSC-resursens logg *klass hittades inte* fel i `omsconfig.log` logg filen
* Log Analytics-agenten för data som säkerhetskopieras
* Det *finns ingen aktuell konfiguration för DSC-loggar. Kör kommandot start-DscConfiguration med parametern-Path för att ange en konfigurations fil och skapa en aktuell konfiguration först.* i `omsconfig.log` loggfil, men det finns inget logg meddelande om `PerformRequiredConfigurationChecks` åtgärder.

### <a name="resolution"></a>Lösning
1. Installera alla beroenden som auditd-paketet.
2. Kontrol lera om onboarding till Azure Monitor lyckades genom att kontrol lera om följande fil finns: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Om det inte var det kan du återanvända kommando rads [anvisningarna](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)för omsadmin.sh.
4. Om du använder en proxy, kontrollera proxy felsökning stegen ovan.
5. I vissa Azure distributionssystem startar omid OMI server daemon inte efter att den virtuella datorn startas om. Detta leder inte se granskning eller ChangeTracking UpdateManagement lösning-relaterade data. Lösningen är att starta OMI-servern manuellt genom att köra `sudo /opt/omi/bin/service_control restart`.
6. Efter OMI paketet manuellt till en nyare version, måste den startas om manuellt för Log Analytics-agenten ska fortsätta att fungera. Det här steget är obligatoriskt för vissa distributioner där OMI-servern inte startar automatiskt när den har uppgraderats. Kör `sudo /opt/omi/bin/service_control restart` för att starta om OMI.
7. Om det inte finns något fel i DSC-resurs *klassen* i omsconfig. log kör du `sudo /opt/omi/bin/service_control restart`.
8. I vissa fall, när Log Analytics agent för Linux inte kan kommunicera med Azure Monitor, säkerhets kopie ras data på agenten till den fulla buffertstorleken: 50 MB. Agenten ska startas om genom att köra följande kommando `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Det här problemet löses i agenten version 1.1.0-28 eller senare
    >

* Om `omsconfig.log` logg filen inte indikerar att `PerformRequiredConfigurationChecks` åtgärder körs regelbundet på systemet kan det bero på ett problem med cron-jobbet/-tjänsten. Se till att cron-jobbet finns under `/etc/cron.d/OMSConsistencyInvoker`. Om det behövs kör du följande kommandon för att skapa cron-jobb:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Kontrollera också cron-tjänsten körs. Du kan använda `service cron status` med Debian, Ubuntu, SUSE eller `service crond status` med RHEL, CentOS, Oracle Linux för att kontrol lera status för den här tjänsten. Om tjänsten inte finns, kan du installera binärfilerna och starta tjänsten med hjälp av följande:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: När du konfigurerar samlingen från portalen för Syslog- eller Linux-prestandaräknare, gäller inte inställningarna

### <a name="probable-causes"></a>Troliga orsaker
* Log Analytics-agenten för Linux har inte hämtas den senaste konfigurationen
* De ändrade i portalen har inte tillämpats

### <a name="resolution"></a>Lösning
**Bakgrund:** `omsconfig` är Log Analytics agent för konfigurations agenten för Linux som söker efter ny portal konfiguration var femte minut. Den här konfigurationen tillämpas sedan på Log Analytics-agenten för Linux-konfigurationsfiler finns på /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* I vissa fall kanske inte Log Analytics-agenten för Linux-konfiguration-agenten kan kommunicera med tjänsten Portalkonfiguration, vilket resulterar i senaste konfiguration som inte används.
  1. Kontrol lera att `omsconfig`-agenten har installerats genom att köra `dpkg --list omsconfig` eller `rpm -qi omsconfig`.  Om det inte är installerat installerar du om den senaste versionen av Log Analytics-agenten för Linux.

  2. Kontrol lera att `omsconfig`-agenten kan kommunicera med Azure Monitor genom att köra följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Det här kommandot returnerar konfigurationen agenten tar emot från tjänsten, inklusive Syslog-inställningar, prestandaräknare för Linux och anpassade loggar. Om det här kommandot Miss lyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: Du ser inte alla anpassade loggdata 

### <a name="probable-causes"></a>Troliga orsaker
* Det gick inte att registrera till Azure Monitor.
* Inställningen **tillämpa följande konfiguration på mina Linux-servrar** har inte valts.
* omsconfig har inte hämtas den senaste konfigurationen för den anpassade loggen från tjänsten.
* Log Analytics agent för Linux User `omsagent` kan inte komma åt den anpassade loggen på grund av behörigheter eller inte hittas.  Du kan se följande fel:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Kända problem med konkurrenstillstånd fast i Log Analytics-agenten för Linux-version 1.1.0-217

### <a name="resolution"></a>Lösning
1. Det gick att verifiera att registreringen till Azure Monitor lyckades genom att kontrol lera om följande fil finns: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Om inte, antingen:  

  1. Återpublicera med hjälp av kommando rads [anvisningarna](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)för omsadmin.sh.
  2. Under **Avancerade inställningar** i Azure Portal kontrollerar du att inställningen **tillämpa följande konfiguration på mina Linux-servrar** är aktive rad.  

2. Kontrol lera att `omsconfig`-agenten kan kommunicera med Azure Monitor genom att köra följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Det här kommandot returnerar konfigurationen agenten tar emot från tjänsten, inklusive Syslog-inställningar, prestandaräknare för Linux och anpassade loggar. Om det här kommandot Miss lyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

**Bakgrund:** I stället för Log Analytics-agenten för Linux som körs som privilegie rad användare-`root`körs agenten som `omsagent` användare. I de flesta fall måste explicit behörighet beviljas till den här användaren för att vissa filer som ska läsas. Om du vill bevilja behörighet för `omsagent` användare kör du följande kommandon:

1. Lägg till `omsagent` användare i en speciell grupp `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Bevilja Universal Read-åtkomst till den nödvändiga filen `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Det finns ett känt problem med ett konkurrenstillstånd med Log Analytics-agenten för Linux tidigare version än 1.1.0-217. När du har uppdaterat till den senaste agenten kör du följande kommando för att hämta den senaste versionen av plugin-programmet för utdata `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: Du försöker att reonboard till en ny arbetsyta
När du försöker reonboard en agent till en ny arbetsyta, måste konfigurationen för logganalys-agenten rensas innan reonboarding. Om du vill rensa upp den gamla konfigurationen från agenten kör du Shell-paketet med `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Eller

```
sudo sh ./onboard_agent.sh --purge
```

Du kan fortsätta att återanvända när du har använt alternativet `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics agent-tillägget i Azure-portalen är märkt med ett felaktigt tillstånd: Etableringen misslyckades

### <a name="probable-causes"></a>Troliga orsaker
* Log Analytics-agenten har tagits bort från operativsystemet
* Log Analytics agent-tjänsten inte är tillgängligt, inaktiverat eller inte konfigurerat

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.
1. Ta bort tillägg från Azure-portalen.
2. Installera agenten genom att följa [anvisningarna](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Starta om agenten genom att köra följande kommando: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Vänta några minuter och etablerings statusen ändras till **etableringen har slutförts**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: Uppgradera Log Analytics-agenten på begäran

### <a name="probable-causes"></a>Troliga orsaker

Paket för Log Analytics-agenten på värden är inaktuell.

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.

1. Sök efter den senaste versionen på [sidan](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Ladda ned installationsskriptet (1.4.2-124 som exempel version):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uppgradera paket genom att köra `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
