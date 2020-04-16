---
title: Skapa ett Azure Red Hat OpenShift 4.3-kluster | Microsoft-dokument
description: Skapa ett kluster med Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, röd hatt, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398896"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Skapa, komma åt och hantera ett Azure Red Hat OpenShift 4.3-kluster

> [!IMPORTANT]
> Observera att Azure Red Hat OpenShift 4.3 för närvarande endast är tillgängligt i privat förhandsversion i östra USA och östra USA 2. Accepterar privat förhandsgranskning sker endast genom inbjudan. Var noga med att registrera din prenumeration innan du försöker aktivera den här funktionen: [Azure Red Hat OpenShift Private Preview Registration](https://aka.ms/aro-preview-register)

> [!NOTE]
> Förhandsversionsfunktioner är självbetjäning och tillhandahålls i dess fall och är undantagna från servicenivåavtalet (SLA) och begränsad garanti. Därför är funktionerna inte avsedda för produktionsanvändning.

## <a name="prerequisites"></a>Krav

Du behöver följande för att skapa ett Azure Red Hat OpenShift 4.3-kluster:

- Azure CLI version 2.0.72 eller senare
  
- Förlängningen "az aro"

- Ett virtuellt nätverk som innehåller två tomma undernät, var och en utan nätverkssäkerhetsgrupp ansluten.  Klustret distribueras i dessa undernät.

- Ett klusterAAD-program (klient-ID och hemligt) och tjänsthuvudnamn, eller tillräcklig AAD-behörighet för `az aro create` att skapa ett AAD-program och tjänsthuvudnamn för dig automatiskt.

- Huvudhuvudhuvudbeloppet för RP-tjänsten och klustertjänstens huvudnamn måste ha rollen Deltagare i klustrets virtuella nätverk.  Om du har rollen "Administratör för användaråtkomst" i det virtuella nätverket `az aro create` ställer du in rolltilldelningarna för dig automatiskt.

### <a name="install-the-az-aro-extension"></a>Installera tillägget "az aro"
Tillägget `az aro` kan du skapa, komma åt och ta bort Azure Red Hat OpenShift-kluster direkt från kommandoraden med hjälp av Azure CLI.

> [!Note] 
> Tillägget `az aro` är aktuellt i förhandsgranskningen. Det kan ändras eller tas bort i en framtida version.
> För att anmäla `az aro` dig till förhandsgranskningen `Microsoft.RedHatOpenShift` av tillägget måste du registrera resursleverantören.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Logga in på Azure.

   ```console
   az login
   ```

2. Kör följande kommando för `az aro` att installera tillägget:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Kontrollera att ARO-tillägget är registrerat.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Få en Red Hat pull hemlighet (tillval)

En Red Hat pull secret gör det möjligt för ditt kluster att komma åt Red Hat-behållarregister och ytterligare innehåll. Att använda en pull-hemlighet är valfritt men rekommenderas.

För att få din pull hemlighet:

1. Gå till https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto med hjälp av företagets e-postadress; godkänna villkoren.
1. Välj **Ladda ner dra hemlighet**.

Spara *pull-secret.txt-filen* någonstans säkert; du kommer att använda filen varje gång du skapar ett kluster.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Skapa ett virtuellt nätverk som innehåller två tomma undernät

Följ dessa steg för att skapa ett virtuellt nätverk som innehåller två tomma undernät.

1. Ange följande variabler.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Skapa en resursgrupp för klustret.

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

5. Inaktivera nätverksprinciper för private link-tjänsten i ditt virtuella nätverk och i undernät. Detta är ett krav för ARO-tjänsten att komma åt och hantera klustret.

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

## <a name="access-the-cluster-console"></a>Få tillgång till klusterkonsolen

Du hittar klusterkonsolens URL `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`(i formuläret) under Azure Red Hat OpenShift 4.3-klusterresursen. Kör följande kommando för att visa resursen:

```console
az aro list -o table
```

Du kan logga in `kubeadmin` i klustret med hjälp av användaren.  Kör följande kommando för att `kubeadmin` hitta lösenordet för användaren:

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
