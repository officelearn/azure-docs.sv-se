---
title: Använd AAD-identitet med din webb tjänst
titleSuffix: Azure Machine Learning
description: Använd AAD-identitet med din webb tjänst i Azure Kubernetes-tjänsten för att få åtkomst till moln resurser under poängsättningen.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1789f83f048a2ab0fb75aa33635e58b0850b865b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319136"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Använda Azure AD-identitet med din Machine Learning-webbtjänst i Azure Kubernetes-tjänsten

I den här instruktionen får du lära dig hur du tilldelar en Azure Active Directory identitet (AAD) till din distribuerade maskin inlärnings modell i Azure Kubernetes-tjänsten. [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) Project ger program åtkomst till moln resurser på ett säkert sätt med AAD genom att använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) och Kubernetes-primitiver. Detta gör att webb tjänsten kan komma åt dina Azure-resurser på ett säkert sätt utan att behöva bädda in autentiseringsuppgifter eller hantera tokens direkt inuti `score.py` skriptet. I den här artikeln beskrivs stegen för att skapa och installera en Azure-identitet i Azure Kubernetes service-klustret och tilldela identiteten till den distribuerade webb tjänsten.

## <a name="prerequisites"></a>Förutsättningar

- [Azure CLI-tillägget för Machine Learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- Åtkomst till ditt AKS-kluster med hjälp av `kubectl` kommandot. Mer information finns i [ansluta till klustret](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- En Azure Machine Learning-webbtjänst som distribueras till ditt AKS-kluster.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Skapa och installera en Azure-identitet i ditt AKS-kluster

1. Använd följande kommando för att avgöra om ditt AKS-kluster är RBAC-aktiverat:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Det här kommandot returnerar värdet `true` om RBAC är aktiverat. Det här värdet anger kommandot som ska användas i nästa steg.

1. Använd något av följande kommandon för att installera [AAD Pod-identitet](https://github.com/Azure/aad-pod-identity#getting-started) i ditt AKS-kluster:

    * Om ditt AKS-kluster har **RBAC aktiverat** använder du följande kommando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Om AKS-klustret **inte har RBAC aktive rad** , använder du följande kommando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Kommandots utdata liknar följande text:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Skapa en Azure-identitet](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) enligt stegen som visas i AAD Pod Identity Project-sidan.

1. [Installera Azure-identiteten](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) enligt stegen som visas i AAD Pod Identity Project.

1. [Installera Azure Identity-bindningen](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) enligt stegen som visas i AAD Pod Identity Project.

1. Om Azure-identiteten som skapades i föregående steg inte finns i samma resurs grupp som ditt AKS-kluster följer du [Ange behörigheter för MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) enligt stegen som visas i AAD Pod Identity Project-sidan.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Tilldela Azure Identity till Machine Learning-webbtjänst

Följande steg använder den Azure-identitet som skapades i föregående avsnitt och tilldelar den till AKS-webbtjänsten via en **väljar-etikett**.

Börja med att identifiera namnet och namn området för distributionen i ditt AKS-kluster som du vill tilldela Azure-identiteten. Du kan hämta den här informationen genom att köra följande kommando. Namn områdena ska vara Azure Machine Learning arbets ytans namn och distributions namnet ska vara ditt slut punkts namn som det visas i portalen.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Lägg till etiketten för Azure Identity Selector i distributionen genom att redigera distributions specifikationen. Värdet väljaren ska vara det som du definierade i steg 5 i [Installera Azure Identity binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Redigera distributionen för att lägga till etiketten för Azure Identity Selector. Gå till följande avsnitt under `/spec/template/metadata/labels` . Du bör se värden som `isazuremlapp: “true”` . Lägg till etiketten AAD-Pod-Identity som visas nedan.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Kontrol lera att etiketten har lagts till korrekt genom att köra följande kommando.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Kör följande kommando för att se alla Pod-statusar.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

När poddar är igång kommer webb tjänsterna för den här distributionen nu att kunna komma åt Azure-resurser via din Azure-identitet utan att behöva bädda in autentiseringsuppgifterna i din kod. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Tilldela lämpliga roller till din Azure-identitet

[Tilldela din Azure-hanterade identitet med lämpliga roller](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för att få åtkomst till andra Azure-resurser. Se till att de roller som du tilldelar har rätt **data åtgärder**. Till exempel har [rollen Storage BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Läs behörighet till lagrings-bloben medan den allmänna [läsar rollen](../role-based-access-control/built-in-roles.md#reader) kanske inte är det.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Använd Azure Identity med din Machine Learning-webbtjänst

Distribuera en modell till ditt AKS-kluster. `score.py`Skriptet kan innehålla åtgärder som pekar på de Azure-resurser som din Azure-identitet har åtkomst till. Kontrol lera att du har installerat de klient biblioteks beroenden som krävs för resursen som du försöker få åtkomst till. Nedan visas några exempel på hur du kan använda din Azure-identitet för att få åtkomst till olika Azure-resurser från din tjänst.

### <a name="access-key-vault-from-your-web-service"></a>Åtkomst Key Vault från din webb tjänst

Om du har fått Läs behörighet för Azure Identity till en hemlighet i en **Key Vault** kan du `score.py` komma åt den med följande kod.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> I det här exemplet används DefaultAzureCredential. Om du vill ge din identitet åtkomst med hjälp av en speciell åtkomst princip läser du [tilldela en Key Vault åtkomst princip med hjälp av Azure CLI](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Få åtkomst till BLOB från webb tjänsten

Om du har fått Läs åtkomst till data i en **lagrings-BLOB** med Azure Identity `score.py` kan du komma åt den med följande kod.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du använder python-klient biblioteket för Azure Identity finns i [lagrings platsen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) på GitHub.
* En detaljerad guide om hur du distribuerar modeller till Azure Kubernetes service-kluster finns i [instruktionen How-to](how-to-deploy-azure-kubernetes-service.md).