---
title: Säker poddar med principer för nätverk i Azure Kubernetes Service (AKS)
description: Lär dig att skydda trafik som flödar in och ut ur poddar med hjälp av Kubernetes-nätverksprinciper i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 78c07fedc89980edf54c1b317d5d93a5de6a070f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301425"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Säker trafik mellan poddar med hjälp av principer för nätverk i Azure Kubernetes Service (AKS)

När du kör moderna, mikrotjänstbaserade program i Kubernetes kan vill du ofta styra vilka komponenter som kan kommunicera med varandra. Principen om lägsta behörighet ska tillämpas på hur trafiken kan flöda mellan poddar i ett AKS-kluster. Till exempel vill du förmodligen att blockera trafik direkt till serverdelsprogrammen. I Kubernetes, den *nätverkspolicy* funktionen kan du definiera regler för ingående och utgående trafik mellan poddar i ett kluster.

Den här artikeln visar hur du använder principer för nätverk för att styra trafikflödet mellan poddar i AKS.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.56 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="overview-of-network-policy"></a>Översikt över nätverksprincip

Som standard kan alla poddar i ett AKS-kluster skicka och ta emot trafik utan begränsningar. Du kan definiera regler som styr flödet av trafik för att förbättra säkerheten. Till exempel serverdelsprogrammen exponeras ofta bara till nödvändiga frontend-tjänster eller databaskomponenter är endast tillgängliga på nivån för program som ansluter till dem.

Nätverksprinciper är Kubernetes-resurser att reglera trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar, till exempel tilldelade etiketter, namnområde eller trafik port. Nätverksprinciper definieras som en YAML manifest, och kan ingå som en del av ett bredare manifest som även skapar en distribution eller tjänst.

Nu ska vi se nätverksprinciper i praktiken, skapa och expandera sedan en princip som definierar trafikflödet på följande sätt:

* Neka all trafik till pod.
* Tillåta trafik baserat på pod etiketter.
* Tillåta trafik baserat på namnområdet.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Skapa ett AKS-kluster och aktivera principen för nätverk

