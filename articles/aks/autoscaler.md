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
ms.openlocfilehash: 06b93273ea096f2d24df4411a64ce99d054892cf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058819"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Autoskalningen-kluster på Azure Kubernetes Service (AKS) – förhandsversion

Azure Kubernetes Service (AKS) ger en flexibel lösning för att distribuera ett hanterat Kubernetes-kluster i Azure. Som resurs efterfrågan ökar, klustret autoskalningen innebär din klustret kan växa för att uppfylla som kräver utifrån villkor du anger. Klustret autoskalningen (CA) gör detta genom att skala din agentnoder utifrån väntande poddar. Den söker igenom klustret med jämna mellanrum för att söka efter väntande poddar eller tom noder och ökar storleken om det är möjligt. Som standard CA: N söker efter väntande poddar var tionde sekund och tar bort en nod om det inte behövs i mer än 10 minuter. När det används med den [vågrät pod autoskalningen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA), HPA uppdateras poddrepliker och resurser enligt begäran. Om det inte finns tillräckligt med noder eller onödiga noder enligt den här pod som skalning, CA: N kommer att besvara och schemalägga poddarna på den nya uppsättningen noder.

Den här artikeln beskriver hur du distribuerar klustret autoskalningen på agentnoderna. Men eftersom autoskalningen kluster distribueras i namnområdet kube system, skalas autoskalningen inte ned noden som kör den pod.

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) kluster autoskalningen-integrering är för närvarande i **förhandsversion**. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="prerequisites"></a>Förutsättningar

Det här dokumentet förutsätter att du har ett RBAC-aktiverade AKS-kluster. Om du behöver ett AKS-kluster finns i den [Snabbstart för Azure Kubernetes Service (AKS)][aks-quick-start].

 Om du vill använda kluster autoskalningen ditt kluster med Kubernetes v1.10.X eller högre och måste vara RBAC-aktiverade. Om du vill uppgradera ditt kluster, kan du läsa artikeln om [uppgradera ett AKS-kluster][aks-upgrade].

## <a name="gather-information"></a>Samla in information

Om du vill generera behörigheter för ditt kluster autoskalningen att köras i ditt kluster, kör du följande bash-skript:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

När du har följt stegen i skriptet skriptets utdata är din information i form av en hemlighet, t.ex:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Hämta sedan namnet på noden poolen genom att köra följande kommando. 

```console
$ kubectl get nodes --show-labels
```

Resultat:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Extrahera värdet för etiketten **agentpool**. Standardnamnet för nodpoolen i ett kluster är ”nodepool1”.

Med hjälp av din hemlighet och nod-pool, skapar du en distribution-diagram.

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
kubectl create -f aks-cluster-autoscaler.yaml
```

Om du vill kontrollera om klustret autoskalningen körs, använder du följande kommando och kontrollera listan över poddar. Det bör finnas en pod prefixet ”kluster-autoskalningen” som körs. Om du ser det här har klustret-autoskalningen distribuerats.

```console
kubectl -n kube-system get pods
```

Om du vill visa status för autoskalningen kluster, kör

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Tolka autoskalningen klusterstatus

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

Autoskalningen klusterstatus kan du se status för autoskalningen kluster på två olika nivåer: hela och inom varje nodgrupp. Eftersom AKS stöder för närvarande endast en nodpool, är desamma i de här måtten.

* Hälsotillstånd anger den övergripande hälsan för noderna. Om klustret autoskalningen struggles du skapar eller tar bort noder i klustret, ändras den här statusen till ”inte felfri”. Det finns också en detaljerad analys av status för olika noder:
    * ”Klar” syftar på en nod är klara att ha poddar som schemalagts på den.
    * ”Unready” innebär att en nod som delades upp när den startats.
    * ”Ej startad” innebär att en nod inte är fullständigt startat ännu.
    * ”LongNotStarted” innebär att en nod inte kunde starta inom en rimlig tidsgräns.
    * ”Registrerade innebär en nod registreras i gruppen
    * ”Avregistrera” innebär att en nod är tillgänglig på klustret providern sida men kunde inte registreras i Kubernetes.
  
* ScaleUp kan du kontrollera när klustret anger en skala upp ska ske i klustret.
    * En övergång är när antalet noder i klustret ändras eller status för en nod ändras.
    * Antalet noder som är klar är antalet noder som är tillgänglig och redo i klustret. 
    * CloudProviderTarget är antalet noder som klustret autoskalningen har fastställt att klustret behöver hantera belastningen.

* ScaleDown kan du kontrollera om det finns kandidater för att skala ned. 
    * En kandidat för skala ned är en nod har fastställt att klustret autoskalningen kan tas bort utan att påverka klustrets förmåga att hantera belastningen. 
    * De tid som anges visar senast checkades klustret för att skala ned kandidater och dess senaste övergångstid.

Slutligen under händelser kan du få upp alla skalor eller skala ned händelser, misslyckad eller lyckad, och deras tider som klustret autoskalningen utfört.

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
