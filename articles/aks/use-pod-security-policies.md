---
title: Använda säkerhetsprinciper för pod i Azure Kubernetes Service (AKS)
description: Lär dig hur du styr pod sjukhusvistelse med PodSecurityPolicy i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027383"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Förhandsgranskning – säkra ditt kluster med hjälp av pod säkerhetsprinciperna i Azure Kubernetes Service (AKS)

För att förbättra säkerheten för AKS-klustret kan du begränsa vad poddar kan vara schemalagda. Poddar som begär resurser som du inte tillåter kan inte köras i AKS-kluster. Du definierar den här åtkomsten via pod säkerhetsprinciper. Den här artikeln visar hur du använder pod säkerhetsprinciper begränsa distributionen av poddar i AKS.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning och delta i. Förhandsversioner tillhandahålls för att samla in feedback och buggar från vår community. De stöds dock inte av teknisk support för Azure. Om du skapar ett kluster eller lägga till dessa funktioner i befintliga kluster, stöds klustret inte förrän funktionen är inte längre i förhandsversion och uppgraderas till allmän tillgänglighet (GA).
>
> Om du stöter på problem med funktioner i förhandsversion [öppna ett ärende på AKS GitHub-lagringsplatsen] [ aks-github] med namnet på funktionen för förhandsgranskning i rubriken för bugg.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion

AKS-kluster uppdateras för att aktivera pod-säkerhetsprinciper med hjälp av den *aks-förhandsversion* CLI-tillägg. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till] [ az-extension-add] kommandot, som visas i följande exempel:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Om du tidigare har installerat den *förhandsversionen av aks* tillägg, installera alla tillgängliga uppdateringar med hjälp av den `az extension update --name aks-preview` kommando.

### <a name="register-pod-security-policy-feature-provider"></a>Registrera pod security funktionen principleverantör

