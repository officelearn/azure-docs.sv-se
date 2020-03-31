---
title: Använda AAD-identitet med din webbtjänst
titleSuffix: Azure Machine Learning
description: Använd AAD-identitet med din webbtjänst i Azure Kubernetes-tjänsten för att komma åt molnresurser under bedömning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122849"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Använda Azure AD-identitet med din machine learning-webbtjänst i Azure Kubernetes Service

I det här indataprogrammet får du lära dig hur du tilldelar en Azure Active Directory-identitet (AAD) till din distribuerade maskininlärningsmodell i Azure Kubernetes Service. [AAD Pod Identity-projektet](https://github.com/Azure/aad-pod-identity) gör det möjligt för program att komma åt molnresurser på ett säkert sätt med AAD med hjälp av primitiver [för hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) och Kubernetes. På så sätt kan webbtjänsten komma åt dina Azure-resurser på ett `score.py` säkert sätt utan att behöva bädda in autentiseringsuppgifter eller hantera token direkt i skriptet. I den här artikeln beskrivs stegen för att skapa och installera en Azure Identity i azure Kubernetes-tjänstklustret och tilldela identiteten till din distribuerade webbtjänst.

## <a name="prerequisites"></a>Krav

- [Azure CLI-tillägget för machine learning-tjänsten](reference-azure-machine-learning-cli.md), [Azure Machine Learning SDK för Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller Azure Machine Learning Visual Studio [Code-tillägget](tutorial-setup-vscode-extension.md).

- Åtkomst till AKS-klustret med `kubectl` kommandot. Mer information finns i [Ansluta till klustret](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- En Azure Machine Learning-webbtjänst som distribueras till AKS-klustret.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Skapa och installera en Azure Identity i AKS-klustret

1. Ta reda på om AKS-klustret är RBAC-aktiverat använder du följande kommando:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Det här kommandot `true` returnerar ett värde för om RBAC är aktiverat. Det här värdet bestämmer vilket kommando som ska användas i nästa steg.

1. Om du vill installera [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) i AKS-klustret använder du något av följande kommandon:

    * Om AKS-klustret har **RBAC aktiverat** använder du följande kommando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Om AKS-klustret **inte har RBAC aktiverat**använder du följande kommando:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        Utdata för kommandot liknar följande text:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Skapa en Azure Identity](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) enligt stegen som visas på projektsidan för AAD Pod Identity.

1. [Installera Azure Identity](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) enligt stegen som visas på projektsidan för AAD Pod Identity.

1. [Installera Azure Identity Binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) enligt stegen som visas på projektsidan AAD Pod Identity.

1. Om Azure Identity som skapades i föregående steg inte finns i samma resursgrupp som AKS-klustret följer [du Ange behörigheter för MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) enligt stegen som visas på projektsidan för AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Tilldela Azure Identity till webbkameratjänsten För maskininlärning

I följande steg används den Azure Identity som skapats i föregående avsnitt och tilldela den till din AKS-webbtjänst via en **väljaretikett**.

Identifiera först namnet och namnområdet för distributionen i AKS-klustret som du vill tilldela Azure Identity. Du kan hämta den här informationen genom att köra följande kommando. Namnområdena ska vara ditt Azure Machine Learning-arbetsytenamn och ditt distributionsnamn ska vara ditt slutpunktsnamn som visas i portalen.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Lägg till Azure Identity-väljareniketten i distributionen genom att redigera distributionsspecifikationen. Väljarvärdet ska vara det som du definierade i steg 5 i [Installera Azure Identity Binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding).

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Redigera distributionen för att lägga till etiketten Azure Identity-väljare. Gå till följande `/spec/template/metadata/labels`avsnitt under . Du bör se `isazuremlapp: “true”`värden som . Lägg till aad-pod-identitetsetiketten som visas nedan.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Om du vill kontrollera att etiketten har lagts till korrekt kör du följande kommando.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Kör följande kommando om du vill visa alla pod-statusar.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

När poddarna är igång kan webbtjänsterna för den här distributionen nu komma åt Azure-resurser via din Azure Identity utan att behöva bädda in autentiseringsuppgifterna i koden. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Tilldela lämpliga roller till din Azure Identity

[Tilldela din Azure Managed Identity med lämpliga roller](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) för att komma åt andra Azure-resurser. Kontrollera att de roller du tilldelar har rätt **dataåtgärder**. [Rollen Lagringsblobbdataläsare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) har till exempel läsbehörighet till din Storage Blob medan den allmänna [läsrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) kanske inte är det.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Använda Azure Identity med din machine learning-webbtjänst

Distribuera en modell till AKS-klustret. Skriptet `score.py` kan innehålla åtgärder som pekar på De Azure-resurser som din Azure Identity har åtkomst till. Kontrollera att du har installerat de nödvändiga klientbiblioteksberoendena för den resurs som du försöker komma åt. Nedan följer några exempel på hur du kan använda din Azure Identity för att komma åt olika Azure-resurser från din tjänst.

### <a name="access-key-vault-from-your-web-service"></a>Få tillgång till Key Vault från din webbtjänst

Om du har gett din Azure Identity läsbehörighet till en hemlighet i ett **Key Vault**kan du `score.py` komma åt den med hjälp av följande kod.

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

### <a name="access-blob-from-your-web-service"></a>Få åtkomst till Blob från webbtjänsten

Om du har gett din Azure Identity läsbehörighet till data i en **Storage Blob**kan du `score.py` komma åt den med hjälp av följande kod.

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

* Mer information om hur du använder Python Azure Identity-klientbiblioteket finns i [databasen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) på GitHub.
* En detaljerad guide om hur du distribuerar modeller till Azure Kubernetes [Service-kluster](how-to-deploy-azure-kubernetes-service.md)finns i instruktioner .