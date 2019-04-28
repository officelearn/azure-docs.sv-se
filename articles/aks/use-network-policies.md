---
title: Säker poddar med principer för nätverk i Azure Kubernetes Service (AKS)
description: Lär dig att skydda trafik som flödar in och ut ur poddar genom att använda nätverksprinciper för Kubernetes i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/08/2019
ms.author: iainfou
ms.openlocfilehash: 29180d6c1bb5f0991a4f33c3b7c9418f84d8260c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027980"
---
# <a name="preview---secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Förhandsgranskning – säker trafik mellan poddar med hjälp av principer för nätverk i Azure Kubernetes Service (AKS)

När du kör moderna, mikrotjänstbaserade program i Kubernetes kan vill du ofta styra vilka komponenter som kan kommunicera med varandra. Principen om lägsta behörighet ska tillämpas på hur trafiken kan flöda mellan poddar i ett kluster i Azure Kubernetes Service (AKS). Vi antar att du förmodligen vill blockera trafik direkt till backend-program. Den *nätverkspolicy* funktionen i Kubernetes kan du definiera regler för ingående och utgående trafik mellan poddar i ett kluster.

Den här artikeln visar hur du installerar principmodulen för nätverket och skapa principer för Kubernetes-nätverk för att styra trafikflödet mellan poddar i AKS. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning och delta i. Förhandsversioner tillhandahålls för att samla in feedback och buggar från vår community. De stöds dock inte av teknisk support för Azure. Om du skapar ett kluster eller lägga till dessa funktioner i befintliga kluster, stöds klustret inte förrän funktionen är inte längre i förhandsversion och uppgraderas till allmän tillgänglighet (GA).
>
> Om du stöter på problem med funktioner i förhandsversion [öppna ett ärende på AKS GitHub-lagringsplatsen] [ aks-github] med namnet på funktionen för förhandsgranskning i rubriken för bugg.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