Om du vill skapa eller uppdatera ett AKS-kluster om du vill använda pod säkerhetsprinciper, först aktivera en funktionsflagga i prenumerationen. Att registrera den *PodSecurityPolicyPreview* funktion flaggan, använda den [az funktionen registrera] [ az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan] [ az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register] [ az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Översikt över pod säkerhetsprinciper

I ett Kubernetes-kluster används en åtkomst-kontrollant fånga upp begäranden till API-servern när en resurs ska skapas. Kontrollanten åtkomst kan sedan *Validera* resursbegäran mot en uppsättning regler, eller *mutera* resursen att ändra distributionsparametrarna.

*PodSecurityPolicy* är en åtkomst-kontroll som validerar en pod-specifikation uppfyller dina krav på definierade. Dessa krav kan begränsa användningen av Privilegierade behållare, åtkomst till vissa typer av lagring, eller användaren eller gruppen som behållaren kan köras som. När du försöker distribuera en resurs där pod-specifikationer inte uppfyller kraven som anges i säkerhetsprincipen pod, nekas begäran. Den här möjligheten att styra vad poddar kan vara schemalagd i AKS kluster förhindrar vissa möjligt säkerhetsproblem eller privilegieutökningar.

När du aktiverar pod säkerhetsprincip i ett AKS-kluster kan tillämpas vissa standardprinciper. Dessa standardprinciperna förser dig med en out-of the box-upplevelse för att definiera vad poddar kan schemaläggas. Kluster-användare kan dock köra till problem med att distribuera poddar tills du definierar dina egna principer. Det rekommenderas att sättet är att:

* Skapa ett AKS-kluster
* Definiera dina egna pod säkerhetsprinciper
* Aktivera princip för pod säkerhetsfunktion

Att visa hur standardprinciperna begränsa pod-distributioner, i den här artikeln vi först aktivera principer för pod säkerhetsfunktion och sedan skapa en anpassad princip.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivera pod säkerhetsprincipen på ett AKS-kluster

Du kan aktivera eller inaktivera pod säkerhet en princip med hjälp av den [az aks uppdatera] [ az-aks-update] kommando. Följande exempel aktiverar pod säkerhetsprincip på klustrets namn *myAKSCluster* i resursgruppen med namnet *myResourceGroup*.

> [!NOTE]
> För verklig användning inte säkerhetsprincip pod tills du har definierat dina egna anpassade principer. I den här artikeln får du har aktiverat pod säkerhetsprincip som det första steget att se hur standardprinciperna begränsa pod distributioner.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standardprinciper för AKS

När du aktiverar pod säkerhetsprincip AKS skapar två standardprinciper med namnet *Privilegierade* och *begränsade*. Inte redigera eller ta bort dessa standardprinciper. I stället skapa dina egna principer som definierar de inställningar som du vill att styra. Nu ska vi en första titt på vad dessa standardprinciper är hur de påverkar pod-distributioner.

Du kan visa principerna som är tillgängliga i [kubectl hämta psp] [ kubectl-get] kommandot, som visas i följande exempel. Som en del av standard *begränsade* princip, användaren nekas *PRIV* finns Privilegierade pod eskalering och användaren *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Den *begränsade* pod säkerhetsprincip tillämpas på alla autentiserade användare i AKS-kluster. Den här tilldelningen styrs av ClusterRoles och ClusterRoleBindings. Använd den [kubectl hämta clusterrolebindings] [ kubectl-get] kommandot och Sök efter den *standard: begränsade:* bindning:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Enligt följande komprimerade utdata, den *psp: begränsade* ClusterRole har tilldelats någon *system: autentiserad* användare. Den här möjligheten ger en grundläggande nivå av begränsningar utan att dina egna principer som definieras.

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

Det är viktigt att förstå hur dessa standardprinciper interagerar med användarförfrågningar om att schemalägga poddar innan du börjar skapa dina egna pod säkerhetsprinciper. I nästa avsnitt ska vi schemalägga vissa poddar att se dessa standardprinciper fungerar i praktiken.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Skapa en testanvändare i ett AKS-kluster

Som standard när du använder den [aaz aks get-credentials] [ az-aks-get-credentials] kommandot, den *admin* autentiseringsuppgifter för AKS-klustret och läggs till i din `kubectl` config. Administratörsanvändare kringgår verkställandet av pod säkerhetsprinciper. Om du använder Azure Active Directory-integrering för AKS-kluster kan du logga in med autentiseringsuppgifterna för en icke-administratörsanvändare att se tillämpning av principer i praktiken. Nu ska vi skapa ett test-användarkonto i AKS-klustret som du kan använda den här artikeln.

Skapa ett namnområde för exemplet med namnet *psp aks* för test-resurser med hjälp av den [kubectl skapa namnområde] [ kubectl-create] kommando. Skapa sedan ett tjänstkonto med namnet *nonadmin användare* med hjälp av den [kubectl skapa serviceaccount] [ kubectl-create] kommando:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Skapa sedan en RoleBinding för den *nonadmin användare* att utföra grundläggande åtgärder i en namnrymd med hjälp av den [kubectl skapa rolebinding] [ kubectl-create] kommando:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Skapa aliaskommandon för användare och icke-delad admin

Att markera skillnaden mellan vanliga admin-användare när du använder `kubectl` och icke-administratörsanvändare skapades i föregående steg, skapa två kommandoradsverktyget alias:

* Den **kubectl-administratörer** alias för vanliga admin-användare och är begränsad till den *psp aks* namnområde.
* Den **kubectl nonadminuser** alias avser den *nonadmin användare* skapades i föregående steg och är begränsad till den *psp aks* namnområde.

Skapa dessa två alias som du ser i följande kommandon:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testa skapandet av en privilegierad pod

Låt oss först testa vad som händer när du schemalägger en pod med säkerhetskontexten för `privileged: true`. Den här säkerhetskontexten eskalerar din pod privilegier. I föregående avsnitt som visade standard AKS pod IPSec-principer i *begränsade* princip ska neka denna begäran.

Skapa en fil med namnet `nginx-privileged.yaml` och klistra in följande YAML-manifest:

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

Skapa en pod med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

En pod inte kan schemaläggas, som visas i följande Exempelutdata:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Poden når inte schemaläggning scenen, så det finns inga resurser att ta bort innan du går vidare.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testa skapandet av en utan privilegier pod

I exemplet ovan begärda pod-specifikationen eskalering av privilegier. Den här begäran nekas som standard *begränsade* pod säkerhetsprinciper, så din pod inte kan schemaläggas. Nu ska vi prova nu körs den samma NGINX-pod utan privilegier eskalering begäran.

Skapa en fil med namnet `nginx-unprivileged.yaml` och klistra in följande YAML-manifest:

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

Skapa en pod med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes-scheduler accepterar pod-begäran. Men om du tittar på status för en pod med hjälp av `kubectl get pods`, det finns ett fel:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Använd den [kubectl beskriver pod] [ kubectl-describe] kommando för att titta på händelser för din pod. Följande komprimerade exempel visar behållaren och avbildning krävs rotbehörighet, även om vi inte begärt dem:

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

Även om vi inte begärde privilegierad åtkomst, behållaravbildning för NGINX behövs för att skapa en bindning för port *80*. Att binda portar *1024* och nedan, den *rot* användaren krävs. När en pod försöker starta, den *begränsade* pod säkerhetsprincip nekar denna begäran.

Det här exemplet visar att de standard pod-säkerhetsprinciper som skapats av AKS tillämpas och begränsa de åtgärder som en användare kan utföra. Det är viktigt att förstå beteendet för dessa standardprinciper som du inte förväntar dig en grundläggande NGINX-pod ska blockeras.

Innan du går vidare till nästa steg, ta bort det här testet pod med den [kubectl ta bort pod] [ kubectl-delete] kommando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testa skapandet av en pod med en viss användare-kontext

I exemplet ovan försökte behållaravbildningen automatiskt använda roten för att binda NGINX till port 80. Den här förfrågan nekades av standard *begränsade* pod säkerhetsprinciper, så att det inte går att starta en pod. Nu ska vi prova nu körs den samma NGINX-pod med en viss användare-kontext som `runAsUser: 2000`.

Skapa en fil med namnet `nginx-unprivileged-nonroot.yaml` och klistra in följande YAML-manifest:

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

Skapa en pod med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes-scheduler accepterar pod-begäran. Men om du tittar på status för en pod med hjälp av `kubectl get pods`, det finns ett annat fel än det föregående exemplet:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Använd den [kubectl beskriver pod] [ kubectl-describe] kommando för att titta på händelser för din pod. Följande komprimerade exempel visar pod-händelser:

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

Händelserna indikerar att behållaren har skapats och igång. Det finns inget omedelbart uppenbar om varför poden är i ett felaktigt tillstånd. Låt oss titta på pod-loggar med den [kubectl loggar] [ kubectl-logs] kommando:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

Följande Exempelutdata log ger en indikation som i själva NGINX-konfigurationen finns det ett behörighetsfel när tjänsten försöker starta. Det här felet beror igen behöva bindas till port 80. Även om pod-specifikationen definierat ett vanligt användarkonto, är det här användarkontot inte tillräcklig på OS-nivå för NGINX-tjänsten att starta och binda till porten som är begränsade.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Igen, är det viktigt att förstå beteendet för säkerhetsprinciperna som standard pod. Det här felet var lite svårare att spåra och igen och du förväntar dig inte en grundläggande NGINX-pod ska blockeras.

Innan du går vidare till nästa steg, ta bort det här testet pod med den [kubectl ta bort pod] [ kubectl-delete] kommando:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Skapa en anpassad pod-säkerhetsprincip

Nu när du har sett beteendet för säkerhetsprinciperna som standard pod vi gör det möjligt för den *nonadmin användare* har schemalägga poddar.

Nu ska vi skapa en princip för att avvisa poddar som begär privilegierad åtkomst. Andra alternativ som *användare* eller tillåts *volymer*, inte uttryckligen begränsade. Den här typen av princip nekar en begäran om privilegierad åtkomst, men annars gör att klustret körs begärda poddarna.

Skapa en fil med namnet `psp-deny-privileged.yaml` och klistra in följande YAML-manifest:

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

Skapa en princip med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Du kan visa principerna som är tillgängliga i [kubectl hämta psp] [ kubectl-get] kommandot, som visas i följande exempel. Jämför den *psp neka behörighet* princip med standard *begränsade* princip som har tvingats fram i föregående exempel för att skapa en pod. Bara använda *PRIV* eskalering nekas i din princip. Det finns inga begränsningar för användaren eller gruppen för den *psp neka behörighet* princip.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Tillåt användarkonto för att använda den anpassa pod-säkerhetsprincipen

I det föregående steget skapade du en pod-säkerhetsprincip för att avvisa poddar som begäran privilegierad åtkomst. Om du vill tillåta principen som ska användas som du skapar en *rollen* eller en *ClusterRole*. Sedan kan du koppla ett av dessa roller med hjälp av en *RoleBinding* eller *ClusterRoleBinding*.

I det här exemplet skapar du en ClusterRole som låter dig *använder* den *psp neka behörighet* principen skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrole.yaml` och klistra in följande YAML-manifest:

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

Skapa ClusterRole med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Nu ska du skapa en ClusterRoleBinding om du vill använda ClusterRole som skapades i föregående steg. Skapa en fil med namnet `psp-deny-privileged-clusterrolebinding.yaml` och klistra in följande YAML-manifest:

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

Skapa en ClusterRoleBinding med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> I det första steget i den här artikeln aktiverades pod princip säkerhetsfunktion på AKS-klustret. Den rekommenderade metoden var att endast aktivera funktionen pod security policy när du har definierat dina egna principer. Det här är steget där du vill aktivera princip för pod säkerhetsfunktion. En eller flera anpassade principer har definierats och användarkonton har associerats med dessa principer. Nu kan du på ett säkert sätt säkerhetsprincip pod funktion och minimerar risken för problem som orsakas av standard-principer.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testa skapandet av en utan privilegier pod igen

Med din anpassade pod-säkerhetsprincip tillämpas och en bindning för användarkonton du använder principen, prova med att skapa ett utan privilegier pod igen. Använd samma `nginx-privileged.yaml` manifest för att skapa en pod med hjälp av den [kubectl gäller] [ kubectl-apply] kommando:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

En pod är schemalagt. När du kontrollera status för en pod med hjälp av den [kubectl hämta poddar] [ kubectl-get] kommandot poden är *kör*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Det här exemplet visar hur du kan skapa anpassade pod säkerhetsprinciper för att definiera åtkomst till AKS-kluster för olika användare eller grupper. Standard AKS-principerna förser dig med strikta kontroller på vilka poddar kan köra, så skapa dina egna anpassade principer korrekt definiera de begränsningar som du behöver.

Ta bort en NGINX som ej Privilegierade pod med hjälp av den [kubectl ta bort] [ kubectl-delete] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill inaktivera pod säkerhetsprincip, använda den [az aks uppdatera] [ az-aks-update] -kommandot på nytt. Följande exempel inaktiverar pod säkerhetsprincip på klustrets namn *myAKSCluster* i resursgruppen med namnet *myResourceGroup*:

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

Ta bort nätverk princip med [kubectl ta bort] [ kubectl-delete] kommandot och ange namnet på ditt YAML-manifest:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Slutligen kan ta bort den *psp aks* namnområde:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en pod-säkerhetsprincip för att förhindra användning av privilegierad åtkomst. Det finns många olika funktioner som en princip kan genomdriva, till exempel typ av volym eller RunAs-användaren. Mer information om de tillgängliga alternativen finns i den [Kubernetes pod security policy referensdokument][kubernetes-policy-reference].

Läs mer om hur du begränsar nätverkstrafiken för pod [skydda trafik mellan poddar med nätverksprinciper i AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

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
