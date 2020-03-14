---
title: Aktivera Azure Monitor (för hands version) för en hybrid miljö | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en hybrid moln miljö som innehåller en eller flera virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: bd44eebf8aceaf7fe32cf8cf1b1152db32acb344
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275144"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Aktivera Azure Monitor for VMs (för hands version) för en hybrid miljö

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I den här artikeln förklaras hur du aktiverar Azure Monitor for VMs (för hands version) för virtuella datorer eller fysiska datorer som finns i ditt data Center eller i annan moln miljö. I slutet av den här processen kommer du att ha börjat övervaka dina virtuella datorer i din miljö och lära dig om de har problem med prestanda eller tillgänglighet.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](vminsights-enable-overview.md) resurser uppfyller kraven. Granska kraven och distributions metoderna för [Log Analytics Linux-och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Kartdata överförs alltid av Log Analytics agenten till tjänsten Azure Monitor, antingen direkt eller via [Operations Management Suite-gatewayen](../../azure-monitor/platform/gateway.md) om dina IT-säkerhetsprinciper inte tillåter att datorer i nätverket kan ansluta till Internet.

Stegen för att slutföra den här uppgiften sammanfattas på följande sätt:

1. Installera Log Analytics agent för Windows eller Linux. Innan du installerar agenten kan du läsa artikeln [Översikt över Log Analytics agent](../platform/log-analytics-agent.md) för att förstå system krav och distributions metoder.

2. Hämta och installera Azure Monitor for VMs mappa beroende agent för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).

3. Aktivera insamling av prestandaräknare.

4. Distribuera Azure Monitor för virtuella datorer.

>[!NOTE]
>Den information som beskrivs i den här artikeln för att distribuera beroende agenten gäller även för den [tjänstkarta lösningen](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten på Windows

Du kan installera beroende agenten manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administratörs* behörighet krävs för att installera eller avinstallera agenten.

Följande tabell visar de parametrar som stöds av installationsprogrammet för agenten från kommandoraden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utför en tyst installation utan interaktion från användaren. |

Om du till exempel vill köra installations programmet med parametern `/?` anger du **InstallDependencyAgent-Windows. exe/?** .

Filer för Windows beroende Agent installeras i *C:\Program Files\Microsoft Dependency agent* som standard. Om beroende agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är en *%program%\Microsoft-Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux

Beroende agenten är installerad på Linux-servrar från *InstallDependencyAgent-Linux64. bin*, ett gränssnitts skript med en självextraherande binärfil. Du kan köra filen genom att använda `sh` eller lägga till kör behörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
>

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men inte installera agenten. |

Om du till exempel vill köra installations programmet med parametern `-help` anger du **InstallDependencyAgent-Linux64. bin – hjälp**.

Installera Linux-beroende agenten som rot genom att köra kommandot `sh InstallDependencyAgent-Linux64.bin`.

Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. I Linux-agenter är logg katalogen */var/opt/Microsoft/Dependency-Agent/log*.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | plats. |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exempel på installationsskript

För att enkelt distribuera beroendeagent flera servrar samtidigt, tillhandahålls i följande exempel för skriptet att hämta och installera beroendeagenten i Windows eller Linux.

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

För att distribuera beroendeagenten med Desired State Configuration (DSC), kan du använda modulen xPSDesiredStateConfiguration med följande exempelkod:

```powershell
configuration ServiceMap {

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

Om Log Analytics-arbetsytan som refereras av lösningen inte redan har konfigurerats för att samla in prestandaräknare som krävs av lösningen, måste du aktivera dem. Det kan du göra på något av två sätt:
* Manuellt, enligt beskrivningen i [prestanda data källor för Windows och Linux i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell-skript som är tillgängligt från [Azure PowerShell galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Distribuera Azure Monitor för virtuella datorer

Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera komponenterna för lösningen i Log Analytics-arbetsytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Du kan identifiera din version genom att köra `az --version`. Information om hur du installerar eller uppgraderar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara filen som *installsolutionsforvminsights. JSON* i en lokal mapp.

1. Avbilda värdena för *WorkspaceName*, *ResourceGroupName*och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på din Log Analytics-arbetsyta. Värdet för *WorkspaceLocation* är den region som arbets ytan definieras i.

1. Är du redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
   När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybrid-dator.

## <a name="troubleshooting"></a>Felsökning

### <a name="vm-doesnt-appear-on-the-map"></a>Den virtuella datorn finns inte på kartan

Om beroende Agent installationen lyckades, men du inte ser datorn på kartan, kan du diagnostisera problemet genom att följa dessa steg.

1. Beroendeagenten installeras? Du kan kontrollera detta genom att markera om du vill se om tjänsten är installerad och körs.

    **Windows**: Sök efter tjänsten med namnet "Microsoft Dependency agent".

    **Linux**: Sök efter den process som körs "Microsoft-Dependency-agent".

2. Är du på den [kostnads fria pris nivån av Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Den kostnads fria planen tillåter upp till fem unika datorer. Eventuella efterföljande datorer visas inte på kartan, även om de fem föregående fem inte längre skickar data.

3. Skickar datorn logg-och perf-data till Azure Monitor loggar? Utför följande fråga för datorn:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Returnerade du ett eller flera resultat? Är data de senaste? I så fall fungerar din Log Analytics-agenten som den ska och kommunicerar med tjänsten. Om inte, kontrol lera agenten på servern: [Log Analytics agent för Windows fel sökning](../platform/agent-windows-troubleshoot.md) eller [Log Analytics agent för Linux-felsökning](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Datorn visas på kartan men saknar processer

Om du ser din server på kartan, men inte har någon process-eller anslutnings data, indikerar att beroende agenten är installerad och körs, men kernel-drivrutinen har inte lästs in.

Kontrollera C:\Program Files\Microsoft beroende Agent\logs\wrapper.log fil (Windows) eller /var/opt/microsoft/dependency-agent/log/service.log fil (Linux). De sista raderna i filen ska indikera varför kernel lästes inte in. Till exempel kanske kernel inte kan användas i Linux om du har uppdaterat din kernel.


## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs.

- Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).

- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