Om du vill skapa ett AKS-kluster som kan använda nätverksprincip först aktivera en funktionsflagga i prenumerationen. Att registrera den *EnableNetworkPolicy* funktion flaggan, använda den [az funktionen registrera] [ az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera registreringsstatus med hjälp av den [az funktionslistan] [ az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern genom att använda den [az provider register] [ az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>Översikt över nätverksprincip

Alla poddar i ett AKS-kluster kan skicka och ta emot trafik utan begränsningar, som standard. Du kan definiera regler som styr flödet av trafik för att förbättra säkerheten. Serverprogram exponeras ofta bara för nödvändiga frontend-tjänster, till exempel. Eller databaskomponenter är endast tillgängliga på nivån för program som ansluter till dem.

Nätverksprincip är en Kubernetes-specifikation som definierar åtkomstprinciper för kommunikation mellan Poddar. Med principer för nätverk kan definiera du en ordnad uppsättning regler för att skicka och ta emot trafik och använda dem i en samling av poddar som matchar en eller flera väljare för etiketten.

Principregler dessa nätverk definieras som YAML-manifestet. Nätverksprinciper kan ingå som en del av ett bredare manifest som även skapar en distribution eller tjänst.

### <a name="network-policy-options-in-aks"></a>Alternativ för nätverk i AKS

Azure ger dig två sätt att implementera nätverksprincip. Du kan välja ett alternativ för principen när du skapar ett AKS-kluster. Alternativet kan inte ändras när klustret har skapats:

* Implementering av azures egna kallas *Azure nätverksprinciper*.
* *Calico nätverksprinciper*, ett nätverk för öppen källkod och network security-lösningen från [Tigera][tigera].

Båda implementeringar använda Linux *IPTables* att angivna principerna. Principer har översatts till uppsättningar med tillåtna och inte IP-par. Dessa par är sedan programmerade som IPTable filterregler.

Nätverksprincip fungerar bara med alternativet Azure CNI (Avancerat). Implementeringen är olika för de två alternativen:

* *Azure nätverksprinciper* -Azure-CNI ställer in en brygga i VM-värden för nätverk för kommunikation mellan noder. Filtreringsregler tillämpas när paketen passera bryggan.
* *Calico nätverksprinciper* -Azure-CNI ställer in lokal kernel vägar för kommunikation mellan noder-trafik. Principerna tillämpas i en pod-nätverksgränssnitt.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Skillnader mellan Azure och Calico principer och deras funktioner

| Funktion                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plattformar som stöds                      | Linux                      | Linux                       |
| Alternativ för nätverksfunktioner som stöds             | Azure CNI                  | Azure CNI                   |
| Kompatibilitet med Kubernetes-specifikation | Alla principtyper som stöds |  Alla principtyper som stöds |
| Ytterligare funktioner                      | Ingen                       | Utökade modellen princip som består av globala nätverksprincip, globala nätverket inställt och värd-slutpunkten. Mer information om hur du använder den `calicoctl` CLI för att hantera dessa utökade funktioner, finns i [calicoctl användaren referens][calicoctl]. |
| Support                                  | Stöds av Azure-supporten och teknikerteam | Calico community-support. Mer information om ytterligare betald support finns i [projekt Calico supportalternativ][calico-support]. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Skapa ett AKS-kluster och aktivera principen för nätverk

Nu ska vi se nätverksprinciper i praktiken, skapa och expandera sedan en princip som definierar trafikflödet:

* Neka all trafik till pod.
* Tillåta trafik baserat på pod etiketter.
* Tillåta trafik baserat på namnområdet.

Först måste vi skapa ett AKS-kluster som har stöd för nätverksprincipen. Principfunktionen nätverk kan bara aktiveras när klustret har skapats. Du kan inte aktivera principen för nätverk i ett befintligt AKS-kluster.

Om du vill använda principen för nätverk med ett AKS-kluster, måste du använda den [Azure CNI plugin-programmet] [ azure-cni] och definiera dina egna virtuella nätverk och undernät. Mer detaljerad information om hur du planerar ut nödvändiga undernätets adressintervall finns i [konfigurera avancerade nätverk][use-advanced-networking].

Följande exempelskript:

* Skapar ett virtuellt nätverk och undernät.
* Skapar ett Azure Active Directory (Azure AD) tjänstens huvudnamn för användning med AKS-klustret.
* Tilldelar *deltagare* behörigheter för AKS-kluster tjänstens huvudnamn i det virtuella nätverket.
* Skapar ett AKS-kluster i definierade virtuella nätverk och aktiverar nätverksprincip.
    * Den *azure* nätverk princip alternativet används. Om du vill använda Calico som alternativet nätverk i stället använda den `--network-policy calico` parametern.

Ange ditt eget säkra *SP_PASSWORD*. Du kan ersätta den *RESOURCE_GROUP_NAME* och *klusternamn* variabler:

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
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
    --kubernetes-version 1.12.6 \
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

Det tar några minuter att skapa klustret. När klustret är klart, konfigurera `kubectl` att ansluta till ditt Kubernetes-kluster med hjälp av den [aaz aks get-credentials] [ az-aks-get-credentials] kommando. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Neka alla inkommande trafik till en pod

Innan du kan definiera regler för att tillåta specifika nätverkstrafik, bör du först skapa en princip för nätverk om du vill neka all trafik. Den här principen ger dig en utgångspunkt för att börja godkänna önskad trafik. Du kan också tydligt se att trafiken ignoreras när nätverksprincipen som tillämpas.

Exemplet Programmiljö och trafikregler, vi först skapa ett namnområde som kallas *utveckling* att köra exemplet poddarna:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Skapa en pod för exempel backend-server som kör NGINX. Den här backend-pod kan användas för att simulera en backend-webbaserade exempelprogrammet. Skapa den här pod i den *utveckling* namnområdet, och öppna port *80* att betjäna webbtrafik. Etiketten pod med *app = webapp, roll = serverdel* så att vi kan rikta dem med en princip i nästa avsnitt:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Skapa en annan pod och bifoga en terminalsession för att testa att du har kan nå standard NGINX-webbsidan:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

I shell-prompten använder `wget` att bekräfta att du kan komma åt NGINX-webbsidan standard:

```console
wget -qO- http://backend
```

Följande exempel på utdata visar att NGINX-webbsidan standard returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Skapa och tillämpa en princip för nätverk

Nu när du har bekräftat att du kan använda den grundläggande NGINX-webbsidan på exemplet backend-pod kan du skapa en princip för nätverk om du vill neka all trafik. Skapa en fil med namnet `backend-policy.yaml` och klistra in följande YAML-manifestet. Den här manifestet använder en *podSelector* att koppla principen till poddar som har den *app:webapp, roll: serverdel* etikett som din pod för NGINX av exemplet. Inga regler har definierats *ingående*, så att all inkommande trafik till din pod nekas:

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

Tillämpa princip för nätverk med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testa nätverksprincipen


Låt oss se om du kan använda NGINX-webbsidan på backend-pod igen. Skapa en annan test pod och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

I shell-prompten använda `wget` om du har åtkomst till standard NGINX-webbsidan. Den här tiden kan ange ett timeout-värde *2* sekunder. Nätverksprincipen blockerar nu all inkommande trafik, så det inte går att läsa in sidan, som visas i följande exempel:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Tillåt inkommande trafik baserat på en pod-etikett

I det föregående avsnittet en backend-NGINX pod har schemalagts, en princip har skapats för att neka all trafik. Nu ska vi skapa en frontend pod och uppdatera nätverksprincipen för att tillåta trafik från klientdelen poddar.

Uppdatera princip för nätverk för att tillåta trafik från poddar med etiketter *app:webapp, roll: frontend* och i alla namnområden. Redigera den tidigare *backend-policy.yaml* filen och Lägg till *matchLabels* inkommande regler så att ditt manifest ser ut som i följande exempel:

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
> Den här nätverksprincipen används en *namespaceSelector* och en *podSelector* element för inkommande regel. YAML-syntax är viktigt för inkommande regler är additiva. Båda elementen måste matcha för inkommande regel som ska användas i det här exemplet. Kubernetes-versioner före *1.12* kan inte tolka de här elementen på rätt sätt och begränsa nätverkstrafik som förväntat. Mer information om det här problemet finns i [funktionssätt till och från väljare][policy-rules].

Tillämpa den uppdaterade nätverksprincipen med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Schemalägga en pod som är märkta som *app = webapp, roll = klientdel* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

I shell-prompten använda `wget` om du har åtkomst till standard NGINX-webbsidan:

```console
wget -qO- http://backend
```

Eftersom den inkommande regeln tillåter trafik med poddar som har etiketterna *app: webapp, roll: frontend*, tillåts trafik från klientdelen pod. Följande Exempelutdata visar standard NGINX-webbsidan returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Poden tas bort automatiskt.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testa en pod utan en motsvarande etikett

Nätverksprincipen tillåter trafik från poddar som är märkt *app: webapp, roll: frontend*, men bör nekar all annan trafik. Nu ska vi testa för att se om en annan pod utan dessa etiketter kan komma åt NGINX-pod backend-server. Skapa en annan test pod och bifoga en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

I shell-prompten använda `wget` om du har åtkomst till standard NGINX-webbsidan. Nätverksprincipen blockerar inkommande trafik, så det inte går att läsa in sidan, som visas i följande exempel:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Tillåt endast trafik från inom en definierad namnområde

I föregående exempel har skapat du en princip för nätverk som nekas all trafik och sedan uppdateras principen för att tillåta trafik från poddar med en viss etikett. Behovet av en annan vanliga är att begränsa trafik till endast inom ett visst namnområde. Om föregående exempel för trafik i en *utveckling* namnrymd, skapa en princip för nätverk som förhindrar trafik från en annan namnrymd som *produktion*, från att nå poddarna.

Börja med att skapa ett nytt namnområde för att simulera ett namnområde för produktion:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Schemalägga en test-pod i den *produktion* namnområde som är märkta som *app = webapp, roll = klientdel*. Koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

I shell-prompten använder `wget` att bekräfta att du kan komma åt NGINX-webbsidan standard:

```console
wget -qO- http://backend.development
```

Eftersom etiketterna för poden matchar vad tillåts för närvarande i nätverksprincipen, tillåts trafik. Nätverksprincipen titta inte på namnrymder, endast pod-etiketter. Följande Exempelutdata visar standard NGINX-webbsidan returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt.

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
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

I mer komplexa exempel kan du definiera flera inkommande regler som en *namespaceSelector* och sedan en *podSelector*.

Tillämpa den uppdaterade nätverksprincipen med hjälp av den [kubectl gäller] [ kubectl-apply] kommandot och ange namnet på ditt YAML-manifest:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testa den uppdaterade nätverksprincipen

Schemalägga en annan pod i den *produktion* namnområde och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

I shell-prompten använder `wget` att se att nätverksprincipen nu nekar trafik:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Stänger ner test-pod:

```console
exit
```

Med trafik nekas från den *produktion* namnområde, schema för en test-pod igen den *utveckling* namnområde och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

I shell-prompten använder `wget` att se att nätverksprincipen tillåter trafik:

```console
wget -qO- http://backend
```

Trafik tillåts eftersom poden schemaläggs i namnområdet som matchar vad tillåts i nätverksprincipen. Följande exempel på utdata visar standard NGINX-webbsidan returneras:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Stänger ner anslutna terminalsessionen. Test-pod tas bort automatiskt.

```console
exit
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har vi skapat två namnområdena och tillämpat en princip för. Om du vill rensa de här resurserna kan använda den [kubectl ta bort] [ kubectl-delete] kommandot och ange resursnamnen:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverksresurser finns i [Network begrepp för program i Azure Kubernetes Service (AKS)][concepts-network].

Mer information om principer finns [Kubernetes nätverksprinciper][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues]
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.5/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
