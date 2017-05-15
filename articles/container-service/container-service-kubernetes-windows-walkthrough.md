---
title: "Azure Kubernetes-kluster för Windows | Microsoft Docs"
description: "Distribuera och kom igång med ett Kubernetes-kluster för Windows-behållare i Azure Container Service"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: sv-se
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Kom igång med Kubernetes och Windows-behållare i Container Service


Den här artikeln beskriver hur du skapar ett Kubernetes-kluster i Azure Container Service som innehåller Windows-noder för att köra Windows-behållare. Kom igång med `az acs` Azure CLI 2.0-kommandon för att skapa ett Kubernetes-kluster i Azure Container Service. Använd sedan Kubernetes `kubectl` kommandoradsverktyg för att börja arbeta med Windows-behållare som har byggts från Docker-avbildningar. 

> [!NOTE]
> Stöd för Windows-behållare med Kubernetes i Azure Container Service är tillgängligt som en förhandsversion. 
>



Följande bild illustrerar arkitekturen i ett Kubernetes-kluster i Azure Container Service med en Linux-huvudnod och två Windows-agentnoder. 

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Linux-huvudnoden använder REST-API:et för Kubernetes och kan nås via SSH på port 22 eller `kubectl` på port 443. 
* Windows-agentnoderna är grupperade i en Azure-tillgänglighetsuppsättning och kör dina behållare. Windows-noderna kan nås via en RDP SSH-tunnel via huvudnoden. Azure-belastningsutjämningsregler läggs till dynamiskt i klustret beroende på vilka tjänster som exponeras.



Alla virtuella datorer finns i samma privata virtuella nätverk och kan nås av varandra. Alla virtuella datorer kör en kubelet, Docker och en proxy.

Mer bakgrundsinformation finns i [Introduktion till Azure Container Service](container-service-intro.md) och [Kubernetes-dokumentationen](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Krav
För att skapa ett Azure Container Service-kluster med hjälp av Azure CLI 2.0 måste du
* ha ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/))
* ha installerat och loggat in på [Azure CLI 2.0](/cli/azure/install-az-cli2)

Du måste också följande för Kubernetes-klustret. Du kan förbereda dem i förväg eller använda `az acs create`-kommandoalternativ för att generera dem automatiskt under distributionen av klustret. 

* **Offentlig SSH-RSA-nyckel**: Om du vill skapa SSH (Secure Shell) RSA-nycklar läser du hjälpartiklarna för [macOS och Linux](../virtual-machines/linux/mac-create-ssh-keys.md) eller [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Klient-ID och hemlighet för tjänstobjekt**: Anvisningar för att skapa ett tjänstehuvudnamn för Azure Active Directory och ytterligare information finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md).

Kommandoexemplet i den här artikeln genererar automatiskt SSH-nycklar och tjänstens huvudnamn.
  
## <a name="create-your-kubernetes-cluster"></a>Skapa Kubernetes-klustret

