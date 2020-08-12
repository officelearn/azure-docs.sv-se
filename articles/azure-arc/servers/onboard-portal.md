---
title: Ansluta hybrid datorer till Azure från Azure Portal
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter datorer till Azure med hjälp av Azure Arc for Servers (för hands version) från Azure Portal.
ms.date: 08/07/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 08f2563aaa67cdd45760af34ef3bef3e8e472ae7
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120991"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ansluta hybrid datorer till Azure från Azure Portal

Du kan aktivera Azure Arc for Servers (för hands version) för ett eller ett litet antal Windows-eller Linux-datorer i din miljö genom att utföra en uppsättning steg manuellt. Du kan också använda en automatiserad metod genom att köra ett mall-skript som vi tillhandahåller. Det här skriptet automatiserar nedladdning och installation av båda agenterna.

Den här metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, är du medlem i den lokala gruppen Administratörer.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](agent-overview.md#prerequisites) resurser uppfyller kraven.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generera installations skriptet från Azure Portal

Skriptet för att automatisera nedladdningen och installationen, och för att upprätta anslutningen till Azure Arc, är tillgängligt från Azure Portal. Slutför processen genom att göra följande:

1. Gå till [Azure Portal](https://aka.ms/hybridmachineportal)i webbläsaren.

1. På sidan **datorer – Azure-båge** väljer du antingen **Lägg till**, längst upp till vänster eller alternativet **skapa dator-Azure-båge** längst ned i fönstret i mitten.

1. På sidan **Välj en metod** markerar du rutan **Lägg till datorer med interaktiv skript** och väljer sedan **skapa skript**.

1. På sidan **skapa skript** väljer du den prenumeration och resurs grupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras.

    >[!NOTE]
    >Azure-båge för servrar (för hands version) stöder endast följande regioner:
    >- Platsen eastus
    >- WestUS2
    >- Västeuropa
    >- Sydostasien
    >
    >Granska ytterligare överväganden när du väljer en region [här](overview.md#supported-regions) i översikts artikeln.

1. På sidan **generera skript** i list rutan **operativ system** väljer du det operativ system som skriptet ska köras på.

1. Om datorn kommunicerar via en proxyserver för att ansluta till Internet väljer du **Nästa: proxyserver**.

1. På fliken **proxyserver** anger du IP-adressen för proxyservern eller namnet och port numret som datorn ska använda för att kommunicera med proxyservern. Ange värdet i formatet `http://<proxyURL>:<proxyport>` .

1. Välj **Granska + generera**.

1. På fliken **Granska och skapa** granskar du sammanfattnings informationen och väljer sedan **Hämta**. Om du fortfarande behöver göra ändringar väljer du **föregående**.

## <a name="install-and-validate-the-agent-on-windows"></a>Installera och verifiera agenten i Windows

### <a name="install-manually"></a>Installera manuellt

Du kan installera den anslutna dator agenten manuellt genom att köra Windows Installer-paketet *AzureConnectedMachineAgent.msi*. Du kan ladda ned den senaste versionen av [Windows agent Windows Installer-paketet](https://aka.ms/AzureConnectedMachineAgent) från Microsoft Download Center.

>[!NOTE]
>* Du måste ha *Administratörs* behörighet för att installera eller avinstallera agenten.
>* Du måste först hämta och kopiera installations paketet till en mapp på mål servern eller från en delad nätverksmapp. Om du kör installations paketet utan några alternativ startas en installations guide som du kan följa för att installera agenten interaktivt.

Om datorn behöver kommunicera via en proxyserver till tjänsten måste du köra ett kommando som beskrivs i stegen nedan när du har installerat agenten. Det här kommandot anger proxy-serverns system miljö variabel `https_proxy` .

Om du inte känner till kommando rads alternativen för Windows Installer-paket kan du läsa mer om kommando rads alternativ i [msiexec](/windows/win32/msi/standard-installer-command-line-options) och [kommando rads alternativ i msiexec](/windows/win32/msi/command-line-options).

Du kan till exempel köra installations programmet med `/?` parametern för att granska alternativet hjälp och snabb referens.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Kör följande kommando för att installera agenten tyst och skapa en installations logg fil i den `C:\Support\Logs` mapp som finns.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

2. Om datorn behöver kommunicera via en proxyserver, så kör du följande kommando för att ange miljövariabeln för proxyservern:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >Agenten har inte stöd för att ställa in proxyautentisering i den här för hands versionen.
    >

3. När du har installerat agenten behöver du konfigurera den så att den kommunicerar med Azure Arc-tjänsten genom att köra följande kommando:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Installera med den skriptbaserade metoden

1. Logga in på servern.

1. Öppna en upphöjd PowerShell-kommandotolk.

    >[!NOTE]
    >Skriptet stöder bara körning från en 64-bitars version av Windows PowerShell.
    >

1. Ändra till den mapp eller resurs som du kopierade skriptet till och kör det på servern genom att köra `./OnboardingScript.ps1` skriptet.

Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

## <a name="install-and-validate-the-agent-on-linux"></a>Installera och verifiera agenten på Linux

Den anslutna dator agenten för Linux finns i det önskade paket formatet för distributionen (. RPM eller. DEB) som finns i Microsoft- [paketets lagrings plats](https://packages.microsoft.com/). [Gränssnitts skript paketet `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) utför följande åtgärder:

- Konfigurerar värd datorn för att ladda ned agent paketet från packages.microsoft.com.
- Installerar hybrid resurs leverantörs paketet.
- Registrerar datorn med Azure Arc

Alternativt kan du konfigurera agenten med din proxyinformation genom att inkludera `--proxy "{proxy-url}:{proxy-port}"` parametern.

Skriptet innehåller också logik för att identifiera de distributioner som stöds och som inte stöds, och det verifierar de behörigheter som krävs för att utföra installationen.

I följande exempel hämtas agenten och installeras:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

Kör följande kommandon för att ladda ned och installera agenten, inklusive `--proxy` parametern för att konfigurera agenten för att kommunicera via proxyservern:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc for Servers (för hands version) går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-policy.md)och mycket mer.

- Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).