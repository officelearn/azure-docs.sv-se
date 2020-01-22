---
title: Anslut Linux-datorer till Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du ansluter Linux-datorer som finns i andra moln eller lokalt för att Azure Monitor med Log Analytics-agenten för Linux.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 15334f0c58f602a2728e3daa6645b957dfcd7129
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290333"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Anslut Linux-datorer till Azure Monitor

För att kunna övervaka och hantera virtuella datorer eller fysiska datorer i ditt lokala data Center eller i annan moln miljö med Azure Monitor måste du distribuera Log Analytics agent och konfigurera den så att den rapporterar till en Log Analytics-arbetsyta. Agenten stöder också Hybrid Runbook Worker-rollen för Azure Automation.

Log Analytics-agenten för Linux kan installeras med någon av följande metoder. Information om hur du använder varje metod finns längre fram i artikeln.

* [Hämta och installera](#install-the-agent-manually) agenten manuellt. Detta krävs när Linux-datorn inte har åtkomst till Internet och kommer att kommunicera med Azure Monitor eller Azure Automation via [Log Analytics Gateway](gateway.md). 
* [Installera agenten för Linux med hjälp av ett omslutnings skript som](#install-the-agent-using-wrapper-script) finns på GitHub. Detta är den rekommenderade metoden för att installera och uppgradera agenten när datorn har anslutning till Internet, direkt eller via en proxyserver.

Om du vill förstå konfigurationen som stöds, så granska [de Linux-operativsystem som stöds](log-analytics-agent.md#supported-linux-operating-systems) och [nätverkets brandväggskonfiguration](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>Log Analytics-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics-arbetsyta. Den kan bara konfigureras att rapportera till både en System Center Operations Manager hanterings grupp och Log Analytics arbets yta samtidigt, eller för någon enskild.

## <a name="agent-install-package"></a>Agent installations paket

Log Analytics agenten för Linux består av flera paket. Versions filen innehåller följande paket, som är tillgängliga genom att köra Shell-paketet med parametern `--extract`:

**Paketfilerna** | **Version** | **Beskrivning**
----------- | ----------- | --------------
omsagent | 1.12.15 | Log Analytics agent för Linux
omsconfig | 1.1.1 | Konfigurations agent för Log Analytics agent
OMI | 1.6.3 | Open Management Infrastructure (OMI) – en Lightweight CIM-server. *Observera att OMI kräver rot åtkomst för att köra ett cron-jobb som krävs för att tjänsten ska fungera*
SCX | 1.6.3 | OMI CIM-providers för prestanda mått för operativ system
Apache-cimprov | 1.0.1 | Apache HTTP server Performance Monitoring Provider för OMI. Installeras endast om Apache HTTP-servern identifieras.
MySQL-cimprov | 1.0.1 | Provider för prestanda övervakning av MySQL-server för OMI. Installeras endast om MySQL/MariaDB-servern har identifierats.
Docker-cimprov | 1.0.0 | Docker-Provider för OMI. Installeras endast om Docker har identifierats.

### <a name="agent-installation-details"></a>Information om agent installation

När du har installerat Log Analytics agent för Linux-paket tillämpas följande ytterligare konfigurations ändringar i hela systemet. Dessa artefakter tas bort när omsagent-paketet avinstalleras.

* En icke-privilegie rad användare med namnet: `omsagent` skapas. Daemonen körs under denna autentiseringsuppgift. 
* En sudoers *include* -fil skapas i `/etc/sudoers.d/omsagent`. Detta tillåter `omsagent` att starta om syslog-och omsagent-daemonarna. Om sudo *include* -direktiv inte stöds i den installerade versionen av sudo skrivs dessa poster till `/etc/sudoers`.
* Syslog-konfigurationen ändras för att vidarebefordra en delmängd av händelser till agenten. Mer information finns i [Konfigurera syslog-datainsamling](data-sources-syslog.md).

På en övervakad Linux-dator anges agenten som `omsagent`. `omsconfig` är Log Analytics agent för konfigurations agenten för Linux som söker efter ny portal konfiguration var 5: e minut. Den nya och uppdaterade konfigurationen tillämpas på agentens konfigurationsfiler som finns på `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans id och nyckel

Innan du installerar Log Analytics-agenten för Linux behöver du arbetsytans id och nyckel för Log Analytics-arbetsytan. Den här informationen krävs under installationen av agenten för att konfigurera den korrekt och se till att den kan kommunicera med Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. I det övre vänstra hörnet av Azure Portal väljer du **alla tjänster**. I rutan Sök anger du **Log Analytics**. När du skriver filtreras listan baserat på dina inaktuella inaktuella inaktuella inaktuella. Välj **Log Analytics arbets ytor**.

2. I listan med Log Analytics arbets ytor väljer du den arbets yta som du skapade tidigare. (Du kan ha namngett det **DefaultLAWorkspace**.)

3. Välj **Avancerade inställningar**:

    ![Menyn Avancerade inställningar för Log Analytics i Azure Portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Välj **Anslutna källor** och välj sedan **Linux-servrar**.

5. Värdet till höger om **Id för arbetsyta** och **Primär nyckel**. Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

## <a name="install-the-agent-manually"></a>Installera agenten manuellt

Log Analytics agenten för Linux finns i ett självextraherande och installerbart gränssnitts skript paket. Paketet innehåller Debian och RPM-paket för varje agent komponent och kan installeras direkt eller extraheras för att hämta de enskilda paketen. Ett paket tillhandahålls för x64 och ett för x86-arkitekturer. 

> [!NOTE]
> För virtuella Azure-datorer rekommenderar vi att du installerar agenten på dem med hjälp av [Azure Log Analytics VM-tillägget](../../virtual-machines/extensions/oms-linux.md) för Linux. 

1. [Hämta](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) och överför lämpligt paket (x64 eller x86) till din virtuella Linux-dator eller fysiska dator med hjälp av SCP/SFTP.

2. Installera paketet med hjälp av argumentet `--install`. Om du vill publicera till en Log Analytics arbets yta under installationen anger du de `-w <WorkspaceID>`-och `-s <workspaceKey>`-parametrarna som du kopierade tidigare.

    >[!NOTE]
    >Du måste använda `--upgrade` argumentet om ett beroende paket, till exempel OMI, SCX, omsconfig eller deras äldre versioner är installerade, som skulle vara fallet om System Center Operations Manager-agenten för Linux redan är installerad. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Om du vill konfigurera Linux-agenten att installera och ansluta till en Log Analytics-arbetsyta via en Log Analytics Gateway kör du följande kommando som anger proxy, arbetsyte-ID och arbets ytans nyckel parametrar. Den här konfigurationen kan anges på kommando raden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]`. Egenskapen *ProxyHost* accepterar ett fullständigt kvalificerat domän namn eller en IP-adress för Log Analytics Gateway-servern.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Om autentisering krävs måste du ange användar namn och lösen ord. Ett exempel: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Om du vill konfigurera Linux-datorn för att ansluta till en Log Analytics arbets yta i Azure Government molnet kör du följande kommando som tillhandahåller arbetsyte-ID och primär nyckel som kopierats tidigare.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Om du vill installera agent paketen och konfigurera det att rapportera till en speciell Log Analytics arbets yta vid ett senare tillfälle kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Om du vill extrahera agent paketen från paketet utan att installera agenten kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Installera agenten med hjälp av omslutnings skript

Följande steg konfigurerar installationen av agenten för Log Analytics i Azure och Azure Government molnet med hjälp av omslutnings skriptet för Linux-datorer som kan kommunicera direkt eller via en proxyserver för att ladda ned agenten som finns på GitHub och installera agenten.  

Om Linux-datorn behöver kommunicera via en proxyserver till Log Analytics kan du ange den här konfigurationen på kommando raden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]`. *Protokoll* egenskapen accepterar `http` eller `https`och *ProxyHost* -egenskapen accepterar ett fullständigt kvalificerat domän namn eller en IP-adress för proxyservern. 

Exempel: `https://proxy01.contoso.com:30443`

Om autentisering krävs i båda fallen måste du ange användar namn och lösen ord. Exempel: `https://user01:password@proxy01.contoso.com:30443`

1. Kör följande kommando för att ange arbetsyte-ID och primär nyckel för att konfigurera Linux-datorn för att ansluta till en Log Analytics-arbetsyta. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller parametern `-p` proxy och exempel på syntax när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn så att den ansluter till Log Analytics arbets yta i Azure Government molnet kör du följande kommando som tillhandahåller arbetsyte-ID och primär nyckel som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller parametern `-p` proxy och exempel på syntax när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version

Att uppgradera från en tidigare version, från och med version 1.0.0-47, stöds i varje version. Utför installationen med parametern `--upgrade` för att uppgradera alla komponenter i agenten till den senaste versionen.

## <a name="next-steps"></a>Nästa steg

- Granska [hantering och underhåll av Log Analytics agent för Windows och Linux](agent-manage.md) för att lära dig hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [fel sökning av Linux-agenten](agent-linux-troubleshoot.md) om det uppstår problem när du installerar eller hanterar agenten.
