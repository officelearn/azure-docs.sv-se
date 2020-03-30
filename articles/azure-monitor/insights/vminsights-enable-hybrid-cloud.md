---
title: Aktivera Azure Monitor för en hybridmiljö | Microsoft-dokument
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för en hybridmolnmiljö som innehåller en eller flera virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480750"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Aktivera Azure Monitor för virtuella datorer för en hybridmiljö

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer eller fysiska datorer som finns i ditt datacenter eller annan molnmiljö. I slutet av den här processen har du framgångsrikt börjat övervaka dina virtuella datorer i din miljö och lära dig om de upplever några prestanda- eller tillgänglighetsproblem.

Innan du börjar bör du se till att granska [förutsättningarna](vminsights-enable-overview.md) och kontrollera att din prenumeration och dina resurser uppfyller kraven. Granska kraven och distributionsmetoderna för [Log Analytics Linux och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor för virtuella datorer Map Dependency agent överför inte några data själv, och det kräver inga ändringar i brandväggar eller portar. Kartdata överförs alltid av Log Analytics-agenten till Azure Monitor-tjänsten, antingen direkt eller via [Operations Management Suite-gatewayen](../../azure-monitor/platform/gateway.md) om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till internet.

Stegen för att slutföra den här uppgiften sammanfattas på följande sätt:

1. Installera Log Analytics-agent för Windows eller Linux. Innan du installerar agenten kan du läsa översiktsartikeln [log Analytics-agent](../platform/log-analytics-agent.md) för att förstå systemförutsättningar och distributionsmetoder.

2. Hämta och installera Azure Monitor för virtuella datorer Map Dependency Agent för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).

3. Aktivera insamling av prestandaräknare.

4. Distribuera Azure Monitor för virtuella datorer.

>[!NOTE]
>Informationen som beskrivs i den här artikeln för distribution av beroendeagenten gäller också för [servicemappningslösningen](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten i Windows

Du kan installera beroendeagenten manuellt på `InstallDependencyAgent-Windows.exe`Windows-datorer genom att köra . Om du kör den här körbara filen utan några alternativ startar den en installationsguide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administrator* Administratörsbehörighet krävs för att installera eller avinstallera agenten.

I följande tabell visas de parametrar som stöds av inställningar för agenten från kommandoraden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativen. |
| / S | Utför en tyst installation utan användarinteraktion. |

Om du till exempel vill `/?` köra installationsprogrammet med parametern anger du **InstallDependencyAgent-Windows.exe /?**.

Filer för Windows Dependency-agenten installeras som standard i *C:\Program\Microsoft Dependency Agent.* Om beroendeagenten inte startar när installationen är klar kontrollerar du om det finns detaljerad felinformation i loggarna. Loggkatalogen är *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux

Beroendeagenten är installerad på Linux-servrar från *InstallDependencyAgent-Linux64.bin*, ett skalskript med en självextraherande binär. Du kan köra filen `sh` med hjälp av eller lägga till körningsbehörigheter i själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
>

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| --check | Kontrollera behörigheter och operativsystemet, men installera inte agenten. |

Om du till exempel vill `-help` köra installationsprogrammet med parametern anger du **InstallDependencyAgent-Linux64.bin -help**.

Installera Linux Dependency Agent som root `sh InstallDependencyAgent-Linux64.bin`genom att köra kommandot .

Om beroendeagenten inte startar kontrollerar du om det finns detaljerad felinformation i loggarna. På Linux-agenter är loggkatalogen */var/opt/microsoft/dependency-agent/log*.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Location |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exempel på installationsskript

För att enkelt distribuera beroendeagenten på många servrar samtidigt, ges följande skriptexempel för att hämta och installera beroendeagenten på antingen Windows eller Linux.

### <a name="powershell-script-for-windows"></a>PowerShell-skript för Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Kommandoskript för Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Önskad tillståndskonfiguration

Om du vill distribuera beroendeagenten med DSC (Desired State Configuration) kan du använda modulen xPSDesiredStateConfiguration med följande exempelkod:

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Aktivera prestandaräknare

Om arbetsytan Log Analytics som refereras av lösningen inte redan är konfigurerad för att samla in de prestandaräknare som krävs av lösningen måste du aktivera dem. Du kan göra det på ett av två sätt:
* Manuellt, enligt beskrivningen i [Windows och Linux prestandadatakällor i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell-skript som är tillgängligt från [Azure PowerShell Gallery](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Distribuera Azure Monitor för virtuella datorer

Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösningskomponenterna på logganalysarbetsytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall läser du:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Om du vill `az --version`identifiera din version kör du . Information om hur du installerar eller uppgraderar Azure CLI finns [i Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara den här filen som *installsolutionsforvminsights.json* i en lokal mapp.

1. Samla in värdena för *WorkspaceName,* *ResourceGroupName*och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på log analytics-arbetsytan. Värdet för *WorkspaceLocation* är den region som arbetsytan har definierats i.

1. Du är redo att distribuera den här mallen med följande PowerShell-kommando:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
   När du har aktiverat övervakning kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybriddatorn.

## <a name="troubleshooting"></a>Felsökning

### <a name="vm-doesnt-appear-on-the-map"></a>Den virtuella datorn visas inte på kartan

Om installationen av beroendeagenten lyckades, men du inte ser datorn på kartan, diagnostiserar du problemet genom att följa dessa steg.

1. Är beroendeagenten installerad? Du kan validera detta genom att kontrollera om tjänsten är installerad och körs.

    **Windows**: Leta efter tjänsten "Microsoft Dependency agent".

    **Linux**: Leta efter den pågående processen "microsoft-dependency-agent."

2. Är du på den [kostnadsfria prisnivån i Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Den kostnadsfria planen tillåter upp till fem unika datorer. Efterföljande datorer visas inte på kartan, även om de föregående fem inte längre skickar data.

3. Skickar datorn logg- och perf-data till Azure Monitor Logs? Utför följande fråga för datorn:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Har det tillbaka ett eller flera resultat? Är uppgifterna aktuella? I så fall fungerar din Log Analytics-agent korrekt och kommunicerar med tjänsten. Om inte, kontrollera agenten på servern: [Log Analytics agent för Windows felsökning](../platform/agent-windows-troubleshoot.md) eller Log Analytics agent för Linux [felsökning](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Datorn visas på kartan men har inga processer

Om du ser servern på kartan, men den inte har några process- eller anslutningsdata, betyder det att beroendeagenten är installerad och körs, men kärndrivrutinen läses inte in.

Kontrollera filen C:\Program\Microsoft Dependency Agent\logs\wrapper.log (Windows) eller /var/opt/microsoft/dependency-agent/log/service.log file (Linux). De sista raderna i filen bör ange varför kärnan inte laddas. Kärnan kanske till exempel inte stöds på Linux om du har uppdaterat din kärna.


## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer.

- Information om hur du visar identifierade programberoenden finns i [Visa Azure Monitor för virtuella datorer Karta](vminsights-maps.md).

- Information om hur du identifierar flaskhalsar och övergripande användning med den virtuella datorns prestanda finns i [Visa Azure VM-prestanda](vminsights-performance.md).
