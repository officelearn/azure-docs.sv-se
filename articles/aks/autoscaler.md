---
title: Kubernetes på Azure - kluster Autoskalningen
description: Lär dig hur du använder autoskalningen kluster med Azure Kubernetes Service (AKS) för att automatiskt skala ditt kluster för att möta efterfrågan.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186721"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Autoskalningen-kluster på Azure Kubernetes Service (AKS) – förhandsversion

Azure Kubernetes Service (AKS) ger en flexibel lösning för att distribuera ett hanterat Kubernetes-kluster i Azure. Som resurs efterfrågan ökar, klustret autoskalningen innebär din klustret kan växa för att uppfylla som kräver utifrån villkor du anger. Klustret autoskalningen (CA) gör detta genom att skala din agentnoder utifrån väntande poddar. Den söker igenom klustret med jämna mellanrum för att söka efter väntande poddar eller tom noder och ökar storleken om det är möjligt. Som standard CA: N söker efter väntande poddar var tionde sekund och tar bort en nod om det inte behövs i mer än 10 minuter. När det används med vågrät pod autoskalningen (HPA), uppdateras HPA poddrepliker och resurser enligt begäran. Om det inte finns tillräckligt med noder eller onödiga noder enligt den här pod som skalning, CA: N kommer att besvara och schemalägga poddarna på den nya uppsättningen noder.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) kluster autoskalningen-integrering är för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="prerequisites"></a>Förutsättningar

Det här dokumentet förutsätter att du har ett RBAC-aktiverade AKS-kluster. Om du behöver ett AKS-kluster finns i den [Snabbstart för Azure Kubernetes Service (AKS)][aks-quick-start].

 Om du vill använda kluster autoskalningen ditt kluster med Kubernetes v1.10.X eller högre och måste vara RBAC-aktiverade. Om du vill uppgradera ditt kluster, kan du läsa artikeln om [uppgradera ett AKS-kluster][aks-upgrade].

## <a name="gather-information"></a>Samla in information

I följande lista visas all information du måste ange i definitionen för autoskalningen.

- *Prenumerations-ID*: ID som motsvarar den prenumeration som används för det här klustret
- *Namn på resursgrupp* : namnet på resursgruppen som klustret tillhör 
- *Klusternamn*: namnet på klustret
- *Klient-ID*: App-ID som har beviljats av behörighet som genererar steg
- *Klienthemlighet*: apphemlighet har beviljats av behörighet som genererar steg
- *Klient-ID*: ID för innehavaren (ägare)
- *Resursgrupp för noden*: namnet på resursgruppen som innehåller agentnoder i klustret
- *Nodnamnet för poolen*: namnet på noden programpool du vill skala
- *Lägsta antal noder*: minsta antal noder som finns i klustret
- *Maximalt antal noder*: maximalt antal noder som finns i klustret
- *Typ av virtuell dator*: replikeringstjänsten som används för att skapa Kubernetes-kluster

Få ditt prenumerations-ID med: 

``` azurecli
az account show --query id
```

Skapa en uppsättning autentiseringsuppgifter för Azure genom att köra följande kommando:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

App-ID, lösenord och Klientorganisations-ID är din clientID, clientSecret och tenantID i följande steg.

Hämta namnet på noden poolen genom att köra följande kommando. 

```console
$ kubectl get nodes --show-labels
```

Resultat:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Extrahera värdet för etiketten **agentpool**. Standardnamnet för nodpoolen i ett kluster är ”nodepool1”.

Hämtar namnet på resursgruppen noden genom att extrahera värdet för etiketten **kubernetes.azure.com<span></span>/cluster**. Noden resursgruppens namn är vanligtvis formulärets MC_ [resursgrupp]\_[klusternamnet] _ [plats].

Parametern vmType refererar till den tjänst som används, vilket är här AKS.

Du bör nu ha följande information:

- prenumerations-ID
- ResourceGroup
- Klusternamn
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

Därefter koda allt av följande värden med base64. Till exempel att koda VMType värdet med base64:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Skapa hemliga
Med dessa data kan skapa en hemlighet för distributionen med värdena i de föregående stegen i följande format:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

## <a name="create-a-deployment-chart"></a>Skapa ett diagram för distribution

Skapa en fil med namnet `aks-cluster-autoscaler.yaml`, och kopiera följande YAML-kod till den.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Kopiera och klistra in hemligheten som skapades i föregående steg och infoga dem i början av filen.

Därefter för att ange en uppsättning noder, Fyll i argumentet för `--nodes` under `command` i formuläret MIN:MAX:NODE_POOL_NAME. Till exempel: `--nodes=3:10:nodepool1` anger det minsta antalet noder till 3, det maximala antalet noder till 10 och namnet på noden poolen till nodepool1.

Fyll sedan i fältet avbildningen under **behållare** med versionen av autoskalningen för kluster som du vill använda. AKS kräver v1.2.2 eller senare. I det här exemplet använder v1.2.2.

## <a name="deployment"></a>Distribution

Distribuera kluster – autoskalningen genom att köra

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Om du vill kontrollera om klustret autoskalningen körs, använder du följande kommando och kontrollera listan över poddar. Om det finns en pod prefixet ”kluster-autoskalningen” som körs, har kluster-autoskalningen distribuerats.

```console
kubectl -n kube-system get pods
```

Om du vill visa status för autoskalningen kluster, kör

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="next-steps"></a>Nästa steg

Om du vill använda kluster autoskalningen med vågrät pod autoskalningen, Kolla in [skala Kubernetes-program och infrastruktur][aks-tutorial-scale].

Läs mer om att distribuera och hantera AKS med självstudierna om AKS.

> [!div class="nextstepaction"]
> [Självstudier om AKS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
