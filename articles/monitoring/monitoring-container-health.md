---
title: Övervaka Azure Kubernetes Service (AKS) hälsotillstånd (förhandsversion) | Microsoft Docs
description: Den här artikeln beskrivs hur du kan enkelt granska prestanda för AKS-behållare för att snabbt förstå användningen av Kubernetes-miljön.
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
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069159"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Övervaka hälsotillstånd för behållare i Azure Kubernetes Service (AKS) (förhandsversion)

Den här artikeln beskriver hur du konfigurerar och använder hälsotillstånd för behållare i Azure Monitor för att övervaka prestanda för dina arbetsbelastningar som distribueras till Kubernetes-miljöer finns på Azure Kubernetes Service (AKS).  Det är viktigt att du övervakar Kubernetes-klustret och containrarna, särskilt när du kör ett produktionskluster i skala med flera program.

Hälsotillstånd för behållare ger dig möjligheten genom att samla in minne och processor mått från domänkontrollanter, noder och behållare som är tillgängliga i Kubernetes via mått-API för prestandaövervakning.  När du har aktiverat hälsotillstånd för behållare, är de här måtten automatiskt samlas in för dig med en behållare version av OMS-agenten för Linux och lagras i din [Log Analytics](../log-analytics/log-analytics-overview.md) arbetsyta.  De fördefinierade vyer som ingår visas som förvaras behållararbetsbelastningar och vad påverkar prestanda hälsotillståndet för Kubernetes-klustret så att du kan förstå:  

* Vilka behållare som körs på noden och deras genomsnittlig användning av processor och minne att identifiera flaskhalsar för resurser
* Identifiera där behållaren finns i en domänkontrollant och/eller poddar att se den övergripande prestandan för en domänkontrollant eller en pod 
* Granska arbetsbelastningar som körs på värden inte är relaterade till standardprocedurerna som stöd för poden Resursanvändning
* Förstå beteendet för klustret vid genomsnittliga och högsta belastning för att identifiera kapacitetsbehov och avgöra den maximala belastningen som den kan klara 

