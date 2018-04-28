---
title: Distribuera till Azure-Behållarinstanser från Azure-behållaren registret
description: Lär dig hur du distribuerar behållare i Azure-Behållarinstanser som använder behållaren bilder i registret för en Azure-behållaren.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7a7d2aa61f25bc4782c6a1a6744e329935477f8c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure-Behållarinstanser från Azure-behållaren registret

Azure Container registret är en Azure-baserade, privat bilderna Docker-behållaren i registret. Den här artikeln beskriver hur du distribuerar behållare bilderna som lagras i en Azure-behållaren registret till Behållarinstanser som Azure.

## <a name="prerequisites"></a>Förutsättningar

**Azure Container registret**: du behöver ett Azure-behållaren registret-- och minst en behållare bilden i registret--för att slutföra stegen i den här artikeln. Om du behöver ett register finns [skapa en behållare registret med hjälp av Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: exemplen i den här artikeln används den [Azure CLI](/cli/azure/) och formateras för Bash-gränssnitt. Du kan [installerar Azure CLI](/cli/azure/install-azure-cli) lokalt, eller använda den [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurera registret autentisering

I någon form av produktionsscenario, åtkomst till en Azure-behållaren registret ska tillhandahållas med hjälp av [tjänsten säkerhetsobjekt](../container-registry/container-registry-auth-service-principal.md). Tjänstens huvudnamn kan du ge rollbaserad åtkomstkontroll till bilderna behållare. Du kan till exempel konfigurera ett huvudnamn för tjänsten med pull åtkomst till ett register.

I det här avsnittet, skapa ett Azure key vault och ett huvudnamn för tjänsten och lagra autentiseringsuppgifter för tjänstens huvudnamn i valvet.

### <a name="create-key-vault"></a>Skapa nyckelvalv

Om du inte redan har ett valv [Azure Key Vault](/azure/key-vault/), skapa ett med Azure CLI med hjälp av följande kommandon.

Uppdatering av `RES_GROUP` variabeln med namnet på resursgruppen där du vill skapa nyckelvalvet, och `ACR_NAME` med namnet på behållaren registret. Ange ett namn för din nya nyckelvalvet i `AKV_NAME`. Valvnamnet måste vara unikt i Azure och måste bestå av 3 till 24 alfanumeriska tecken långt, börja med en bokstav, sluta med en bokstav eller siffra, och får inte innehålla bindestreck efter varandra.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Skapa tjänstens huvudnamn och lagra autentiseringsuppgifter

Du måste nu skapa ett huvudnamn för tjänsten och lagra sina autentiseringsuppgifter i nyckelvalvet.

Följande kommando använder [az ad sp skapa-för-rbac] [ az-ad-sp-create-for-rbac] att skapa tjänstens huvudnamn och [az keyvault hemlig ställa] [ az-keyvault-secret-set] att lagra den tjänstens huvudnamn **lösenord** i valvet.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

Den `--role` argumentet i föregående kommando konfigurerar huvudnamn för tjänsten med den *reader* vilket ger den pull åtkomst till registret. Om du vill bevilja åtkomst till både push och pull åtkomst måste du ändra den `--role` argumentet för *deltagare*.

Därefter lagra tjänstens huvudnamn *appId* i valvet, vilket är den **användarnamn** du skickar till Azure-behållare registret för autentisering.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Du har skapat ett Azure Key Vault och lagras två hemligheter i den:

* `$ACR_NAME-pull-usr`: Tjänsten ägar-ID, som ska användas som behållare registret **användarnamn**.
* `$ACR_NAME-pull-pwd`: Lösenordet för tjänstens huvudnamn, som ska användas som behållare registret **lösenord**.

Nu kan du referera dessa hemligheter efter namn när du eller ditt program och tjänster pull-avbildningar från registret.

## <a name="deploy-container-with-azure-cli"></a>Distribuera behållare med Azure CLI

Nu när tjänstens huvudnamn autentiseringsuppgifterna lagras i Azure Key Vault hemligheter, kan dina program och tjänster använda dem åtkomst till privata registret.

Kör följande [az behållaren skapa] [ az-container-create] kommando för att distribuera en instans i behållaren. Kommandot använder tjänstens huvudnamn autentiseringsuppgifter lagras i Azure Key Vault för att autentisera till registret behållare och förutsätter att du har tidigare pushas den [aci helloworld](container-instances-quickstart.md) avbildningen till registret. Uppdatering av `--image` värde om du vill använda en annan bild från registret.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Den `--dns-name-label` värdet måste vara unikt i Azure, så föregående kommando lägger till ett slumptal till behållarens DNS-namnetikett. Utdata från kommandot visar behållarens fullständigt kvalificerade domännamnet (FQDN), till exempel:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

När behållaren har startats, kan du navigera till dess FQDN i webbläsaren och kontrollera programmet körts.

## <a name="deploy-with-azure-resource-manager-template"></a>Distribuera med Azure Resource Manager-mall

Du kan ange egenskaper för Azure-behållare registret i en Azure Resource Manager-mall genom att inkludera den `imageRegistryCredentials` egenskap i Gruppdefinition behållare:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Mer information om refererar till Azure Key Vault hemligheter i en Resource Manager-mall finns [Använd Azure Key Vault för att skicka säkra parametervärdet under distributionen av](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure-portalen

Om du underhåller behållaren avbildningar i Azure Container registret kan du enkelt skapa en behållare i Azure Container instanser med Azure-portalen.

1. Navigera till behållaren registret i Azure-portalen.

1. Välj **databaser**, välj sedan databasen som du vill distribuera, högerklicka på taggen för behållaren-avbildning som du vill distribuera och välj **kör instans**.

    ![”Kör instans” i registret för Azure-behållaren i Azure-portalen][acr-runinstance-contextmenu]

1. Ange ett namn för behållaren och ett namn för resursgruppen. Du kan också ändra standardvärden om du vill.

    ![Skapa meny för Azure-Behållarinstanser][acr-create-deeplink]

1. När distributionen är klar kan du gå till behållargruppen från fönstret meddelanden för att hitta sin IP-adress och andra egenskaper.

    ![Detaljvyn för Azure-behållare instansgrupp behållare][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure-behållare registret autentisering [autentisera med en Azure-behållaren registret](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set