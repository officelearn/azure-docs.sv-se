---
title: Skapa datakontrollant med [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
description: Skapa en Azure Arc-datakontrollant på ett typiskt Kubernetes-kluster med flera noder som du redan har skapat med hjälp av [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 94f347cc24c675c69c69dad6a7d7a796b395c1a6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493621"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Skapa en Azure Arc-dataenhet med hjälp av [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Läs avsnittet [skapa data styrenheten för Azure båg](create-data-controller.md) för översikts information.

Om du vill skapa data styrenheten för Azure-bågen med hjälp av [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] måste du ha [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installerat.

   [Installera [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Oavsett vilken mål plattform du väljer måste du ange följande miljövariabler innan du skapar användaren för administratörs användaren för datakontrollanten. Du kan ange autentiseringsuppgifterna till andra personer som måste ha administratörs behörighet till datakontrollanten vid behov.

**AZDATA_USERNAME** ett användar namn som du väljer för administratörs användaren för data kontrollanten. Exempel: `arcadmin`

**AZDATA_PASSWORD** – ett valfritt lösen ord för användaren som administrerar data kontrollanten. Lösen ordet måste innehålla minst åtta tecken och innehålla tecken från tre av följande fyra uppsättningar: versaler, gemena bokstäver, siffror och symboler.

### <a name="linux-or-macos"></a>Linux eller macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

Du måste ansluta och autentisera till ett Kubernetes-kluster och ha en befintlig Kubernetes-kontext markerad innan du påbörjar skapandet av data styrenheten för Azure-bågen. Hur du ansluter till ett Kubernetes-kluster eller en tjänst varierar. Se dokumentationen för den Kubernetes-distribution eller tjänst som du använder för att ansluta till Kubernetes-API-servern.

Du kan kontrol lera att du har en aktuell Kubernetes-anslutning och bekräfta din aktuella kontext med följande kommandon.

```console
kubectl get namespace

kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>Skapa data styrenheten för Azure-bågen

> [!NOTE]
> Du kan använda ett annat värde för `--namespace` parametern för kommandot azdata Arc DC Create i exemplen nedan, men se till att använda namn områdets namn för `--namespace parameter` alla andra kommandon nedan.

Följ lämpligt avsnitt nedan beroende på din mål plattform för att konfigurera skapandet.

[Skapa på Azure Kubernetes service (AKS)](#create-on-azure-kubernetes-service-aks)

[Skapa på AKS-motorn på Azure Stack Hub](#create-on-aks-engine-on-azure-stack-hub)

[Skapa på AKS på Azure Stack HCI](#create-on-aks-on-azure-stack-hci)

[Skapa på Azure Red Hat OpenShift (ARO)](#create-on-azure-red-hat-openshift-aro)

[Skapa i Red Hat OpenShift container Platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)

[Skapa vid öppen källkod, överordnad Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[Skapa på AWS Elastic Kubernetes service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)

[Skapa på Google Cloud Kubernetes Engine service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Skapa på Azure Kubernetes service (AKS)

Som standard använder AKS-distributions profilen `managed-premium` lagrings klassen. `managed-premium`Lagrings klassen fungerar bara om du har virtuella datorer som har distribuerats med hjälp av VM-avbildningar som har Premium diskar.

Om du ska använda `managed-premium` som lagrings klass kan du köra följande kommando för att skapa data kontrollen. Ersätt plats hållarna i kommandot med resurs gruppens namn, prenumerations-ID och Azure-plats.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Om du inte är säker på vilken lagrings klass som ska användas bör du använda `default` lagrings klassen som stöds oavsett vilken VM-typ som du använder. Det ger bara den snabbaste prestandan.

Om du vill använda `default` lagrings klassen kan du köra det här kommandot:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Skapa på AKS-motorn på Azure Stack Hub

Som standard använder distributions profilen `managed-premium` lagrings klassen. `managed-premium`Lagrings klassen fungerar bara om du har distribuerade virtuella datorer som har distribuerats med hjälp av virtuella dator avbildningar som har Premium diskar på Azure Stack Hub.

Du kan köra följande kommando för att skapa datakontrollanten med hjälp av lagrings klassen Managed-Premium:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Om du inte är säker på vilken lagrings klass som ska användas bör du använda `default` lagrings klassen som stöds oavsett vilken VM-typ som du använder. I Azure Stack hubb backas Premium disks och standard diskar av samma lagrings infrastruktur. Därför förväntas de ge samma allmänna prestanda, men med olika IOPS-gränser.

Om du vill använda `default` lagrings klassen kan du köra det här kommandot.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Skapa på AKS på Azure Stack HCI

Som standard använder distributions profilen en lagrings klass med namnet `default` och tjänst typen `LoadBalancer` .

Du kan köra följande kommando för att skapa data styrenheten med hjälp av `default` lagrings klassen och tjänst typen `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Skapa på Azure Red Hat OpenShift (ARO)

Om du vill skapa en datakontrollant på en Red Hat OpenShift-växel måste du köra följande kommandon mot klustret för att minska säkerhets begränsningarna. Detta är ett tillfälligt krav som kommer att tas bort i framtiden.
> [!NOTE]
>   Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` .

Börja med att hämta den anpassade säkerhets kontext begränsningen (SCC) från [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) och tillämpa den på klustret.

Du kan köra följande kommando för att skapa data styrenheten:
> [!NOTE]
>   Använd samma namnrymd här och i `oc adm policy add-scc-to-user` kommandona ovan. Exempel är `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Skapa i Red Hat OpenShift container Platform (OCP)


> [!NOTE]
> Om du använder Red Hat OpenShift container Platform på Azure rekommenderar vi att du använder den senaste tillgängliga versionen.

Om du vill skapa en datakontrollant med Red Hat OpenShift-plattform måste du köra följande kommandon mot klustret för att minska säkerhets begränsningarna. Detta är ett tillfälligt krav som kommer att tas bort i framtiden.
> [!NOTE]
>   Använd samma namnrymd här och i `azdata arc dc create` kommandot nedan. Exempel är `arc` .

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

Du måste också bestämma vilken lagrings klass som ska användas genom att köra följande kommando.

```console
kubectl get storageclass
```

Börja med att skapa en ny anpassad distributions profil fil baserat på distributions profilen för Azure-Arc-OpenShift genom att köra följande kommando. Det här kommandot skapar en katalog `custom` i din aktuella arbets katalog och en anpassad distributions profil fil `control.json` i den katalogen.

Använd profilen `azure-arc-openshift` för OpenShift container Platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
Använd profilen `azure-arc-azure-openshift` för Azure RedHat Open Shift.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

Nu ska du ange önskad lagrings klass genom att ersätta `<storageclassname>` i kommandot nedan med namnet på den lagrings klass som du vill använda som fastställdes genom att köra `kubectl get storageclass` kommandot ovan.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Som standard används den Azure-Arc-OpenShift-distributions profil `NodePort` som tjänst typen. Om du använder ett OpenShift-kluster som är integrerat med en belastningsutjämnare kan du ändra konfigurationen så att den använder typen LoadBalancer genom att använda följande kommando:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

När du använder OpenShift kanske du vill köra med standard säkerhets principerna i OpenShift eller om du ofta vill låsa miljön mer än vanligt. Du kan välja att inaktivera vissa funktioner för att minimera de behörigheter som krävs vid distributions tiden och vid körning genom att köra följande kommandon.

Det här kommandot inaktiverar mått samlingar om poddar. Du kommer inte att kunna se mått för poddar i Grafana-instrumentpaneler om du inaktiverar den här funktionen. Standardvärdet är true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Det här kommandot inaktiverar mått samlingar om noder. Du kommer inte att kunna se mått för noder i Grafana-instrumentpaneler om du inaktiverar den här funktionen. Standardvärdet är true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Detta kommando inaktiverar möjligheten att ta minnes dum par i fel söknings syfte.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

Nu är du redo att skapa datakontrollanten med hjälp av följande kommando.
> [!NOTE]
>   Använd samma namnrymd här och i `oc adm policy add-scc-to-user` kommandona ovan. Exempel är `arc` .

> [!NOTE]
>   `--path`Parametern ska peka på _katalogen_ som innehåller control.jspå filen som inte är control.jssjälva filen.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Skapa vid öppen källkod, överordnad Kubernetes (kubeadm)

Som standard använder kubeadm-distributions profilen en lagrings klass med namnet `local-storage` och tjänst typen `NodePort` . Om detta är acceptabelt kan du hoppa över anvisningarna nedan som anger önskad lagrings klass och tjänst typ och kör `azdata arc dc create` kommandot direkt nedan.

Om du vill anpassa din distributions profil för att ange en angiven lagrings klass och/eller tjänst typ, börjar du med att skapa en ny anpassad distributions profil fil baserat på kubeadm distributions profil genom att köra följande kommando. Det här kommandot skapar en katalog `custom` i din aktuella arbets katalog och en anpassad distributions profil fil `control.json` i den katalogen.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

Du kan leta upp tillgängliga lagrings klasser genom att köra följande kommando.

```console
kubectl get storageclass
```

Nu ska du ange önskad lagrings klass genom att ersätta `<storageclassname>` i kommandot nedan med namnet på den lagrings klass som du vill använda som fastställdes genom att köra `kubectl get storageclass` kommandot ovan.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Som standard används kubeadm distributions profil `NodePort` som tjänst typ. Om du använder ett Kubernetes-kluster som är integrerat med en belastningsutjämnare kan du ändra konfigurationen med hjälp av följande kommando.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

Nu är du redo att skapa datakontrollanten med hjälp av följande kommando.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Skapa på AWS Elastic Kubernetes service (EKS)

Som standard är lagrings klassen EKS `gp2` och tjänst typen `LoadBalancer` .

Kör följande kommando för att skapa datakontrollanten med den angivna EKS-distributions profilen.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Skapa på Google Cloud Kubernetes Engine service (GKE)

Som standard är lagrings klassen GKE `standard` och tjänst typen `LoadBalancer` .

Kör följande kommando för att skapa datakontrollanten med den angivna GKE-distributions profilen.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

När du har kört kommandot fortsätter du med att [övervaka skapande status](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet `arc` . Om du har använt ett annat namn på namn område/data enhet kan du ersätta `arc` med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan. Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).
