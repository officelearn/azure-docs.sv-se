---
title: Använda Pod säkerhets principer i Azure Kubernetes service (AKS)
description: Lär dig hur du styr Pod-inåtkomster med PodSecurityPolicy i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: df8aa51558bc3aa456758510792c198a8bd9cf78
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061841"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>För hands version – skydda klustret med Pod säkerhets principer i Azure Kubernetes service (AKS)

För att förbättra säkerheten för ditt AKS-kluster kan du begränsa vilka poddar som kan schemaläggas. Poddar som begär resurser som du inte tillåter kan inte köras i AKS-klustret. Du definierar den här åtkomsten med Pod säkerhets principer. Den här artikeln visar hur du använder Pod säkerhets principer för att begränsa distributionen av poddar i AKS.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda Pod säkerhets principer behöver du *AKS-Preview CLI-* tillägget version 0.4.1 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrera Pod för säkerhets princip

Om du vill skapa eller uppdatera ett AKS-kluster för att använda Pod-säkerhetsprinciper måste du först aktivera en funktions flagga i din prenumeration. Registrera funktions flaggan *PodSecurityPolicyPreview* genom att använda kommandot [AZ Feature register][az-feature-register] , som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Översikt över Pod säkerhets principer

I ett Kubernetes-kluster används en åtkomst kontroll för att avlyssna förfrågningar till API-servern när en resurs ska skapas. -Kontroll enheten kan sedan *Verifiera* resurs förfrågan mot en uppsättning regler, eller genom att *i motsvarande* resurs ändra distributions parametrar.

*PodSecurityPolicy* är en kontroll enhet som validerar en POD-specifikation som uppfyller dina definierade krav. Dessa krav kan begränsa användningen av privilegierade behållare, åtkomst till vissa typer av lagring eller den användare eller grupp som behållaren kan köras som. När du försöker distribuera en resurs där Pod-specifikationerna inte uppfyller kraven som beskrivs i säkerhets principen för Pod nekas begäran. Den här möjligheten att kontrol lera vilka poddar som kan schemaläggas i AKS-klustret förhindrar säkerhets sårbarheter eller behörighets eskalering.

När du aktiverar Pod säkerhets princip i ett AKS-kluster tillämpas vissa standard principer. Dessa standard principer ger en välkomst upplevelse för att definiera vilka poddar som kan schemaläggas. Kluster användare kan dock stöta på problem med att distribuera poddar tills du definierar dina egna principer. Den rekommenderade metoden är att:

* Skapa ett AKS-kluster
* Definiera egna Pod säkerhets principer
* Aktivera säkerhets princip funktionen Pod

För att visa hur standard principerna begränsar Pod-distributioner, i den här artikeln, aktiverar vi först funktionen Pod Security Policies och skapar sedan en anpassad princip.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivera Pod säkerhets princip i ett AKS-kluster

Du kan aktivera eller inaktivera Pod säkerhets princip med kommandot [AZ AKS Update][az-aks-update] . I följande exempel aktive ras säkerhets principer för Pod på kluster namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*.

> [!NOTE]
> För verklig användning aktiverar du inte säkerhets principen Pod förrän du har definierat dina egna anpassade principer. I den här artikeln aktiverar du Pod säkerhets princip som det första steget för att se hur standard principerna begränsar Pod-distributioner.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standard principer för AKS

När du aktiverar Pod säkerhets princip skapar AKS två standard principer med namnet *Privileged* och *restricted*. Redigera eller ta inte bort standard principerna. Skapa i stället egna principer som definierar de inställningar som du vill kontrol lera. Vi ska börja med att titta på vad dessa standard principer är för att påverka Pod-distributioner.

Om du vill visa tillgängliga principer använder du kommandot [kubectl get PSP][kubectl-get] , som du ser i följande exempel. Som en del av standard principen för *begränsade begränsade* användare nekas användaren en *föreg* användning av privilegie rad Pod eskalering och användaren *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Den *begränsade* säkerhets principen för Pod tillämpas på alla autentiserade användare i AKS-klustret. Den här tilldelningen styrs av ClusterRoles och ClusterRoleBindings. Använd kommandot [kubectl get clusterrolebindings][kubectl-get] och Sök efter *standard: begränsad:* bindning:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Som det visas i följande komprimerade utdata tilldelas *PSP: s begränsade* ClusterRole till alla *system: autentiserade* användare. Den här funktionen ger en grundläggande nivå av begränsningar utan att dina egna principer definieras.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Det är viktigt att förstå hur dessa standard principer interagerar med användar förfrågningar för att schemalägga poddar innan du börjar skapa egna Pod säkerhets principer. I följande avsnitt kan vi schemalägga vissa poddar för att se de här standard principerna i praktiken.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Skapa en test användare i ett AKS-kluster

