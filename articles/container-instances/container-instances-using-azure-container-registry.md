---
title: Distribuera till Azure Container Instances från Azure Container Registry
description: Lär dig hur du distribuerar behållare i Azure Container Instances via behållaravbildningar i ett Azure container registry.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b596af8ae9fbbaee6964622df44d316a11582cb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686155"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure Container Instances från Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) är en Azure-baserat och hanterade container registry-tjänst som används för att lagra privata Docker-behållaravbildningar. Den här artikeln beskriver hur du distribuerar behållaravbildningar lagras i ett Azure container registry till Azure Container Instances.

## <a name="prerequisites"></a>Nödvändiga komponenter

**Azure-behållarregister**: Du behöver en Azure-behållarregister – och minst en behållaravbildning i registret – för att slutföra stegen i den här artikeln. Om du behöver ett register, se [skapa ett behållarregister med hjälp av Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Exemplen i den här artikeln i [Azure CLI](/cli/azure/) och formateras för Bash-gränssnittet. Du kan [installera Azure CLI](/cli/azure/install-azure-cli) lokalt, eller Använd den [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

I alla scenarier för produktion, åtkomst till ett Azure container registry måste anges med hjälp av [tjänsthuvudnamn](../container-registry/container-registry-auth-service-principal.md). Tjänstens huvudnamn kan du ge [rollbaserad åtkomstkontroll](../container-registry/container-registry-roles.md) till dina behållaravbildningar. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

I följande avsnitt, skapa ett Azure key vault och ett huvudnamn för tjänsten och lagra autentiseringsuppgifter för tjänstens huvudnamn i valvet. 

### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](../key-vault/key-vault-overview.md), skapar du ett med Azure CLI och följande kommandon.

Uppdatera den `RES_GROUP` variabeln med namnet på en befintlig resursgrupp där du vill skapa key vault och `ACR_NAME` med namnet på ditt behållarregister. Ange ett namn för ett nytt nyckelvalv i `AKV_NAME`. Valvnamnet måste vara unikt i Azure och måste vara 3-24 alfanumeriska tecken långt, börja med en bokstav, sluta med en bokstav eller siffra, och får inte innehålla bindestreck.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Du måste nu skapa ett huvudnamn för tjänsten och lagra dess autentiseringsuppgifter i nyckelvalvet.

Följande kommando använder [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] att skapa tjänstens huvudnamn, och [az keyvault secret set] [ az-keyvault-secret-set] att lagra den tjänstens huvudnamn **lösenord** i valvet.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argumentet `--role` i föregående kommando konfigurerar huvudnamnet för tjänsten med rollen *acrpull*, vilket endast ger den hämtningsåtkomst till registret. Om du vill bevilja både sändnings- och hämtningsåtkomst ändrar du argumentet `--role` till *acrpush*.

Nu ska lagra tjänstens huvudnamn *appId* i valvet, vilket är den **användarnamn** du skickar till Azure Container Registry för autentisering.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure Key Vault och lagrat två hemligheter i det:

* `$ACR_NAME-pull-usr`: ID för tjänstens huvudnamn som ska användas som containerregistrets **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenord för tjänstens huvudnamn som ska användas som containerregistrets **lösenord**.

Nu kan du referera till dessa hemligheter efter namn när du eller dina program och tjänster hämtar avbildningar från registret.

## <a name="deploy-container-with-azure-cli"></a>Distribuera containrar med Azure CLI

Nu när autentiseringsuppgifter för tjänstens huvudnamn är lagrade i Azure Key Vault-hemligheter, kan dina program och tjänster använda dem för att få åtkomst till ditt privata register.

Först få namnet på inloggningsservern för registret med hjälp av den [az acr show] [ az-acr-show] kommando. Namnet på inloggningsservern är alla gemener och liknar `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Kör kommandot [az container create][az-container-create] för att distribuera en behållarinstans. Kommandot använder autentiseringsuppgifter för tjänstens huvudnamn i Azure Key Vault för att autentisera till behållarregistret och förutsätter att du tidigare har push-överfört den [aci-helloworld](container-instances-quickstart.md) avbildningen till registret. Uppdatera den `--image` värde om du vill använda en annan avbildning från ditt register.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Den `--dns-name-label` värdet måste vara unikt i Azure, så föregående kommando lägger till ett slumptal till behållarens DNS-namnetikett. Utdata från kommandot visar containerns fullständiga domännamn (FQDN), till exempel:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

När behållaren har startat, kan du navigera till dess FQDN i webbläsaren för att kontrollera programmet körts.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuera med Azure Resource Manager-mall

Du kan ange egenskaperna för ditt Azure-Behållarregister i en Azure Resource Manager-mall genom att inkludera den `imageRegistryCredentials` -egenskapen i definitionen för behållargruppen:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Mer information för att hänvisa till Azure Key Vault-hemligheter i Resource Manager-mall finns i [använda Azure Key Vault för att skicka säkra parametervärdet under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure-portalen

Om du behåller behållaravbildningar i ett Azure container registry kan du enkelt skapa en behållare i Azure Container Instances via Azure-portalen. När du använder portalen för att distribuera en behållarinstans från ett behållarregister, måste du aktivera registrets [administratörskonto](../container-registry/container-registry-authentication.md#admin-account). Administratörskontot som har utformats för en enskild användare åtkomst till registret, främst i testsyfte. 

1. Gå till ditt behållarregister i Azure-portalen.

1. För att bekräfta att administratörskontot som är aktiverad, Välj **åtkomstnycklar**, och under **administratörsanvändare** Välj **aktivera**.

1. Välj **databaser**, och välj sedan databasen som du vill distribuera från, högerklicka på taggen för den behållaravbildning som du vill distribuera och välj **kör instans**.

    ![”Kör instans” i Azure Container Registry på Azure-portalen][acr-runinstance-contextmenu]

1. Ange ett namn för behållaren och ett namn för resursgruppen. Du kan också ändra standardinställningarna om du vill.

    ![Skapa meny för Azure Container Instances][acr-create-deeplink]

1. När distributionen är klar kan du navigera till behållargruppen via meddelandefönstret för att hitta dess IP-adress och andra egenskaper.

    ![Detaljer för Azure Container Instances-behållargrupp][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Container Registry authentication [autentisera med ett Azure container registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
