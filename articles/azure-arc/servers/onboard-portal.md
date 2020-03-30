---
title: Ansluta hybriddatorer till Azure från Azure-portalen
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter datorer till Azure med hjälp av Azure Arc för servrar (förhandsversion) från Azure-portalen.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366373"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ansluta hybriddatorer till Azure från Azure-portalen

Du kan aktivera Azure Arc för servrar (förhandsversion) för en eller ett litet antal Windows- eller Linux-datorer i din miljö genom att utföra en uppsättning steg manuellt. Du kan också använda en automatiserad metod genom att köra ett mallskript som vi tillhandahåller. Detta skript automatiserar nedladdning och installation av båda agenterna.

Den här metoden kräver att du har administratörsbehörighet på datorn för att installera och konfigurera agenten. På Linux, med hjälp av rotkontot och i Windows är du medlem i gruppen Lokala administratörer.

Innan du börjar bör du se till att granska [förutsättningarna](overview.md#prerequisites) och kontrollera att din prenumeration och dina resurser uppfyller kraven.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generera installationsskriptet från Azure-portalen

Skriptet för att automatisera hämtning och installation och för att upprätta anslutningen till Azure Arc är tillgängligt från Azure-portalen. Gör följande för att slutföra processen:

1. Gå till [Azure-portalen](https://aka.ms/hybridmachineportal)från webbläsaren .

1. På sidan **Datorer - Azure Arc** väljer du antingen Lägg **till**, längst upp till vänster eller alternativet Skapa dator - **Azure Arc** längst ned i mittenrutan. 

1. På sidan **Välj en metod** väljer du lägg till datorer med hjälp av interaktiv **skriptpanel** och väljer sedan **Generera skript**.

1. På sidan **Generera skript** väljer du den prenumeration och resursgrupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras.

    >[!NOTE]
    >Azure Arc för servrar (förhandsversion) stöder endast följande regioner:
    >- WestUS2 (På andra sätt)
    >- Västeuropa
    >- WestAsia (på ett år)
    >
    >Granska ytterligare överväganden när du väljer en region [här](overview.md#supported-regions) i artikeln Översikt.

1. På sidan **Generera skript** i listrutan **Operativsystemet** väljer du det operativsystem som skriptet ska köras på.

1. Om datorn kommunicerar via en proxyserver för att ansluta till internet väljer du **Nästa: Proxyserver**. 
1. På fliken **Proxyserver** anger du proxyserverns IP-adress eller det namn och portnummer som datorn ska använda för att kommunicera med proxyservern. Ange värdet i `http://<proxyURL>:<proxyport>`formatet . 
1. Välj **Granska + generera**.

1. På fliken **Granska + generera** granskar du sammanfattningsinformationen och väljer sedan **Hämta**. Om du fortfarande behöver göra ändringar väljer du **Föregående**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installera och validera agenten i Windows

### <a name="install-manually"></a>Installera manuellt

Du kan installera agenten ansluten dator manuellt genom att köra Windows Installer-paketet *AzureConnectedMachineAgent.msi*. 

> [!NOTE]
> * Om du vill installera eller avinstallera agenten måste du ha *administratörsbehörighet.*
> * Du måste först hämta och kopiera installationspaketet till en mapp på målservern eller från en delad nätverksmapp. Om du kör installationspaketet utan några alternativ startar det en installationsguide som du kan följa för att installera agenten interaktivt.

Om datorn behöver kommunicera via en proxyserver till tjänsten måste du köra ett kommando som beskrivs senare i artikeln när du har installerat agenten. Detta anger proxyserversystemets `https_proxy`miljövariabel .

Om du inte känner till kommandoradsalternativen för Windows Installer-paket läser du [Msiexecs standardkommandoradsalternativ](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) och [msiexec-kommandoradsalternativ](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Kör till exempel installationsprogrammet `/?` med parametern för att granska hjälp- och snabbreferensalternativet. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Om du vill installera agenten tyst och `C:\Support\Logs` skapa en installationsloggfil i mappen kör du följande kommando.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Filer för agenten ansluten dator installeras som standard i *C:\Program Files\AzureConnectedMachineAgent*. Om agenten inte startar när installationen är klar kontrollerar du om det finns detaljerad felinformation i loggarna. Loggkatalogen är *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Installera med skriptmetoden

1. Logga in på servern.

1. Öppna en upphöjd PowerShell-kommandotolk.

1. Ändra till mappen eller resursen som du kopierade skriptet till `./OnboardingScript.ps1` och kör det på servern genom att köra skriptet.

### <a name="configure-the-agent-proxy-setting"></a>Konfigurera proxyinställningen för agent

Om du vill ange proxyservermiljövariabeln kör du följande kommando:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agenten stöder inte inställning av proxyautentisering i den här förhandsversionen.
>

### <a name="configure-agent-communication"></a>Konfigurera agentkommunikation

När du har installerat agenten måste du konfigurera agenten så att den kommunicerar med Azure Arc-tjänsten genom att köra följande kommando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Installera och validera agenten på Linux

Connected Machine-agenten för Linux finns i det önskade paketformatet för distributionen (. RPM eller . DEB) som finns i [Microsofts paketdatabas](https://packages.microsoft.com/). [Skalskriptpaketet `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) utför följande åtgärder:

- Konfigurerar värddatorn för att hämta agentpaketet från packages.microsoft.com.
- Installerar hybridresursproviderpaketet.

Du kan också konfigurera agenten med din `--proxy "{proxy-url}:{proxy-port}"` proxyinformation genom att inkludera parametern.

Skriptet innehåller också logik för att identifiera distributioner som stöds och inte stöds, och det verifierar de behörigheter som krävs för att utföra installationen. 

I följande exempel hämtas agenten och den installeras:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Om du vill hämta och `--proxy` installera agenten, inklusive parametern för att konfigurera agenten så att den kommunicerar via proxyservern, kör du följande kommandon:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurera agentkommunikationen

När du har installerat agenten konfigurerar du den så att den kommunicerar med Azure Arc-tjänsten genom att köra följande kommando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Verifiera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc för servrar (förhandsversion) går du till Azure-portalen för att kontrollera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

![En lyckad serveranslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med [Azure Policy](../../governance/policy/overview.md), för till exempel [VM-gästkonfiguration,](../../governance/policy/concepts/guest-configuration.md)verifierar att datorn rapporterar till den förväntade Log Analytics-arbetsytan, aktiverar övervakning med [Azure Monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)och mycket mer.

- Läs mer om [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du proaktivt vill övervaka operativsystemet och arbetsbelastningar som körs på datorn, hantera det med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).
