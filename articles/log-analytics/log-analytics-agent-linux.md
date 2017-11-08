---
title: Linux-datorer ansluta till Azure Log Analytics | Microsoft Docs
description: "Den här artikeln beskriver hur du ansluter Linux-datorer i Azure, andra molntjänster eller lokala till logganalys med OMS-Agent för Linux."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: magoedte
ms.openlocfilehash: 56c666d1a18937df21a6aca8acde87beda1cad8e
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Linux-datorer ansluta till logganalys 

Med Azure Log Analytics kan du samla in och fungerar på data som genereras från Linux-datorer och lösningar för behållare som Docker, som finns i ditt lokala datacenter som fysiska servrar eller virtuella datorer, virtuella datorer i en molnbaserad tjänst som Amazon Webbtjänster (AWS) eller Microsoft Azure. Du kan också använda hanteringslösningar som är tillgängliga i [Azure Automation](../automation/automation-intro.md) , till exempel ändringsspårning som identifierar konfigurationsändringar och uppdateringshantering att hantera programuppdateringar för att proaktivt hantera livscykeln för dina Linux Virtuella datorer. 

OMS-Agent för Linux kommunicerar utgående med Log Analytics och Azure Automation-tjänster via TCP port 443 och om datorn är ansluten till en brandvägg eller proxyserver server för att kommunicera över Internet, granska [konfigurera agenten för användning med en proxyserver eller OMS Gateway](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway) att förstå vilka konfigurationsändringar kommer måste tillämpas.  Om du övervakar datorn med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 kan det vara multi-homed Log Analytics-tjänsten för att samla in data och vidarebefordra till tjänsten och fortfarande övervakas av Operations Manager.  Linux-datorer som övervakas av en Operations Manager-hanteringsgrupp som är integrerad med logganalys (för närvarande enligt Operations Management Suite Operations Manager Operations-konsolen) inte ta emot konfiguration för datakällor och vidarebefordra insamlade data till hanteringsgruppen.  OMS-agenten kan inte konfigureras för att rapportera till flera logganalys-arbetsytan.  

Om din IT-säkerhetsprinciper inte tillåter datorer i nätverket för att ansluta till Internet, kan agenten konfigureras för att ansluta till OMS-Gateway för att ta emot information om konfiguration och skicka insamlade data beroende på lösningen som du har aktiverat. Mer information och anvisningar om hur du konfigurerar OMS Linux-agenten kan kommunicera via en OMS-Gateway till tjänsterna finns [ansluta datorer till OMS med OMS-gatewayen](log-analytics-oms-gateway.md).  

Följande diagram visar anslutningen mellan Linux-datorer som hanteras med agent och logganalys, inklusive riktning och portar.

