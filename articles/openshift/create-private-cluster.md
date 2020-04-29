---
title: Skapa ett privat kluster i Azure Red Hat OpenShift 4
description: Lär dig hur du skapar ett privat kluster för Red Hat OpenShift som kör OpenShift 4
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: ms-jasondel
ms.author: jasondel
keywords: Aro, OpenShift, AZ Aro, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: a0f726d32f2f63cf85101254fded005fc0b5a1db
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233558"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Skapa ett privat kluster i Azure Red Hat OpenShift 4

I den här artikeln förbereder du din miljö för att skapa Azure Red Hat OpenShift-privata kluster som kör OpenShift 4. Du lär dig följande:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och skapa det virtuella nätverk och undernät som krävs
> * Distribuera ett kluster med en privat API-Server slut punkt och en privat ingångs kontroll

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.0.75 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Innan du börjar

### <a name="install-the-az-aro-extension"></a>Installera tillägget ' AZ Aro '
Med `az aro` tillägget kan du skapa, komma åt och ta bort Azure Red Hat OpenShift-kluster direkt från kommando raden med hjälp av Azure CLI.

Kör följande kommando för att installera `az aro` tillägget.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/stable
```

Om du redan har installerat tillägget kan du uppdatera genom att köra följande kommando.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/stable
```

### <a name="register-the-resource-provider"></a>Registrera resursprovidern

Sedan måste du registrera `Microsoft.RedHatOpenShift` resurs leverantören i din prenumeration.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Kontrol lera att tillägget har registrerats.

```azurecli-interactive
az -v
```

  Du bör få en utdata som liknar den nedan.

```output
...
Extensions:
aro                                1.0.0
...
```

### <a name="obtain-a-red-hat-pull-secret-optional"></a>Skaffa en Red Hat pull-hemlighet (valfritt)

Med en Red Hat pull-hemlighet kan ditt kluster få åtkomst till Red Hat container-register tillsammans med ytterligare innehåll. Det här steget är valfritt men rekommenderas.

Hämta din pull-hemlighet genom att gå https://cloud.redhat.com/openshift/install/azure/aro-provisioned till och klicka på *Hämta pull-hemlighet*.

Du måste logga in på ditt Red Hat-konto eller skapa ett nytt Red Hat-konto med ditt företags-e-post och godkänna de allmänna villkoren.

Behåll den sparade `pull-secret.txt` filen någonstans säkert – den kommer att användas i varje kluster som skapas.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Skapa ett virtuellt nätverk som innehåller två tomma undernät

Härnäst ska du skapa ett virtuellt nätverk som innehåller två tomma undernät.

1. **Ange följande variabler.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Skapa en resurs grupp**

    En Azure-resursgrupp är en logisk grupp där Azure-resurser distribueras och hanteras. När du skapar en resursgrupp uppmanas du att ange en plats. Den här platsen är den plats där resurs gruppens metadata lagras, men det är även där dina resurser körs i Azure om du inte anger någon annan region när du skapar en resurs. Skapa en resurs grupp med kommandot [AZ Group Create] [AZ-Group-Create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    Följande exempelutdata visar den resursgrupp som skapats:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Skapa ett virtuellt nätverk.**

    Azure Red Hat OpenShift-kluster som kör OpenShift 4 kräver ett virtuellt nätverk med två tomma undernät för Master-och Worker-noderna.

    Skapa ett nytt virtuellt nätverk i samma resurs grupp som du skapade tidigare.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    Följande exempel på utdata visar att det virtuella nätverket har skapats:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Lägg till ett tomt undernät för huvudnoderna.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Lägg till ett tomt undernät för arbetsnoderna.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Inaktivera privata slut punkts principer för undernät](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) i huvud under nätet.** Detta krävs för att kunna ansluta och hantera klustret.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Skapa klustret

Kör följande kommando för att skapa ett kluster. Observera `ingress-visibility` parametrarna `apiserver-visibility` och. Alternativt kan du skicka en pull-hemlighet som gör det möjligt för ditt kluster att få åtkomst till Red Hat container-register tillsammans med ytterligare innehåll. Få åtkomst till din pull-hemlighet genom att gå till den [Red Hat OpenShift-klusterresursen](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) och klicka på Kopiera pull-hemlighet.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain aro.example.com # [OPTIONAL] custom domain
  # --pull-secret 'Pull secret from https://cloud.redhat.com/openshift/install/azure/installer-provisioned/' # [OPTIONAL]
```

>[!NOTE]
> Det tar normalt cirka 35 minuter att skapa ett kluster.

>[!IMPORTANT]
> Om du väljer att ange en anpassad domän, till exempel **foo.example.com**, blir OpenShift-konsolen tillgänglig på en URL, till exempel `https://console-openshift-console.apps.foo.example.com`, i stället för den inbyggda domänen `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Som standard använder OpenShift självsignerade certifikat för alla vägar som skapas på `*.apps.<random>.<location>.aroapp.io`.  Om du väljer Anpassad DNS måste du efter att ha anslutit till klustret följa OpenShift-dokumentationen för att [Konfigurera en anpassad certifikat utfärdare för din](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) ingångs kontroll och [anpassad ca för din API-Server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

## <a name="connect-to-the-private-cluster"></a>Anslut till det privata klustret

Du kan logga in på klustret med hjälp `kubeadmin` av användaren.  Kör följande kommando för att hitta lösen ordet för `kubeadmin` användaren.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

Följande exempel på utdata visar att lösen ordet är i `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Du kan hitta kluster konsolens URL genom att köra följande kommando, som ser ut så här:`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> För att kunna ansluta till ett privat Azure Red Hat OpenShift-kluster måste du utföra följande steg från en värd som antingen finns i Virtual Network du skapade eller i en Virtual Network som [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) -kopplas med den Virtual Network klustret har distribuerats till.

Starta konsol-URL: en i en webbläsare och logga `kubeadmin` in med autentiseringsuppgifterna.

![Inloggnings skärm för Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Installera OpenShift CLI

När du är inloggad i OpenShift-webbkonsolen klickar du på **?** längst upp till höger och sedan på **kommando rads verktyg**. Ladda ned den version som är lämplig för din dator.

![Inloggnings skärm för Azure Red Hat OpenShift](media/aro4-download-cli.png)

Du kan också hämta den senaste versionen av CLI-versionen som är lämplig för <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>din dator från.

## <a name="connect-using-the-openshift-cli"></a>Anslut med OpenShift CLI

Hämta API-serverns adress.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> För att kunna ansluta till ett privat Azure Red Hat OpenShift-kluster måste du utföra följande steg från en värd som antingen finns i Virtual Network du skapade eller i en Virtual Network som [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) -kopplas med den Virtual Network klustret har distribuerats till.

Logga in på OpenShift-klustrets API-server med hjälp av följande kommando. Ersätt ** \<kubeadmin-lösenordet>** med det lösen ord som du nyss hämtade.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln distribuerades ett Kör som-kluster i Azure Red Hat som kör OpenShift 4. Du har lärt dig att:

> [!div class="checklist"]
> * Konfigurera förutsättningarna och skapa det virtuella nätverk och undernät som krävs
> * Distribuera ett kluster
> * Anslut till klustret med hjälp av `kubeadmin` användaren

Gå vidare till nästa artikel och lär dig hur du konfigurerar klustret för autentisering med hjälp av Azure Active Directory.


* [Konfigurera autentisering med Azure Active Directory med hjälp av kommando raden](configure-azure-ad-cli.md)


* [Konfigurera autentisering med Azure Active Directory med hjälp av webb konsolen Azure Portal och OpenShift](configure-azure-ad-cli.md)
