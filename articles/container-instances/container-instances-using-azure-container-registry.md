---
title: Distribuera behållaravbildning från Azure Container Registry
description: Lär dig hur du distribuerar behållare i Azure Container Instances genom att dra behållaravbildningar från ett Azure-behållarregister.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453531"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure Container Instances från Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) är en Azure-baserad, hanterad behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. I den här artikeln beskrivs hur du hämtar behållaravbildningar som lagras i ett Azure-behållarregister när du distribuerar till Azure Container Instances. Ett rekommenderat sätt att konfigurera registeråtkomst är att skapa ett huvudnamn och lösenord för Azure Active Directory-tjänsten och lagra inloggningsuppgifterna i ett Azure-nyckelvalv.

## <a name="prerequisites"></a>Krav

**Azure-behållarregister:** Du behöver ett Azure-behållarregister - och minst en behållaravbildning i registret – för att slutföra stegen i den här artikeln. Om du behöver ett register läser du [Skapa ett behållarregister med Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Kommandoradsexempelna i den här artikeln använder [Azure CLI](/cli/azure/) och är formaterade för Bash-skalet. Du kan [installera Azure CLI](/cli/azure/install-azure-cli) lokalt eller använda Azure Cloud [Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurera registerautentisering

I ett produktionsscenario där du ger åtkomst till "huvudlösa" tjänster och program rekommenderas att registeråtkomst konfigureras med hjälp av ett [huvudnamn](../container-registry/container-registry-auth-service-principal.md)för tjänsten . Med ett tjänsthuvudnamn kan du tillhandahålla [rollbaserad åtkomstkontroll](../container-registry/container-registry-roles.md) till dina behållaravbildningar. Du kan till exempel konfigurera ett huvudnamn för tjänsten med enbart hämtningsåtkomst till ett register.

Azure Container Registry innehåller ytterligare [autentiseringsalternativ](../container-registry/container-registry-authentication.md).

> [!NOTE]
> Du kan inte autentisera till Azure Container Registry för att hämta avbildningar under distribution av behållargrupper med hjälp av en [hanterad identitet](container-instances-managed-identity.md) som konfigurerats i samma behållargrupp.

I följande avsnitt skapar du ett Azure-nyckelvalv och ett huvudnamn för tjänsten och lagrar tjänstens huvudnamnsautentiseringsuppgifter i valvet. 

### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv i [Azure Key Vault](../key-vault/general/overview.md), skapar du ett med Azure CLI och följande kommandon.

Uppdatera `RES_GROUP` variabeln med namnet på en befintlig resursgrupp där `ACR_NAME` nyckelvalvet ska skapas och med namnet på behållarregistret. För enkelhetens skull förutsätter kommandon i den här artikeln att register-, nyckelvalv och behållarinstanser alla skapas i samma resursgrupp.

 Ange ett namn på det `AKV_NAME`nya nyckelvalvet i . Valvets namn måste vara unikt i Azure och måste vara 3-24 alfanumeriska tecken i längd, börja med en bokstav, sluta med en bokstav eller siffra och kan inte innehålla på varandra följande bindestreck.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Nu skapa en tjänst huvudnamn och lagra sina referenser i din nyckel valv.

Följande kommando använder [az ad sp create-for-rbac för][az-ad-sp-create-for-rbac] att skapa tjänstens huvudnamn och az [keyvault hemlig uppsättning][az-keyvault-secret-set] för att lagra tjänstens huvudnamnslösenord i valvet. **password**

```azurecli
# Create service principal, store its password in vault (the registry *password*)
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

Därefter lagra tjänsthuvudhuvudets *appId* i valvet, vilket är **användarnamnet** som du skickar till Azure Container Registry för autentisering.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure-nyckelvalv och lagrat två hemligheter i det:

* `$ACR_NAME-pull-usr`: ID för tjänstens huvudnamn som ska användas som containerregistrets **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenord för tjänstens huvudnamn som ska användas som containerregistrets **lösenord**.

Nu kan du referera till dessa hemligheter efter namn när du eller dina program och tjänster hämtar avbildningar från registret.

## <a name="deploy-container-with-azure-cli"></a>Distribuera containrar med Azure CLI

Nu när tjänstens huvudautentiseringsuppgifter lagras i Azure Key Vault hemligheter, kan dina program och tjänster använda dem för att komma åt ditt privata register.

Först få registrets inloggningsserver namn med hjälp av [az acr visa][az-acr-show] kommandot. Inloggningsserverns namn är alla `myregistry.azurecr.io`gemener och liknar .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Kör kommandot [az container create][az-container-create] för att distribuera en behållarinstans. Kommandot använder tjänsthuvudmannens autentiseringsuppgifter som lagras i Azure Key Vault för att autentisera till ditt behållarregister och förutsätter att du tidigare har drivit [aci-helloworld-avbildningen](container-instances-quickstart.md) till registret. Uppdatera `--image` värdet om du vill använda en annan avbildning än registret.

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

Värdet `--dns-name-label` måste vara unikt i Azure, så föregående kommando lägger till ett slumptal till behållarens DNS-namnetikett. Utdata från kommandot visar containerns fullständiga domännamn (FQDN), till exempel:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

När behållaren har startats kan du navigera till dess FQDN i webbläsaren för att verifiera att programmet körs.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuera med Azure Resource Manager-mall

Du kan ange egenskaperna för ditt Azure-behållarregister i `imageRegistryCredentials` en Azure Resource Manager-mall genom att inkludera egenskapen i behållargruppdefinitionen. Du kan till exempel ange registerautentiseringsuppgifter direkt:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Fullständiga inställningar för behållargrupp finns i [resource manager-mallreferensen](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups).    

Mer information om hur du refererar till Azure Key Vault-hemligheter i en Resource Manager-mall finns i [Använda Azure Key Vault för att skicka säkert parametervärde under distributionen](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure-portal

Om du underhåller behållaravbildningar i ett Azure-behållarregister kan du enkelt skapa en behållare i Azure Container Instances med Azure-portalen. När du använder portalen för att distribuera en behållarinstans från ett behållarregister måste du aktivera registrets [administratörskonto](../container-registry/container-registry-authentication.md#admin-account). Administratörskontot är utformat för att en enskild användare ska komma åt registret, främst för testning. 

1. Navigera till behållarregistret i Azure-portalen.

1. Om du vill bekräfta att administratörskontot är aktiverat väljer du **Åtkomstnycklar**och väljer Aktivera under **Administratörsanvändare** . **Enable**

1. Välj **Databaser**och välj sedan den databas som du vill distribuera från, högerklicka på taggen för den behållaravbildning som du vill distribuera och välj **Kör förekomst**.

    !["Kör instans" i Azure Container Registry i Azure-portalen][acr-runinstance-contextmenu]

1. Ange ett namn på behållaren och ett namn för resursgruppen. Du kan också ändra standardvärdena om du vill.

    ![Skapa-menyn för Azure Container-instanser][acr-create-deeplink]

1. När distributionen är klar kan du navigera till behållargruppen från meddelandefönstret för att hitta dess IP-adress och andra egenskaper.

    ![Informationsvy för behållaregruppen för Azure-behållarinstanser][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Container Registry-autentisering finns [i Autentisera med ett Azure-behållarregister](../container-registry/container-registry-authentication.md).

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
