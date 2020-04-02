---
title: Felsöka Azure Log Analytics Linux Agent | Microsoft-dokument
description: Beskriv symptom, orsaker och lösning för de vanligaste problemen med Log Analytics-agenten för Linux i Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 2343de97d06abdefed2c2977a7341aa411429319
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520744"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Felsöka problem med Log Analytics-agenten för Linux 

Den här artikeln innehåller hjälp med felsökning av fel som kan uppstå med Log Analytics-agenten för Linux i Azure Monitor och föreslår möjliga lösningar för att lösa dem.

Om inget av dessa steg fungerar för dig är även följande supportkanaler tillgängliga:

* Kunder med Premier-supportförmåner kan öppna en supportbegäran med [Premier](https://premier.microsoft.com/).
* Kunder med Azure-supportavtal kan öppna en supportbegäran [i Azure-portalen](https://manage.windowsazure.com/?getsupport=true).
* Diagnostisera OMI-problem med [felsökningsguiden för OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Arkivera ett [GitHub-problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Besök log analytics feedback-sidan för att [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) granska inskickade idéer och buggar eller fil en ny.  

## <a name="important-log-locations-and-log-collector-tool"></a>Viktiga loggplatser och log collector-verktyg

 Fil | Sökväg
 ---- | -----
 Log Analytics-agent för Linux-loggfil | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Konfigurationsloggfil för logganalysagent | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Vi rekommenderar att du använder vårt logginsamlareverktyg för att hämta viktiga loggar för felsökning eller innan du skickar in ett GitHub-problem. Du kan läsa mer om verktyget och hur du kör det [här](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Viktiga konfigurationsfiler

 Kategori | Filplats
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf`eller `/etc/rsyslog.conf` eller`/etc/rsyslog.d/95-omsagent.conf`
 Prestanda, Nagios, Zabbix, Log Analytics-utgång och generalagent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Ytterligare konfigurationer | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Redigering av konfigurationsfiler för prestandaräknare och Syslog skrivs över om samlingen är konfigurerad från [datamenyn Avancerade inställningar](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) för Logganalys i Azure-portalen för din arbetsyta. Om du vill inaktivera konfigurationen för alla agenter inaktiverar du insamling från **avancerade inställningar** för Logganalys eller för en enda agent som kör följande:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Felkoder för installation

| Felkod | Betydelse |
| --- | --- |
| NOT_DEFINED | Eftersom de nödvändiga beroenden inte är installerade, kommer auoms auditerade plugin inte installeras | Installationen av auoms misslyckades, installera paketgranskning. |
| 2 | Ogiltigt alternativ som anges i skalbunten. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning |
| 3 | Inget alternativ till skalbunten. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning. |
| 4 | Ogiltig pakettyp ELLER ogiltiga proxyinställningar; *omsagent-rpm*.sh-paket kan endast installeras på RPM-baserade system, och omsagent-*deb*.sh-paket kan endast installeras på Debianbaserade system. Det är rekommenderat att du använder det universella installationsprogrammet från den [senaste versionen](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Också granska för att verifiera dina proxyinställningar. |
| 5 | Skalbunten måste köras som root ELLER det uppstod 403 fel under introduktionen. Kör kommandot `sudo`med . |
| 6 | Ogiltig paketarkitektur ELLER det uppstod fel 200 fel som returnerades under introduktionen. omsagent-*x64.sh förpackningar kan endast installeras på 64-bitarssystem, och omsagent-* x86.sh paket kan endast installeras på 32-bitarssystem. Ladda ner rätt paket för din arkitektur från den [senaste versionen](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Installationen av OMS-paketet misslyckades. Titta igenom kommandoutdata för rotfelet. |
| 19 | Installationen av OMI-paketet misslyckades. Titta igenom kommandoutdata för rotfelet. |
| 20 | Installationen av SCX-paketet misslyckades. Titta igenom kommandoutdata för rotfelet. |
| 21 | Installationen av providerpaket misslyckades. Titta igenom kommandoutdata för rotfelet. |
| 22 | Installationen av paketet med paket misslyckades. Titta igenom kommandoutdata för rotfelet |
| 23 | SCX- eller OMI-paketet är redan installerat. Använd `--upgrade` istället `--install` för att installera skalbunten. |
| 30 | Internt paketfel. Arkivera ett [GitHub-problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 55 | Opensl-versionen som inte stöds eller kan inte ansluta till Azure Monitor ELLER dpkg är låst eller det krullningsprogram som saknas. |
| 61 | Python-ctypebibliotek saknas. Installera Python-ctypes-biblioteket eller paketet (python-ctypes). |
| 62 | Saknas tjära program, installera tjära. |
| 63 | Saknas sed program, installera sed. |
| 64 | Saknade curl program, installera curl. |
| 65 | Saknas gpg program, installera gpg. |

## <a name="onboarding-error-codes"></a>Felkoder för introduktionsnummer

| Felkod | Betydelse |
| --- | --- |
| 2 | Ogiltigt alternativ som anges i skriptet omsadmin. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 3 | Ogiltig konfiguration som tillhandahålls till omsadmin-skriptet. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 4 | Ogiltig proxy som tillhandahålls till omsadmin-skriptet. Verifiera proxyn och se vår [dokumentation för att använda en HTTP-proxy](log-analytics-agent.md#firewall-requirements). |
| 5 | 403 HTTP-fel som tagits emot från Azure Monitor. Mer information finns i hela utdata från skriptet omsadmin. |
| 6 | HTTP-fel som inte är 200 från Azure Monitor. Mer information finns i hela utdata från skriptet omsadmin. |
| 7 | Det gick inte att ansluta till Azure Monitor. Mer information finns i hela utdata från skriptet omsadmin. |
| 8 | Det gick inte att gå in på log Analytics-arbetsytan. Mer information finns i hela utdata från skriptet omsadmin. |
| 30 | Internt skriptfel. Arkivera ett [GitHub-problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 31 | Det gick inte att generera agent-ID. Arkivera ett [GitHub-problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 32 | Det gick inte att generera certifikat. Mer information finns i hela utdata från skriptet omsadmin. |
| 33 | Det gick inte att generera metakonfiguration för omsconfig. Arkivera ett [GitHub-problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 34 | Metakonfiguration generation skript inte närvarande. Försök att skriva `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`in den igen med . |

## <a name="enable-debug-logging"></a>Aktivera felsökningsloggning
### <a name="oms-output-plugin-debug"></a>OMS-utdata plugin debug
 Flytande gör det möjligt för plugin-specifika loggningsnivåer så att du kan ange olika loggnivåer för in- och utdata. Om du vill ange en annan loggnivå för `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`OMS-utdata redigerar du generalagentkonfigurationen på .  

 I OMS-utdatainsticksprogrammet, innan konfigurationsfilen `log_level` är `info` `debug`slut, ändrar du egenskapen från till:

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

Med felsökningsloggning kan du se batchade uppladdningar till Azure Monitor åtskilda efter typ, antal dataobjekt och tid det tar att skicka:

*Exempel felsökning aktiverad logg:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Utförlig utdata
Istället för att använda OMS-utdatainsticksprogrammet `stdout`kan du också mata ut dataobjekt direkt till , vilket är synligt i Log Analytics-agenten för Linux-loggfilen.

I log analytics general agent `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`konfigurationsfil på , kommentera `#` ut OMS utdata plugin genom att lägga till en framför varje rad:

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

Under utdata plugin, avkommenta följande `#` avsnitt genom att ta bort framför varje rad:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: Det går inte att ansluta via proxy till Azure Monitor

### <a name="probable-causes"></a>Sannolika orsaker
* Proxyn som angavs under introduktionen var felaktig
* Slutpunkterna för Azure Monitor och Azure Automation Service är inte vitlistade i ditt datacenter 

### <a name="resolution"></a>Lösning
1. Reonboard till Azure Monitor med Log Analytics-agenten för `-v` Linux med hjälp av följande kommando med alternativet aktiverat. Det möjliggör utförlig utdata av agenten som ansluter via proxyn till Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Granska avsnittet [Uppdatera proxyinställningar för](agent-manage.md#update-proxy-settings) att kontrollera att du har konfigurerat agenten korrekt för att kommunicera via en proxyserver.    

3. Dubbelkolla att slutpunkterna som beskrivs i listan över [azure monitor-nätverksbrandväggsbehov](log-analytics-agent.md#firewall-requirements) läggs till i en tillåt-lista korrekt. Om du använder Azure Automation länkas även de nödvändiga nätverkskonfigurationsstegen ovan.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Ett 403-fel visas när du försöker gå ombord

### <a name="probable-causes"></a>Sannolika orsaker
* Datum och tid är felaktigt på Linux Server 
* Arbetsyte-ID och Arbetsytenyckel som används är inte korrekta

### <a name="resolution"></a>Lösning

1. Kontrollera tiden på din Linux-server med kommandodatumet. Om tiden är +/- 15 minuter från aktuell tid misslyckas introduktionen. Så här korrigerar du datum och/eller tidszon för din Linux-server. 
2. Kontrollera att du har installerat den senaste versionen av Log Analytics-agenten för Linux.  Den senaste versionen meddelar dig nu om tidsnedställning orsakar in-boardingfel.
3. Reonboard med rätt arbetsyte-ID och Arbetsytenyckel enligt installationsanvisningarna tidigare i den här artikeln.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Du ser ett 500 och 404 fel i loggfilen direkt efter onboarding
Detta är ett känt problem som uppstår vid första uppladdningen av Linux-data till en Log Analytics-arbetsyta. Detta påverkar inte data som skickas eller tjänstupplevelse.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: Du ser omiagent med 100% CPU

### <a name="probable-causes"></a>Sannolika orsaker
En regression i nss-pem paket [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) orsakade en allvarlig prestanda problem, att vi har sett komma upp en hel del i Redhat / Centos 7.x distributioner. Mer information om problemet finns i följande dokumentation: Fel [1667121 Prestandaregression i libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

Prestandarelaterade buggar händer inte hela tiden, och de är mycket svåra att reproducera. Om du upplever ett sådant problem med omiagent bör du använda skriptet omiHighCPUDiagnostics.sh som kommer att samla stackspårningen av omiagenten när du överskrider ett visst tröskelvärde.

1. Ladda ner skriptet <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Kör diagnostik i 24 timmar med 30% CPU-tröskel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack kommer att dumpas i omiagent_trace fil, Om du märker många Curl och NSS funktion samtal, följ resolutionsstegen nedan.

### <a name="resolution-step-by-step"></a>Upplösning (steg för steg)

1. Uppgradera nss-pem-paketet till [v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Om nss-pem inte är tillgänglig för uppgradering (oftast händer på Centos), sedan nedgradera curl till 7.29.0-46. Om av misstag du kör "yum update", då curl kommer att uppgraderas till 7.29.0-51 och problemet kommer att hända igen. <br/>
`sudo yum downgrade curl libcurl`

3. Starta om OMI: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Du ser inga data i Azure-portalen

### <a name="probable-causes"></a>Sannolika orsaker

- Introduktion till Azure Monitor misslyckades
- Anslutningen till Azure Monitor är blockerad
- Log Analytics-agent för Linux-data säkerhetskopieras

### <a name="resolution"></a>Lösning
1. Kontrollera om introduktionen av Azure Monitor lyckades genom att kontrollera om följande fil finns:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Gå tillbaka med `omsadmin.sh` kommandoradsinstruktionerna
3. Om du använder en proxy läser du stegen för proxymatchning som angavs tidigare.
4. I vissa fall, när Log Analytics-agenten för Linux inte kan kommunicera med tjänsten, köas data på agenten till den fullständiga buffertstorleken, vilket är 50 MB. Agenten bör startas om genom `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`att köra följande kommando: . 

    >[!NOTE]
    >Det här problemet åtgärdas i agentversion 1.1.0-28 och senare.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: Du ser inte vidarebefordrade Syslog-meddelanden 

### <a name="probable-causes"></a>Sannolika orsaker
* Konfigurationen som tillämpas på Linux-servern tillåter inte insamling av skickade anläggningar och/eller loggnivåer
* Syslog vidarebefordras inte korrekt till Linux-servern
* Antalet meddelanden som vidarebefordras per sekund är för stort för baskonfigurationen för Log Analytics-agenten för Linux att hantera

### <a name="resolution"></a>Lösning
* Kontrollera att konfigurationen på log analytics-arbetsytan för Syslog har alla faciliteter och rätt loggnivåer. Granska [konfigurera Syslog-samlingen i Azure-portalen](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifiera att de inbyggda syslog-meddelandedemonerna (`rsyslog`, `syslog-ng`) kan ta emot vidarebefordrade meddelanden
* Kontrollera brandväggsinställningarna på Syslog-servern för att säkerställa att meddelanden inte blockeras
* Simulera ett Syslog-meddelande till `logger` Log Analytics med kommandot
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: Du får Errno-adress som redan används i omsagentloggfilen
Om du `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` ser i omsagent.log.

### <a name="probable-causes"></a>Sannolika orsaker
Det här felet indikerar att Tillägget Linux Diagnostic (LAD) installeras sida vid sida med tillägget Log Analytics Linux VM och använder samma port för syslog-datainsamling som omsagent.

### <a name="resolution"></a>Lösning
1. Som root, kör följande kommandon (observera att 25224 är ett exempel och det är möjligt att i din miljö ser du ett annat portnummer som används av LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Du måste sedan redigera `rsyslogd` `syslog_ng` rätt eller config-fil och ändra LAD-relaterade konfigurationen för att skriva till port 25229.

2. Om den virtuella `rsyslogd`datorn körs är filen `/etc/rsyslog.d/95-omsagent.conf` som ska ändras: (om den finns, annars `/etc/rsyslog`). Om den virtuella `syslog_ng`datorn körs är filen `/etc/syslog-ng/syslog-ng.conf`som ska ändras: .
3. Starta om `sudo /opt/microsoft/omsagent/bin/service_control restart`omsagent .
4. Starta om tjänsten syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: Du kan inte avinstallera omsagent med hjälp av rensningsalternativ

### <a name="probable-causes"></a>Sannolika orsaker

* Linux Diagnostic Extension är installerat
* Linux Diagnostic Extension installerades och avinstallerades, men du ser fortfarande ett fel om omsagent som används av mdsd och kan inte tas bort.

### <a name="resolution"></a>Lösning
1. Avinstallera Linux Diagnostic Extension (LAD).
2. Ta bort Linux Diagnostic Extension-filer från datorn om `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` `/var/opt/microsoft/omsagent/LAD/`de finns på följande plats: och .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: Du kan inte se data några Nagios data 

### <a name="probable-causes"></a>Sannolika orsaker
* Omsagent-användare har inte behörighet att läsa från Nagios loggfil
* Nagios källa och filter har inte tagits bort från omsagent.conf fil

### <a name="resolution"></a>Lösning
1. Lägg till omsagent-användare för att läsa från Nagios-filen genom att följa dessa [instruktioner](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. I Log Analytics-agenten för `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`Linux-allmän konfigurationsfil på kontrollerar du att **både** Nagios-källan och filtret inte har kommenteras.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: Du ser inga Linux-data 

### <a name="probable-causes"></a>Sannolika orsaker
* Introduktion till Azure Monitor misslyckades
* Anslutningen till Azure Monitor är blockerad
* Virtuell maskin startades om
* OMI-paketet uppgraderades manuellt till en nyare version jämfört med vad som installerades av Log Analytics-agent för Linux-paketet
* DSC-resursloggar har inte `omsconfig.log` *hittat* fel i loggfilen
* Log Analytics-agent för data säkerhetskopieras
* DSC loggar *Aktuell konfiguration finns inte. Kör start-DscConfiguration-kommando med -Path-parameter för att ange en konfigurationsfil och skapa en aktuell konfiguration först.* i `omsconfig.log` loggfilen, men det `PerformRequiredConfigurationChecks` finns inget loggmeddelande om åtgärder.

### <a name="resolution"></a>Lösning
1. Installera alla beroenden som granskningspaket.
2. Kontrollera om introduktionen till Azure Monitor lyckades genom `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`att kontrollera om följande fil finns: .  Om så inte var fallet kan du gå in igen med hjälp av [kommandoradsinstruktionerna](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)omsadmin.sh .
4. Om du använder en proxy kontrollerar du stegen för proxyfelsökning ovan.
5. I vissa Azure-distributionssystem startar omid OMI-serverdemon inte när den virtuella datorn har startats om. Detta resulterar i att inte se lösningar relaterade data för granskning, ChangeTracking eller UpdateManagement. Lösningen är att manuellt starta omi-servern `sudo /opt/omi/bin/service_control restart`genom att köra .
6. När OMI-paketet uppgraderas manuellt till en nyare version måste det startas om manuellt för att Log Analytics-agenten ska fortsätta fungera. Det här steget krävs för vissa distributioner där OMI-servern inte startar automatiskt när den har uppgraderats. Kör `sudo /opt/omi/bin/service_control restart` för att starta om OMI.
7. Om DSC-resursklassen *inte visas i* omsconfig.log kör du `sudo /opt/omi/bin/service_control restart`.
8. I vissa fall, när Log Analytics-agenten för Linux inte kan prata med Azure Monitor, säkerhetskopieras data på agenten till den fullständiga buffertstorleken: 50 MB. Agenten bör startas om genom `/opt/microsoft/omsagent/bin/service_control restart`att köra följande kommando .

    >[!NOTE]
    >Det här problemet åtgärdas i Agent version 1.1.0-28 eller senare
    >

* Om `omsconfig.log` loggfilen inte `PerformRequiredConfigurationChecks` anger att åtgärder körs regelbundet på systemet kan det vara problem med cron-jobbet/-tjänsten. Kontrollera att cron-jobbet finns under `/etc/cron.d/OMSConsistencyInvoker`. Om det behövs kör följande kommandon för att skapa cron-jobbet:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Kontrollera också att cron-tjänsten körs. Du kan `service cron status` använda med Debian, Ubuntu, SUSE eller `service crond status` med RHEL, CentOS, Oracle Linux för att kontrollera status för den här tjänsten. Om tjänsten inte finns kan du installera binärfilerna och starta tjänsten med följande:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: När du konfigurerar samling från portalen för Syslog- eller Linux-prestandaräknare tillämpas inte inställningarna

### <a name="probable-causes"></a>Sannolika orsaker
* Log Analytics-agenten för Linux har inte plockat upp den senaste konfigurationen
* De ändrade inställningarna i portalen tillämpades inte

### <a name="resolution"></a>Lösning
**Bakgrund:** `omsconfig` är Log Analytics-agenten för Linux-konfigurationsagenten som letar efter ny konfiguration på portalsidan var femte minut. Den här konfigurationen tillämpas sedan på Log Analytics-agenten för Linux-konfigurationsfiler som finns på /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* I vissa fall kanske Log Analytics-agenten för Linux-konfigurationsagenten inte kan kommunicera med portalkonfigurationstjänsten, vilket resulterar i att den senaste konfigurationen inte tillämpas.
  1. Kontrollera att `omsconfig` agenten är `dpkg --list omsconfig` `rpm -qi omsconfig`installerad genom att köra eller .  Om den inte är installerad installerar du om den senaste versionen av Log Analytics-agenten för Linux.

  2. Kontrollera att `omsconfig` agenten kan kommunicera med Azure `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Monitor genom att köra följande kommando . Det här kommandot returnerar den konfiguration som agenten tar emot från tjänsten, inklusive Syslog-inställningar, Linux-prestandaräknare och anpassade loggar. Om det här kommandot misslyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: Du ser inga anpassade loggdata 

### <a name="probable-causes"></a>Sannolika orsaker
* Introduktion till Azure Monitor misslyckades.
* Inställningen **Använd följande konfiguration på mina Linux-servrar** har inte valts.
* omsconfig har inte plockat upp den senaste anpassade loggkonfigurationen från tjänsten.
* Log Analytics-agent `omsagent` för Linux-användare kan inte komma åt den anpassade loggen på grund av behörigheter eller inte hittas.  Följande fel kan visas:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Känt problem med racevillkoret åtgärdat i Log Analytics-agent för Linux version 1.1.0-217

### <a name="resolution"></a>Lösning
1. Verifiera introduktion till Azure Monitor lyckades genom att `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`kontrollera om följande fil finns: . Om inte, antingen:  

  1. Reonboard med hjälp av omsadmin.sh [kommandoradsinstruktioner](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Under **Avancerade inställningar** i Azure-portalen kontrollerar du att inställningen Använd följande konfiguration på mina **Linux-servrar** är aktiverad.  

2. Kontrollera att `omsconfig` agenten kan kommunicera med Azure `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`Monitor genom att köra följande kommando .  Det här kommandot returnerar den konfiguration som agenten tar emot från tjänsten, inklusive Syslog-inställningar, Linux-prestandaräknare och anpassade loggar. Om det här kommandot misslyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

**Bakgrund:** I stället för Log Analytics-agenten för `root`Linux som körs `omsagent` som en privilegierad användare körs agenten som användare. I de flesta fall måste den här användaren beviljas explicit behörighet för att vissa filer ska kunna läsas. Om du `omsagent` vill ge behörighet till användaren kör du följande kommandon:

1. Lägga `omsagent` till användaren i en viss grupp`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Bevilja allmän läsåtkomst till den nödvändiga filen`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Det finns ett känt problem med ett konkurrenstillstånd med Log Analytics-agenten för Linux-versionen tidigare än 1.1.0-217. När du har uppdaterat till den senaste agenten kör `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`du följande kommando för att få den senaste versionen av plugin-programmet för utdata .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: Du försöker gå ombord på en ny arbetsyta
När du försöker skriva om en agent till en ny arbetsyta måste log Analytics-agentkonfigurationen rensas innan du går ombord. Om du vill rensa upp gammal konfiguration från agenten kör du skalbunten med`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Eller

```
sudo sh ./onboard_agent.sh --purge
```

Du kan fortsätta skriva om `--purge` när du har använt alternativet

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Loggagenttillägget för Logganalys i Azure-portalen har markerats med ett feltillstånd: Etableringen misslyckades

### <a name="probable-causes"></a>Sannolika orsaker
* Log Analytics-agenten har tagits bort från operativsystemet
* Log Analytics-agenttjänsten är inaktiverad eller inte konfigurerad

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.
1. Ta bort tillägg från Azure-portalen.
2. Installera agenten enligt [instruktionerna](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Starta om agenten genom `sudo /opt/microsoft/omsagent/bin/service_control restart`att köra följande kommando: .
* Vänta flera minuter och etableringstillståndet ändrades till **Etableringstillståndet lyckades**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: Uppgradering av Log Analytics-agenten på begäran

### <a name="probable-causes"></a>Sannolika orsaker

Log Analytics-agentpaketen på värden är inaktuella.

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.

1. Kontrollera om den senaste versionen på [sidan](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Ladda ner installationsskript (1.4.2-124 som exempelversion):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uppgradera paket genom `sudo sh ./omsagent-*.universal.x64.sh --upgrade`att köra .
