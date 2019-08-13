---
title: Snabbstart – Skapa ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du snabbt skapar ett Kubernetes-kluster med hjälp av en Azure Resource Manager-mall och distribuera ett program i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: quickstart
ms.date: 04/19/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: e7cc9b63768385e4665e330b2b02a884b84c2188
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615388"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-azure-resource-manager-template"></a>Snabbstart: Distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av en Azure Resource Manager-mall

Azure Kubernetes Service (AKS) är en hanterad Kubernetes-tjänst som gör att du snabbt kan distribuera och hantera kluster. I den här snabb starten distribuerar du ett AKS-kluster med hjälp av en Azure Resource Manager-mall. Ett flerbehållarprogram som består av en webbklientdel och en Redis-instans körs sedan i klustret.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Kubernetes Core Concepts for Azure Kubernetes service (AKS)][kubernetes-concepts].

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver den här snabb starten att du kör Azure CLI-version 2.0.61 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa ett AKS-kluster med en Resource Manager-mall anger du en offentlig SSH-nyckel och Azure Active Directory tjänstens huvud namn. Om du behöver någon av dessa resurser kan du läsa mer i följande avsnitt: Annars hoppar du till avsnittet [skapa ett AKS-kluster](#create-an-aks-cluster) .

### <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Om du vill komma åt AKS-noder ansluter du med ett SSH-nyckelpar. `ssh-keygen` Använd kommandot för att generera offentliga och privata SSH-nyckelfiler. Som standard skapas de här filerna i katalogen *~/.ssh* . Om det finns ett SSH-nyckelpar med samma namn på den aktuella platsen, skrivs filerna över.

Följande kommando skapar ett SSH-nyckelpar med RSA-kryptering och en bit-längd på 2048:

```azurecli-interactive
ssh-keygen -t rsa -b 2048
```

Mer information om hur du skapar SSH-nycklar finns i [skapa och hantera SSH-nycklar för autentisering i Azure][ssh-keys].

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om ett AKS-kluster ska kunna interagera med andra Azure-resurser behövs ett huvudnamn för tjänsten i Azure Active Directory. Skapa ett huvudnamn för tjänsten med kommandot [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. Parametern `--skip-assignment` gör att inga ytterligare behörigheterna tilldelas. Som standard är tjänstens huvudnamn giltigt i ett år.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Utdata ser ut ungefär så här:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Anteckna värdena för *appId* och *password*. De här värdena används i senare steg.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Mallen som används i den här snabb starten är att [distribuera ett Azure Kubernetes service-kluster](https://azure.microsoft.com/resources/templates/101-aks/). Fler AKS-exempel finns på webbplatsen för [AKS snabb starts mallar][aks-quickstart-templates] .

1. Välj följande bild för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](./media/kubernetes-walkthrough-rm-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Välj eller ange följande värden.  

    I den här snabb starten lämnar du standardvärdena för *OS-diskens storlek GB*, *antal agenter*, *virtuell dator storlek för virtuell dator*, *OS-typ*och Kubernetes- *version*. Ange dina egna värden för följande mallparametrar:

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resursgrupp**: Välj **Skapa ny**. Ange ett unikt namn för resurs gruppen, till exempel *myResourceGroup*, och välj sedan **OK**.
    * **Plats**: Välj en plats, t. ex. **USA, östra**.
    * **Kluster namn**: Ange ett unikt namn för AKS-klustret, till exempel *myAKSCluster*.
    * **DNS-prefix**: Ange ett unikt DNS-prefix för klustret, till exempel *myakscluster*.
    * **Användar namn för Linux-administratör**: Ange ett användar namn för att ansluta med SSH, till exempel *azureuser*.
    * **Offentlig SSH RSA-nyckel**: Kopiera och klistra in den *offentliga* delen av ditt SSH-nyckelpar (som standard innehåller innehållet på *~/.ssh/id_rsa.pub*).
    * **Klient-ID för tjänstens huvud namn**: Kopiera och klistra in *appId* för ditt tjänst huvud namn från `az ad sp create-for-rbac` kommandot.
    * **Tjänstens huvud namn för klient hemlighet**: Kopiera och klistra in *lösen ordet* för tjänstens huvud namn `az ad sp create-for-rbac` från kommandot.
    * **Jag godkänner villkoren ovan**: Markera den här rutan om du vill godkänna.

    ![Resource Manager-mall för att skapa ett Azure Kubernetes service-kluster i portalen](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Välj **Köp**.

Det tar några minuter att skapa AKS-klustret. Vänta tills klustret har distribuerats innan du går vidare till nästa steg.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

Om du vill hantera ett Kubernetes-kluster använder du [kubectl][kubectl], Kubernetes kommando rads klient. Om du använder Azure Cloud Shell är `kubectl` redan installerat. Installera `kubectl` lokalt genom att använda kommandot [AZ AKS install-CLI][az-aks-install-cli] :

```azurecli
az aks install-cli
```

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][az-aks-get-credentials]. Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Du kan kontrollera anslutningen till klustret genom att köra kommandot [kubectl get][kubectl-get] för att returnera en lista över klusternoderna.

```azurecli-interactive
kubectl get nodes
```

Följande exempel på utdata visar de noder som skapades i föregående steg. Kontrol lera att status för alla noder är *klar*:

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

## <a name="run-the-application"></a>Köra programmet

En Kubernetes-manifestfil definierar ett önskat tillstånd för klustret, till exempel vilka containeravbildningar som ska köras. I den här snabbstarten används ett manifest för att skapa alla objekt som behövs för att köra Azure Vote-programmet. Det här manifestet innehåller två [Kubernetes][kubernetes-deployment] -distributioner – en för Azures rösten python-program och en annan för en Redis-instans. Två [Kubernetes-tjänster][kubernetes-service] skapas också – en intern tjänst för Redis-instansen och en extern tjänst för att få åtkomst till Azures röst program från Internet.

> [!TIP]
> I den här snabbstarten skapar och distribuerar du manuellt applikationsmanifest till AKS-klustret. I verkliga scenarier kan du använda [Azure dev Spaces][azure-dev-spaces] för att snabbt iterera och felsöka koden direkt i AKS-klustret. Du kan använda Dev Spaces på olika OS-plattformar och i olika utvecklingsmiljöer samt arbeta tillsammans med andra i ditt team.

Skapa en fil med namnet `azure-vote.yaml` och kopiera följande YAML-definition. Om du använder Azure Cloud Shell, kan du skapa filen med `vi` eller `nano` som om du arbetar i ett virtuellt eller fysiskt system:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Distribuera programmet med kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```azurecli-interactive
kubectl apply -f azure-vote.yaml
```

Följande exempelutdata visar de distributioner och tjänster som skapats:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *väntande*.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Om du vill se hur Azure Vote-appen fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst.

![Bild som illustrerar hur du navigerar till Azure Vote](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Ta bort kluster

När klustret inte längre behövs kan du använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, behållar tjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Anvisningar om hur du tar bort tjänstens huvud namn finns i [AKS Service Principal överväganden och borttagning][sp-delete].

## <a name="get-the-code"></a>Hämta koden

I den här snabbstarten har fördefinierade containeravbildningar användes för att skapa en Kubernetes-distribution. Den tillhörande programkoden, Dockerfile och Kubernetes-manifestfilen finns på GitHub.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden distribuerade du ett Kubernetes-kluster och distribuerade sedan ett flerbehållarprogram till det. [Öppna Kubernetes-][kubernetes-dashboard] webbinstrumentpanelen för det kluster som du skapade.

Om du vill lära dig mer om AKS, och gå igenom ett exempel med fullständig distributionskod, fortsätter du till självstudiekursen om Kubernetes-kluster.

> [!div class="nextstepaction"]
> [AKS-självstudie][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
