---
title: Skapa ett Azure Red Hat OpenShift 4,3-kluster | Microsoft Docs
description: Skapa ett kluster med Azure Red Hat OpenShift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398896"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Skapa, komma åt och hantera ett Azure Red Hat OpenShift 4,3-kluster

> [!IMPORTANT]
> Observera att Azure Red Hat OpenShift 4,3 är för närvarande endast tillgängligt i privat för hands version i USA, östra och USA, östra 2. Godkännande av privat för hands version är endast efter inbjudan. Var noga med att registrera din prenumeration innan du försöker aktivera den här funktionen: [Azure Red Hat OpenShift, privat för hands registrering](https://aka.ms/aro-preview-register)

> [!NOTE]
> För hands versions funktionerna är självbetjäning och tillhandahålls och är tillgängliga och omfattas inte av service nivå avtalet (SLA) och begränsad garanti. Därför är funktionerna inte avsedda att användas för produktion.

## <a name="prerequisites"></a>Krav

Du behöver följande för att skapa ett Azure Red Hat OpenShift 4,3-kluster:

- Azure CLI-version 2.0.72 eller senare
  
- Tillägget ' AZ Aro '

- Ett virtuellt nätverk som innehåller två tomma undernät, var och en med ingen nätverks säkerhets grupp ansluten.  Klustret kommer att distribueras till dessa undernät.

- Ett AAD-program för kluster (klient-ID och hemlighet) och tjänstens huvud namn, `az aro create` eller tillräckliga AAD-behörigheter för att skapa ett AAD-program och tjänstens huvud namn åt dig automatiskt.

- Tjänstens huvud namn och tjänstens huvud namn för tjänsten RP måste var och en ha rollen deltagare i det virtuella kluster nätverket.  Om du har rollen som administratör för användar åtkomst i det virtuella nätverket, `az aro create` kommer roll tilldelningarna att skapas automatiskt.

### <a name="install-the-az-aro-extension"></a>Installera tillägget ' AZ Aro '
Med `az aro` tillägget kan du skapa, komma åt och ta bort Azure Red Hat OpenShift-kluster direkt från kommando raden med hjälp av Azure CLI.

> [!Note] 
> `az aro` Tillägget är aktuellt i för hands versionen. Den kan ändras eller tas bort i en framtida version.
> Om du vill välja för för `az aro` hands versionen av tillägget måste du registrera `Microsoft.RedHatOpenShift` resurs leverantören.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Logga in på Azure.

   ```console
   az login
   ```

2. Kör följande kommando för att installera `az aro` tillägget:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Kontrol lera att ARO-tillägget är registrerat.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Hämta en Red Hat pull-hemlighet (valfritt)

Med en Red Hat pull-hemlighet kan ditt kluster få åtkomst till Red Hat container-register och ytterligare innehåll. Att använda en pull-hemlighet är valfritt men rekommenderas.

Så här hämtar du din pull-hemlighet:

1. Gå till https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto genom att använda ditt företags-e-post. Godkänn de allmänna villkoren.
1. Välj **Hämta pull-hemlighet**.

Spara filen *pull-Secret. txt* någonstans på ett säkert sätt. du kommer att använda filen varje gången du skapar ett kluster.

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
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> Det tar normalt cirka 35 minuter att skapa ett kluster.

## <a name="access-the-cluster-console"></a>Åtkomst till kluster konsolen

Du hittar kluster konsolens URL (av formuläret `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) under Azure Red Hat openshift 4,3-kluster resursen. Kör följande kommando för att Visa resursen:

```console
az aro list -o table
```

Du kan logga in på klustret med hjälp `kubeadmin` av användaren.  Kör följande kommando för att hitta lösen ordet för `kubeadmin` användaren:

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
