---
title: Snabb start – skapa geo-replikerad register – Resource Manager-mall
description: Lär dig hur du skapar ett geo-replikerat Azure Container Registry med hjälp av en Azure Resource Manager-mall.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: c94cd3b4b455691e85e7525007fcdf7a056a2b28
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558082"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-a-resource-manager-template"></a>Snabb start: skapa ett geo-replikerat behållar register med hjälp av en Resource Manager-mall

Den här snabb starten visar hur du skapar en Azure Container Registry-instans med hjälp av en Azure Resource Manager mall. Mallen konfigurerar ett [geo-replikerat](container-registry-geo-replication.md) register, som automatiskt synkroniserar register innehåll i mer än en Azure-region. Geo-replikering ger nätverks nära åtkomst till bilder från regionala distributioner, samtidigt som en enda hanterings upplevelse tillhandahålls. Det är en funktion i tjänst nivån [Premium](container-registry-skus.md) Registry. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="create-a-geo-replicated-registry"></a>Skapa ett geo-replikerat register

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Mallen konfigurerar ett register och en ytterligare regional replik.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json" range="1-81" highlight="45-74" :::

Följande resurser definieras i mallen:

* **[Microsoft. ContainerRegistry/register](/azure/templates/microsoft.containerregistry/registries)**: skapa ett Azure Container Registry
* **[Microsoft. ContainerRegistry/register/replikeringar](/azure/templates/microsoft.containerregistry/registries/replications)**: skapa en behållare register replik

Du hittar fler Azure Container Registry mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuera mallen

 1. Välj följande bild för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Välj eller ange följande värden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och välj sedan **OK**.
    * **Plats**: Välj en plats för resursgruppen. Exempel: **centrala USA**.
    * **ACR namn**: acceptera det genererade namnet för registret eller ange ett namn. Det måste vara globalt unikt.
    * **Plats**: acceptera den genererade platsen för registrets hem replik eller ange en plats som t. ex. **Central USA**. 
    * **ACR-replik plats**: Ange en plats för register repliken med regionens korta namn. Det måste skilja sig från start register platsen. Exempel: **westeurope**.
    * **Jag godkänner de villkor som anges ovan**: Välj.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Mallegenskaper":::

 3. Om du godkänner de allmänna villkoren väljer du **köp**. När du har skapat registret får du ett meddelande:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portal meddelande":::

 Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal eller ett verktyg som Azure CLI för att granska egenskaperna för behållar registret.

1. I portalen söker du efter behållar register och väljer det behållar register som du har skapat.

1. Notera **inloggnings servern** för registret på sidan **Översikt** . Använd den här URI: n när du använder Docker för att tagga och skicka avbildningar till registret. Mer information finns i [skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Register översikt":::

1. På sidan **replikeringar** bekräftar du platserna för hem repliken och repliken som lagts till via mallen. Om du vill kan du lägga till fler repliker på den här sidan.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Register-replikeringar":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resurs gruppen, registret och register repliken. Om du vill göra det går du till Azure Portal, väljer den resurs grupp som innehåller registret och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container Registry med en Resource Manager-mall och konfigurerade en register replik på en annan plats. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry](container-registry-tutorial-prepare-registry.md)

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första Azure Resource Manager-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
