---
title: Linux-datorer ansluta till Operations Management Suite (OMS) | Microsoft Docs
description: "Den här artikeln beskriver hur du ansluter Linux-datorer i Azure, andra molntjänster eller lokala till OMS med hjälp av OMS-Agent för Linux."
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
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c9902e1b8644c2b0a894f9cde98f2056564775c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>Linux-datorer ansluta till Operations Management Suite (OMS) 

Med Microsoft Operations Management Suite (OMS), som du kan samla in och arbeta med data som genereras från Linux-datorer och lösningar för behållare som Docker, som finns i ditt lokala datacenter som fysiska servrar eller virtuella datorer, virtuella datorer i en molnbaserad tjänst som Amazon Web Services (AWS) eller Microsoft Azure. Du kan också använda hanteringslösningar som är tillgängliga i OMS, till exempel spårning av ändringar för att identifiera ändringar i konfiguration och hantering av att hantera programuppdateringar för att proaktivt hantera livscykeln för din virtuella Linux-datorer. 

OMS-Agent för Linux kommunicerar utgående med OMS-tjänsten via TCP-port 443 och om datorn är ansluten till en brandvägg eller proxyserver server för att kommunicera över Internet, granska [konfigurera agenten för användning med en HTTP-proxyserver eller OMS Gateway](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway) att förstå vilka konfigurationsändringar kommer måste tillämpas.  Om du övervakar datorn med System Center 2016 - Operations Manager eller Operations Manager 2012 R2 kan det vara multi-homed OMS-tjänsten för att samla in data och vidarebefordra till tjänsten och fortfarande övervakas av Operations Manager.  Linux-datorer som övervakas av en Operations Manager-hanteringsgrupp som är integrerad med OMS får inte konfigurationen för datakällor och vidarebefordra insamlade data till hanteringsgruppen.  OMS-agenten kan inte konfigureras för att rapportera till mer än en arbetsyta.  

Om din IT-säkerhetsprinciper inte tillåter datorer i nätverket för att ansluta till Internet, kan agenten konfigureras för att ansluta till OMS-Gateway för att ta emot information om konfiguration och skicka insamlade data beroende på lösningen som du har aktiverat. Mer information och anvisningar om hur du konfigurerar OMS Linux-agenten kan kommunicera via en OMS-Gateway till OMS-tjänsten finns [ansluta datorer till OMS med OMS-gatewayen](log-analytics-oms-gateway.md).  

Följande diagram visar anslutningen mellan Linux-datorer som hanteras med agent och OMS, inklusive riktning och portar.

![direkt agentkommunikation med OMS-diagram](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

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
Informationen nedan lista över proxy- och brandväggsinställningarna configuration information som krävs för Linux-agenten kan kommunicera med OMS. Trafiken är utgående från nätverket till OMS-tjänsten. 

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
omsagent | 1.4.1 | Operations Management Suite-agenten för Linux
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

* En icke-privilegierade användare med namnet: `omsagent` skapas. Daemonen omsagent körs som det här kontot.
* En sudoers ”innehåller” filen skapas vid /etc/sudoers.d/omsagent. Den här filen auktoriserar omsagent att starta om daemon för syslog- och omsagent. Om sudo ”” Includes inte stöds i den installerade versionen av sudo, skrivs dessa poster till /etc/sudoers.
* Syslog-konfigurationen har ändrats för att vidarebefordra en delmängd av händelser till agenten. Mer information finns i **konfigurera datainsamling** nedan.

### <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version
Uppgradera från versioner tidigare än 1.0.0-47 stöds i den här versionen. Utför installationen med de `--upgrade` kommandot uppgraderar alla komponenter i agenten till den senaste versionen.

## <a name="installing-the-agent"></a>Installerar agenten

Det här avsnittet beskrivs hur du installerar OMS-Agent för Linux med en bunndle som innehåller Debian och RPM-paket för varje agent-komponenter.  Den kan installeras direkt eller extraheras för att hämta enskilda paket.  

Du måste först ditt OMS arbetsyte-ID och nyckel som du hittar genom att växla till den [klassiska OMS-portalen](https://mms.microsoft.com).  På den **översikt** sida från huvudmenyn väljer **inställningar**, och gå sedan till **anslutna servrar Sources\Linux**.  Du ser värdet till höger om **arbetsyte-ID** och **primärnyckel**.  Kopiera och klistra in både i din favorit-redigeraren.    

1. Ladda ned senaste [OMS-Agent för Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x64.sh) eller [OMS-Agent för Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x86.sh) från GitHub.  
2. Över rätt paket (x86 eller x64) till Linux-datorn med scp/sftp.
3. Installera paketet med hjälp av den `--install` eller `--upgrade` argumentet. 

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
OMS-Agent för Linux stöder kommunikation via en proxyserver eller OMS Gateway till OMS-tjänsten med hjälp av HTTPS-protokollet.  Både anonyma och grundläggande autentisering (användarnamn/lösenord) stöds.  

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

## <a name="onboarding-with-operations-management-suite"></a>Onboarding med Operations Management Suite
Om ett arbetsyte-ID och nyckel inte angavs under installationen av paketet, måste agenten registreras senare med Operations Management Suite.

### <a name="onboarding-using-the-command-line"></a>Onboarding med hjälp av kommandoraden
Kör kommandot omsadmin.sh tillhandahåller arbetsyte-ID och nyckel för din arbetsyta. Det här kommandot måste köras som rot (med sudo-höjning):
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>Onboarding med hjälp av en fil
1.  Skapa filen `/etc/omsagent-onboard.conf`. Filen måste vara Läs- och skrivbara för roten.
`sudo vi /etc/omsagent-onboard.conf`
2.  Infoga följande rader i filen med ditt arbetsyte-ID och delad nyckel:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  Kör följande kommando för att Onboard till OMS:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
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

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Problem: Det gick inte att ansluta via proxy till OMS

#### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som anges under onboarding var felaktigt
* OMS Tjänsteslutpunkter är inte whitelistested i ditt datacenter 

#### <a name="resolutions"></a>Lösningar
1. Reonboard till OMS-tjänsten med OMS-Agent för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. Den här settubg kan utförlig utdata av agenten ansluter via proxy till OMS-tjänsten. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Läs avsnittet [konfigurera agenten för användning med en proxyserver eller OMS Gateway](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) att verifiera att du har konfigurerat agenten för kommunikation via en proxyserver korrekt.    
* Kontrollera att följande slutpunkter i OMS-tjänsten är godkända:

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
Det här felet är ett känt problem som uppstår på första uppladdning av Linux-data i en OMS-arbetsyta. Detta fel påverkar inte data som skickas eller tjänsten erfarenhet.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>Problem: Du inte ser några data i OMS-portalen

#### <a name="probable-causes"></a>Troliga orsaker

- Det gick inte att Onboarding till OMS-tjänsten
- Anslutningen till OMS-tjänsten är blockerad
- OMS-Agent för Linux data säkerhetskopieras

#### <a name="resolutions"></a>Lösningar
1. Kontrollera om onboarding OMS-tjänsten lyckades genom att kontrollera om det finns följande fil:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard med hjälp av den `omsadmin.sh` kommandoradsinstruktioner
3. Om du använder en proxyserver, referera till proxy upplösning stegen ovan.
4. I vissa fall när OMS-Agent för Linux inte kan kommunicera med tjänsten OMS data på agenten är i kö på den fullständiga buffertstorleken, vilket är 50 MB. OMS-Agent för Linux ska startas om genom att köra följande kommando: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Det här problemet har lösts i agenten version 1.1.0-28 och senare.

