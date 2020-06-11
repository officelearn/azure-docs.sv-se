---
title: Aktivera tillägg för ingångs kontroll för nya AKS-kluster med New Azure Application Gateway
description: Använd den här självstudien för att aktivera tillägg för ingångs kontroll för ditt nya AKS-kluster med en ny Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 8595b7fc37a46dbb27dec4d1388e4b0251606411
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670938"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-through-azure-cli-preview"></a>Självstudie: Aktivera Application Gateway ingress Controller-tillägg för ett nytt AKS-kluster med en ny Application Gateway via Azure CLI (för hands version)

Du kan använda Azure CLI för att aktivera [AGIC-tillägget (Application Gateway ingress Controller)](ingress-controller-overview.md) , som för närvarande är en för hands version, för ditt [AKS-kluster (Azure Kubernetes Services](https://azure.microsoft.com/services/kubernetes-service/) ). I den här självstudien får du skapa ett AKS-kluster med AGIC-tillägget aktiverat som automatiskt skapar ett Application Gateway som ska användas. Sedan distribuerar du ett exempel program som kommer att använda AGIC-tillägget för att exponera programmet genom Application Gateway. Tillägget ger ett mycket snabbare sätt att distribuera AGIC för ditt AKS-kluster än [tidigare via Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) och erbjuder även en fullständigt hanterad upplevelse.    

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa en resursgrupp 
> * Skapa ett nytt AKS-kluster med AGIC-tillägget aktiverat 
> * Distribuera ett exempel program med AGIC för ingress i AKS-klustret
> * Kontrol lera att programmet kan kontaktas via Application Gateway

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du ha Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Registrera funktions flaggan *AKS-IngressApplicationGatewayAddon* med hjälp av kommandot [AZ Feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , som du ser i följande exempel. du behöver bara göra detta en gång per prenumeration medan tillägget fortfarande är i för hands version:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Det kan ta några minuter för statusen att Visa registrerad. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register) :
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören Microsoft. container service med hjälp av [AZ Provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) kommando:
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Se till att installera/uppdatera AKS för förhands granskning för den här självstudien. Använd följande Azure CLI-kommandon
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i *indiensödra* -platsen (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster-with-agic-add-on-enabled"></a>Distribuera ett nytt AKS-kluster med AGIC-tillägget aktiverat

Nu ska du distribuera ett nytt AKS-kluster med AGIC-tillägget aktiverat. När du distribuerar ett nytt AKS-kluster med AGIC-tillägget aktiverat och inte tillhandahåller en befintlig Application Gateway att använda, skapas och konfigureras automatiskt en ny Application Gateway för att betjäna trafik till AKS-klustret.  

> [!NOTE]
> AGIC-tillägget (Application Gateway ingress Controller) stöder **bara** Application Gateway v2 SKU: er (standard och WAF) och **inte** Application Gateway v1 SKU: er. När du distribuerar en ny Application Gateway via AGIC-tillägget kan du bara distribuera en Application Gateway Standard_v2 SKU. Om du vill aktivera AGIC-tillägget för en Application Gateway WAF_v2 SKU, aktiverar du antingen WAF på Application Gateway via portalen eller skapar WAF_v2 Application Gateway först och följer sedan anvisningarna om hur du [aktiverar AGIC-tillägg med ett befintligt AKS-kluster och befintliga Application Gateway](tutorial-ingress-controller-add-on-existing.md). 

I följande exempel ska du distribuera ett nytt AKS-kluster *med namnet IT-kluster* med [Azure cni](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) och [hanterade identiteter](https://docs.microsoft.com/azure/aks/use-managed-identity) med AGIC-tillägget aktiverat i resurs gruppen som du skapade, *myResourceGroup*. Eftersom distribution av ett nytt AKS-kluster med AGIC-tillägget aktiverat utan att ange en befintlig Application Gateway innebär att en Standard_v2 SKU-Application Gateway automatiskt skapas, anger du också namnet och under nätets adress utrymme för Application Gateway. Namnet på Application Gateway kommer att vara *myApplicationGateway* och under näts adress utrymmet som vi använder är 10.2.0.0/16. Se till att du har lagt till/uppdaterat AKS i början av den här självstudien. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Om du vill konfigurera ytterligare parametrar för `az aks create` kommandot kan du gå till referenser [här](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Det AKS-kluster som du har skapat visas i den resurs grupp som du skapade, *myResourceGroup*. Men de automatiskt skapade Application Gateway bor i resurs gruppen resurs, där agent poolerna är. Resurs gruppen för noden som standard får namnet *MC_resource-Group-name_cluster-name_location*, men kan ändras. 

## <a name="deploy-a-sample-application-using-agic"></a>Distribuera ett exempel program med AGIC

Nu ska du distribuera ett exempel program till det AKS-kluster som du skapade som kommer att använda AGIC-tillägget för ingress och ansluta Application Gateway till AKS-klustret. Först får du autentiseringsuppgifter till det AKS-kluster som du distribuerade genom att köra `az aks get-credentials` kommandot. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

När du har autentiseringsuppgifterna till klustret som du har skapat kör du följande kommando för att konfigurera ett exempel program som använder AGIC för ingångs händelser till klustret. AGIC kommer att uppdatera Application Gateway som du ställer in tidigare med motsvarande routningsregler till det nya exempel program som du har distribuerat.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Kontrol lera att programmet kan kommas åt

Nu när Application Gateway har kon figurer ATS för att betjäna trafik till AKS-klustret ska vi kontrol lera att det går att komma åt programmet. Du får först ta emot IP-adressen för ingångarna. 

```azurecli-interactive
kubectl get ingress
```

Kontrol lera att det exempel program som du har skapat är igång genom att antingen gå till IP-adressen för den Application Gateway som du fick från att köra kommandot ovan eller genom att markera med `curl` . Det kan ta Application Gateway en minut att hämta uppdateringen, så om Application Gateway fortfarande är i ett "uppdaterings läge" på portalen, kan den avslutas innan den försöker nå IP-adressen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg
* [Läs mer om hur du inaktiverar AGIC-tillägget](./ingress-controller-disable-addon.md)
* [Lär dig mer om vilka anteckningar som stöds med AGIC](./ingress-controller-annotations.md)
* [Felsöka problem med AGIC](./ingress-controller-troubleshoot.md)