Principen för nätverk kan bara aktiveras när klustret har skapats. Du kan inte aktivera principen för nätverk i ett befintligt AKS-kluster. Om du vill skapa ett AKS med nätverksprincip först aktivera en funktionsflagga i prenumerationen. Att registrera den *EnableNetworkPolicy* funktion flaggan, använda den [az funktionen registrera] [ az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan] [ az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register] [ az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Om du vill använda principen för nätverk med ett AKS-kluster, måste du använda den [plugin-programmet Azure CNI] [ azure-cni] och definiera dina egna virtuella nätverk och undernät. Mer detaljerad information om hur du planerar ut nödvändiga undernätets adressintervall finns i [konfigurera avancerade nätverk][use-advanced-networking]. Följande exempelskript:

* Skapar ett virtuellt nätverk och undernät.
* Skapar ett Azure Active Directory (AD) tjänstens huvudnamn för användning med AKS-klustret.
* Tilldelar *deltagare* behörigheter för AKS-kluster tjänstens huvudnamn i det virtuella nätverket.
* Skapar ett AKS-kluster i definierade virtuella nätverk och aktiverar nätverksprincip.

Ange ditt eget säkra *SP_PASSWORD*. Om du vill kan du ersätta den *RESOURCE_GROUP_NAME* och *klusternamn* variabler:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
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
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

Det tar några minuter att skapa klustret. När du är klar kan du konfigurera `kubectl` att ansluta till ditt Kubernetes-kluster med den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Neka alla inkommande trafik till en pod

Innan du kan definiera regler för att tillåta specifika nätverkstrafik, bör du först skapa en princip för nätverk om du vill neka all trafik. Den här principen ger dig en utgångspunkt för att börja godkänna önskad trafik. Du kan också tydligt se att trafiken ignoreras när nätverksprincipen som tillämpas.

Våra exempel Programmiljö och trafikregler vi först skapa ett namnområde som kallas *utveckling* att köra våra exempel poddarna:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Nu ska du skapa en exempel-serverdel poddar som kör NGINX. Den här backend-pod kan användas för att simulera en serverdel webbaserade exempelprogrammet. Skapa den här pod i den *utveckling* namnområdet, och öppna port *80* att betjäna webbtrafik. Etiketten pod med *app = webapp, roll = serverdel* så att vi kan rikta dem med en princip i nästa avsnitt:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Om du vill testa att du har kan nå standardwebbsidan för NGINX, skapa en annan pod och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

En gång i shell-prompten använder `wget` att bekräfta att du kan komma åt NGINX-standardwebbsidan:

```console
wget -qO- http://backend
```

Följande exempel på utdata visar att standardwebbsidan för NGINX returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt:

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Skapa och tillämpa en princip för nätverk

Nu när du har bekräftat att du kan komma åt grundläggande NGINX-webbsidan på backend-pod exemplet kan du skapa en princip för nätverk om du vill neka all trafik. Skapa en fil med namnet `backend-policy.yaml` och klistra in följande YAML-manifestet. Den här manifestet använder en *podSelector* att koppla principen till poddar som har den *app:webapp, roll: serverdel* etikett, till exempel din pod för NGINX av exemplet. Inga regler har definierats *ingående*, så att all inkommande trafik till din pod nekas:

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

Använd nätverk princip med de [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testa nätverksprincipen

Låt oss se om du kan komma åt NGINX-webbsidan på backend-pod igen. Skapa en annan test pod och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

En gång i shell-prompten använda `wget` om du har åtkomst till standardwebbsidan för NGINX. Den här tiden kan ange ett timeout-värde *2* sekunder. Nätverksprincipen blockerar nu all inkommande trafik, så det inte går att läsa in sidan, som visas i följande exempel:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt:

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Tillåt inkommande trafik baserat på en pod-etikett

En serverdel NGINX pod har schemalagts i föregående avsnitt, en princip har skapats för att neka all trafik. Nu ska vi skapa en frontend-pod och uppdatera nätverksprincipen för att tillåta trafik från klientdelens poddar.

Uppdatera princip för nätverk för att tillåta trafik från poddar med etiketter *app:webapp, roll: frontend* och i alla namnområden. Redigera den tidigare *backend-policy.yaml* filen och Lägg till en *matchLabels* inkommande regler så att ditt manifest ser ut som i följande exempel:

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
    - podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Tillämpa den uppdaterade nätverk princip med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Schemalägg nu en pod som är märkta som *app = webapp, roll = klientdel* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

En gång i shell-prompten använda `wget` om du har åtkomst till NGINX-standardwebbsidan:

```console
wget -qO- http://backend
```

Som inkommande regeln tillåter trafik med poddar som har etiketterna *app: webapp, roll: frontend*, tillåts trafik från frontend-pod. Följande Exempelutdata visar den standardwebbsidan för NGINX som returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Poden tas bort automatiskt:

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testa en pod utan en motsvarande etikett

Nätverksprincipen tillåter trafik från poddar som är märkt *app: webapp, roll: frontend*, men bör nekar all annan trafik. Nu ska vi testa att en annan pod utan dessa etiketter inte kan komma åt NGINX-pod serverdel. Skapa en annan test pod och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

En gång i shell-prompten använda `wget` om du har åtkomst till standardwebbsidan för NGINX. Nätverksprincipen blockerar inkommande trafik, så det inte går att läsa in sidan, som visas i följande exempel:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt:

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Tillåt endast trafik från inom en definierad namnområde

I föregående exempel har skapat du en princip för nätverk som nekas all trafik och sedan uppdatera principen för att tillåta trafik från poddar med en viss etikett. En andra vanliga behov är att begränsa trafik till endast inom ett visst namnområde. Om föregående exempel för trafik i en *utveckling* namnområdet, kan du sedan skapa en princip för nätverk som förhindrar trafik från en annan namnrymd som *produktion*, från att nå den poddar.

Börja med att skapa ett nytt namnområde för att simulera ett namnområde för produktion:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Schemalägga en test-pod i den *produktion* namnområde som är märkta som *app = webapp, roll = klientdel*. Koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

En gång i shell-prompten använder `wget` att bekräfta att du kan komma åt NGINX-standardwebbsidan:

```console
wget -qO- http://backend.development
```

Eftersom etiketterna för poden matchar vad tillåts för närvarande i nätverksprincipen, tillåts trafik. Nätverksprincipen titta inte på namnrymder, endast pod-etiketter. Följande Exempelutdata visar den standardwebbsidan för NGINX som returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt:

```console
exit
```

### <a name="update-the-network-policy"></a>Uppdatera nätverksprincipen

Nu ska vi uppdatera den inkommande regeln *namespaceSelector* avsnitt för att endast tillåta trafik inifrån den *utveckling* namnområde. Redigera den *backend-policy.yaml* manifestfil som visas i följande exempel:

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
    - podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

I mer komplexa exempel du definiera flera inkommande regler som du använder en *namespaceSelector* och sedan en *podSelector*.

Tillämpa den uppdaterade nätverk princip med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testa den uppdaterade nätverksprincipen

Nu schemalägga en annan pod i den *produktion* namnområde och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

En gång i shell-prompten använder `wget` att se nätverksprincipen som nu nekar trafik:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Stänger ner test-pod:

```console
exit
```

Med trafik nekas från den *produktion* namnområde nu schemat en test-pod tillbaka i den *utveckling* namnområde och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

En gång i shell-prompten använder `wget` för att se nätverket principen Tillåt trafiken:

```console
wget -qO- http://backend
```

Som en pod har schemalagts i namnområdet som matchar vad som tillåts i nätverksprincipen tillåts trafiken. Följande exempel på utdata visar den standardwebbsidan för NGINX som returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt:

```console
exit
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har vi skapa två namnområden och tillämpas en princip. Om du vill rensa de här resurserna kan använda den [kubectl ta bort] [ kubectl-delete] kommandot och ange resursnamnen enligt följande:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverksresurser finns [Network begrepp för program i Azure Kubernetes Service (AKS)][concepts-network].

Mer information om hur du använder principer finns [Kubernetes nätverksprinciper][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