Om du vill övervaka och hantera Docker och Windows behållare-värdar att visa konfiguration, granskning och användningen av resurser finns i den [lösning för övervakning av behållare](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Krav 
Granska följande information innan du startar, så du kan förstå krav som stöds.

- En ny eller befintlig AKS-kluster
- En behållare OMS-agenten för Linux-versionen microsoft / oms:ciprod04202018 och senare. Versionsnumret representeras av ett datum följande format: *mmddyyyy*.  Den installeras automatiskt under publiceringen av hälsotillstånd för behållare.  
- En Log Analytics-arbetsyta.  Det kan skapas när du aktiverar övervakning av din nya AKS-kluster eller skapa ett till [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), eller från den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).
- Medlem i rollen som deltagare Log Analytics för att aktivera behållarövervakning av.  Läs mer om hur du styr åtkomst till en Log Analytics-arbetsyta, [hantera arbetsytor](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Komponenter 

Den här funktionen är beroende av en behållare OMS-agenten för Linux för att samla in prestanda- och händelsedata från alla noder i klustret.  Agenten har automatiskt distribuerat och registrerat med den angivna Log Analytics-arbetsytan när du aktiverar behållarövervakning. 

>[!NOTE] 
>Om du redan har distribuerat ett AKS-kluster kan aktivera du övervakning med hjälp av en angiven Azure Resource Manager-mall som visas längre fram i den här artikeln. Du kan inte använda `kubectl` om du vill uppgradera, ta bort, omdistribuera eller distribuera agenten.  
>

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Aktivera övervakning av behållare hälsotillstånd för ett nytt kluster
Du kan aktivera övervakning av ett nytt AKS-kluster under distributionen från Azure-portalen.  Följ stegen i snabbstartsartikeln [distribuera ett kluster i Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  När du är på den **övervakning** väljer **Ja** för alternativet **aktivera övervakning** att aktivera, och sedan välja en befintlig eller skapa en ny Log Analytics-arbetsyta.  

När övervakning är aktiverat alla konfigurationsåtgärder har slutförts kan du övervaka prestanda för ditt kluster från en av två sätt:

1. Direkt från AKS-kluster genom att välja **hälsotillstånd** i den vänstra rutan.<br><br> 
2. Genom att klicka på den **övervaka behållarens hälsa** panel på sidan AKS-kluster för det markerade klustret.  I Azure Monitor, Välj **hälsotillstånd** i den vänstra rutan.  

![Alternativ att välja hälsotillstånd för behållare i AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

När övervakning har aktiverats kan ta det ungefär 15 minuter innan du kan se driftdata för klustret.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Aktivera övervakning av behållare hälsotillstånd för befintliga hanterade kluster
Du kan aktivera övervakning av ett AKS-kluster som redan har distribuerats från Azure portal eller med den angivna Azure Resource Manager-mallen med hjälp av PowerShell-cmdleten **New-AzureRmResourceGroupDeployment** eller Azure CLI.  


### <a name="enable-from-azure-portal"></a>Aktivera från Azure-portalen
Utför följande steg om du vill aktivera övervakning av din AKS-behållare från Azure-portalen.

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver **behållare**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Kubernetes-tjänster**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. Välj en behållare i din lista över behållare.
3. På översiktssidan behållaren väljer **övervaka behållarens hälsa** och **registrering för hälsotillstånd för behållare och loggar** visas.
4. På den **registrering för hälsotillstånd för behållare och loggar** om du har en befintlig Log Analytics-arbetsyta i samma prenumeration som klustret, markerar du den i den nedrullningsbara listan.  Listan förväljer standardarbetsytan och plats AKS behållaren distribueras till i prenumerationen.<br><br> ![Aktivera hälsoövervakning för AKS-behållare](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Om du vill skapa en ny Log Analytics-arbetsyta för att lagra övervakningsdata från klustret, följer du stegen i [Cretae Log Analytics-arbetsytan](../log-analytics/log-analytics-quick-create-workspace.md) och se till att skapa arbetsytan i samma prenumeration som AKS-behållare distribuerat till.  
>
 
När övervakning har aktiverats kan ta det ungefär 15 minuter innan du kan se driftdata för klustret. 

### <a name="enable-using-azure-resource-manager-template"></a>Aktivera med hjälp av Azure Resource Manager-mall
Den här metoden innehåller två JSON-mallar, en mall anger konfigurationen för att aktivera övervakning och andra JSON-mallen innehåller parametervärden som du konfigurerar för att ange följande:

* Resurs-ID för AKS-behållare 
* Resursgrupp klustret har distribuerats i 
* Log Analytics-arbetsytan och region för att skapa arbetsytan i 

Log Analytics-arbetsytan måste du skapa dem manuellt.  För att skapa arbetsytan, du kan konfigurera ett via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), från den [Azure-portalen](../log-analytics/log-analytics-quick-create-workspace.md).

Om du inte är bekant med principerna för att distribuera resurser med hjälp av en mall med PowerShell, se [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)eller Azure CLI finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt.  Det krävs att du kör Azure CLI version 2.0.27 eller senare. Kör `az --version` att identifiera versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Skapa och köra mallen

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
3. Kopiera och klistra in följande JSON-syntax i filen:

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

4. Redigera värdet för **aksResourceId**, **aksResourceLocation** med värden som du hittar på den **översikt över AKS** för AKS-klustret.  Värdet för **workspaceResourceId** är fullständiga resurs-ID för Log Analytics-arbetsytan, som innehåller namnet på arbetsytan.  Även ange den plats som arbetsytan finns i för **workspaceRegion**.    
5. Spara filen som **existingClusterParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Använd följande PowerShell-kommandon från mappen som innehåller mallen:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar kan du se ett meddelande som liknar följande som innehåller resultatet:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Att köra följande kommando med Azure CLI på Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar kan du se ett meddelande som liknar följande som innehåller resultatet:

        ```azurecli
        provisioningState       : Succeeded
        ```
När övervakning har aktiverats kan ta det ungefär 15 minuter innan du kan se driftdata för klustret.  

## <a name="verify-agent-and-solution-deployment"></a>Kontrollera distributionen av agenten och lösning
Med agentversion *06072018* och högre, du kan kontrollera att både agenten och lösningen har distribuerats.  Du kan bara kontrollera distributionen av agenten med tidigare versioner av agenten.

### <a name="agent-version-06072018-and-higher"></a>Agentversion 06072018 och högre
Kör följande kommando för att kontrollera att agenten har distribuerats.   

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna för följande som anger den distribuerades korrekt:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Kontrollera distributionen av lösningen genom att köra följande kommando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Utdata bör likna för följande som anger den distribuerades korrekt:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Tidigare än 06072018 agentversion

Verifiera OMS-agent-version som getts ut före *06072018* har distribuerats korrekt, kör du följande kommando:  

```
kubectl get ds omsagent --namespace=kube-system
```

Utdata bör likna för följande som anger den distribuerades korrekt:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Visa prestanda användning
När du öppnar hälsotillstånd för behållare, anger sidan omedelbart prestanda utnyttjande av hela klustret.  Visa information om AKS-klustret är uppdelad i fyra perspektiv:

- Kluster
- Noder 
- Kontrollanter  
- Containrar

Gå till klustret och visar prestanda linjediagram nyckelprestandamått på klustret.  

![Prestandadiagram för exempel på fliken kluster](./media/monitoring-container-health/container-health-cluster-perfview.png)

Följande är en uppdelning av prestandamått som visas:

- Noden CPU-utnyttjande i % – det här diagrammet representerar en aggregerade perspektiv CPU-belastningen för hela klustret.  Du kan filtrera resultaten för tidsintervallet genom att välja *genomsnittlig*, *Min*, *Max*, *50*, *90*, och *95: e* från percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- Noden minne utnyttjande i % – det här diagrammet representerar en aggregerade perspektiv av minnesanvändningen för hela klustret.  Du kan filtrera resultaten för tidsintervallet genom att välja *genomsnittlig*, *Min*, *Max*, *50*, *90*, och *95: e* från percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- Antal noder – det här diagrammet representerar antalet noder och status från Kubernetes.  Status för noderna i klustret som representeras är *alla*, *redo*, och *inte klara* och kan filtreras individuellt eller det kombinerade antalet från Väljaren ovanför diagrammet.    
- Aktiviteten pod antal – det här diagrammet representerar pod antal och status från Kubernetes.  Status poddarna representeras är *alla*, *väntande*, *kör*, och *okänd* och kan filtreras individuellt eller det kombinerade antalet från den väljare ovanför diagrammet.  

Växla till fliken noder följer rad hierarkin Kubernetes-objektmodell som börjar med en nod i klustret.  Expandera noden och du ser en eller flera poddar som körs på noden och om det finns flera behållare grupperade en pod, de visas som den sista raden i hierarkin. Du kan också se hur många icke-pod relaterade arbetsbelastningar körs på värden om värden har processor eller minne.

![Exempel Kubernetes Node-hierarkin i prestandavyn](./media/monitoring-container-health/container-health-nodes-view.png)

Du kan välja domänkontrollanter eller behållare högst upp på sidan och granska status och Resursanvändning för dessa objekt.  Använda rutorna listrutan överst på skärmen för att filtrera efter namnområde, tjänst och nod. Om istället du vill granska minnesanvändningen, från den **mått** listrutan Välj **minne RSS** eller **arbetsminne**.  **Minne RSS** har endast stöd för Kubernetes version 1.8 och senare. Annars ser du värdena för **MIN %** dyker som *NaN %*, vilket är ett värde för typ av numeriska data som representerar en odefinierad eller inte går att representera värdet. 

![Behållaren noder prestandavy](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Som standard prestandadata baseras på de senaste sex timmarna men du kan ändra i fönstret med den **tidsintervall** nedrullningsbara lista finns i det övre högra hörnet på sidan. För tillfället sidan inte automatisk uppdatering, så du måste manuellt uppdatera den. Du kan också filtrera resultaten inom tidsintervallet genom att välja *genomsnittlig*, *Min*, *Max*, *50*, *90*, och *95: e* från Väljaren: e percentilen. 

![: E percentilen val för filtrering av data](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

I följande exempel du ser för noden *aks-nodepool-3977305*, värdet för **behållare** är 5, vilket är en sammanslagning av det totala antalet behållare som distribueras.

![Sammanslagning av behållare per nod-exempel](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Det kan du snabbt kan identifiera om du inte har en rätt balans mellan behållare mellan noderna i klustret.  

I följande tabell beskrivs den information som visas när du visar noder.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden |
| Status | Kubernetes vy av nod-status |
| GENOMSNITTLIG %, % FÖR MIN, MAX %, 50%, 90% | Genomsnittlig nod procent baserat på: e percentilen under den tidslängd som valts. |
| GENOMSN, MIN, MAX, 50, 90 | Genomsnittlig noder faktiskt värde baserat på: e percentilen under den tidslängd som valts.  Medelvärdet mäts från processor/minne gränsen för en nod. Det är genomsn värdet som rapporteras av värden för poddar och behållare. |
| Containrar | Antal behållare. |
| Drifttid | Representerar tid eftersom en nod startas eller startades om. |
| Kontrollanter | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt. Inte alla poddar ska finnas i en domänkontrollant så att vissa kan indikera att saknas. | 
| Trend Genomsnittlig %, % för MIN, MAX %, 50%, 90% | Stapeldiagram trend presentera: e percentilen mått % för kontrollenheten. |


Väljaren, Välj **styrenheter**.

![Välj domänkontrollanter vy](./media/monitoring-container-health/container-health-controllers-tab.png)

Här kan du se hälsotillståndet för prestanda för dina domänkontrollanter.

![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-health-controllers-view.png)

En rad hierarki börjar med en domänkontrollant och utökas kontrollanten så ser du en eller en eller flera behållare.  Expandera en pod och den sista raden visa behållaren som grupperats till din pod.  

I följande tabell beskrivs den information som visas när du visar domänkontrollanter.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten|
| Status | Insamling av status för behållarna när den har slutförts kör med status, till exempel *OK*, *Uppsagd*, *misslyckades* *stoppad*, eller  *Pausad*. Om behållaren körs, men status har inte korrekt visas eller hämtades inte av agenten och har inte svarat mer än 30 minuter, statusen är *okänd*. Ytterligare information om statusikonen finns i tabellen nedan.|
| GENOMSNITTLIG %, % FÖR MIN, MAX %, 50%, 90% | Rulla upp medelvärdet för varje entitet för valda mått och percentil medel-%. |
| GENOMSN, MIN, MAX, 50, 90  | Samlar in den genomsnittliga CPU millicore eller minne över prestanda behållaren för den valda: e percentilen.  Medelvärdet mäts från processor/minne gränsen för en pod. |
| Containrar | Totalt antal behållare för domänkontrollant eller pod. |
| Startar om | Summera för omstart räkningen från behållare. |
| Drifttid | Representerar tid efter att en behållare startades. |
| Node | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt. | 
| Trend Genomsnittlig %, % för MIN, MAX %, 50%, 90%| Stapeldiagram trend som representerar: e percentilen mått för kontrollenheten. |

Ikonerna i statusfältet onlinestatus behållare:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/monitoring-container-health/container-health-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/monitoring-container-health/container-health-grey-icon.png) | Senast rapporterat körs men har inte svarat mer än 30 minuter|
| ![Avslutade statusikon](./media/monitoring-container-health/container-health-green-icon.png) | Har stoppats eller gick inte att stoppa|

Statusikonen visar antalet baserat på din pod tillhandahåller. Den visar sämre två tillstånd och när du hovrar över statusen visar en sammanställd in status från alla poddar i behållaren.  Om det inte finns ett färdigt tillstånd, statusvärdet visas en **(0)**.  

Väljaren, Välj **behållare**.

![Välj behållare vy](./media/monitoring-container-health/container-health-containers-tab.png)

Här kan vi se prestandatillståndet för dina behållare.

![< namn > domänkontrollanter prestandavy](./media/monitoring-container-health/container-health-containers-view.png)

I följande tabell beskrivs den information som visas när du visar behållare.

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten|
| Status | Status för behållare, om sådana. Ytterligare information om statusikonen finns i tabellen nedan.|
| GENOMSNITTLIG %, % FÖR MIN, MAX %, 50%, 90% | Rulla upp medelvärdet för varje entitet för valda mått och percentil medel-%. |
| GENOMSN, MIN, MAX, 50, 90  | Samlar in den genomsnittliga CPU millicore eller minne över prestanda behållaren för den valda: e percentilen.  Medelvärdet mäts från processor/minne gränsen för en pod. |
| Pod | Behållaren där poden finns.| 
| Node |  Noden där behållaren finns. | 
| Startar om | Representerar tid efter att en behållare startades. |
| Drifttid | Representerar tid eftersom en behållare startades eller startas om. |
| Trend Genomsnittlig %, % för MIN, MAX %, 50%, 90% | Stapeldiagram trend som representerar genomsnittliga mått % av behållaren. |

Ikonerna i statusfältet onlinestatus av poddarna:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/monitoring-container-health/container-health-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/monitoring-container-health/container-health-grey-icon.png) | Senast rapporterat körs men har inte svarat mer än 30 minuter|
| ![Avslutade statusikon](./media/monitoring-container-health/container-health-terminated-icon.png) | Har stoppats eller gick inte att stoppa|
| ![Misslyckade statusikon](./media/monitoring-container-health/container-health-failed-icon.png) | Felaktigt tillstånd |

## <a name="container-data-collection-details"></a>Samling data-behållarinformation
Hälsotillstånd för behållare samlar in olika mått och loggfiler prestandadata från behållare-värdar och behållare. Data som samlas in var tredje minut.

### <a name="container-records"></a>Behållarposter

I följande tabell visas exempel på poster som samlas in av hälsotillstånd för behållare och vilka datatyper av som visas i sökresultaten för loggen.

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
Log Analytics kan hjälpa dig att söka trender, diagnostisera flaskhalsar, prognoser och korrelera data som kan hjälpa dig att avgöra om den aktuella klusterkonfigurationen presterar optimalt.  Fördefinierade loggsökningar tillhandahålls till omedelbart börja använda eller anpassa för att returnera information som du vill. 

Du kan utföra interaktiva analyser av data på arbetsytan genom att välja den **Visa logg** alternativet är tillgängligt längst till höger när du expanderar en domänkontrollant eller behållare.  **Loggsöknings-** visas rätt ovan sidan du var på i portalen.

![Analysera data i Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Behållaren loggar utdata vidarebefordras till logganalys är STDOUT- och STDERR. Eftersom behållare health övervakar Azure hanterade Kubernetes (AKS), samlas Kube system inte in idag på grund av den stora mängden data som genereras.     

### <a name="example-log-search-queries"></a>Exempel loggsökningsfrågor
Det är ofta bra att skapa frågor som börjar med ett exempel eller två och ändra dem efter dina behov. Du kan experimentera med följande Exempelfrågor för att skapa mer avancerade frågor.

| Fråga | Beskrivning | 
|-------|-------------|
| ContainerInventory<br> &#124;projektet dator, namn, bild, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Rendera tabell | Visa Information om alla behållarens livstid| 
| KubeEvents_CL<br> &#124;där not(isempty(Namespace_s))<br> &#124;Sortera efter TimeGenerated fall<br> &#124;Rendera tabell | Kubernetes-händelser|
| ContainerImageInventory<br> &#124;Sammanfatta AggregatedValue = antal() efter bild, ImageTag, körs | Avbildningslager | 
| **I Advanced Analytics väljer du linjediagram**:<br> Perf<br> &#124;där ObjectName == ”behållare” och CounterName == ”% processortid”<br> &#124;Sammanfatta AvgCPUPercent = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Processorprestanda för behållare | 
| **I Advanced Analytics väljer du linjediagram**:<br> Perf &#124; där ObjectName == ”behållare” och CounterName == ”Instansminne (MB) användning”<br> &#124;Sammanfatta AvgUsedMemory = avg(CounterValue) efter bin (TimeGenerated, 30m), instansnamn | Behållare-minne |

## <a name="how-to-stop-monitoring-with-container-health"></a>Hur du stoppar övervakningen med hälsotillstånd för behållare
När du har aktiverat övervakningen av AKS-behållare som du beslutar att du inte längre vill övervaka den, kan du *avanmäla dig* med hjälp av de angivna Azure Resource Manager-mallarna med PowerShell-cmdlet  **Ny AzureRmResourceGroupDeployment** eller Azure CLI.  En JSON-mallen används för att konfigurationen av *avanmäla dig* och andra JSON-mallen innehåller parametervärden som du konfigurerar för att ange AKS-ID och resurs klusterresursgruppen klustret distribueras i.  Om du inte är bekant med principerna för att distribuera resurser med hjälp av en mall med PowerShell, se [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) eller Azure CLI finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Om du väljer att använda Azure CLI, måste du först installera och använda CLI lokalt.  Det krävs att du kör Azure CLI version 2.0.27 eller senare. Kör `az --version` att identifiera versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

4. Redigera värdet för **aksResourceId** och **aksResourceLocation** med värdena för AKS-kluster som finns på den **egenskaper** sidan för det markerade klustret.

    ![Egenskapssidan för behållare](./media/monitoring-container-health/container-properties-page.png)

    När du är på den **egenskaper** kan också kopiera den **Arbetssyteresurs-ID**.  Det här värdet krävs om du vill ta bort Log Analytics-arbetsytan senare, vilket inte utförs som en del av den här processen.  

5. Spara filen som **OptOutParam.json** till en lokal mapp.
6. Nu är det dags att distribuera den här mallen. 

    * Så här använder du följande PowerShell-kommandon från mappen som innehåller mallen:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar returneras ett meddelande som liknar följande som innehåller resultatet:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Att köra följande kommando med Azure CLI på Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Konfigurationsändringen kan ta några minuter att slutföra. När den är klar returneras ett meddelande som liknar följande som innehåller resultatet:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Om arbetsytan har skapats enbart för att stöd för övervakning av klustret och inte längre behövs kan behöva du manuellt ta bort den. Om du inte är bekant med hur du tar bort en arbetsyta kan se [ta bort en Azure Log Analytics-arbetsyta med Azure portal](../log-analytics/log-analytics-manage-del-workspace.md).  Glöm inte att den **Arbetssyteresurs-ID** vi kopierade tidigare i steg 4, du kommer att behöva som.  

## <a name="troubleshooting"></a>Felsökning
Det här avsnittet innehåller information för att felsöka problem med hälsotillstånd för behållare.

Om hälsotillstånd för behållare som har aktiverats och konfigurerats men du inte ser några statusinformation eller resultat i Log Analytics när du utför en loggsökning kan du utföra följande steg för att diagnostisera problemet.   

1. Kontrollera statusen för agenten genom att köra följande kommando: 

    `kubectl get ds omsagent --namespace=kube-system`

    Utdata bör likna för följande som anger den distribuerades korrekt:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Kontrollera distributionsstatusen lösning med agentversion *06072018* eller senare genom att köra följande kommando:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Utdata bör likna för följande som anger den distribuerades korrekt:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Kontrollera status för poden att verifiera att den körs eller inte genom att köra följande kommando: `kubectl get pods --namespace=kube-system`

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

4. Loggarna för agenten. När behållare agenten distribueras, körs den en snabb kontroll genom att köra OMI kommandon och visar vilken version av agenten och 
5.  providern. Om du vill se att agenten har integrerats har, kör du följande kommando: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[Söka loggarna](../log-analytics/log-analytics-log-search.md) att visa detaljerad behållare hälsotillstånd och information om programprestanda.  