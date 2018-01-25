---
title: "Distribuera till Azure-Behållarinstanser från Azure-behållaren registret"
description: "Lär dig hur du distribuerar behållare i Azure Container instanser som använder behållaren bilder i ett Azure Container registret."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/24/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c69b95f66bf2eaf4975961da5b25f5ac6172798c
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Distribuera till Azure-Behållarinstanser från Azure-behållaren registret

Azure Container registret är en Azure-baserade, privat registret för Docker behållare bilder. Den här artikeln beskriver hur du distribuerar behållare bilderna som lagras i registret för Azure-behållare till Behållarinstanser som Azure.

## <a name="deploy-with-azure-cli"></a>Distribuera med Azure CLI

Azure CLI innehåller kommandon för att skapa och hantera behållare i Azure Container instanser. Om du anger en privat bilden i den [az behållaren skapa] [ az-container-create] kommando, du kan också ange avbildningen registret lösenord som krävs för att autentisera med behållaren registret.

```azurecli-interactive
az container create --resource-group myResourceGroup --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword
```

Den [az behållaren skapa] [ az-container-create] kommandot stöder även att ange `--registry-login-server` och `--registry-username`. Inloggningsserver för Azure-behållare registret är dock alltid *registryname*. azurecr.io och Standardanvändarnamnet är *registryname*, så dessa värden är om den inte att härleda från avbildningens namn uttryckligen anges.

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

För att undvika att lagra lösenordet behållaren registret direkt i mallen, rekommenderar vi att du sparar den som en hemlighet i [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) och referera till den i mallen med hjälp av [integrering mellan Azure Resource Manager och Nyckelvalv](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Distribuera med Azure-portalen

Om du underhåller behållaren avbildningar i Azure Container registret kan du enkelt skapa en behållare i Azure Container instanser med Azure-portalen.

1. Navigera till behållaren registret i Azure-portalen.

1. Välj **databaser**, välj sedan databasen som du vill distribuera, högerklicka på taggen för behållaren-avbildning som du vill distribuera och välj **kör instans**.

    ![”Kör instans” i registret för Azure-behållaren i Azure-portalen][acr-runinstance-contextmenu]

1. Ange ett namn för behållaren och ett namn för resursgruppen. Du kan också ändra standardvärden om du vill.

    ![Skapa meny för Azure-Behållarinstanser][acr-create-deeplink]

1. När distributionen är klar kan du gå till behållargruppen från fönstret meddelanden för att hitta sin IP-adress och andra egenskaper.

    ![Detaljvyn för Azure-behållare instansgrupp behållare][aci-detailsview]

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Om användaren administratör för Azure-behållaren registernyckeln har inaktiverats kan du använda ett Azure Active Directory [tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md) att autentisera till registret när du skapar en instans i behållaren. Du bör också använda ett huvudnamn för tjänsten för autentisering i fjärradministrerade scenarier, till exempel ett skript eller program som skapar behållarinstanser i oövervakat.

Mer information finns i [autentisera med Azure Container registret från Azure Behållarinstanser](../container-registry/container-registry-auth-aci.md).

## <a name="next-steps"></a>Nästa steg

Lär dig att bygga behållare, push-installera dem till ett privat behållaren register och distribuera dem till Azure-Behållarinstanser av [igenom kursen](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create