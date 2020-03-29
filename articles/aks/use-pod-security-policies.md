---
title: Använda säkerhetsprinciper för pod i Azure Kubernetes Service (AKS)
description: Lär dig hur du styr pod-åtkomst med PodSecurityPolicy i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 74177136a7a61186ab1d273b57dbfce550a18ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914542"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview - Skydda ditt kluster med hjälp av pod säkerhetsprinciper i Azure Kubernetes Service (AKS)

Om du vill förbättra säkerheten för AKS-klustret kan du begränsa vilka poddar som kan schemaläggas. Poddar som begär resurser som du inte tillåter kan inte köras i AKS-klustret. Du definierar den här åtkomsten med hjälp av pod-säkerhetsprinciper. Den här artikeln visar hur du använder pod säkerhetsprinciper för att begränsa distributionen av poddar i AKS.

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäningsanmälan. Förhandsvisningar tillhandahålls "i nu och "som tillgängligt" och är undantagna från servicenivåavtalen och den begränsade garantin. AKS-förhandsvisningar omfattas delvis av kundsupport efter bästa ansträngning. Därför är dessa funktioner inte avsedda för produktionsanvändning. Mer information finns i följande supportartiklar:
>
> * [Aks-supportpolicyer][aks-support-policies]
> * [Vanliga frågor och svar om Azure-support][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver Azure CLI version 2.0.61 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för förhandsversion

Om du vill använda säkerhetsprinciper för pod behöver du *VERSION FÖR AKS-förhandsversion av CLI-tillägg* 0.4.1 eller senare. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrera funktionen för säkerhetsprincip för pod

Om du vill skapa eller uppdatera ett AKS-kluster för att använda pod-säkerhetsprinciper aktiverar du först en funktionsflagga på din prenumeration. Om du vill registrera funktionsflaggan *PodSecurityPolicyPreview* använder du kommandot [för a-funktionsregister][az-feature-register] som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte registrera den funktionen. När du har aktiverat vissa förhandsversionsfunktioner kan standardvärden användas för alla AKS-kluster som sedan skapas i prenumerationen. Aktivera inte förhandsgranskningsfunktioner på produktionsprenumerationer. Använd en separat prenumeration för att testa förhandsgranskningsfunktioner och samla in feedback.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Det tar några minuter innan statusen *visas.* Du kan kontrollera registreringsstatus med kommandot [az-funktionslista:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *Microsoft.ContainerService-resursprovidern* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Översikt över pod säkerhetsprinciper

I ett Kubernetes-kluster används en åtkomstkontrollant för att avlyssna begäranden till API-servern när en resurs ska skapas. Åtkomstkontrollanten kan sedan *validera* resursbegäran mot en uppsättning regler eller *stänga* av resursen för att ändra distributionsparametrar.

*PodSecurityPolicy* är en antagningskontroll som validerar en pod-specifikation som uppfyller dina definierade krav. Dessa krav kan begränsa användningen av privilegierade behållare, åtkomst till vissa typer av lagring eller den användare eller grupp som behållaren kan köra som. När du försöker distribuera en resurs där pod-specifikationerna inte uppfyller kraven i pod-säkerhetsprincipen nekas begäran. Den här möjligheten att styra vilka poddar som kan schemaläggas i AKS-klustret förhindrar vissa möjliga säkerhetsproblem eller eskalering av privilegier.

När du aktiverar pod-säkerhetsprincipen i ett AKS-kluster tillämpas vissa standardprinciper. Dessa standardprinciper ger en out-of-the-box-upplevelse för att definiera vad poddar kan schemaläggas. Klusteranvändare kan dock stöta på problem med att distribuera poddar tills du definierar dina egna principer. Det rekommenderade tillvägagångssättet är att:

* Skapa ett AKS-kluster
* Definiera dina egna säkerhetsprinciper för pod
* Aktivera säkerhetsprincipfunktionen för pod

Om du vill visa hur standardprinciperna begränsar pod-distributioner aktiverar vi först funktionen för pod-säkerhetsprinciper i den här artikeln och skapar sedan en anpassad princip.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivera säkerhetsprincip för pod i ett AKS-kluster

Du kan aktivera eller inaktivera pod-säkerhetsprincipen med kommandot [az aks update.][az-aks-update] I följande exempel kan pod-säkerhetsprincipen i klusternamnet *myAKSCluster* i resursgruppen *myResourceGroup*.

> [!NOTE]
> För verklig användning ska du inte aktivera pod-säkerhetsprincipen förrän du har definierat dina egna anpassade principer. I den här artikeln aktiverar du pod-säkerhetsprincipen som ett första steg för att se hur standardprinciperna begränsar pod-distributionerna.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>AKS-standardprinciper för standard

När du aktiverar pod-säkerhetsprincipen skapar AKS en standardprincip med namnet *privilegierad*. Redigera eller ta inte bort standardprincipen. Skapa i stället egna principer som definierar de inställningar som du vill styra. Låt oss först titta på vad dessa standardprinciper är hur de påverkar pod-distributioner.

Om du vill visa tillgängliga principer använder du kommandot [kubectl get psp,][kubectl-get] som visas i följande exempel

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Säkerhetsprincipen för *privilegierad* pod tillämpas på alla autentiserade användare i AKS-klustret. Den här tilldelningen styrs av ClusterRoles och ClusterRoleBindings. Använd kommandot [kubectl get clusterrolebindings][kubectl-get] och sök efter *standard:privilegierad:* bindning:

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

Som visas i följande komprimerade utdata tilldelas *psp:restricted* ClusterRole till alla *system:autentiserade* användare. Den här möjligheten ger en grundläggande nivå av begränsningar utan att dina egna principer har definierats.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Det är viktigt att förstå hur dessa standardprinciper interagerar med användarbegäranden om att schemalägga poddar innan du börjar skapa dina egna säkerhetsprinciper för podden. I de närmaste avsnitten ska vi schemalägga några poddar för att se dessa standardprinciper i praktiken.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Skapa en testanvändare i ett AKS-kluster

När du använder kommandot [az aks get-credentials][az-aks-get-credentials] läggs *administratörsautentiseringsuppgifterna* för `kubectl` AKS-klustret som standard till i konfigurationen. Administratörsanvändaren kringgår efterlevnaden av pod-säkerhetsprinciper. Om du använder Azure Active Directory-integrering för AKS-kluster kan du logga in med autentiseringsuppgifterna för en icke-administratörsanvändare för att se tvingande principer i aktion. I den här artikeln ska vi skapa ett testanvändarkonto i AKS-klustret som du kan använda.

Skapa ett exempelnamnområde med namnet *psp-aks* för testresurser med kommandot [kubectl create namespace.][kubectl-create] Skapa sedan ett tjänstkonto med namnet *icke-användare* med kommandot [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Skapa sedan ett rollbindning för *icke-användare* att utföra grundläggande åtgärder i namnområdet med kommandot [kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Skapa aliaskommandon för administratörsanvändare och användare som inte är administratörer

Om du vill markera skillnaden mellan `kubectl` den vanliga administratörsanvändaren när du använder och den icke-administratörsanvändare som skapats i föregående steg skapar du två kommandoradsalias:

* **Kubectl-admin alias** är för den vanliga administratörsanvändaren, och är begränsad till *psp-aks* namnområdet.
* Det **kubectl-nonadminuser-aliaset** är för den *icke-användare som* skapats i föregående steg och begränsas till *psp-aks-namnområdet.*

Skapa dessa två alias enligt följande kommandon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testa skapandet av en privilegierad pod

Låt oss först testa vad som händer när du `privileged: true`schemalägger en pod med säkerhetskontexten . Den här säkerhetskontexten eskalerar poddens privilegier. I föregående avsnitt som visade standardsäkerhetsprinciperna för AKS-pod bör den *begränsade* principen neka den här begäran.

Skapa en `nginx-privileged.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Podden kan inte schemaläggas, vilket visas i följande exempelutdata:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Podden når inte schemaläggningsfasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa skapandet av en oprivilegierad pod

I föregående exempel begärde pod-specifikationen privilegierad eskalering. Den här begäran nekas av *standardsäkerhetsprincipen* för begränsad pod, så podden kan inte schemaläggas. Nu kör vi samma NGINX-pod utan begäran om eskalering av privilegiet.

Skapa en `nginx-unprivileged.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes schemaläggare accepterar pod-begäran. Men om du tittar på status `kubectl get pods`för pod med , det finns ett fel:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Använd [kommandot kubectl describe pod][kubectl-describe] för att titta på händelserna för podden. I följande komprimerade exempel visas behållaren och avbildningen som kräver rotbehörigheter, även om vi inte begärde dem:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Även om vi inte begärde någon privilegierad åtkomst måste behållaravbildningen för NGINX skapa en bindning för port *80*. För att binda portar *1024* och lägre krävs *rotanvändaren.* När podden försöker starta nekar den *begränsade* pod-säkerhetsprincipen den här begäran.

Det här exemplet visar att standardpodssäkerhetsprinciperna som skapas av AKS är i kraft och begränsar de åtgärder som en användare kan utföra. Det är viktigt att förstå beteendet hos dessa standardprinciper, eftersom du kanske inte förväntar dig att en grundläggande NGINX-pod ska nekas.

Innan du går vidare till nästa steg tar du bort den här testenheten med kommandot [kubectl delete pod:][kubectl-delete]

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testa skapandet av en pod med en specifik användarkontext

I föregående exempel försökte behållaravbildningen automatiskt använda rot för att binda NGINX till port 80. Den här begäran nekades av *standardsäkerhetsprincipen* för begränsad pod, så podden kan inte starta. Nu kör vi samma NGINX-pod med en specifik `runAsUser: 2000`användarkontext, till exempel .

Skapa en `nginx-unprivileged-nonroot.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Skapa podden med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes schemaläggare accepterar pod-begäran. Men om du tittar på status `kubectl get pods`för pod med , det finns ett annat fel än föregående exempel:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Använd [kommandot kubectl describe pod][kubectl-describe] för att titta på händelserna för podden. Följande komprimerade exempel visar pod-händelserna:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Händelserna anger att behållaren har skapats och startats. Det finns inget omedelbart uppenbart om varför podden är i ett misslyckat tillstånd. Låt oss titta på pod loggar med [kubectl loggar][kubectl-logs] kommandot:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Följande exempelloggutdata ger en indikation på att det finns ett behörighetsfel inom själva NGINX-konfigurationen när tjänsten försöker starta. Det här felet orsakas återigen av att behöva binda till port 80. Även om pod-specifikationen definierade ett vanligt användarkonto är det här användarkontot inte tillräckligt på OS-nivå för att NGINX-tjänsten ska kunna starta och binda till den begränsade porten.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Återigen är det viktigt att förstå beteendet hos standardpodssäkerhetsprinciperna. Detta fel var lite svårare att spåra, och igen, du kanske inte förväntar dig en grundläggande NGINX pod att nekas.

Innan du går vidare till nästa steg tar du bort den här testenheten med kommandot [kubectl delete pod:][kubectl-delete]

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Skapa en anpassad säkerhetsprincip för pod

Nu när du har sett beteendet hos standardenhetens säkerhetsprinciper ska vi ge en möjlighet för *icke-användare* att schemalägga poddar.

Nu ska vi skapa en princip för att avvisa poddar som begär privilegierad åtkomst. Andra alternativ, till exempel *runAsUser* eller tillåtna *volymer,* är inte uttryckligen begränsade. Den här typen av princip nekar en begäran om privilegierad åtkomst, men i annat fall kan klustret köra de begärda poddarna.

Skapa en `psp-deny-privileged.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Skapa principen med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Om du vill visa tillgängliga principer använder du kommandot [kubectl get psp,][kubectl-get] som visas i följande exempel. Jämför *principen psp-deny-privileged* med *standardprincipen* för begränsad standard som tillämpades i föregående exempel för att skapa en pod. Endast användningen *PRIV* av PRIV-eskalering nekas av din policy. Det finns inga begränsningar för användaren eller gruppen för *psp-deny-privilegierad* princip.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Tillåt användarkonto att använda den anpassade säkerhetsprincipen för pod

I föregående steg skapade du en säkerhetsprincip för podden för att avvisa poddar som begär privilegierad åtkomst. Om du vill att principen ska kunna användas skapar du en *roll* eller en *ClusterRole*. Sedan associerar du en av dessa roller med hjälp av en *RoleBinding* eller *ClusterRoleBinding*.

I det här exemplet skapar du en ClusterRole som gör att du kan *använda* *den psp-deny-privilegierad* princip som skapades i föregående steg. Skapa en `psp-deny-privileged-clusterrole.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Skapa ClusterRole med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Skapa nu en ClusterRoleBinding för att använda ClusterRole som skapades i föregående steg. Skapa en `psp-deny-privileged-clusterrolebinding.yaml` fil med namnet och klistra in följande YAML-manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Skapa en ClusterRoleBinding med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> I det första steget i den här artikeln aktiverades säkerhetsprincipfunktionen pod på AKS-klustret. Den rekommenderade metoden var att bara aktivera säkerhetsprincipfunktionen för podden när du har definierat dina egna principer. Det här är det stadium där du aktiverar säkerhetsprincipfunktionen för podden. En eller flera anpassade principer har definierats och användarkonton har associerats med dessa principer. Nu kan du på ett säkert sätt aktivera säkerhetsprincipfunktionen för pod och minimera problem som orsakas av standardprinciperna.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testa skapandet av en oprivilegierad pod igen

Med din anpassade pod-säkerhetsprinciper tillämpad och en bindning för användarkontot att använda principen, låt oss försöka skapa en oprivilegierad pod igen. Använd samma `nginx-privileged.yaml` manifest för att skapa podden med kommandot [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Podden har schemalagts. När du kontrollerar poddens status med kommandot [kubectl get pods][kubectl-get] *körs*podden:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Det här exemplet visar hur du kan skapa anpassade pod-säkerhetsprinciper för att definiera åtkomst till AKS-klustret för olika användare eller grupper. Standard-AKS-principerna ger snäva kontroller för vad poddar kan köra, så skapa dina egna anpassade principer för att sedan korrekt definiera de begränsningar du behöver.

Ta bort NGINX unprivileged pod med kommandot [kubectl delete][kubectl-delete] och ange namnet på YAML-manifestet:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera pod-säkerhetsprincipen använder du kommandot [az aks update][az-aks-update] igen. I följande exempel inaktiveras pod-säkerhetsprincipen på klusternamnet *myAKSCluster* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ta sedan bort ClusterRole och ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Ta bort säkerhetsprincipen med [kommandot kubectl delete][kubectl-delete] och ange namnet på YAML-manifestet:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Slutligen, ta bort *psp-aks* namnområde:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en pod-säkerhetsprincip för att förhindra användning av privilegierad åtkomst. Det finns många funktioner som en princip kan tillämpa, till exempel typ av volym eller RunAs-användaren. Mer information om tillgängliga alternativ finns i [referensdokumenten för Kubernetes pod-säkerhetsprincip][kubernetes-policy-reference].

Mer information om hur du begränsar nätverkstrafiken i pod finns i [Säker trafik mellan poddar med hjälp av nätverksprinciper i AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