![direkt agentkommunikation med Azure Services-diagram](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Systemkrav
Innan du börjar måste du granska följande information för att verifiera att du uppfyller kraven.

### <a name="supported-linux-operating-systems"></a>Linux-operativsystem som stöds
Följande Linux-distributioner stöds officiellt.  OMS-Agent för Linux kan också köra på andra distributioner som inte finns.

* Amazon Linux 2012.09 till 2015.09 (x86/x64)
* CentOS Linux 5, 6 och 7 (x86/x64)
* Oracle Linux 5, 6 och 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 och 7 (x86/x64)
* Debian GNU/Linux 6, 7 och 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 och 12 (x86/x64)

### <a name="network"></a>Nätverk
Informationen nedan lista över proxy- och brandväggsinställningarna configuration information som krävs för Linux-agenten kan kommunicera med logganalys och Azure Automation. Trafiken är utgående från nätverket till tjänsten. 

|Agentresurs| Portar |  
|------|---------|  
|*.ods.opinsights.azure.com | Port 443|   
|*.oms.opinsights.azure.com | Port 443|   
|*.BLOB.Core.Windows.NET/ | Port 443|   
|*.azure-automation.net | Port 443|  

### <a name="package-requirements"></a>Krav för paketet

 **Nödvändigt paket**   | **Beskrivning**   | **Lägsta version**
--------------------- | --------------------- | -------------------
Glibc | GNU C-bibliotek   | 2.5-12 
Openssl | OpenSSL-bibliotek | 0.9.8e eller 1.0
CURL | cURL Webbklient | 7.15.5
Python-ctypes | | 
PAM | Lösenordssynkronisering moduler | 

> [!NOTE]
>  Rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard-daemon för syslog på version 5 Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för syslog händelseinsamling. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska daemonen rsyslog installeras och konfigureras för att ersätta sysklog, 

Agenten innehåller flera paket. Versionen-filen innehåller följande paket tillgängliga genom att köra shell-paket med `--extract`:

**Paketet** | **Version** | **Beskrivning**
----------- | ----------- | --------------
omsagent | 1.4.0 | Operations Management Suite-agenten för Linux
omsconfig | 1.1.1 | Konfiguration av agenten för OMS-Agent
omi | 1.2.0 | Öppna Management Infrastructure (OMI) - lightweight CIM-servern
scx | 1.6.3 | OMI CIM-Providers för operativsystemet prestandamått
Apache cimprov | 1.0.1 | Apache HTTP-Server prestandaövervakning provider för OMI. Installeras om Apache HTTP-Server har identifierats.
MySQL-cimprov | 1.0.1 | MySQL-Server prestandaövervakning provider för OMI. Installeras om MySQL/MariaDB server har identifierats.
docker-cimprov | 1.0.0 | Docker-provider för OMI. Installeras om Docker har identifierats.

### <a name="compatibility-with-system-center-operations-manager"></a>Kompatibilitet med System Center Operations Manager
OMS-Agent för Linux delar agent binärfiler med System Center Operations Manager-agenten. Om du installerar OMS-Agent för Linux på ett system som för närvarande hanteras av Operations Manager, uppgraderar OMI och SCX paketen på datorn till en nyare version. I den här versionen OMS och System Center 2016 - är Operations Manager/Operations Manager 2012 R2-agenter för Linux kompatibla. 

> [!NOTE]
> System Center 2012 SP1 och tidigare versioner är för närvarande inte kompatibelt och stöds med OMS-Agent för Linux.<br>
> Om OMS-Agent för Linux är installerad på en dator som inte är för närvarande övervakas av Operations Manager, och du vill övervaka med Operations Manager-datorn, måste du ändra den [OMI configuration](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) innan identifiering av datorn. **Det här steget är *inte* behövs om Operations Manager-agenten är installerad före en OMS-Agent för Linux.**

### <a name="system-configuration-changes"></a>Ändringar i systemkonfigurationen
När du har installerat OMS-Agent för Linux-paket, tillämpas följande ytterligare systemomfattande konfigurationsändringar. Dessa artefakter tas bort när omsagent paketet är avinstallerat.

* En icke-privilegierade användare med namnet: `omsagent` skapas. Är omsagent daemon kör som-kontot.
* En sudoers ”innehåller” filen skapas vid /etc/sudoers.d/omsagent. Detta ger rätt omsagent att starta om daemon för syslog- och omsagent. Om sudo ”” Includes inte stöds i den installerade versionen av sudo, skrivs dessa poster till /etc/sudoers.
* Syslog-konfigurationen har ändrats för att vidarebefordra en delmängd av händelser till agenten. Mer information finns i **konfigurera datainsamling** nedan

### <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version
Uppgradera från versioner tidigare än 1.0.0-47 stöds i den här versionen. Utför installationen med de `--upgrade` kommandot uppgraderar alla komponenter i agenten till den senaste versionen.

## <a name="installing-the-agent"></a>Installerar agenten

Det här avsnittet beskrivs hur du installerar OMS-Agent för Linux manuellt med hjälp av en bunndle som innehåller Debian och RPM-paket för varje agent-komponenter.  Den kan installeras direkt eller extraheras för att hämta enskilda paket.  Om du planerar att installera agenten på en Azure Linux-VM finns i följande avsnitt [samla in data om Azure Virtual Machines](log-analytics-quick-collect-azurevm.md) information om hur du installerar agenten med VM-tillägget Log Analytics.  Följ stegen i avsnittet *aktivera Log Analytics VM-tillägget*.  För Linux-datorer i din miljö, kan du förenkla installationsprocessen med ett skriptbaserade metod som beskrivs i artikeln [samla in data från Linux-datorer i din miljö](log-analytics-quick-collect-linux-computer.md).  

> [!NOTE]
> När två artiklar som anges ovan är avsedda för en person som är ny för Log Analytics och börja använda tjänsten snabbt är stegen för att konfigurera datorn som relevanta.  Om du redan har en arbetsyta och vill ansluta Linux-dator, väljer du en befintlig arbetsyta om en Azure Linux-dator, eller för en dator som finns utanför Azure, kopiera arbetsyte-ID och för att skicka till skriptet.  

Innan du installerar OMS-agent för Linux måste arbetsyte-ID och nyckel för logganalys-arbetsytan.  

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 
2. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
3. Välj arbetsytan som du vill att datorn rapporterar till i din lista över logganalys arbetsytor.
3. Välj **avancerade inställningar**.<br><br> ![Logganalys-avancerade inställningar](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **anslutna källor**, och välj sedan **Linux-servrar**.   
5. Värdet till höger om **arbetsyte-ID** och **primärnyckel**. Kopiera och klistra in både i din favorit-redigeraren.  
6. Ladda ned senaste [OMS-Agent för Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) eller [OMS-Agent för Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) från GitHub.  
7. Över rätt paket (x86 eller x64) till Linux-datorn med scp/sftp.
8. Installera paketet med hjälp av den `--install` eller `--upgrade` argumentet. 

    > [!NOTE]
    > Om alla befintliga paket installeras, till exempel när System Center Operations Manager-agenten för Linux redan är installerad, använda den `--upgrade` argumentet. För att ansluta till Operations Management Suite under installationen, ange den `-w <WorkspaceID>` och `-s <Shared Key>` parametrar.


#### <a name="to-install-and-onboard-directly"></a>Installera och publicera direkt
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Så här uppgraderar du agentpaket
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>Installera och publicera till en arbetsyta i US Government-moln
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Konfigurera agenten för användning med en proxyserver eller OMS-Gateway
OMS-Agent för Linux stöder kommunikation via en proxyserver eller OMS Gateway till Log Analytics-tjänsten med hjälp av HTTPS-protokollet.  Både anonyma och grundläggande autentisering (användarnamn/lösenord) stöds.  

### <a name="proxy-configuration"></a>Proxykonfiguration
Konfigurationsvärdet proxy har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

Egenskap|Beskrivning
-|-
Protokoll|https
Användaren|Valfritt användarnamn för proxyautentisering
lösenord|Lösenord för proxyautentisering
proxyhost|Adress eller FQDN för proxy server/OMS Gateway
port|Valfria portnumret för proxyservern server/OMS Gateway

Exempel: `https://user01:password@proxy01.contoso.com:30443`

Proxyservern kan anges under installationen eller genom att ändra konfigurationsfilen proxy.conf efter installationen.   

### <a name="specify-proxy-configuration-during-installation"></a>Ange proxykonfiguration under installationen
Den `-p` eller `--proxy` argumentet för paket-omsagent installationen anger proxykonfiguration som ska användas. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Definiera proxykonfigurationen i en fil
Proxykonfigurationen kan anges i filerna `/etc/opt/microsoft/omsagent/proxy.conf` och `/etc/opt/microsoft/omsagent/conf/proxy.conf `. Filerna kan skapas och redigeras direkt, men deras behörigheter måste uppdateras för att bevilja användaren omiuser läsbehörighet på filer. Exempel:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>Tar bort proxykonfigurationen
Om du vill ta bort en tidigare definierad proxykonfiguration och återgå till direkt anslutning, tar du bort filen proxy.conf:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Onboarding med logganalys
Om ett arbetsyte-ID och nyckel inte angavs under installationen av paketet, måste agenten registreras senare med logganalys.

### <a name="onboarding-using-the-command-line"></a>Onboarding med hjälp av kommandoraden
Kör kommandot omsadmin.sh tillhandahåller arbetsyte-id och nyckel för din arbetsyta. Det här kommandot måste köras som rot (med sudo-höjning):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>Registrera med hjälp av en fil
1.  Skapa filen `/etc/omsagent-onboard.conf`. Filen måste vara Läs- och skrivbara för roten.
`sudo vi /etc/omsagent-onboard.conf`
2.  Infoga följande rader i filen med ditt arbetsyte-ID och delad nyckel:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Kör följande kommando för att registrera med logganalys:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  Filen tas bort på lyckad onboarding.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>Aktivera OMS-Agent för Linux rapportera till System Center Operations Manager
Utför följande steg för att konfigurera OMS-Agent för Linux rapportera till en System Center Operations Manager-hanteringsgrupp.  

1. Redigera filen`/etc/opt/omi/conf/omiserver.conf`
2. Se till att den rad som början med **httpsport =** definierar port 1270. Exempelvis:`httpsport=1270`
3. Starta om OMI-servern:`sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Agenten loggar
Loggar för OMS-Agent för Linux finns på: `/var/opt/microsoft/omsagent/<workspace id>/log/` loggar efter programmet omsconfig (agentkonfiguration) finns på: `/var/opt/microsoft/omsconfig/log/` loggar för OMI och SCX-komponenter (som tillhandahåller mått prestandadata) finns på:`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Loggen rotation konfiguration ##
Rotera konfigurationen av loggen för omsagent finns på:`/etc/logrotate.d/omsagent-<workspace id>`

Standardinställningarna är: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>Avinstallerar OMS-Agent för Linux
Agent-paket kan avinstalleras genom att köra .sh samlingsfilen med den `--purge` argument, vilket tar bort agenten och dess konfiguration helt från datorn.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Felsökning

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Problem: Det gick inte att ansluta via proxy till logganalys

#### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som anges under onboarding var felaktigt
* Log Analytics och Azure Automation-Tjänsteslutpunkter är inte vitlistad i ditt datacenter 

#### <a name="resolutions"></a>Lösningar
1. Reonboard till Log Analytics-tjänsten med OMS-Agent för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. Detta gör att utförliga utdata av agenten ansluter via proxy till OMS-tjänsten. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Läs avsnittet [konfigurera agenten för användning med en proxyserver eller OMS Gateway](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) att verifiera att du har konfigurerat agenten för kommunikation via en proxyserver korrekt.    
* Kontrollera att följande slutpunkter för Log Analytics-tjänsten är godkända:

    |Agentresurs| Portar |  
    |------|---------|  
    |*.ods.opinsights.azure.com | Port 443|   
    |*.oms.opinsights.azure.com | Port 443|   
    |ods.systemcenteradvisor.com | Port 443|   
    |*.BLOB.Core.Windows.NET/ | Port 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: Du får ett 403-fel vid försök att publicera

#### <a name="probable-causes"></a>Troliga orsaker
* Datum och tid är felaktiga på Linux-Server 
* Arbetsyte-ID och Arbetsytenyckel som används är inte korrekt

#### <a name="resolution"></a>Lösning

1. Kontrollera tiden på Linux-servern med kommandot datum. Om tiden är +/-15 minuter från aktuell tid, misslyckas onboarding. Till rätt detta Uppdatera datum och/eller tidszonen för Linux-servern. 
2. Kontrollera att du har installerat den senaste versionen av OMS-Agent för Linux.  Den senaste versionen nu meddelas du om tidsförskjutningsintervallet orsakar onboarding felet.
3. Reonboard med rätt arbetsyte-ID och Arbetsytenyckel följa installationsanvisningarna tidigare i det här avsnittet.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: Du ser ett 500 och 404-fel i loggfilen direkt efter onboarding
Detta är ett känt problem som uppstår på första uppladdning av Linux-data i logganalys-arbetsytan. Detta påverkar inte data som skickas eller tjänsten erfarenhet.

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Problem: Du inte ser några data i Azure-portalen

#### <a name="probable-causes"></a>Troliga orsaker

- Det gick inte att Onboarding till Log Analytics-tjänsten
- Anslutningen till Log Analytics-tjänsten är blockerad
- OMS-Agent för Linux data säkerhetskopieras

#### <a name="resolutions"></a>Lösningar
1. Kontrollera om onboarding Log Analytics-tjänsten lyckades genom att kontrollera om det finns följande fil:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard med hjälp av den `omsadmin.sh` kommandoradsinstruktioner
3. Om du använder en proxyserver, referera till proxy upplösning stegen ovan.
4. I vissa fall när OMS-Agent för Linux inte kan kommunicera med tjänsten, data på agenten är i kö på den fullständiga buffertstorleken, vilket är 50 MB. OMS-Agent för Linux ska startas om genom att köra följande kommando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Det här problemet har lösts i agenten version 1.1.0-28 och senare.

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>Problem: OMSAgent skapar många användarprocessen på datorn och avslutar aldrig dem
När du aktiverar hanteringslösningar som stöd för hantering av din virtuella Linux-datorer, initierar ett antal processer på Linux-agenten. Men innan processen avslutas, en annan process har startats på grund av ett känt problem. 

#### <a name="resolutions"></a>Lösningar
Om du vill ändra antalet användarprocesser som genereras av OMSAgent, kan du konfigurera agenten med omsadmin.sh.  Antal processer som skapas som standard är 75 och innan du ändrar gränsen, bör du först köra följande kommando för att se hur många OMSAgent processer körs: `ps aux | grep -E '^omsagent' | wc -l`.  
Du kan kontrollera vad den aktuella gränsen är inställd på genom att köra följande kommando:`cat /etc/security/limits.conf | grep -E '^omsagent'`

Använd följande kommandon och antingen konfigurera en egen process gräns eller ange processgränsen tillbaka till standardvärdet.

1. Ange processgränsen för OMSAgent: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`.<br>Observera att det lägsta tillåtna värdet kan ställas in till 5.  

2. Du återställer processgränsen för OMSAgent till standardvärdet:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

Kontrollera att den nya inställningen har tillämpats genom att köra följande kommando: `cat /etc/security/limits.conf | grep -E '^omsagent'`.  Om du inte ser den nya konfigurationen tillämpas kanske eftersom du ange processgränsen för lågt.  
