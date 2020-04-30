---
title: Aktivera Azure Monitor för en hybrid miljö | Microsoft Docs
description: I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en hybrid moln miljö som innehåller en eller flera virtuella datorer.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480750"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Aktivera Azure Monitor for VMs för en hybrid miljö

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I den här artikeln förklaras hur du aktiverar Azure Monitor for VMs för virtuella datorer eller fysiska datorer som finns i ditt data Center eller i annan moln miljö. I slutet av den här processen kommer du att ha börjat övervaka dina virtuella datorer i din miljö och lära dig om de har problem med prestanda eller tillgänglighet.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](vminsights-enable-overview.md) resurser uppfyller kraven. Granska kraven och distributions metoderna för [Log Analytics Linux-och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor for VMs kartans beroende agent skickar inte några data och kräver inte några ändringar i brand väggar eller portar. Kartdata överförs alltid av Log Analytics agenten till tjänsten Azure Monitor, antingen direkt eller via [Operations Management Suite-gatewayen](../../azure-monitor/platform/gateway.md) om dina IT-säkerhetsprinciper inte tillåter att datorer i nätverket kan ansluta till Internet.

Stegen för att slutföra den här uppgiften sammanfattas på följande sätt:

1. Installera Log Analytics agent för Windows eller Linux. Innan du installerar agenten kan du läsa artikeln [Översikt över Log Analytics agent](../platform/log-analytics-agent.md) för att förstå system krav och distributions metoder.

2. Hämta och installera Azure Monitor for VMs mappa beroende agent för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).

3. Aktivera insamling av prestanda räknare.

4. Distribuera Azure Monitor for VMs.

>[!NOTE]
>Den information som beskrivs i den här artikeln för att distribuera beroende agenten gäller även för den [tjänstkarta lösningen](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Installera beroende agenten i Windows

Du kan installera beroende agenten manuellt på Windows-datorer genom att `InstallDependencyAgent-Windows.exe`köra. Om du kör den här körbara filen utan några alternativ startas en installations guide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administratörs* behörighet krävs för att installera eller avinstallera agenten.

I följande tabell beskrivs de parametrar som stöds av installations programmet för agenten från kommando raden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista med kommando rads alternativen. |
| / S | Utför en tyst installation utan användar interaktion. |

Om du till exempel vill köra installations programmet med `/?` parametern anger du **InstallDependencyAgent-Windows. exe/?**.

Filer för Windows beroende Agent installeras i *C:\Program Files\Microsoft Dependency agent* som standard. Om beroende agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är en *%program%\Microsoft-Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installera beroende agenten på Linux

Beroende agenten är installerad på Linux-servrar från *InstallDependencyAgent-Linux64. bin*, ett gränssnitts skript med en självextraherande binärfil. Du kan köra filen genom att använda `sh` eller lägga till kör-behörigheter till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
>

| Parameter | Beskrivning |
|:--|:--|
| – hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| --kontrol lera | Kontrol lera behörigheter och operativ systemet, men installera inte agenten. |

Om du till exempel vill köra installations programmet med- `-help` parametern anger du **InstallDependencyAgent-Linux64. bin – hjälp**.

Installera Linux-beroende agenten som rot genom att köra kommandot `sh InstallDependencyAgent-Linux64.bin`.

Om det inte går att starta beroende agenten kontrollerar du om det finns detaljerad fel information i loggarna. I Linux-agenter är logg katalogen */var/opt/Microsoft/Dependency-Agent/log*.

Filer för beroende agenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Exempel på installationsskript

För att enkelt kunna distribuera beroende agenten på många servrar samtidigt, finns följande skript exempel för att ladda ned och installera beroende agenten på antingen Windows eller Linux.

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

Om du vill distribuera beroende agenten med önskad tillstånds konfiguration (DSC) kan du använda xPSDesiredStateConfiguration-modulen med följande exempel kod:

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

## <a name="enable-performance-counters"></a>Aktivera prestanda räknare

Om Log Analytics-arbetsytan som refereras till av lösningen inte redan har kon figurer ATS för att samla in de prestanda räknare som krävs för lösningen måste du aktivera dem. Det kan du göra på något av två sätt:
* Manuellt, enligt beskrivningen i [prestanda data källor för Windows och Linux i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell-skript som är tillgängligt från [Azure PowerShell galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Distribuera Azure Monitor for VMs

Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera lösnings komponenterna i Log Analytics arbets ytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du vill använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.27 eller senare. För att identifiera din version, `az --version`kör. Information om hur du installerar eller uppgraderar Azure CLI finns i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Spara filen som *installsolutionsforvminsights. JSON* i en lokal mapp.

1. Avbilda värdena för *WorkspaceName*, *ResourceGroupName*och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på din Log Analytics-arbetsyta. Värdet för *WorkspaceLocation* är den region som arbets ytan definieras i.

1. Du är redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
   När du har aktiverat övervakning kan det ta cirka 10 minuter innan du kan se hälso tillståndet och måtten för Hybrid datorn.

## <a name="troubleshooting"></a>Felsökning

### <a name="vm-doesnt-appear-on-the-map"></a>Den virtuella datorn finns inte på kartan

Om beroende Agent installationen lyckades, men du inte ser datorn på kartan, kan du diagnostisera problemet genom att följa dessa steg.

1. Har beroende agenten installerats? Du kan verifiera detta genom att kontrol lera om tjänsten är installerad och körs.

    **Windows**: Sök efter tjänsten med namnet "Microsoft Dependency agent".

    **Linux**: Sök efter den process som körs "Microsoft-Dependency-agent".

2. Är du på den [kostnads fria pris nivån av Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Den kostnads fria planen tillåter upp till fem unika datorer. Eventuella efterföljande datorer visas inte på kartan, även om de fem föregående fem inte längre skickar data.

3. Skickar datorn logg-och perf-data till Azure Monitor loggar? Utför följande fråga för datorn:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Returnerade du ett eller flera resultat? Är data nyligen? I så fall fungerar din Log Analytics-agenten som den ska och kommunicerar med tjänsten. Om inte, kontrol lera agenten på servern: [Log Analytics agent för Windows fel sökning](../platform/agent-windows-troubleshoot.md) eller [Log Analytics agent för Linux-felsökning](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Datorn visas på kartan men saknar processer

Om du ser din server på kartan, men inte har någon process-eller anslutnings data, indikerar att beroende agenten är installerad och körs, men kernel-drivrutinen har inte lästs in.

Kontrol lera C:\Program Files\Microsoft Agent\logs\wrapper.log-filen (Windows) eller/var/opt/Microsoft/Dependency-Agent/log/service.log-filen (Linux). De sista raderna i filen bör ange varför kerneln inte lästes in. Till exempel kanske kärnan inte stöds på Linux om du har uppdaterat din kernel.


## <a name="next-steps"></a>Nästa steg

Nu när övervakning är aktiverat för dina virtuella datorer är den här informationen tillgänglig för analys med Azure Monitor for VMs.

- Information om hur du visar identifierade program beroenden finns i [visa Azure Monitor for VMS karta](vminsights-maps.md).

- Information om hur du identifierar Flask halsar och övergripande användning med den virtuella datorns prestanda finns i [Visa prestanda för virtuella Azure-datorer](vminsights-performance.md).