Som standard när du använder kommandot `kubectl` [AZ AKS get-credentials][az-aks-get-credentials] , läggs administratörsautentiseringsuppgifter för AKS-klustret till i konfigurationen. Administratörs användaren kringgår verk ställandet av Pod säkerhets principer. Om du använder Azure Active Directory-integrering för dina AKS-kluster kan du logga in med autentiseringsuppgifterna för en användare som inte är administratör för att se verk ställandet av principer i praktiken. I den här artikeln ska vi skapa ett test användar konto i AKS-klustret som du kan använda.

Skapa ett exempel namn område med namnet *PSP-AKS* för test resurser med hjälp av kommandot [kubectl Create namespace][kubectl-create] . Skapa sedan ett tjänst konto med namnet *ej administratör – användare* med kommandot [kubectl Create ServiceAccount][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Skapa sedan en RoleBinding för den användare som inte är *administratör* för att utföra grundläggande åtgärder i namn området med kommandot [kubectl Create RoleBinding][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Skapa alias kommandon för administratörer och användare som inte är administratörer

Om du vill markera skillnaden mellan den vanliga administratörs användaren `kubectl` när du använder och den icke-administratör som skapades i föregående steg, skapar du två kommando rads Ali Aset:

* **Kubectl-admin-** aliaset är för den vanliga administratörs användaren och är begränsad till *PSP-AKS-* namnområdet.
* **Kubectl-nonadminuser** alias är för den *ej administratörer-användare* som skapades i föregående steg och som är begränsad till namn området *PSP-AKS* .

Skapa dessa två alias som du ser i följande kommandon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testa skapandet av en privilegie rad Pod

Vi börjar med att testa vad som händer när du schemalägger en POD med säkerhets `privileged: true`kontexten för. Den här säkerhets kontexten eskalerar Pod privilegier. I föregående avsnitt som visade standard principerna för AKS-Pod bör den *begränsade* principen neka denna begäran.

Skapa en fil med `nginx-privileged.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Pod kan inte schemaläggas, vilket visas i följande exempel på utdata:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod når inte schemaläggnings fasen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa att skapa en ej privilegie rad Pod

I det föregående exemplet begärde Pod-specifikationen privilegie rad eskalering. Den här begäran nekas av standard säkerhets principen för *begränsade* pod, så Pod kan inte schemaläggas. Nu ska vi prova att köra samma NGINX-Pod utan begäran om behörighets eskalering.

Skapa en fil med `nginx-unprivileged.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes Scheduler accepterar Pod-begäran. Men om du tittar på status för Pod med `kubectl get pods`, finns det ett fel:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Använd kommandot [kubectl beskriver Pod][kubectl-describe] för att titta på händelserna för pod. I följande komprimerade exempel visas behållaren och avbildningen kräver rot behörigheter, även om vi inte begärde dem:

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

Även om vi inte begärde någon privilegie rad åtkomst måste behållar avbildningen för NGINX skapa en bindning för port *80*. För att binda portarna *1024* och nedan krävs *rot* användaren. När Pod försöker starta, nekar säkerhets principen *begränsade* Pod den här begäran.

Det här exemplet visar att standard säkerhets principerna för Pod som skapas av AKS är aktiva och begränsar vilka åtgärder en användare kan utföra. Det är viktigt att förstå beteendet för dessa standard principer, eftersom du kanske inte förväntar dig att en grundläggande NGINX-Pod ska nekas.

Innan du går vidare till nästa steg tar du bort test-Pod med kommandot [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testa att skapa en POD med en speciell användar kontext

I föregående exempel försökte behållar avbildningen automatiskt använda roten för att binda NGINX till port 80. Den här begäran nekades av standard säkerhets principen för *begränsade* pod, så Pod kan inte starta. Nu ska vi prova att köra samma NGINX-Pod med en speciell användar kontext, till `runAsUser: 2000`exempel.

Skapa en fil med `nginx-unprivileged-nonroot.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa Pod med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes Scheduler accepterar Pod-begäran. Men om du tittar på status för Pod med `kubectl get pods`, finns det ett annat fel än det tidigare exemplet:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Använd kommandot [kubectl beskriver Pod][kubectl-describe] för att titta på händelserna för pod. Följande kondenserade exempel visar Pod-händelser:

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

Händelserna indikerar att behållaren har skapats och startats. Det finns inget direkt uppenbart för varför pod är i ett felaktigt tillstånd. Nu ska vi titta på pod-loggarna med kommandot [kubectl logs][kubectl-logs] :

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

I följande exempel på loggfiler visas ett meddelande om att i själva NGINX-konfigurationen. det finns ett behörighets fel när tjänsten försöker starta. Felet orsakas återigen av att du måste binda till port 80. Även om Pod-specifikationen definierade ett vanligt användar konto är det här användar kontot inte tillräckligt i OS-nivån för att NGINX-tjänsten ska starta och binda till den begränsade porten.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Återigen är det viktigt att förstå beteendet för standard säkerhets principerna för pod. Det här felet var lite svårare att spåra, och återigen kanske du inte förväntar dig att en grundläggande NGINX-Pod ska nekas.

Innan du går vidare till nästa steg tar du bort test-Pod med kommandot [kubectl Delete Pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Skapa en anpassad säkerhets princip för Pod

Nu när du har sett hur du kan använda standard säkerhets principerna för Pod kan du ge den andra *användaren* möjlighet att schemalägga poddar.

Nu ska vi skapa en princip för att avvisa poddar som begär privilegie rad åtkomst. Andra alternativ, till exempel *runAsUser* eller tillåtna *volymer*, är inte uttryckligen begränsade. Den här typen av princip nekar en begäran om privilegie rad åtkomst, men tillåter annars att klustret kör den begärda poddar.

Skapa en fil med `psp-deny-privileged.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa principen med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Om du vill visa tillgängliga principer använder du kommandot [kubectl get PSP][kubectl-get] , som du ser i följande exempel. Jämför principen *PSP-Deny-Privileged* med den standard *begränsade* principen som tillämpades i föregående exempel för att skapa en pod. Endast användningen av den tidigare eskaleringen nekas av principen. Det finns inga begränsningar för användaren eller gruppen för principen *PSP-Deny-Privileged* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Tillåt att användar kontot använder den anpassade säkerhets principen för Pod

I föregående steg skapade du en POD säkerhets princip för att avvisa poddar som begär privilegie rad åtkomst. Om du vill tillåta att principen används skapar du en *roll* eller en *ClusterRole*. Sedan associerar du en av dessa roller med hjälp av en *RoleBinding* eller *ClusterRoleBinding*.

I det här exemplet skapar du en ClusterRole som gör att du kan *använda* principen *PSP-Deny-Privileged* som skapades i föregående steg. Skapa en fil med `psp-deny-privileged-clusterrole.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa ClusterRole med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Skapa nu en ClusterRoleBinding för att använda ClusterRole som skapades i föregående steg. Skapa en fil med `psp-deny-privileged-clusterrolebinding.yaml` namnet och klistra in följande yaml-manifest:

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

Skapa en ClusterRoleBinding med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> I det första steget i den här artikeln har funktionen säkerhets princip för Pod Aktiver ATS i AKS-klustret. Den rekommenderade metoden var att endast aktivera funktionen Pod säkerhets princip när du har definierat dina egna principer. Det här är steget där du aktiverar funktionen Pod Security Policy. En eller flera anpassade principer har definierats och användar konton har associerats med dessa principer. Nu kan du aktivera funktionen Pod säkerhets princip och minimera problemen som orsakas av standard principerna.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testa skapandet av en pod som inte har privilegierat igen

Med din anpassade Pod-säkerhetsprincip tillämpad och en bindning för användar kontot för att använda principen, försöker vi att skapa en pod som inte har privilegierat igen. Använd samma `nginx-privileged.yaml` manifest för att skapa Pod med kommandot [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod har schemalagts. När du kontrollerar status för Pod med kommandot [kubectl get poddar][kubectl-get] *körs*pod:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Det här exemplet visar hur du kan skapa anpassade Pod säkerhets principer för att definiera åtkomst till AKS-klustret för olika användare eller grupper. Standard principerna för AKS ger tätt kontroll över vad poddar kan köra, så skapa dina egna anpassade principer för att sedan definiera de begränsningar du behöver.

Ta bort NGINX Pod med kommandot [kubectl Delete][kubectl-delete] och ange namnet på din yaml-manifest:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera Pod säkerhets princip använder du kommandot [AZ AKS Update][az-aks-update] igen. I följande exempel inaktive ras Pod säkerhets princip på kluster namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

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

Ta bort nätverks principen med kommandot [kubectl Delete][kubectl-delete] och ange namnet på ditt yaml-manifest:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Ta slutligen bort *PSP-AKS-* namnområdet:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar dig hur du skapar en POD säkerhets princip för att förhindra användning av privilegie rad åtkomst. Det finns många funktioner som en princip kan genomdriva, t. ex. typ av volym eller RunAs-användare. Mer information om tillgängliga alternativ finns i [referens dokument för säkerhets princip för Kubernetes Pod][kubernetes-policy-reference].

Mer information om hur du begränsar Pod nätverks trafik finns i [skydda trafik mellan poddar med hjälp av nätverks principer i AKS][network-policies].

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
