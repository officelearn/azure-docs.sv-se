---
title: Ansluta Linux-datorer till Azure Monitor | Microsoft-dokument
description: I den här artikeln beskrivs hur du ansluter Linux-datorer som finns i andra moln eller lokalt till Azure Monitor med Log Analytics-agenten för Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637526"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Ansluta Linux-datorer till Azure Monitor

För att övervaka och hantera virtuella datorer eller fysiska datorer i ditt lokala datacenter eller annan molnmiljö med Azure Monitor måste du distribuera Log Analytics-agenten och konfigurera den för att rapportera till en Log Analytics-arbetsyta. Agenten stöder också hybridkörningsarbetsrollen för Azure Automation.

Log Analytics-agenten för Linux kan installeras med någon av följande metoder. Information om hur du använder varje metod finns senare i artikeln.

* [Hämta och installera](#install-the-agent-manually) agenten manuellt. Detta krävs när Linux-datorn inte har tillgång till Internet och kommer att kommunicera med Azure Monitor eller Azure Automation via [Log Analytics-gatewayen](gateway.md). 
* [Installera agenten för Linux med hjälp av ett omslagsskript](#install-the-agent-using-wrapper-script) som finns på GitHub. Detta är den rekommenderade metoden för att installera och uppgradera agenten när datorn har anslutning till Internet, direkt eller via en proxyserver.

Om du vill förstå konfigurationen som stöds, så granska [de Linux-operativsystem som stöds](log-analytics-agent.md#supported-linux-operating-systems) och [nätverkets brandväggskonfiguration](log-analytics-agent.md#network-requirements).

>[!NOTE]
>Log Analytics-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics-arbetsyta. Den kan bara konfigureras för att rapportera till både en Hanteringsgrupp för System Center Operations Manager och Log Analytics samtidigt, eller till antingen individuellt.

## <a name="agent-install-package"></a>Installationspaket för agent

Log Analytics-agenten för Linux består av flera paket. Utgivningsfilen innehåller följande paket, som är tillgängliga genom `--extract` att köra skalpaketet med parametern:

**Paket** | **Version** | **Beskrivning**
----------- | ----------- | --------------
omsagent (olikartade) | 1.12.15 | Log Analytics Agent för Linux
omsconfig (olika) | 1.1.1 | Konfigurationsagent för Log Analytics-agenten
Omi | 1.6.3 | Open Management Infrastructure (OMI) – en lätt CIM-server. *Observera att OMI kräver root-åtkomst för att köra ett cron-jobb som krävs för att tjänsten ska fungera*
Scx | 1.6.3 | OMI CIM-leverantörer för prestandamått för operativsystemet
apache-cimprov | 1.0.1 | Apache HTTP Server prestandaövervakningsprovider för OMI. Endast installerat om Apache HTTP-server identifieras.
mysql-cimprov | 1.0.1 | MySQL Server prestandaövervakningsleverantör för OMI. Endast installerat om MySQL/MariaDB-servern identifieras.
docker-cimprov | 1.0.0 | Docker-leverantör för OMI. Endast installerat om Docker identifieras.

### <a name="agent-installation-details"></a>Information om agentinstallation

När du har installerat Log Analytics-agenten för Linux-paket tillämpas följande ytterligare konfigurationsändringar för hela systemet. Dessa artefakter tas bort när omsagentpaketet avinstalleras.

* En icke-privilegierad användare `omsagent` med namnet: skapas. Demonen körs under denna referens. 
* En sudoers *include-fil* skapas i `/etc/sudoers.d/omsagent`. Detta ger `omsagent` tillstånd att starta om syslog och omsagent daemons. Om sudo *include-direktiv* inte stöds i den installerade versionen av `/etc/sudoers`sudo skrivs dessa poster till .
* Syslog-konfigurationen ändras för att vidarebefordra en delmängd av händelser till agenten. Mer information finns i [Konfigurera Syslog-datainsamling](data-sources-syslog.md).

På en övervakad Linux-dator listas `omsagent`agenten som . `omsconfig`är Log Analytics-agenten för Linux-konfigurationsagenten som letar efter ny konfiguration på portalsidan var femte minut. Den nya och uppdaterade konfigurationen tillämpas på `/etc/opt/microsoft/omsagent/conf/omsagent.conf`agentkonfigurationsfilerna som finns på .

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans id och nyckel

Innan du installerar Log Analytics-agenten för Linux behöver du arbetsytans id och nyckel för Log Analytics-arbetsytan. Den här informationen krävs under installationen av agenten för att korrekt konfigurera den och se till att den kan kommunicera med Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. I det övre vänstra hörnet av Azure-portalen väljer du **Alla tjänster**. Skriv **Logganalys**i sökrutan . När du skriver filtrerar listan baserat på dina indata. Välj **Log Analytics-arbetsytor**.

2. Välj arbetsytan som du skapade tidigare i listan över Log Analytics-arbetsytor. (Du kanske har döpt den **till DefaultLAWorkspace**.)

3. Välj **Avancerade inställningar:**

    ![Menyn Avancerade inställningar för Logganalys i Azure-portalen](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Välj **Anslutna källor** och välj sedan **Linux-servrar**.

5. Värdet till höger om **Id för arbetsyta** och **Primär nyckel**. Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

## <a name="install-the-agent-manually"></a>Installera agenten manuellt

Log Analytics-agenten för Linux finns i ett självextraherande och installerbart skalskriptpaket. Det här paketet innehåller Debian- och RPM-paket för var och en av agentkomponenterna och kan installeras direkt eller extraheras för att hämta de enskilda paketen. Ett paket tillhandahålls för x64 och en för x86-arkitekturer. 

> [!NOTE]
> För virtuella Azure-datorer rekommenderar vi att du installerar agenten på dem med azure [log analytics-vm-tillägget](../../virtual-machines/extensions/oms-linux.md) för Linux. 

1. [Ladda ned](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) och överför lämpligt paket (x64 eller x86) till din Virtuella Linux-dator eller fysiska dator med scp/sftp.

2. Installera paketet med `--install` hjälp av argumentet. Om du vill gå in på en `-w <WorkspaceID>` Log `-s <workspaceKey>` Analytics-arbetsyta under installationen anger du parametrarna och som kopierades tidigare.

    >[!NOTE]
    >Du måste använda `--upgrade` argumentet om några beroende paket som OMI, SCX, OMSconfig eller deras äldre versioner är installerade, vilket skulle vara fallet om systemet Center Operations Manager agent för Linux redan är installerat. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Om du vill konfigurera Linux-agenten för att installera och ansluta till en Log Analytics-arbetsyta via en Log Analytics-gateway kör du följande kommando som ger parametrarna proxy, arbetsyta id och arbetsyta. Den här konfigurationen kan anges på `-p [protocol://][user:password@]proxyhost[:port]`kommandoraden genom att inkludera . Egenskapen *proxyhost* accepterar ett fullständigt kvalificerat domännamn eller IP-adress för Log Analytics gateway-servern.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Om autentisering krävs måste du ange användarnamn och lösenord. Ett exempel: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Om du vill konfigurera Linux-datorn för att ansluta till en Log Analytics-arbetsyta i Azure Government-molnet kör du följande kommando som ger arbetsytans ID och primärnyckeln som kopierats tidigare.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Om du vill installera agentpaketen och konfigurera dem för att rapportera till en viss Log Analytics-arbetsyta vid ett senare tillfälle kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Om du vill extrahera agentpaketen från paketet utan att installera agenten kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Installera agenten med hjälp av omslagsskript

Följande steg konfigurerar installationen av agenten för Log Analytics i Azure- och Azure Government-molnet med hjälp av omslagsskriptet för Linux-datorer som kan kommunicera direkt eller via en proxyserver för att hämta agenten som finns på GitHub och installera agenten.  

Om din Linux-dator behöver kommunicera via en proxyserver till Log Analytics kan `-p [protocol://][user:password@]proxyhost[:port]`den här konfigurationen anges på kommandoraden genom att inkludera . *Egenskapen protocol* `http` accepts or `https`, and the *proxyhost* property accepts a full qualified domain name or IP address of the proxy server. 

Exempel: `https://proxy01.contoso.com:30443`

Om autentisering krävs i båda fallen måste du ange användarnamn och lösenord. Exempel: `https://user01:password@proxy01.contoso.com:30443`

1. Om du vill konfigurera Linux-datorn för att ansluta till en Log Analytics-arbetsyta kör du följande kommando som ger arbetsyte-ID och primärnyckel. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller `-p` proxyparametern och exempelsyntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn för att ansluta till Log Analytics-arbetsytan i Azure Government-molnet kör du följande kommando som ger arbetsytans ID och primärnyckeln som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller `-p` proxyparametern och exempelsyntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version

Uppgradering från en tidigare version, som börjar med version 1.0.0-47, stöds i varje utgåva. Utför installationen med `--upgrade` parametern för att uppgradera alla komponenter i agenten till den senaste versionen.

## <a name="next-steps"></a>Nästa steg

- Granska [Hantera och underhålla Log Analytics-agenten för Windows och Linux om](agent-manage.md) du vill veta mer om hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [Felsökning av Linux-agenten](agent-linux-troubleshoot.md) om du stöter på problem när du installerar eller hanterar agenten.
