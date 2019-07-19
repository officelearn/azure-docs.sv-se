---
title: Distribuera till Azure Container Instances från Azure Container Registry
description: Lär dig hur du distribuerar behållare i Azure Container Instances att använda behållar avbildningar i ett Azure Container Registry.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 502f178b66e7ba233552d7db4e095363c8bb8628
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325567"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure Container Instances från Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) är en Azure-baserad, hanterad behållare register tjänst som används för att lagra privata Docker-behållar avbildningar. Den här artikeln beskriver hur du distribuerar behållar avbildningar som lagras i ett Azure Container Registry till Azure Container Instances.

## <a name="prerequisites"></a>Förutsättningar

**Azure Container Registry**: Du behöver ett Azure Container Registry – och minst en behållar avbildning i registret – för att slutföra stegen i den här artikeln. Om du behöver ett register kan du läsa [skapa ett behållar register med hjälp av Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Kommando rads exemplen i den här artikeln använder [Azure CLI](/cli/azure/) och är formaterade för bash-gränssnittet. Du kan [Installera Azure CLI](/cli/azure/install-azure-cli) lokalt eller använda [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

I alla produktions scenarier bör åtkomst till ett Azure Container Registry tillhandahållas med hjälp av [tjänstens huvud namn](../container-registry/container-registry-auth-service-principal.md). Med tjänstens huvud namn kan du tillhandahålla [rollbaserad åtkomst kontroll](../container-registry/container-registry-roles.md) till behållar avbildningarna. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

I följande avsnitt skapar du ett Azure Key Vault och ett huvud namn för tjänsten och lagrar autentiseringsuppgifterna för tjänstens huvud namn i valvet. 

### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](../key-vault/key-vault-overview.md), skapar du ett med Azure CLI och följande kommandon.

Uppdatera variabeln med namnet på en befintlig resurs grupp där nyckel valvet ska skapas och `ACR_NAME` med namnet på behållar registret. `RES_GROUP` Ange ett namn för ditt nya nyckel valv i `AKV_NAME`. Valv namnet måste vara unikt inom Azure och måste innehålla 3-24 alfanumeriska tecken, börja med en bokstav, sluta med en bokstav eller en siffra och får inte innehålla flera bindestreck.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Du måste nu skapa ett huvudnamn för tjänsten och lagra dess autentiseringsuppgifter i nyckelvalvet.

Följande kommando använder [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] to create the service principal, and [az keyvault secret set][az-keyvault-secret-set] för att lagra **lösen ordet** för tjänstens huvud namn i valvet.

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

Sedan lagrar du tjänstens huvud namn *i* valvet, vilket är det **användar namn** som du skickar till Azure Container Registry för autentisering.

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

Nu när autentiseringsuppgifterna för tjänstens huvud namn lagras i Azure Key Vault hemligheter kan dina program och tjänster använda dem för att få åtkomst till ditt privata register.

Hämta först registrets inloggnings Server namn genom att använda kommandot [AZ ACR show][az-acr-show] . Inloggnings serverns namn är bara gemener och `myregistry.azurecr.io`liknar.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Kör följande kommando för [AZ container Create][az-container-create] för att distribuera en behållar instans. Kommandot använder tjänstens huvud namn som lagras i Azure Key Vault för att autentisera till behållar registret och förutsätter att du tidigare har skickat avbildningen [ACI-HelloWorld](container-instances-quickstart.md) till registret. `--image` Uppdatera värdet om du vill använda en annan avbildning från registret.

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

`--dns-name-label` Värdet måste vara unikt i Azure, så att föregående kommando lägger till ett slumptal i behållarens DNS-namn etikett. Utdata från kommandot visar containerns fullständiga domännamn (FQDN), till exempel:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

När behållaren har startats kan du navigera till dess FQDN i webbläsaren för att kontrol lera att programmet körs utan problem.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuera med Azure Resource Manager mall

Du kan ange egenskaperna för Azure Container Registry i en Azure Resource Manager-mall genom att `imageRegistryCredentials` inkludera egenskapen i behållar grupps definitionen:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Mer information om hur du refererar Azure Key Vault hemligheter i en Resource Manager-mall finns i [använda Azure Key Vault för att skicka säker parameter värde under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure Portal

Om du underhåller behållar avbildningar i ett Azure Container Registry kan du enkelt skapa en behållare i Azure Container Instances att använda Azure Portal. När du använder portalen för att distribuera en behållar instans från ett behållar register måste du aktivera registrets [administratörs konto](../container-registry/container-registry-authentication.md#admin-account). Administratörs kontot har utformats för att en enskild användare ska kunna komma åt registret, främst i testnings syfte. 

1. I Azure Portal navigerar du till behållar registret.

1. Bekräfta att administratörs kontot är aktiverat genom att välja **åtkomst nycklar**och välj **Aktivera**under **Administratörs användare** .

1. Välj **databaser**, Välj den lagrings plats som du vill distribuera från, högerklicka på taggen för den behållar avbildning som du vill distribuera och välj **Kör instans**.

    !["Kör instans" i Azure Container Registry i Azure Portal][acr-runinstance-contextmenu]

1. Ange ett namn på behållaren och ett namn för resurs gruppen. Du kan också ändra standardvärdena om du vill.

    ![Skapa meny för Azure Container Instances][acr-create-deeplink]

1. När distributionen är klar kan du navigera till behållar gruppen från fönstret meddelanden för att hitta dess IP-adress och andra egenskaper.

    ![Detaljvy för Azure Container Instances behållar grupp][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Container Registry-autentisering finns i [autentisera med ett Azure Container Registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
