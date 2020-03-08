---
title: Skapa ett Azure Red Hat OpenShift 4,3-kluster | Microsoft Docs
description: Skapa ett kluster med Azure Red Hat OpenShift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.openlocfilehash: 81f8edb42be1f73692062d36440890ef5a1e7c9a
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899313"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Skapa, komma åt och hantera ett Azure Red Hat OpenShift 4,3-kluster

> [!IMPORTANT]
> Azure Red Hat OpenShift (ARO) 4,3 erbjuds i för hands versionen. För hands versions funktionerna är självbetjäning och tillhandahålls och är tillgängliga och omfattas inte av service nivå avtalet (SLA) och begränsad garanti. Därför är funktionerna inte avsedda att användas för produktion.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att skapa ett Azure Red Hat OpenShift 4,3-kluster:

- Azure CLI-version 2.0.72 eller senare
  
- Tillägget ' AZ Aro '

- Ett virtuellt nätverk som innehåller två tomma undernät, var och en med ingen nätverks säkerhets grupp ansluten.  Klustret kommer att distribueras till dessa undernät.

- Ett AAD-program (klient-ID och hemlighet) och tjänstens huvud namn, eller tillräckliga AAD-behörigheter för `az aro create` för att skapa ett AAD-program och tjänstens huvud namn åt dig automatiskt.

- Tjänstens huvud namn och tjänstens huvud namn för tjänsten RP måste var och en ha rollen deltagare i det virtuella kluster nätverket.  Om du har rollen som administratör för användar åtkomst i det virtuella nätverket skapar `az aro create` roll tilldelningarna automatiskt.

### <a name="install-the-az-aro-extension"></a>Installera tillägget ' AZ Aro '
Med tillägget `az aro` kan du skapa, komma åt och ta bort Azure Red Hat OpenShift-kluster direkt från kommando raden med hjälp av Azure CLI.

> [!Note] 
> `az aro`-tillägget är aktuellt i för hands versionen. Den kan ändras eller tas bort i en framtida version.
> Om du vill välja för hands versionen av `az aro`-tillägget måste du registrera `Microsoft.RedHatOpenShift` Resource Provider.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Logga in på Azure.

   ```console
   az login
   ```

2. Kör följande kommando för att installera `az aro`-tillägget:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Kontrol lera att ARO-tillägget är registrerat.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Skapa ett virtuellt nätverk som innehåller två tomma undernät

Följ dessa steg om du vill skapa ett virtuellt nätverk med två tomma undernät.

1. Ange följande variabler.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Skapa en resurs grupp för klustret.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Skapa det virtuella nätverket.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Lägg till två tomma undernät i det virtuella nätverket.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Inaktivera nätverks principer för tjänsten för privata länkar i ditt virtuella nätverk och undernät. Detta är ett krav för att ARO-tjänsten ska kunna komma åt och hantera klustret.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Skapa ett kluster

Kör följande kommando för att skapa ett kluster.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Det tar normalt cirka 35 minuter att skapa ett kluster.

## <a name="access-the-cluster-console"></a>Åtkomst till kluster konsolen

Du hittar kluster konsolens URL (av formuläret `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) under kluster resursen för att OpenShift 4,3 i Azure Red Hat. Kör följande kommando för att Visa resursen:

```console
az aro list -o table
```

Du kan logga in på klustret med `kubeadmin` användare.  Kör följande kommando för att hitta lösen ordet för den `kubeadmin` användaren:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Ta bort ett kluster

Kör följande kommando för att ta bort ett kluster.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```