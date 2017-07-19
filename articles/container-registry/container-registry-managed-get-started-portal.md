---
title: "Skapa privat Docker-register – Azure Portal | Microsoft-dokument"
description: "Kom igång med att skapa och hantera privata Docker-behållarregister med Azure Portal"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: sv-se
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Skapa ett hanterat behållarregister med hjälp av Azure Portal

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden beskriver hur du skapar en hanterad Azure Container Registry-instans med hjälp av Azure Portal.

Hanterade Azure-behållarregister finns endast som förhandsversion och är inte tillgängliga i alla regioner.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Sök efter och välj **Azure Container Registry** på Marketplace.

3. Klicka på **Skapa** så att bladet för att skapa ACR öppnas.

    ![Inställningar för behållarregister](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. Ange informationen nedan på bladet **Azure Container Registry**. Klicka på **Skapa** när du är klar.

    a. **Registernamn**: Ett globalt unikt domännamn på den översta nivån för ditt specifika register. I det här exemplet är registernamnet *myAzureContainerRegistry1*, men du kan ersätta namnet med ett eget unikt namn. Namnet får bara innehålla bokstäver och siffror.

    b. **Resursgrupp**: Välj en befintlig [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) eller skriv namnet på en ny resursgrupp.

    c. **Plats**: Välj en plats för ett Azure-datacenter där tjänsten är [tillgänglig](https://azure.microsoft.com/regions/services/), t.ex. **USA, södra centrala**.

    d. **Administratörsanvändarnamn**: Om du vill ger du en administratörsanvändare åtkomst till registret. Du kan ändra den här inställningen när du har skapat registret.

    e. **Använd hanterat register**: Välj Ja om du vill hantera registerlagring automatiskt med ACR, använda webhooks och använda AAD-autentisering.

    f. **Prisnivå**: Välj en prisnivå. Här visas mer information om ACR-priser.

## <a name="log-in-to-acr-instance"></a>Logga in på ACR-instansen

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. 

Det gör du med Azure CLI 2.0. Först loggar du in på Azure med kommandot [az login](/cli/azure/#login), om det behövs. 

```azurecli
az login
```

Sedan använder du kommandot [az acr login](/cli/azure/acr#login) för att logga in på Azure Container Registry.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Använda Azure Container Registry

### <a name="list-container-images"></a>Visa lista över behållaravbildningar

Använd `az acr` CLI-kommandona för att skicka frågor mot avbildningarna och taggarna på en lagringsplats.

> [!NOTE]
> För närvarande stöder inte Container Registry `docker search`-kommandot för att hämta information om avbildningar och taggar.

### <a name="list-repositories"></a>Visa en lista över lagringsplatser

Följande exempel visar en lista över lagringsplatser i ett register, i JSON-format (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Visa en lista över taggar

Följande exempel visar en lista med taggarna som används för **samples/nginx**-lagringsplatsen, i JSON-format:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en hanterad Azure Container Registry-instans med hjälp av Azure Portal.

> [!div class="nextstepaction"]
> [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)
