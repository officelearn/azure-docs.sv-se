---
title: Ansluta hybrid datorer till Azure från Azure Portal
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter datorer till Azure med hjälp av Azure Arc for Servers (för hands version) från Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366373"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ansluta hybrid datorer till Azure från Azure Portal

Du kan aktivera Azure Arc for Servers (för hands version) för ett eller ett litet antal Windows-eller Linux-datorer i din miljö genom att utföra en uppsättning steg manuellt. Du kan också använda en automatiserad metod genom att köra ett mall-skript som vi tillhandahåller. Det här skriptet automatiserar nedladdning och installation av båda agenterna.

Den här metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, är du medlem i den lokala gruppen Administratörer.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](overview.md#prerequisites) resurser uppfyller kraven.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generera installations skriptet från Azure Portal

Skriptet för att automatisera nedladdningen och installationen, och för att upprätta anslutningen till Azure Arc, är tillgängligt från Azure Portal. Slutför processen genom att göra följande:

1. Gå till [Azure Portal](https://aka.ms/hybridmachineportal)i webbläsaren.

1. På sidan **datorer – Azure-båge** väljer du antingen **Lägg till**, längst upp till vänster eller alternativet **skapa dator-Azure-båge** längst ned i fönstret i mitten. 

1. På sidan **Välj en metod** markerar du rutan **Lägg till datorer med interaktiv skript** och väljer sedan **skapa skript**.

1. På sidan **skapa skript** väljer du den prenumeration och resurs grupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras.

    >[!NOTE]
    >Azure-båge för servrar (för hands version) stöder endast följande regioner:
    >- WestUS2
    >- Västeuropa
    >- WestAsia
    >
    >Granska ytterligare överväganden när du väljer en region [här](overview.md#supported-regions) i översikts artikeln.

1. På sidan **generera skript** i list rutan **operativ system** väljer du det operativ system som skriptet ska köras på.

1. Om datorn kommunicerar via en proxyserver för att ansluta till Internet väljer du **Nästa: proxyserver**. 
1. På fliken **proxyserver** anger du IP-adressen för proxyservern eller namnet och port numret som datorn ska använda för att kommunicera med proxyservern. Ange värdet i formatet `http://<proxyURL>:<proxyport>`. 
1. Välj **Granska + generera**.

1. På fliken **Granska och skapa** granskar du sammanfattnings informationen och väljer sedan **Hämta**. Om du fortfarande behöver göra ändringar väljer du **föregående**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installera och verifiera agenten i Windows

### <a name="install-manually"></a>Installera manuellt

Du kan installera den anslutna dator agenten manuellt genom att köra Windows Installer-paketet *AzureConnectedMachineAgent. msi*. 

> [!NOTE]
> * Du måste ha *Administratörs* behörighet för att installera eller avinstallera agenten.
> * Du måste först hämta och kopiera installations paketet till en mapp på mål servern eller från en delad nätverksmapp. Om du kör installations paketet utan några alternativ startas en installations guide som du kan följa för att installera agenten interaktivt.

Om datorn behöver kommunicera via en proxyserver till tjänsten måste du köra ett kommando som beskrivs senare i artikeln när du har installerat agenten. Detta anger proxyserverns system miljö variabel `https_proxy`.

Om du inte känner till kommando rads alternativen för Windows Installer-paket kan du läsa mer om kommando rads alternativ i [msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) och [kommando rads alternativ i msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Du kan till exempel köra installations programmet med `/?` parametern för att granska alternativet hjälp och snabb referens. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Kör följande kommando för att installera agenten tyst och skapa en installations logg `C:\Support\Logs` fil i mappen.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Filer för den anslutna dator agenten installeras som standard i *C:\Program Files\AzureConnectedMachineAgent*. Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Installera med den skriptbaserade metoden

1. Logga in på servern.

1. Öppna en upphöjd PowerShell-kommandotolk.

1. Ändra till den mapp eller resurs som du kopierade skriptet till och kör det på servern genom att `./OnboardingScript.ps1` köra skriptet.

### <a name="configure-the-agent-proxy-setting"></a>Konfigurera agentens proxyinställningar

Kör följande kommando för att ange miljövariabeln för proxyservern:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>Agenten har inte stöd för att ställa in proxyautentisering i den här för hands versionen.
>

### <a name="configure-agent-communication"></a>Konfigurera agent kommunikation

När du har installerat agenten måste du konfigurera agenten att kommunicera med Azure Arc-tjänsten genom att köra följande kommando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Installera och verifiera agenten på Linux

Den anslutna dator agenten för Linux finns i det önskade paket formatet för distributionen (. RPM eller. DEB) som finns i Microsoft- [paketets lagrings plats](https://packages.microsoft.com/). [Gränssnitts skript paketet `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) utför följande åtgärder:

- Konfigurerar värd datorn för att ladda ned agent paketet från packages.microsoft.com.
- Installerar hybrid resurs leverantörs paketet.

Alternativt kan du konfigurera agenten med din proxyinformation genom att inkludera `--proxy "{proxy-url}:{proxy-port}"` parametern.

Skriptet innehåller också logik för att identifiera de distributioner som stöds och som inte stöds, och det verifierar de behörigheter som krävs för att utföra installationen. 

I följande exempel hämtas agenten och installeras:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Kör följande kommandon för att ladda ned och installera `--proxy` agenten, inklusive parametern för att konfigurera agenten för att kommunicera via proxyservern:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>Konfigurera agent kommunikation

När du har installerat agenten konfigurerar du den för att kommunicera med Azure Arc-tjänsten genom att köra följande kommando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Kontrol lera anslutningen till Azure-bågen

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc for Servers (för hands version) går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure Portal](https://aka.ms/hybridmachineportal).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)och mycket mer.

- Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).
