---
title: "Distribuera till Azure-Behållarinstanser från Azure-behållaren registret | Azure-dokument"
description: "Distribuera till Azure-Behållarinstanser från registret Azure-behållaren"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Distribuera till Azure-Behållarinstanser från registret Azure-behållaren

Azure Container registret är en Azure-baserade, privat registret för Docker behållare bilder. Den här artikeln beskriver hur du distribuerar behållare bilderna som lagras i registret för Azure-behållare till Behållarinstanser som Azure.

## <a name="using-the-azure-cli"></a>Använda Azure CLI

Azure CLI innehåller kommandon för att skapa och hantera behållare i Azure Container instanser. Om du anger en privat bilden i den `create` kommando, du kan också ange avbildningen registret lösenord som krävs för att autentisera med behållaren registret.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

Den `create` kommandot stöder även att ange den `registry-login-server` och `registry-username`. Inloggningsserver för Azure-behållare registret är dock alltid *registryname*. azurecr.io och Standardanvändarnamnet är *registryname*, så dessa värden är om den inte att härleda från avbildningens namn uttryckligen anges.

## <a name="using-an-azure-resource-manager-template"></a>Med en Azure Resource Manager-mall

Du kan ange egenskaper för Azure-behållare registret i en Azure Resource Manager-mall genom att inkludera den `imageRegistryCredentials` egenskap i Gruppdefinition behållare:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

För att undvika att lagra lösenordet behållaren registret direkt i mallen, rekommenderar vi att du sparar den som en hemlighet i [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) och referera till den i mallen med hjälp av [integrering mellan Azure Resource Manager och Nyckelvalv](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du underhåller behållaren avbildningar i Azure Container registret kan du enkelt skapa en behållare i Azure Container instanser med Azure-portalen.

1. Navigera till behållaren registret i Azure-portalen.

2. Välj databaser.

    ![Azure Container register-menyn i Azure-portalen][acr-menu]

3. Välj databasen som du vill distribuera från.

4. Högerklicka på taggen för den behållare bilden som du vill distribuera.

    ![Snabbmenyn för att starta behållare med Azure Container instanser][acr-runinstance-contextmenu]

5. Ange ett namn för behållaren och ett namn för resursgruppen. Du kan också ändra standardvärden om du vill.

    ![Skapa meny för Azure-Behållarinstanser][acr-create-deeplink]

6. När distributionen är klar kan du gå till behållargruppen från fönstret meddelanden för att hitta sin IP-adress och andra egenskaper.

    ![Detaljvyn för Azure-behållare instansgrupp behållare][aci-detailsview]

## <a name="next-steps"></a>Nästa steg

Lär dig att bygga behållare, push-installera dem till ett privat behållaren register och distribuera dem till Azure-Behållarinstanser av [igenom kursen](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