Här följer Azure CLI 2.0-kommandon för att skapa klustret. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp på en plats där Azure Container Service är [tillgängligt](https://azure.microsoft.com/regions/services/). Följande kommando skapar en resursgrupp med namnet *myKubernetesResourceGroup* på platsen *westus*.

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Skapa ett Kubernetes-kluster med Windows-agentnoder

Skapa ett Kubernetes-kluster i din resursgrupp med kommandot `az acs create` med `--orchestrator-type=kubernetes` och agentalternativet `--windows`. Kommandosyntax finns i `az acs create` [hjälp](/cli/azure/acs#create).

Följande kommando skapar ett Container Service-kluster med namnet *myKubernetesClusterName* med DNS-prefixet *myPrefix* för hanteringsnoden och de angivna autentiseringsuppgifterna för att nå Windows-noderna. Den här versionen av kommandot genererar automatiskt SSH RSA-nycklar och tjänstens huvudnamn för Kubernetes-klustret.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Kommandot har slutförts och du bör ha ett fungerande Kubernetes-kluster efter ett par minuter.

> [!IMPORTANT]
> Om ditt konto inte har behörighet för att skapa Azure AD-tjänstens huvudnamn genererar kommandot ett fel som liknar `Insufficient privileges to complete the operation.` Mer information finns i [Om tjänstens huvudnamn för ett Kubernetes-kluster](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Anslut till klustret med kubectl

För att ansluta till Kubernetes-klustret från klientdatorn använder du [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), Kubernetes kommandoradsklient. 

Om du inte har installerat `kubectl` kan du göra det med `az acs kubernetes install-cli`. (Du kan även hämta det från [Kubernetes-webbplatsen](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux eller macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Som standard installerar det här kommandot den här `kubectl` binära filen `/usr/local/bin/kubectl` på ett Linux- eller macOS-system, eller på `C:\Program Files (x86)\kubectl.exe` i Windows. Om du vill ange en annan installationssökväg använder du parametern `--install-location`.
>
> När `kubectl` är installerat, se till att dess katalog finns i systemsökvägen eller lägg till det i sökvägen. 


Sedan kör du följande kommando för att hämta huvudkonfigurationen för Kubernetes till den lokala `~/.kube/config`-filen:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

Nu bör du kunna komma åt ditt kluster från datorn. Prova att köra:

```bash
kubectl get nodes
```

Kontrollera att du kan se en lista över datorerna i klustret.

![Noder som körs i ett Kubernetes-kluster](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Skapa din första Kubernetes-tjänst

När klustret har skapats och anslutits till `kubectl` kan du prova att starta en Windows-app från en Docker-behållare och exponera den för Internet. Det här grundläggande exemplet använder en JSON-fil för att ange en behållare för Microsoft Internet Information Server (IIS) och skapar den med hjälp av `kubctl apply`. 

1. Skapa en lokal fil med namnet `iis.json` och kopiera följande. Filen uppmanar Kubernetes att köra IIS på Windows Server 2016 Server Core med en offentlig avbildning från [Docker Hub](https://hub.docker.com/r/microsoft/iis/). Behållaren använder port 80, men är ursprungligen endast tillgänglig i nätverket för klustret.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. Om du vill starta programmet skriver du:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Om du vill följa distributionen av behållaren skriver du:  
  ```bash
  kubectl get pods
  ```
  Medan behållaren distribueras är statusen `ContainerCreating`. 

  ![IIS-behållare med status ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  På grund av IIS-avbildningens storlek kan det ta flera minuter för behållaren att uppnå statusen `Running`.

  ![IIS-behållare i körningsstatus](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Skriv följande kommando för att visa behållaren:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Med detta kommando skapar Kubernetes en Azure Load Balancer-regel med en offentlig IP-adress. Det tar några minuter för ändringen att distribueras till belastningsutjämnaren. Mer information finns i [Load balance containers in a Kubernetes cluster in Azure Container Service](container-service-kubernetes-load-balancing.md) (Belastningsutjämna behållare i ett Kubernetes-kluster i Azure Container Service).

5. Kör följande kommando för att se status för tjänsten.

  ```bash
  kubectl get svc
  ```

  IP-adressen visas först som `pending`:

  ![Väntande extern IP-adress](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  IP-adressen har angetts efter några minuter:
  
  ![Extern IP-adress för IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. När den externa IP-adressen är tillgänglig kan du gå till den i webbläsaren:

  ![Bild som visar hur du går till IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Om du vill ta bort IIS-podden skriver du:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Nästa steg

* För att använda Kubernetes-användargränssnittet kör du kommandot `kubectl proxy`. Gå sedan till http://localhost:8001/ui.

* Anvisningar om hur du bygger en anpassad IIS-webbplats och kör den i en Windows-behållare finns i riktlinjerna för [Docker Hub](https://hub.docker.com/r/microsoft/iis/).

* Om du vill få åtkomst till Windows-noderna via en RDP SSH-tunnel till huvudnyckeln med PuTTy läser du [ACS-Engine-dokumentationen](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

