---
title: Övervaka Azure Kubernetes Service (AKS) hälsotillstånd (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs hur du kan enkelt granska prestanda för AKS-behållare för att snabbt förstå användningen av Kubernetes-miljön.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2018
ms.author: magoedte
ms.openlocfilehash: 2ae61d672083508d49e72afd5a015191082c23e9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521939"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Övervaka hälsotillstånd för behållare i Azure Kubernetes Service (AKS) (förhandsversion)

Den här artikeln beskriver hur du konfigurerar och använder hälsotillstånd för behållare i Azure Monitor för att övervaka prestanda för arbetsbelastningar som distribueras till Kubernetes-miljöer och finns på Azure Kubernetes Service (AKS). Övervaka Kubernetes-kluster och behållare är viktigt, särskilt när du kör ett produktionskluster i skala med flera program.

Hälsotillstånd för behållare ger dig möjligheten genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API för prestandaövervakning. När du har aktiverat hälsotillstånd för behållare som samlas in för dig via en behållare version av Operations Management Suite (OMS)-agenten för Linux och lagras i de här måtten automatiskt din [Log Analytics](../log-analytics/log-analytics-overview.md) arbetsyta. Inkluderade fördefinierade vyer visar som förvaras behållararbetsbelastningar och vad påverkar prestandatillståndet för Kubernetes-klustret så att du kan:  

* Identifiera behållare som körs på noden och deras genomsnittsanvändningen av processor och minne. Den här kunskapen kan hjälpa dig att identifiera flaskhalsar för resurser.
* Identifiera där behållaren finns i en domänkontrollant eller en pod. Med hjälp av den här kunskapen kan du visa kontrollantens eller pod's övergripande prestanda. 
* Granska arbetsbelastningar som körs på värden som inte är relaterade till de processer som standard som har stöd för poden Resursanvändning.
* Förstå beteendet för klustret under genomsnittliga och högsta belastning. Den här kunskapen kan hjälpa dig att identifiera kapacitetsbehov och avgöra den maximala belastningen kan klustret hantera. 

Om du vill övervaka och hantera Docker och Windows behållare-värdar att visa konfiguration, granskning och användningen av resurser finns i den [lösning för övervakning av behållare](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Förutsättningar 
Innan du börjar bör du kontrollera att du har följande:

- En ny eller befintlig AKS-kluster.
- En behållare OMS-agenten för Linux-versionen microsoft / oms:ciprod04202018 eller senare. Versionsnumret representeras av ett datum i följande format: *mmddyyyy*. Agenten installeras automatiskt under publiceringen av hälsotillstånd för behållare. 
- En Log Analytics-arbetsyta. Du kan skapa den när du aktiverar övervakning av din nya AKS-klustret eller låta publiceringsupplevelsen skapa en standardarbetsyta i standardresursgruppen för prenumerationen för AKS-kluster. Om du väljer att skapa den själv, kan du skapa den via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), via [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).
- Log Analytics deltagarrollen, att aktivera behållarövervakning. Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Komponenter 

Du kan övervaka prestanda förlitar sig på en behållare OMS-agenten för Linux som samlar in prestanda- och händelsedata från alla noder i klustret. Agenten har automatiskt distribuerat och registrerat med den angivna Log Analytics-arbetsytan när du aktiverar behållarövervakning. 

>[!NOTE] 
>Om du redan har distribuerat ett AKS-kluster kan aktivera du övervakning genom att använda Azure CLI eller en angiven Azure Resource Manager-mall, som visas längre fram i den här artikeln. Du kan inte använda `kubectl` om du vill uppgradera, ta bort, omdistribuera eller distribuera agenten. 
>

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Aktivera övervakning av behållare hälsotillstånd för ett nytt kluster
Under distributionen kan aktivera du övervakning av ett nytt AKS-kluster i Azure portal eller med Azure CLI. Följ stegen i snabbstartsartikeln [distribuera ett kluster i Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md) om du vill aktivera från portalen. På den **övervakning** sidan för den **aktivera övervakning** väljer **Ja**, och välj en befintlig Log Analytics-arbetsyta eller skapa en ny. 

Om du vill aktivera övervakning av ett nytt AKS-kluster som skapats med Azure CLI följer du steg i snabbstartsartikeln under avsnittet [skapa AKS-kluster](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

När du har aktiverat övervakning och alla åtgärder för konfiguration har slutförts kan övervaka du prestanda för ditt kluster på något av två sätt:

* Direkt i AKS-kluster genom att välja **hälsotillstånd** i den vänstra rutan.
* Genom att välja den **övervaka behållarens hälsa** panel på sidan AKS-kluster för det markerade klustret. I Azure Monitor, i den vänstra rutan väljer **hälsotillstånd**. 

  ![Alternativ för att välja hälsotillstånd för behållare i AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa användningsdata för klustret. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Aktivera övervakning av behållare hälsotillstånd för befintliga hanterade kluster
Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats antingen med hjälp av Azure CLI, från portalen eller med den angivna Azure Resource Manager-mallen med hjälp av PowerShell-cmdleten `New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Aktivera övervakning med hjälp av Azure CLI
Följande steg aktiverar övervakning av AKS-klustret med hjälp av Azure CLI. I det här exemplet måste du behöver inte per skapa eller ange en befintlig arbetsyta. Det här kommandot gör enklare för dig genom att skapa en standardarbetsyta i standardresursgruppen för AKS-kluster-prenumeration om det inte redan finns i regionen.  Standardarbetsytan skapade liknar formatet *DefaultWorkspace -<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Utdata ska likna följande:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-in-the-azure-portal"></a>Aktivera övervakning i Azure portal
Om du vill aktivera övervakning av din AKS-behållare i Azure-portalen, gör du följande:

1. I Azure-portalen väljer du **alla tjänster**. 
2. I listan över resurser, börjar du skriva **behållare**.  
    Filtreras listan baserat på dina indata. 
3. Välj **Kubernetes-tjänster**.  

    ![Länken Kubernetes-tjänster](./media/monitoring-container-health/azure-portal-01.png)

4. I listan över behållare, väljer du en behållare.
5. På översiktssidan behållaren väljer **övervaka behållarens hälsa**.  
6. På den **registrering för hälsotillstånd för behållare och loggar** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret, markerar du den i den nedrullningsbara listan.  
    Listan förväljer standardarbetsytan och plats som AKS-behållare distribueras till i prenumerationen. 

    ![Aktivera hälsoövervakning för AKS-behållare](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från klustret, följer du anvisningarna i [skapa en Log Analytics-arbetsyta](../log-analytics/log-analytics-quick-create-workspace.md). Var noga med att skapa arbetsytan i samma prenumeration som AKS-behållare distribueras till. 
 
När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa användningsdata för klustret. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Aktivera övervakning genom att använda en Azure Resource Manager-mall
Den här metoden innehåller två JSON-mallar. En mall anger konfigurationen för att aktivera övervakning och den andra innehåller parametervärden som du konfigurerar för att ange följande:

* AKS-behållare resurs-ID. 
* Den resursgrupp som klustret har distribuerats i.
* Log Analytics-arbetsyta och region för att skapa arbetsytan i. 

Log Analytics-arbetsytan måste du skapa dem manuellt. För att skapa arbetsytan, du kan konfigurera det via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), via [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), eller i den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
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
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
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
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
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
3. Klistra in följande JSON-syntax i filen:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena på den **översikt över AKS** för AKS-klustret. Värdet för **workspaceResourceId** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan. Också ange platsen för arbetsytan för **workspaceRegion**. 
5. Spara filen som **existingClusterParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Använd följande PowerShell-kommandon i den mapp som innehåller mallen:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Så här kör du följande kommando med hjälp av Azure CLI på Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

        ```azurecli
        provisioningState       : Succeeded
        ```
När du har aktiverat övervakning, kan det ta ungefär 15 minuter innan du kan visa användningsdata för klustret. 

## <a name="verify-agent-and-solution-deployment"></a>Kontrollera distributionen av agenten och lösning
Med agentversion *06072018* eller senare, kan du kontrollera att både agenten och lösningen har distribuerats. Du kan kontrollera endast agentdistribution med tidigare versioner av agenten.

### <a name="agent-version-06072018-or-later"></a>Agentversion 06072018 eller senare
Kör följande kommando för att kontrollera att agenten har distribuerats. 

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kontrollera distributionen av lösningen genom att köra följande kommando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Tidigare än 06072018 agentversion

Kontrollera att OMS-agent-version som publicerades före *06072018* har distribuerats korrekt, kör du följande kommando:  

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visa konfiguration med CLI
Använd den `aks show` för att hämta information om sådana som är den lösning som har aktiverats eller inte, vad är Log Analytics-arbetsyta resourceID och sammanfattningsinformation om klustret.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

Efter ett par minuter kommandot har slutförts och returnerar JSON-formaterad information om lösningen.  Resultatet av kommandot ska visa övervakning tillägg profilen och liknar följande Exempelutdata:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="view-performance-utilization"></a>Visa prestanda användning
När du öppnar hälsotillstånd för behållare, anger sidan omedelbart prestanda utnyttjande av hela klustret. Visa information om AKS-klustret är uppdelad i fyra perspektiv:

- Kluster
- Noder 
- Kontrollanter  
- Containrar

På den **kluster** fliken fyra prestanda linjediagram visar viktiga prestandamått för ditt kluster. 

![Prestandadiagram för exempel på fliken kluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

Prestandadiagrammet visar fyra prestandamått:

- **Processoranvändning för noden&nbsp;%**: ett sammansatt perspektiv CPU-belastningen för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **Minnesanvändning för noden&nbsp;%**: en aggregerade perspektiv av minnesanvändningen för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **Antal noder**: ett antal noder och status från Kubernetes. Status för noderna i klustret som representeras är *alla*, *redo*, och *inte klara* och kan filtreras individuellt eller kombineras i Väljaren ovanför diagrammet. 
- **Antal för aktiviteter pod**: antal poddar och status från Kubernetes. Statusen för poddarna representeras är *alla*, *väntande*, *kör*, och *okänd* och kan filtreras individuellt eller kombineras i den väljare ovanför diagrammet. 

När du växlar till **noder**, **domänkontrollanter**, och **behållare** fliken automatiskt visas till höger på sidan är egenskapsrutan.  Den visar egenskaperna för objekt som valts, inklusive etiketter som du definierar för att organisera Kubernetes-objekten.  Klicka på den **>>** länkar i fönstret till view\hide fönstret.  

![Egenskapsrutan för exempel Kubernetes perspektiv](./media/monitoring-container-health/perspectives-preview-pane-01.png)

När du expanderar objekt inom hierarkin egenskaper fönstret uppdateringar baserat på de objekt som valts. Från fönstret kan du också visa Kubernetes-händelser med fördefinierade loggsökningar genom att klicka på den **visa Kubernetes-händelseloggar** länken längst upp i fönstret. Mer information om hur du visar loggdata för Kubernetes finns i [söka loggarna för att analysera data](#search-logs-to-analyze-data).

Växla till den **noder** fliken och raden hierarkin följer objektmodellen Kubernetes börjar med en nod i klustret. Expandera noden och du kan visa en eller flera poddar som körs på noden. Om flera behållare är grupperat till en pod, visas de som den sista raden i hierarkin. Du kan också visa hur många icke-pod relaterade arbetsbelastningar körs på värden om värden har processor eller minne.

![Exempel Kubernetes Node-hierarkin i prestandavyn](./media/monitoring-container-health/container-health-nodes-view.png)

Du kan välja domänkontrollanter eller behållare överst på sidan och granska status och Resursanvändning för dessa objekt. Använda nedrullningsbara listrutor längst upp för att filtrera efter namnområde, tjänst och noden. Om istället du vill granska minnesanvändningen, i den **mått** listrutan, väljer **minne RSS** eller **arbetsminne**. **Minne RSS** stöds endast för Kubernetes version 1.8 och senare. Annars kan du visa värden för **Min&nbsp; %**  som *NaN&nbsp;%*, vilket är ett värde av numeriska data som representerar en odefinierad eller inte går att representera värde. 

![Behållaren noder prestandavy](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Som standard prestandadata baseras på de senaste sex timmarna, men du kan ändra i fönstret med hjälp av den **tidsintervall** listrutan överst till höger. För tillfället sidan inte automatisk uppdatering, så du måste manuellt uppdatera den. Du kan också filtrera resultaten inom tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i Väljaren: e percentilen. 

![: E percentilen val för filtrering av data](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

I följande exempel Observera för noden *aks-nodepool-3977305*, värdet för **behållare** är 5, vilket är en summering av det totala antalet behållare som distribueras.

![Summering av behållare per nod-exempel](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Det kan du snabbt kan identifiera om du har en rätt balans mellan behållare mellan noderna i klustret. 

I följande tabell beskrivs den information som visas när du visar noder:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden. |
| Status | Kubernetes vy över nodstatusen. |
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Genomsnittlig nod procent baserat på: e percentilen under den valda perioden. |
| Genomsn, Min, Max, 50, 90 | Genomsnittlig noder faktiskt värde baserat på: e percentilen under den tidslängd som valts. Medelvärdet mäts från processor/minne gränsen för en nod. Det är genomsn värdet som rapporteras av värden för poddar och behållare. |
| Containrar | Antal behållare. |
| Drifttid | Representerar tid eftersom en nod startas eller startades om. |
| Kontrollanter | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt i. Inte alla poddar är i en kontrollant så vissa kan visa **ej tillämpligt**. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Stapeldiagram trend presentera: e percentilen mått procentandelen kontrollanten. |


Välj i Väljaren, **styrenheter**.

![Välj domänkontrollanter vy](./media/monitoring-container-health/container-health-controllers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina domänkontrollanter.

![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-health-controllers-view.png)

Rad-hierarki börjar med en domänkontrollant och utökas kontrollanten. Du kan visa en eller flera behållare. Expandera en pod och den sista raden visar behållaren grupperade poden. 

Informationen som visas när du visar domänkontrollanter beskrivs i följande tabell:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Samla in status för behållarna när den har slutförts kör med status, till exempel *OK*, *Uppsagd*, *misslyckades* *stoppad*, eller *Pausats*. Om behållaren körs, men status har inte korrekt visas eller hämtades inte av agenten och har inte svarat mer än 30 minuter, statusen är *okänd*. Ytterligare information om statusikonen finns i tabellen nedan.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Samla in medelvärde för den genomsnittliga procentandelen av varje entitet för valda mått- och: e percentilen. |
| Genomsn, Min, Max, 50, 90  | Samla in den genomsnittliga CPU millicore eller minne över prestanda behållaren för den valda: e percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Containrar | Totalt antal behållare för domänkontrollant eller pod. |
| Startar om | Samla in för den omstart räkningen från behållare. |
| Drifttid | Representerar tid efter att en behållare startades. |
| Node | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;%| Stapeldiagram trend som representerar: e percentilen mått för kontrollenheten. |

Ikonerna i statusfältet onlinestatus av behållarna:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/monitoring-container-health/container-health-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/monitoring-container-health/container-health-grey-icon.png) | Senast rapporterat körs men har inte svarat mer än 30 minuter|
| ![Lyckad statusikon](./media/monitoring-container-health/container-health-green-icon.png) | Har stoppats eller gick inte att stoppa|

Statusikonen visar ett antal baserat på din pod tillhandahåller. Den visar sämsta två tillstånd och när du hovrar över statusen visar samla in status från alla poddar i behållaren. Om det inte finns ett färdigt tillstånd, statusvärdet visar **(0)**. 

Välj i Väljaren, **behållare**.

![Välj behållare vy](./media/monitoring-container-health/container-health-containers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina behållare.

![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-health-containers-view.png)

I följande tabell beskrivs den information som visas när du visar behållare:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Status för behållare, om sådana. Ytterligare information om statusikonen finns i nästa tabell.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Summera för den genomsnittliga procentandelen av varje entitet för valda mått- och: e percentilen. |
| Genomsn, Min, Max, 50, 90  | Summera för Genomsnittlig CPU millicore eller minne prestandan hos en behållare för den valda: e percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Pod | Behållaren där poden finns.| 
| Node |  Noden där behållaren finns. | 
| Startar om | Representerar tid efter att en behållare startades. |
| Drifttid | Representerar tid eftersom en behållare startades eller startas om. |
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Ett stapeldiagram trend som representerar den genomsnittliga procentandelen mått behållaren. |

Ikonen i statusfältet anger online status för poddar, enligt beskrivningen i följande tabell:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/monitoring-container-health/container-health-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/monitoring-container-health/container-health-grey-icon.png) | Senast rapporterat körs men har inte svarat under mer än 30 minuterna|
| ![Avslutade statusikon](./media/monitoring-container-health/container-health-terminated-icon.png) | Har stoppats eller gick inte att stoppa|
| ![Misslyckade statusikon](./media/monitoring-container-health/container-health-failed-icon.png) | Felaktigt tillstånd |

## <a name="container-data-collection-details"></a>Insamling av data-behållarinformation
Hälsotillstånd för behållare samlar in olika mått och loggfiler prestandadata från behållare-värdar och behållare. Data som samlas in var tredje minut.

### <a name="container-records"></a>Behållarposter

Exempel på poster som samlas in av hälsotillstånd för behållare och vilka datatyper av som visas i sökresultaten för loggen visas i följande tabell:

| Datatyp | Datatypen i Loggsökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Datorn, objektnamn, CounterName &#40;läser MB tid i procent för Processor, Disk, Disk skriver MB, MB för användning av minne, nätverk ta emot byte, nätverk skicka byte, Processor användning sek, Network&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem |
| Behållare-inventering | `ContainerInventory` | TimeGenerated, dator, behållarnamn ContainerHostname, bild, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommandot, CreatedTime, StartedTime, FinishedTime, SourceSystem, behållar-ID, ImageID |
| Behållaravbildningar | `ContainerImageInventory` | TimeGenerated, dator, bild, ImageTag, ImageSize, VirtualSize, drift, pausad, stoppas, misslyckades, SourceSystem, ImageID, TotalContainer |
| Behållarloggen | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, namn, LogEntrySource, LogEntry, SourceSystem, behållar-ID |
| Container service-logg | `ContainerServiceLog`  | TimeGenerated, dator, TimeOfCommand, bild, kommandot, SourceSystem, behållar-ID |
| Behållarnodslager | `ContainerNodeInventory_CL`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Behållarprocess | `ContainerProcess_CL` | TimeGenerated, dator, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventering av poddar i ett Kubernetes-kluster | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, Behållar-ID, ContainerName, namn, PodLabel, Namespace, PodStatus, klusternamn, PodIp, SourceSystem |
| Inventering av noder tillhör ett Kubernetes-kluster | `KubeNodeInventory` | TimeGenerated, dator, klusternamn, ClusterId, LastTransitionTimeReady, etiketter, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-händelser | `KubeEvents_CL` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes-kluster | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestandamått för noder som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SNode” | Datorn, objektnamn, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem | 
| Prestandamått för behållare som en del av Kubernetes-kluster | Perf &#124; där ObjectName == ”K8SContainer” | CounterName &#40;cpuUsageNanoCores memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, räknarsökväg, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Sök i loggar att analysera data
Log Analytics kan hjälpa dig att söka trender, diagnostisera flaskhalsar, prognoser och korrelera data som kan hjälpa dig att avgöra om den aktuella klusterkonfigurationen presterar optimalt. Fördefinierade loggsökningar tillhandahålls för du omedelbart börja använda eller anpassa för att returnera informationen som du vill. 

Du kan utföra interaktiva analyser av data på arbetsytan genom att välja den **visa Kubernetes-händelseloggar** eller **visa behållarloggarna** alternativet i förhandsgranskningsfönstret. Den **Loggsökning** visas till höger om Azure portal sidan som du var på.

![Analysera data i Log Analytics](./media/monitoring-container-health/container-health-log-search-example.png)   

Utdata för container-loggar som vidarebefordras till logganalys är STDOUT och STDERR. Eftersom behållare health övervakar Azure-hanterade Kubernetes (AKS), samlas Kube system inte in idag på grund av den stora mängden skapas. 

### <a name="example-log-search-queries"></a>Exempel loggsökningsfrågor
Det är ofta bra att skapa frågor som börjar med ett exempel eller två och ändra dem efter dina behov. För att skapa mer avancerade frågor kan experimentera du med följande exempelfrågor:

| Fråga | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projektet dator, namn, bild, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Rendera tabell | Visa en lista över information om en behållarens livslängd| 
| KubeEvents_CL<br> &#124;där not(isempty(Namespace_s))<br> &#124;Sortera efter TimeGenerated fall<br> &#124;Rendera tabell | Kubernetes-händelser|
| ContainerImageInventory<br> &#124;Sammanfatta AggregatedValue = antal() efter bild, ImageTag, körs | Avbildningslager | 
| **I Advanced Analytics väljer du linjediagram**:<br> Perf<br> &#124;där ObjectName == ”behållare” och CounterName == ”% processortid”<br> &#124;Sammanfatta AvgCPUPercent = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Processorprestanda för behållare | 
| **I Advanced Analytics väljer du linjediagram**:<br> Perf &#124; där ObjectName == ”behållare” och CounterName == ”Instansminne (MB) användning”<br> &#124;Sammanfatta AvgUsedMemory = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Behållare-minne |

## <a name="how-to-stop-monitoring-with-container-health"></a>Hur du stoppar övervakningen med hälsotillstånd för behållare
Om när du aktiverar övervakning av din AKS-behållare kan du beslutar att du inte längre vill du övervaka den, kan du *avanmäla dig* genom att använda antingen de tillhandahållna Azure Resource Manager-mallarna med PowerShell-cmdleten  **Ny AzureRmResourceGroupDeployment** eller Azure CLI. En JSON-mallen används för att konfigurationen av *avanmäla dig*. Den andra innehåller parametervärden som du konfigurerar för att ange AKS-kluster-ID och resurs resursgruppen som klustret distribueras i. 

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du behöver installera eller uppgradera Azure CLI kan du läsa [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
             "description": "AKS Cluster Resource ID"
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
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Spara filen som **OptOutTemplate.json** till en lokal mapp.
3. Klistra in följande JSON-syntax i filen:

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

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena för AKS-kluster som finns på den **egenskaper** sidan för det markerade klustret .

    ![Egenskapssidan för behållare](./media/monitoring-container-health/container-properties-page.png)

    När du är på den **egenskaper** kan också kopiera den **Arbetssyteresurs-ID**. Det här värdet krävs om du vill att ta bort Log Analytics-arbetsytan senare. Ta bort Log Analytics-arbetsytan utförs inte som en del av den här processen. 

5. Spara filen som **OptOutParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Så här använder du följande PowerShell-kommandon i mappen som innehåller mallen:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Att köra följande kommando med Azure CLI på Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När det är klart, returneras ett meddelande som liknar följande som innehåller resultatet:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs kan behöva du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbetsyta kan se [ta bort en Azure Log Analytics-arbetsyta med Azure portal](../log-analytics/log-analytics-manage-del-workspace.md). Glöm inte att den **Arbetssyteresurs-ID** vi kopierade tidigare i steg 4, du kommer att behöva som. 

## <a name="troubleshooting"></a>Felsökning
Det här avsnittet innehåller information för att felsöka problem med hälsotillstånd för behållare.

Om hälsotillstånd för behållare som har aktiverats och konfigurerats, men du kan inte visa statusinformation eller resultat i Log Analytics när du utför en loggsökning, kan du felsöka problemet genom att göra följande: 

1. Kontrollera statusen för agenten genom att köra följande kommando: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatusen lösning med agentversion *06072018* eller senare genom att köra följande kommando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna följande, vilket betyder att den har distribuerats korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för poden att verifiera att den är igång genom att köra följande kommando: `kubectl get pods --namespace=kube-system`

    Utdata bör likna följande med statusen *kör* för omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Loggarna för agenten. När behållare agenten distribueras, kör en snabb kontroll genom att köra OMI kommandon och visar versionen av agenten och providern. 

5. Kontrollera att agenten har integrerats har genom att köra följande kommando: `kubectl logs omsagent-484hw --namespace=kube-system`

    Statusen bör likna följande:

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

Om du vill visa detaljerad behållare hälsotillstånd och information om programprestanda, se [söka loggarna](../log-analytics/log-analytics-log-search.md). 
