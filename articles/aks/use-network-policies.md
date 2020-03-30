---
title: Säkra poddar med nätverksprinciper i Azure Kubernetes Service (AKS)
description: Lär dig hur du skyddar trafik som flödar in och ut ur poddar med hjälp av Kubernetes nätverksprinciper i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 37b6ebd1c8b147db0a9cead4678a0b2bb4ed234d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473616"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Säker trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)

När du kör moderna, mikrotjänstbaserade program i Kubernetes vill du ofta styra vilka komponenter som kan kommunicera med varandra. Principen om lägsta behörighet bör tillämpas på hur trafik kan flöda mellan poddar i ett AKS-kluster (Azure Kubernetes Service). Anta att du förmodligen vill blockera trafik direkt till backend-program. Med *funktionen Nätverksprincip* i Kubernetes kan du definiera regler för ingående och utgående trafik mellan poddar i ett kluster.

Den här artikeln visar hur du installerar nätverksprincipmotorn och skapar Kubernetes nätverksprinciper för att styra trafikflödet mellan poddar i AKS. Nätverksprincipen bör endast användas för Linux-baserade noder och poddar i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.61 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

> [!TIP]
> Om du använde nätverksprincipfunktionen under förhandsversionen rekommenderar vi att du [skapar ett nytt kluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Om du vill fortsätta använda befintliga testkluster som använde nätverksprincip under förhandsversionen uppgraderar du klustret till en ny Kubernetes-version för den senaste GA-versionen och distribuerar sedan följande YAML-manifest för att åtgärda kraschningsmåttservern och Kubernetes-servern Instrumentpanelen. Den här korrigeringsfilen krävs endast för kluster som använde Calico-nätverksprincipmotorn.
>
> Som en säkerhetspraxis [bör du granska innehållet i det här YAML-manifestet][calico-aks-cleanup] för att förstå vad som distribueras i AKS-klustret.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Översikt över nätverksprincipen

Alla poddar i ett AKS-kluster kan skicka och ta emot trafik utan begränsningar, som standard. Om du vill förbättra säkerheten kan du definiera regler som styr trafikflödet. Backend-program är ofta bara utsatta för nödvändiga frontend-tjänster, till exempel. Databaskomponenter är också endast tillgängliga för de programnivåer som ansluter till dem.

Nätverksprincip är en Kubernetes-specifikation som definierar åtkomstprinciper för kommunikation mellan poddar. Med hjälp av nätverksprinciper definierar du en ordnad uppsättning regler för att skicka och ta emot trafik och tillämpa dem på en samling poddar som matchar en eller flera etikettväljare.

Dessa nätverksprincipregler definieras som YAML-manifest. Nätverksprinciper kan inkluderas som en del av ett bredare manifest som också skapar en distribution eller tjänst.

### <a name="network-policy-options-in-aks"></a>Alternativ för nätverksprinciper i AKS

Azure innehåller två sätt att implementera nätverksprincipen. Du väljer ett nätverksprincipalternativ när du skapar ett AKS-kluster. Det går inte att ändra principalternativet när klustret har skapats:

* Azures egen implementering, som kallas *Azure Network Policies*.
* *Calico Network Policies*, en öppen källkod nätverk och nätverkssäkerhet lösning som grundades av [Tigera][tigera].

Båda implementeringarna använder Linux *IPTables* för att tillämpa de angivna principerna. Principer översätts till uppsättningar med tillåtna och otillåtna IP-par. Dessa par programmeras sedan som IPTable-filterregler.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Skillnader mellan Azure- och Calico-principer och deras funktioner

| Funktion                               | Azure                      | Kalikå                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plattformar som stöds                      | Linux                      | Linux                       |
| Nätverksalternativ som stöds             | Azure CNI                  | Azure CNI och kubenet       |
| Överensstämmelse med Kubernetes specifikation | Alla principtyper som stöds |  Alla principtyper som stöds |
| Ytterligare funktioner                      | Inget                       | Utökad principmodell som består av global nätverksprincip, global nätverksuppsättning och värdslutpunkt. Mer information om `calicoctl` hur du använder CLI för att hantera dessa utökade funktioner finns i [calicoctl-användarreferens][calicoctl]. |
| Support                                  | Stöds av Azure support och Engineering team | Calico community support. Mer information om ytterligare betald support finns i [Supportalternativ för Project Calico][calico-support]. |
| Loggning                                  | Regler som läggs till/tas bort i IPTables loggas in på alla värden under */var/log/azure-npm.log* | Mer information finns i [Calico-komponentloggar][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Skapa ett AKS-kluster och aktivera nätverksprincipen

Om du vill visa nätverksprinciper i praktiken ska vi skapa och sedan expandera en princip som definierar trafikflödet:

* Neka all trafik till pod.
* Tillåt trafik baserat på pod-etiketter.
* Tillåt trafik baserat på namnområde.

Låt oss först skapa ett AKS-kluster som stöder nätverksprincipen. 

> [!IMPORTANT]
>
> Nätverksprincipfunktionen kan bara aktiveras när klustret skapas. Du kan inte aktivera nätverksprincipen i ett befintligt AKS-kluster.

Om du vill använda Azure Network Policy måste du använda [Azure CNI-plugin-programmet][azure-cni] och definiera ditt eget virtuella nätverk och undernät. Mer detaljerad information om hur du planerar ut de nödvändiga undernätsområdena finns i [konfigurera avancerade nätverk][use-advanced-networking]. Calico Network Policy kan användas med antingen samma Azure CNI-plugin-program eller med Kubenet CNI-plugin-programmet.

Följande exempelskript:

* Skapar ett virtuellt nätverk och undernät.
* Skapar ett huvudnamn för Azure Active Directory (Azure AD) som ska användas med AKS-klustret.
* Tilldelar *deltagarbehörigheter* för AKS-klustertjänstens huvudnamn i det virtuella nätverket.
* Skapar ett AKS-kluster i det definierade virtuella nätverket och aktiverar nätverksprincipen.
    * Alternativet *azure* network policy används. Om du vill använda Calico som `--network-policy calico` nätverksprincipalternativ i stället använder du parametern. Obs: Calico kan användas `--network-plugin azure` `--network-plugin kubenet`med antingen eller .

Ge din egen säker *SP_PASSWORD.* Du kan ersätta *variablerna RESOURCE_GROUP_NAME* och *CLUSTER_NAME:*

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Det tar några minuter att skapa klustret. När klustret är `kubectl` klart konfigurerar du för att ansluta till Kubernetes-klustret med kommandot [az aks get-credentials.][az-aks-get-credentials] Det här kommandot hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Neka all inkommande trafik till en pod

Innan du definierar regler för att tillåta specifik nätverkstrafik skapar du först en nätverksprincip för att neka all trafik. Den här principen ger dig en utgångspunkt för att börja vitlista endast önskad trafik. Du kan också tydligt se att trafiken tas bort när nätverksprincipen tillämpas.

För exempelprogrammiljön och trafikreglerna ska vi först skapa ett namnområde som kallas *utveckling* för att köra exempelpoddarna:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Skapa ett exempel på backend-pod som kör NGINX. Den här backend-poden kan användas för att simulera ett exempel på backend-webbaserade program. Skapa den här podden i *utvecklingsnamnområdet* och öppna port *80* för att betjäna webbtrafik. Märk podden med *app=webapp,role=backend* så att vi kan rikta den med en nätverksprincip i nästa avsnitt:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Skapa en annan pod och bifoga en terminalsession för att testa att du kan nå standardwebbsidan för NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Vid skalprompten använder du `wget` för att bekräfta att du kan komma åt standardwebbsidan för NGINX:

```console
wget -qO- http://backend
```

Följande exempelutdata visar att standardwebbsidan för NGINX returnerades:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den bifogade terminalsessionen. Testenheten raderas automatiskt.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Skapa och tillämpa en nätverksprincip

Nu när du har bekräftat att du kan använda den grundläggande NGINX-webbsidan på exempelversionen av backend-pod, skapar du en nätverksprincip för att neka all trafik. Skapa en `backend-policy.yaml` fil med namnet och klistra in följande YAML-manifest. Det här manifestet använder en *podSelector* för att koppla principen till poddar som har *appen:webapp,roll:serverdelsetikett,* som exempel på NGINX-pod. Inga regler definieras under *inträngning,* så all inkommande trafik till podden nekas:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Gå [https://shell.azure.com](https://shell.azure.com) till för att öppna Azure Cloud Shell i din webbläsare.

Använd nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testa nätverksprincipen

Låt oss se om du kan använda NGINX webbsidan på backend pod igen. Skapa en annan testkapsel och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Vid skalprompten använder du `wget` för att se om du kan komma åt standardwebbsidan för NGINX. Den här gången anger du ett timeout-värde till *2* sekunder. Nätverksprincipen blockerar nu all inkommande trafik, så sidan kan inte läsas in, vilket visas i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den bifogade terminalsessionen. Testenheten raderas automatiskt.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Tillåt inkommande trafik baserat på en pod-etikett

I föregående avsnitt har en backend NGINX-pod schemalagts och en nätverksprincip skapades för att neka all trafik. Nu ska vi skapa en klientenhetspodd och uppdatera nätverksprincipen så att trafik från frontend-poddar tillåts.

Uppdatera nätverksprincipen så att trafik från poddar med *etiketterna app:webapp,roll:frontend* och i valfritt namnområde. Redigera den tidigare *backend-policy.yaml-filen* och lägg till *matchLabels-ingressregler* så att manifestet ser ut som följande exempel:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Den här nätverksprincipen använder en *namnområdeSelector* och ett *podSelector-element* för ingressregeln. SYNTAXEN FÖR YAML är viktig för att ingressreglerna ska vara additiva. I det här exemplet måste båda elementen matcha för att ingress-regeln ska tillämpas. Kubernetes-versioner före *1.12* kanske inte tolkar dessa element korrekt och begränsar nätverkstrafiken som förväntat. Mer information om det här beteendet finns i [Beteende för till och från väljare][policy-rules].

Använd den uppdaterade nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f backend-policy.yaml
```

Schemalägg en pod som är märkt som *app=webapp,role=frontend* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Vid skalprompten använder du `wget` för att se om du kan komma åt standardwebbsidan för NGINX:

```console
wget -qO- http://backend
```

Eftersom inträngningsregeln tillåter trafik med poddar som har *etikettappen: webapp,roll: frontend,* är trafiken från frontend pod tillåten. I följande exempelutdata visas den returnerade standardwebbsidan för NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den bifogade terminalsessionen. Podden tas bort automatiskt.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testa en pod utan matchande etikett

Nätverksprincipen tillåter trafik från poddar *märkta app: webapp, roll: frontend*, men bör neka all annan trafik. Låt oss testa om en annan pod utan dessa etiketter kan komma åt den bakre NGINX-podden. Skapa en annan testkapsel och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Vid skalprompten använder du `wget` för att se om du kan komma åt standardwebbsidan för NGINX. Nätverksprincipen blockerar inkommande trafik, så sidan kan inte läsas in, vilket visas i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den bifogade terminalsessionen. Testenheten raderas automatiskt.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Tillåt endast trafik från ett definierat namnområde

I föregående exempel har du skapat en nätverksprincip som nekade all trafik och sedan uppdaterat principen för att tillåta trafik från poddar med en viss etikett. Ett annat vanligt behov är att begränsa trafiken till endast inom ett visst namnområde. Om de tidigare exemplen var för trafik i ett *utvecklingsnamnområde* skapar du en nätverksprincip som förhindrar trafik från ett annat namnområde, till exempel *produktion*, från att nå poddar.

Skapa först ett nytt namnområde för att simulera ett produktionsnamnområde:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Schemalägg en testpod i *produktionsnamnområdet* som är märkt som *app=webapp,role=frontend*. Bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Vid skalprompten använder du `wget` för att bekräfta att du kan komma åt standardwebbsidan för NGINX:

```console
wget -qO- http://backend.development
```

Eftersom etiketterna för podden matchar det som för närvarande är tillåtet i nätverksprincipen tillåts trafiken. Nätverksprincipen tittar inte på namnområdena, bara pod-etiketterna. I följande exempelutdata visas den returnerade standardwebbsidan för NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den bifogade terminalsessionen. Testenheten raderas automatiskt.

```console
exit
```

### <a name="update-the-network-policy"></a>Uppdatera nätverksprincipen

Låt oss uppdatera *namnområdesnamnområdesvalområdesregeln* för att bara tillåta trafik från *utvecklingsnamnområdet.* Redigera manifestfilen *backend-policy.yaml* enligt följande exempel:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

I mer komplexa exempel kan du definiera flera ingressregler, till exempel en *namnområdeSelector* och sedan en *podSelector*.

Använd den uppdaterade nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testa den uppdaterade nätverksprincipen

Schemalägg en annan pod i *produktionsnamnområdet* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Vid skal prompt, `wget` använd för att se att nätverksprincipen nu förnekar trafik:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Avsluta testenheten:

```console
exit
```

Med trafik nekad från *produktionsnamnområdet* schemalägger du en testkapsel i *utvecklingsnamnområdet* och bifogar en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Vid skal prompt, `wget` använd för att se att nätverksprincipen tillåter trafiken:

```console
wget -qO- http://backend
```

Trafik tillåts eftersom podden är schemalagd i namnområdet som matchar vad som är tillåtet i nätverksprincipen. Följande exempelutdata visar den returnerade standardwebbsidan för NGINX:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den bifogade terminalsessionen. Testenheten raderas automatiskt.

```console
exit
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade vi två namnområden och tillämpade en nätverksprincip. Om du vill rensa dessa resurser använder du kommandot [kubectl delete][kubectl-delete] och anger resursnamnen:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverksresurser finns [i Nätverksbegrepp för program i Azure Kubernetes Service (AKS)][concepts-network].

Mer information om principer finns i [Kubernetes nätverksprinciper][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
