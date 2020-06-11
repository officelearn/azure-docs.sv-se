---
title: Integrera Azure Key Vault med Kubernetes
description: I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av hemligheter Store CSI (container Storage Interface) driv rutinen för att sedan montera i Kubernetes poddar.
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: 27d602f22aa3915f39f21ac924afa42b98e70720
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667170"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>Självstudie: Konfigurera och kör Azure Key Vault providern för Secret Store CSI-drivrutinen på Kubernetes

I den här självstudien får du åtkomst till och hämtar hemligheter från Azure Key Vault med hjälp av hemligheter Store CSI (container Storage Interface) driv rutinen för att sedan montera i Kubernetes poddar.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa ett huvud namn för tjänsten eller Använd hanterade identiteter
> * Distribuera ett Azure Kubernetes service-kluster med Azure CLI
> * Installera Helm och hemligheter Arkiv CSI driv rutin
> * Skapa en Azure Key Vault och ange hemligheter
> * Skapa ett eget SecretProviderClass-objekt
> * Tilldela tjänstens huvud namn eller Använd hanterade identiteter
> * Distribuera din POD med monterade hemligheter från Key Vault

## <a name="prerequisites"></a>Krav

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Innan du börjar den här självstudien installerar du [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Skapa ett huvud namn för tjänsten eller Använd hanterade identiteter

Om du planerar att använda hanterade identiteter kan du gå vidare till nästa avsnitt.

Skapa ett huvud namn för tjänsten för att kontrol lera vilka resurser som kan nås från din Azure Key Vault. Åtkomsten till tjänstens huvud namn begränsas av de roller som har tilldelats dem. Den här funktionen ger dig kontroll över hur tjänstens huvud namn kan hantera dina hemligheter. I exemplet nedan är namnet på tjänstens huvud namn **contosoServicePrincipal**.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Den här åtgärden returnerar en serie med nyckel/värde-par:

![Bild](../media/kubernetes-key-vault-1.png)

Kopiera det **AppID** och **lösen ordet**. Du behöver dessa autentiseringsuppgifter senare.



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>Distribuera ett Azure Kubernetes service-kluster med Azure CLI

Du behöver inte använda Azure Cloud Shell. kommando tolken (Terminal) med Azure CLI är installerad. 

Följ den här [guiden](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough) och Slutför följande avsnitt: **skapa en resurs grupp**, **skapa AKS-kluster**och **Anslut till klustret**.

**Obs:** Om du planerar att använda Pod identitet i stället för ett huvud namn för tjänsten. Se till att aktivera det när du skapar Kubernetes-klustret, enligt nedan:

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. [Ange miljövariabeln PATH](https://www.java.com/en/download/help/path.xml) till den kubectl.exe-fil som hämtades.
1. Kontrol lera Kubernetes-versionen med hjälp av kommandot nedan. Det här kommandot kommer att mata ut klienten och Server versionen. Klient versionen är "kubectl.exe" som du installerade när Server versionen är Azure Kubernetes-tjänster som klustret körs på.
    ```azurecli
    kubectl version
    ```
1. Se till att din Kubernetes-version är antingen **v-1.16.0** eller större. Med det här kommandot uppgraderas både Kubernetes-klustret och Node-poolen. Det kan ta ett par minuter att köra. I det här exemplet är resurs gruppen **contosoResourceGroup** och Kubernetes-klustret är **contosoAKSCluster**.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Visa metadata för det AKS-kluster som du har skapat med hjälp av kommandot nedan. Kopiera nedåt i **principalId**, **clientId**, **subscriptionId**och **nodeResourceGroup**.
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    Detta är utdata med båda parametrarna markerade.
    
    ![Avbildnings ](../media/kubernetes-key-vault-2.png) ![ bild](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>Installera Helm och hemligheter Arkiv CSI driv rutin

Du måste installera [Helm](https://helm.sh/docs/intro/install/) för att installera hemligheter för att lagra CSI-drivrutinen.

Med [nyckeln hemligheter Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) -drivrutinen kan du hämta hemliga innehåll som lagras i en Azure Key Vault instans och använda driv rutins gränssnittet för att montera dessa hemliga innehåll i Kubernetes poddar.

1. Kontrol lera Helm-versionen och se till att den är v3 eller större:
    ```azurecli
    helm version
    ```
1. Installera driv rutinen för hemligheter Store CSI och Azure Key Vault leverantören för driv rutinen:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>Skapa en Azure Key Vault och ange hemligheter

Följ den här [guiden](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli) för att skapa en egen Key Vault och ange dina hemligheter.

**Obs:** Du behöver inte använda Azure Cloud Shell eller skapa en ny resurs grupp. Det går bra att använda resurs gruppen som skapades tidigare för Kubernetes-klustret.

## <a name="create-your-own-secretproviderclass-object"></a>Skapa ett eget SecretProviderClass-objekt

Använd den här [mallen](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml) för att skapa ett eget anpassat SecretProviderClass-objekt för att tillhandahålla providerspecifika parametrar för hemligheter från CSI-drivrutinen. Det här objektet ger identitets åtkomst till din Key Vault.

Med hjälp av den angivna SecretProviderClass YAML-filen. Du kommer att fylla i de parametrar som saknas. Följande parametrar måste anges:

1.  **userAssignedIdentityID:** Klient-ID för tjänstens huvud namn
1.  **keyvaultName:** Namn på Key Vault
1.  **objekt:** Det här objektet kommer att innehålla allt hemligt innehåll som du vill montera
    1.  **objectName:** Namn på hemligt innehåll
    1.  **objectType:** Objekt typ (hemlighet, nyckel, certifikat)
1.  **resourceGroup:** Resurs gruppens namn
1.  **subscriptionId:** Key Vaultens prenumerations-ID
1.  **tenantID:** Klient-ID (det vill säga katalog-ID) för Key Vault

Nedan finns den uppdaterade mallen, ladda ned den som en. yaml-fil och fyll i motsvarande obligatoriska fält. I det här exemplet är Key Vault **contosoKeyVault5** och har två hemligheter, **secret1** och **secret2**.

**Obs:** Om du använder hanterade identiteter måste fältet **usePodIdentity** vara **Sant** och lämna fältet **userAssignedIdentityID** med bara citat tecken **""**. 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
Nedan visas konsolens utdata för "AZ-contosoKeyVault5" show--name "med relevanta markerade metadata:

![Bild](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Tilldela tjänstens huvud namn eller Använd hanterade identiteter

### <a name="using-service-principal"></a>Använda tjänstens huvud namn

Om du använder ett huvud namn för tjänsten. Du måste ge behörighet till tjänstens huvud namn för att få åtkomst till din Key Vault och hämta hemligheter. Tilldela rollen **"läsare"** och ge tjänstens huvud namn behörighet att **"Hämta"** -hemligheter från din Key Vault genom att följa stegen nedan:

1. Tilldela tjänstens huvud namn till befintliga Key Vault. Parametern **$AZURE _CLIENT_ID** är det **appId** som du kopierade när du skapade tjänstens huvud namn.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    Nedan visas utdata för kommandot: 

    ![Bild](../media/kubernetes-key-vault-5.png)

1. Ge tjänstens huvud namn behörighet att hämta hemligheter:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Nu när du har konfigurerat tjänstens huvud namn så att det har behörighet att läsa hemligheter från din Key Vault. **$AZURE _CLIENT_SECRET** är **lösen ordet** för tjänstens huvud namn. Lägg till dina autentiseringsuppgifter för tjänstens huvud namn som Kubernetes-hemlighet som är tillgänglig för hemligheter från CSI-drivrutinen:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**Obs:** Om du får ett fel senare när du distribuerar Kubernetes-Pod om ett ogiltigt ID för klient hemlighet. Du kan ha ett äldre ID för klient hemlighet som har upphört att gälla eller återställts. Lös problemet genom att ta bort hemligheterna "hemligheter-Store-creds" och skapa ett nytt med det aktuella klient hemlighets-ID: t. Kör kommandot nedan för att ta bort "hemligheter-Store-creds":
```azurecli
kubectl delete secrets secrets-store-creds
```

Om du har glömt ditt tjänste huvud namn för klient hemligheten kan du återställa det med hjälp av följande kommando:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>Använda hanterade identiteter

Om du använder hanterade identiteter tilldelar du vissa roller till det AKS-kluster som du har skapat. 
1. För att skapa/lista/läsa en användardefinierad hanterad identitet måste ditt AKS-kluster tilldelas rollen som [hanterad identitet deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor) . Kontrol lera att **$clientId** är Kubernetes-klustrets.

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installera Azure Active Directory identitet (AAD) i AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Skapa en AAD-identitet. Kopiera nedåt i **clientId** och **principalId**.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Tilldela rollen läsare till AAD-identiteten som du precis har skapat för din Key Vault. Ge sedan identiteten behörighet att hämta hemligheter från din Key Vault. Du kommer att använda **clientId** och **PrincipalId** från den Azure-identitet som du nyss skapade.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>Distribuera din POD med monterade hemligheter från Key Vault

Kommandot nedan konfigurerar SecretProviderClass-objektet:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>Använda tjänstens huvud namn

Om du använder ett huvud namn för tjänsten. Kommandot nedan distribuerar din Kubernetes-poddar med SecretProviderClass och de hemligheter-lagrar-autentiseringsuppgifter som du har konfigurerat. Här är mallen för [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) -och [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) -distribution.
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>Använda hanterade identiteter

Om du använder hanterade identiteter. Du ska skapa en **AzureIdentity** i klustret som refererar till den identitet som du har skapat tidigare. Skapa sedan en **AzureIdentityBinding** som refererar till den **AzureIdentity** som du skapade. Använd mallen nedan och fyll i motsvarande parametrar och spara den som **podIdentityAndBinding. yaml**.  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
Kör följande kommando för att köra bindningen:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Härnäst är den faktiska distributionen av pod. Nedan visas den YAML-fil för distribution som använder Pod-identitets bindningen från det sista steget. Spara filen som **podBindingDeployment. yaml**.

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

Kör följande kommando för att distribuera din POD:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>Kontrol lera status och hemligt innehåll 
Så här visar du de poddar som du har distribuerat:
```azurecli
kubectl get pods
```

Använd följande kommando för att kontrol lera status för din POD:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Bild](../media/kubernetes-key-vault-6.png)

Den distribuerade Pod bör vara i läget "körs". I avsnittet "händelser" längst ned klassificeras alla typer av händelser till vänster som "normal".
När du har verifierat att Pod körs, kan du kontrol lera att din POD har hemligheter från din Key Vault.

Visa alla hemligheter som Pod har:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Så här hämtar du innehåll från en speciell hemlighet:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifiera innehållet i hemligheten som visas.

## <a name="next-steps"></a>Nästa steg

Kontrol lera att Key Vault går att återskapa:
> [!div class="nextstepaction"]
> [Aktivera mjuk borttagning](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
