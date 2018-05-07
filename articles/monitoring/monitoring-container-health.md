---
title: Övervaka Azure Kubernetes Service (AKS) hälsotillstånd (förhandsversion) | Microsoft Docs
description: Den här artikeln beskriver hur du kan granska prestandan för din AKS behållare för att snabbt förstå användning av din värdmiljö Kubernetes enkelt.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: magoedte
ms.openlocfilehash: f0501d4404375ee44b96ae4514c15e69b616d38a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Övervaka Azure Kubernetes Service (AKS) behållaren hälsotillstånd (förhandsgranskning)

Den här artikeln beskriver hur du konfigurerar och använder Azure-Monitor behållaren hälsotillstånd för att övervaka prestanda för din arbetsbelastning distribueras till Kubernetes miljöer finns på Azure-Kubernetes (AKS).  Det är viktigt att du övervakar Kubernetes-behållarna och behållarna, särskilt när du kör ett produktionskluster i skala med flera program.

Behållaren hälsotillstånd ger dig möjligheten genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via API: T för mått för prestandaövervakning.  När du har aktiverat behållaren hälsa de här måtten automatiskt samlas in med en av version av OMS-Agent för Linux och lagras i din [logganalys](../log-analytics/log-analytics-overview.md) arbetsytan.  Fördefinierade vyer som ingår visar residing behållare arbetsbelastningar och vad påverkar prestandahälsa för Kubernetes klustret så att du är medveten om:  

* Vilka behållare som körs på noden och deras genomsnittlig belastning för processor och minne att identifiera flaskhalsar för resurser
* Identifiera där behållaren finns i en domänkontrollant och/eller skida att se den övergripande prestandan för en domänkontrollant eller baljor 
* Granska resursanvändningen för arbetsbelastningar som körs på värden inte är relaterade till standardprocedurerna stöder baljor
* Förstå beteendet för klustret under genomsnittliga och högsta belastning för att identifiera kapacitetsbehov och fastställa den maximala den kan klara 

Om du vill övervaka och hantera dina Docker och Windows behållaren värdar för att visa konfiguration, granskning och resursutnyttjande, finns det [behållare övervakning](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Krav 
Granska följande information innan du startar, så du kan förstå krav som stöds.

- Följande versioner av AKS kluster stöds: 1.7.7 1.9.6.
- En av OMS-agent för Linux-version microsoft / oms:ciprod04202018 och senare. Den här agenten installeras automatiskt när onboarding av behållare hälsa.  
- Logganalys-arbetsytan.  Det kan skapas när du aktiverar övervakning av det nya klustret med AKS eller skapa ett till [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), eller från den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).


## <a name="components"></a>Komponenter 

Den här funktionen är beroende av en av OMS-Agent för Linux samlar in prestanda- och händelsedata från alla noder i klustret.  Agenten är automatiskt distribueras och registreras den angivna logganalys-arbetsytan när du aktiverar övervakning av behållare. 

>[!NOTE] 
>Om du redan har distribuerat ett AKS kluster, aktiverar du övervakning med hjälp av en tillhandahållna Azure Resource Manager-mall som visas nedan. Du kan inte använda `kubectl` om du vill uppgradera, ta bort, omdistribuera eller distribuera agenten.  
>

## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Aktivera behållare för övervakning av hälsotillstånd för ett nytt kluster
Du kan bara aktivera övervakning av AKS klustret när du distribuerar från Azure-portalen.  Följ stegen i artikeln quickstart [distribuera ett kluster som Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  När du är på den **övervakning** väljer **Ja** för alternativet **aktivera övervakning** att aktivera, och sedan välja en befintlig eller skapa en ny logganalys-arbetsyta.  

När övervakning aktiveras alla konfigurationsuppgifter har slutförts kan du övervaka prestanda för klustret från ett av två sätt:

1. Direkt från klustret genom att välja AKS **hälsa** i den vänstra rutan.<br><br> 
2. Genom att klicka på den **övervaka behållaren hälsotillstånd** panelen på sidan AKS kluster för det markerade klustret.  Välj i Azure-Monitor **hälsa** i den vänstra rutan.  

![Alternativ för att markera behållaren hälsa i AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

När övervakning är aktiverad, kan det ta cirka 15 minuter innan du kan visa användningsdata för klustret.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Aktivera behållare för övervakning av hälsotillstånd för befintliga hanterade kluster
Aktivera övervakning av din AKS behållare som redan har distribuerats kan utföras från portalen, den kan endast utföras med hjälp av angivna Azure Resource Manager-mall med PowerShell-cmdleten **New-AzureRmResourceGroupDeployment** eller Azure CLI.  En JSON-mall anger konfiguration för att aktivera övervakning och andra JSON-mall innehåller parametervärden som du konfigurerar för att ange följande:

* AKS behållaren resurs-ID 
* Resursgruppen klustret distribueras i 
* Log Analytics-arbetsyta och region om du vill skapa arbetsytan i 

Logganalys-arbetsytan måste skapas manuellt.  Om du vill skapa arbetsytan, du kan konfigurera något via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), från den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).

Om du inte är bekant med principerna för att distribuera resurser med hjälp av en mall med PowerShell Se [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)eller Azure CLI finns [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt.  Det krävs att du använder Azure CLI version 2.0.27 eller senare. Kör `az --version` att identifiera versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Skapa och köra mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster resource id"
        }
    },
    "aksResourceLocation": {
      "type": "string",
      "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
        }
      },
      "workspaceId": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics resource id"
        }
      },
      "workspaceRegion": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics workspace region"
        }
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": true,
              "config": {
                "logAnalyticsWorkspaceResourceID": "[parameters('workspaceId')]"
              }
            }
          }
        }
      },
      {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
    }
    ```

2. Spara filen som **existingClusterOnboarding.json** till en lokal mapp.
3. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "East US"
        },
        "workspaceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "workspaceRegion": {
          "value": "eastus"
        }
      }
    }
    ```

4. Redigera värdet för **aksResourceId**, **aksResourceLocation** med värden som du hittar på den **AKS översikt** för AKS-klustret.  Värdet för **workspaceId** bör vara namnet på en logganalys-arbetsytan och ange platsen att arbetsytan har skapats i för **workspaceRegion**.    
5. Spara filen som **existingClusterParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Använd följande PowerShell-kommandon från mappen som innehåller mallen:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande som innehåller resultatet:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Att köra följande kommando med Azure CLI på Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande som innehåller resultatet:

        ```azurecli
        provisioningState       : Succeeded
        ```
När övervakning är aktiverad, kan det ta cirka 15 minuter innan du kan visa användningsdata för klustret.  

## <a name="verify-agent-deployed-successfully"></a>Kontrollera agent har distribuerats
Kontrollera OMS-agenten distribueras korrekt genom att köra följande kommando: ` kubectl get ds omsagent -—namespace=kube-system`.

Resultatet bör likna den följande som anger den distribuerades korrekt:

```
User@aksuser:~$ kubectl get ds omsagent -—namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Visa prestanda användning
När du öppnar behållaren hälsa anger sidan omedelbart prestanda utnyttjande av klusternoderna.  Visa information om AKS klustret är uppdelat i tre olika perspektiv:

- Noder 
- Kontrollanter  
- Behållare

Hierarkin raden följer objektmodellen Kubernetes börjar med en nod i klustret.  Expandera noden och du ser en eller flera skida som körs på noden om det finns flera behållare grupperade till en baljor, visas de som den sista raden i hierarkin.<br><br> ![Exempel på Kubernetes nod hierarki i prestandavyn](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Du kan välja domänkontrollanter eller behållare från sidans överkant och granska status och resurs-belastningen för dessa objekt.  Använder du rutorna listrutan överst på skärmen för att filtrera efter namnområde, tjänst och noden. Om i stället du vill granska minnesanvändning, från den **mått** listrutan Välj **minne RSS** eller **minne arbetsminnet**.  **Minne RSS** stöds bara för Kubernetes version 1,8 och senare. I annat fall ser du värden för **AVG %** visas som *NaN %*, vilket är ett värde för typen av numeriska data som representerar ett odefinierat eller unrepresentable värde. 

![Behållaren prestandavy noder prestanda](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Som standard prestandadata baseras på de senaste sex timmarna men du kan ändra i fönstret med den **tidsintervall** listrutan hittades i det övre högra hörnet på sidan. För tillfället sidan inte automatisk uppdatering, så du måste manuellt uppdatera det. 

I följande exempel Observera för noden *aks-agentpool-3402399-0*, värdet för **behållare** är 10, vilket är en sammanfattning av det totala antalet behållare som har distribuerats.<br><br> ![Insamling av behållare per nod-exempel](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Detta kan hjälpa dig att snabbt identifiera om du inte har rätt balans mellan behållare mellan noder i klustret.  

I följande tabell beskrivs syftet med informationen när du visar noder.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden |
| Status | Kubernetes vy över nod-status |
| GENOMSNITTLIG % | Genomsnittlig nod procentandel baserat på valda måtten för den valda varaktigheten. |
| GENOMSNITTLIG | Genomsnittlig noder faktiskt värde baserat på valda måtten för den valda varaktigheten.  Medelvärdet mäts från processorminne/gränsen för en nod. Det är avg-värdet som rapporteras av värden för skida och behållare. |
| Behållare | Antal behållare. |
| Drifttid | Representerar tid eftersom en nod startas eller startades om. |
| Baljor | Endast för behållare. Den visar vilket pods den finns. |
| Kontrollanter | Endast för behållare och skida. Den visar vilka styrenheten den. Inte alla skida blir en domänkontrollant så att vissa visar kanske saknas. | 
| Trend AVG % | Stapeldiagram trend baserat på behållaren och noden avg mått %. |


Selector, Välj **styrenheter**.<br><br> ![Välj domänkontrollanter vy](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Här kan du se prestandahälsa för dina domänkontrollanter.<br><br> ![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-performance-and-health-view-05.png)

Hierarkin rad börjar med en domänkontrollant och expanderar domänkontrollanten och du ser en eller flera skida eller en eller flera behållare.  Expandera en baljor och den sista raden visa behållaren grupperade till baljor.  

I följande tabell beskrivs syftet med informationen när du visar domänkontrollanter.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet för enheten|
| Status | Status för behållare när den har slutförts kör med status, till exempel *Uppsagd*, *misslyckades* *stoppad*, eller *pausad*. Om behållaren körs, men status har inte korrekt visas eller hämtades inte av agenten och har inte svarat mer än 30 minuter, status blir *okänd*. |
| GENOMSNITTLIG % | Dyker upp medelvärdet för varje entitet för de valda måtten medel-%. |
| GENOMSNITTLIG | Dyker upp av Genomsnittlig CPU millicore eller minne prestanda för behållaren.  Medelvärdet mäts från processorminne/gränsen för en baljor. |
| Behållare | Totalt antal behållare för domänkontrollant eller baljor. |
| Startar om | Summera antalet omstart från behållare. |
| Drifttid | Representerar tid eftersom en behållare har startat. |
| Baljor | Endast för behållare. Den visar vilket pods den finns. |
| Node | Endast för behållare och skida. Den visar vilka styrenheten den. | 
| Trend AVG % | Stapeldiagram trend presentera genomsnittlig mått % på behållaren. |

Selector, Välj **behållare**.<br><br> ![Visa Välj behållare](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Här kan vi se prestandahälsa för din behållare.<br><br> ![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-performance-and-health-view-06.png)

I följande tabell beskrivs syftet med informationen när du visar behållare.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet för enheten|
| Status | Dyker upp status för behållare, om sådana finns. |
| GENOMSNITTLIG % | Dyker upp medelvärdet för varje entitet för de valda måtten medel-%. |
| GENOMSNITTLIG | Dyker upp av Genomsnittlig CPU millicore eller minne prestanda för behållaren. Medelvärdet mäts från processorminne/gränsen för en baljor. |
| Behållare | Totalt antal behållare för styrenheten.|
| Startar om | Representerar tid eftersom en behållare har startat. |
| Drifttid | Representerar tid eftersom en behållare startades eller startas om. |
| Baljor | Baljor information där den finns. |
| Node |  Nod där behållaren finns.  | 
| Trend AVG % | Stapeldiagram trend presentera genomsnittlig mått % på behållaren. |

## <a name="container-data-collection-details"></a>Behållaren information för samlingen
Behållaren hälsotillstånd samlar in olika mått och logga prestandadata från behållaren värdar och behållare. Data samlas in tre minuters mellanrum.

### <a name="container-records"></a>Behållaren innehåller

I följande tabell visas exempel på poster som samlas in av behållare hälsa och datatyper som visas i sökresultaten för loggen.

| Datatyp | Datatypen i loggen Sök | Fält |
| --- | --- | --- |
| Prestanda för värdar och -behållare | `Perf` | Dator, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB minne användning MB nätverket tar emot byte, nätverket skicka byte, Processor användning s, nätverket&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållaren inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållaren image inventering | `ContainerImageInventory` | TimeGenerated, dator, bild, ImageTag, ImageSize, VirtualSize, körs, pausas, stoppas, misslyckades, SourceSystem, ImageID, TotalContainer |
| Behållaren logg | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, behållarnamn LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Behållaren loggfiler | `ContainerServiceLog`  | TimeGenerated, dator, TimeOfCommand, bild, kommandot, SourceSystem, behållar-ID |
| Behållaren nod inventering | `ContainerNodeInventory_CL`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Behållaren process | `ContainerProcess_CL` | TimeGenerated, dator, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventering av skida i ett Kubernetes kluster | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, Behållar-ID, ContainerName, namn, PodLabel, Namespace, PodStatus, klusternamn, PodIp, SourceSystem |
| Inventering av noder tillhör ett kluster för Kubernetes | `KubeNodeInventory` | TimeGenerated, dator, klusternamn, ClusterId, LastTransitionTimeReady, etiketter, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes händelser | `KubeEvents_CL` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes klustret | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestandamått för noder i klustret Kubernetes | Perf &#124; där ObjectName == ”K8SNode” | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes | 
| Prestandastatistik för behållare som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SContainer” | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes | 

## <a name="search-logs-to-analyze-data"></a>Sökloggar att analysera data
Logganalys kan hjälpa dig att leta efter trender, diagnostisera flaskhalsar, prognosen eller korrelera data som kan hjälpa dig att avgöra om den aktuella klusterkonfigurationen presterar optimalt.  Fördefinierad loggen sökningar tillhandahålls omedelbart börja använda eller anpassa för att returnera information som du vill. 

Du kan utföra interaktiv dataanalys i arbetsytan genom att välja den **Visa logg** alternativet är tillgängligt på längst till höger när du expanderar en behållare.  **Logga Sök** visas rätt ovanför den sida du befann dig på i portalen.<br><br> ![Analysera data i logganalys](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Behållaren loggar utdata vidarebefordras till logganalys är STDOUT och STDERR. Eftersom behållare health övervakar Azure hanterade Kubernetes (AKS), samlas inte Kube system idag på grund av stora mängder data som genereras.     

### <a name="example-log-search-queries"></a>Exempel loggen sökfrågor
Det vara bra att skapa frågor som börjar med ett exempel eller två och ändra dem så att de passar dina behov. Du kan experimentera med följande exempelfrågor som hjälper dig att skapa mer avancerade frågor.

| Fråga | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projektet dator, namn, bild, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;återge tabell | Visa en lista med alla behållare livscykel Information| 
| KubeEvents_CL<br> &#124;där not(isempty(Namespace_s))<br> &#124;Sortera efter TimeGenerated desc<br> &#124;återge tabell | Kubernetes händelser|
| ContainerImageInventory<br> &#124;Sammanfatta AggregatedValue = count() av avbildningen, ImageTag, körs | Bild-inventering | 
| **Välj linjediagram i Advanced Analytics**:<br> Perf<br> &#124;där ObjectName == ”behållare” och CounterName == ”% processortid”<br> &#124;Sammanfatta AvgCPUPercent = avg(CounterValue) av bin (TimeGenerated, 30m), InstanceName | Behållaren CPU | 
| **Välj linjediagram i Advanced Analytics**:<br> Perf &#124; där ObjectName == ”behållare” och CounterName == ”MB minne användning”<br> &#124;Sammanfatta AvgUsedMemory = avg(CounterValue) av bin (TimeGenerated, 30m), InstanceName | Behållaren minne |

## <a name="how-to-stop-monitoring-with-container-health"></a>Hur du stoppa övervakningen med behållaren hälsa
När du aktiverar övervakning av AKS-behållare du beslutar att du inte längre vill övervaka den, kan du *avanmälas* med hjälp av angivna Azure Resource Manager-mallar med PowerShell-cmdleten  **Nya AzureRmResourceGroupDeployment** eller Azure CLI.  En JSON-mall anger konfigurationen till *avanmälas* och andra JSON-mall innehåller parametervärden som du konfigurerar för att ange AKS-ID: T och resurs klusterresursgruppen klustret distribueras i.  Om du inte är bekant med principerna för att distribuera resurser med hjälp av en mall med PowerShell Se [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) eller Azure CLI finns [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt.  Det krävs att du använder Azure CLI version 2.0.27 eller senare. Kör `az --version` att identifiera versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Skapa och köra mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster resource id"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Spara filen som **OptOutTemplate.json** till en lokal mapp.
3. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Redigera värdet för **aksResourceId** och **aksResourceLocation** med värden för klustret AKS, som finns på den **egenskaper** sidan för det markerade klustret.<br><br> ![Sidan med egenskaper för behållaren](./media/monitoring-container-health/container-properties-page.png)<br>

    När du är på den **egenskaper** kan också kopiera den **arbetsytan resurs-Id**.  Det här värdet krävs om du vill ta bort logganalys arbetsytan senare, vilket inte utförs som en del av den här processen.  

5. Spara filen som **OptOutParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Så här använder du följande PowerShell-kommandon från mappen som innehåller mallen:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande som innehåller resultatet:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Att köra följande kommando med Azure CLI på Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar visas ett meddelande som liknar följande som innehåller resultatet:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs, måste du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbetsyta finns [ta bort en Azure logganalys-arbetsytan med Azure-portalen](../log-analytics/log-analytics-manage-del-workspace.md).  Glöm inte att den **arbetsytan resurs-Id** vi kopierade i steg 4, ska du behövs.  

## <a name="troubleshooting"></a>Felsökning
Det här avsnittet innehåller information för att felsöka problem med behållaren hälsa.

Om behållaren hälsa som har aktiverats och konfigurerats men du inte ser några statusinformation eller resulterar i logganalys när du utför en sökning i loggen kan du utföra följande steg för att diagnostisera problemet.   

1. Kontrollera statusen för agenten genom att köra följande kommando: `kubectl get ds omsagent --namespace=kube-system`

    Resultatet bör likna den följande som anger att agenten körs på alla noder i klustret.  Till exempel det här klustret har två noder och förväntat värde för lika många noder.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Kontrollera status för baljor att verifiera att den körs eller inte genom att köra följande kommando: `kubectl get pods --namespace=kube-system`

    Resultatet bör likna följande med statusen *kör* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. Loggarna agent. När av agenten distribueras kör en snabb kontroll genom att köra OMI kommandon och visar vilken version av agenten och Docker-providern. Kör följande kommando för att se att agenten har publicerats eller så har: `kubectl logs omsagent-484hw --namespace=kube-system`

    Status bör likna följande:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Nästa steg

[Söka i loggar](../log-analytics/log-analytics-log-search.md) att visa detaljerad behållaren hälsa och information om programprestanda.  