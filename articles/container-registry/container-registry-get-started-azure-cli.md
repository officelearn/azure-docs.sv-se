---
title: "Skapa ett behållarregister med hjälp av Azure CLI | Microsoft Docs"
description: "Kom igång med att skapa och hantera privata Dockerbehållarregister med Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e37a3194bb65ccf3bb6168a2f456902a9c48edc5
ms.lasthandoff: 03/28/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Skapa ett privat Dockerbehållarregister med hjälp av Azure CLI 2.0
Använd kommandon i [Azure CLI 2.0](https://github.com/Azure/azure-cli) för att skapa ett behållarregister och hantera inställningarna från din Linux-, Mac- eller Windows-dator. Du kan också skapa och hantera behållarregister med hjälp av [Azure Portal](container-registry-get-started-portal.md) eller via programmering med [REST-API:et](https://go.microsoft.com/fwlink/p/?linkid=834376) för Container Registry.


* Bakgrund och koncept beskrivs i [översikten](container-registry-intro.md)
* Om du vill visa hjälp om Container Registry CLI-kommandon (`az acr`-kommandon) använder du `-h`-parametern med valfritt kommando.


## <a name="prerequisites"></a>Krav
* **Azure CLI 2.0**: Information om hur du installerar och kommer igång med CLI 2.0 finns i avsnittet med [installationsanvisningar](/cli/azure/install-azure-cli). Logga in i din Azure-prenumeration genom att köra `az login`. Mer information finns i [Kom igång med CLI 2.0](/cli/azure/get-started-with-azure-cli).
* **Resursgrupp**: Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md#resource-groups) innan du skapar ett behållarregister eller använd en befintlig resursgrupp. Kontrollera att resursgruppen finns på en plats där Container Registry-tjänsten är [tillgänglig](https://azure.microsoft.com/regions/services/). Information om hur du skapar en resursgrupp med hjälp av CLI 2.0 finns i [CLI 2.0-referensen](/cli/azure/group).
* **Lagringskonto** (valfritt): Skapa ett Azure-[standardlagringskonto](../storage/storage-introduction.md) för användning med behållarregistret på samma plats. Om du inte anger ett lagringskonto när du skapar ett register med `az acr create` skapar kommandot ett automatiskt. Information om hur du skapar ett lagringskonto med hjälp av CLI 2.0 finns i [CLI 2.0-referensen](/cli/azure/storage/account). Premium Storage stöds inte för närvarande.
* **Tjänstobjekt** (valfritt): När du skapar ett register med CLI skapas det inte för åtkomst som standard. Beroende på dina behov kan du tilldela ett befintligt Azure Active Directory-tjänstobjekt till ett register (eller skapa och tilldela ett nytt) eller aktivera registrets administratörsanvändarkonto. Mer information finns i avsnitten nedan. Mer information om registeråtkomst finns i [Authenticate with a container registry](container-registry-authentication.md) (Autentisera med ett behållarregister).

## <a name="create-a-container-registry"></a>Skapa ett behållarregister
Du skapar ett behållarregister genom att köra `az acr create`-kommandot.

> [!TIP]
> När du skapar ett register anger du ett globalt unikt domännamn på den översta nivån, som endast innehåller bokstäver och siffror. Registernamnet i exemplen är `myRegistry1`, men du kan ersätta det med ett eget unikt namn.
>
>

Följande kommando använder de minsta nödvändiga parametrarna för att skapa behållarregistret `myRegistry1` i resursgruppen `myResourceGroup` på platsen USA, södra centrala:

```azurecli
az acr create -n myRegistry1 -g myResourceGroup -l southcentralus
```

* `--storage-account-name` är valfritt. Om inget annat anges skapas ett lagringskonto med ett namn som består av registrets namn och en tidsstämpel i den angivna resursgruppen.

De utdata som genereras liknar följande:

![Utdata från az acr create](./media/container-registry-get-started-azure-cli/acr_create.png)


Notera särskilt:

* `id` – ID:t för registret i din prenumeration, som du behöver om du vill tilldela ett tjänstobjekt.
* `loginServer` – Det fullständigt kvalificerade namnet som du anger för att [logga in i registret](container-registry-authentication.md). I det här exemplet är namnet `myregistry1.exp.azurecr.io` (endast gemener).

## <a name="assign-a-service-principal"></a>Tilldela ett tjänstobjekt
Använd CLI 2.0-kommandon om du vill associera ett Azure Active Directory-tjänstobjekt med ett register. Tjänstobjektet i dessa exempel tilldelas rollen Owner (ägare), men du kan tilldela [andra roller](../active-directory/role-based-access-control-configure.md) om du vill.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Skapa ett tjänstobjekt och tilldela åtkomst till registret
I följande kommando tilldelas ett nytt tjänstobjekt åtkomst med rollen Owner (ägare) till register-ID:t som anges med parametern `--scopes`. Ange ett starkt lösenord med parametern `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Tilldela ett befintligt tjänstobjekt
Om du redan har ett tjänstobjekt och vill ge det åtkomst till registret med rollen Owner (ägare) kör du ett kommando liknande det i följande exempel. Du skickar tjänstobjektets app-ID med parametern `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Hantera administratörsautentiseringsuppgifter
Ett administratörskonto skapas automatiskt för varje behållarregister och är inaktiverat som standard. Följande exempel visar `az acr` CLI-kommandon som du kan använda för att hantera administratörsautentiseringsuppgifter för ett behållarregister.

### <a name="obtain-admin-user-credentials"></a>Hämta administratörsautentiseringsuppgifter
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Aktivera en administratörsanvändare för ett befintligt register
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Inaktivera en administratörsanvändare för ett befintligt register
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Visa en lista med avbildningar och taggar
Använd `az acr` CLI-kommandona för att skicka frågor mot avbildningarna och taggarna på en lagringsplats.

> [!NOTE]
> För närvarande stöder inte Container Registry `docker search`-kommandot för att hämta information om avbildningar och taggar.


### <a name="list-repositories"></a>Visa en lista över lagringsplatser
Följande exempel visar en lista över lagringsplatser i ett register, i JSON-format (JavaScript Object Notation):

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Visa en lista över taggar
Följande exempel visar en lista med taggarna som används för **samples/nginx**-lagringsplatsen, i JSON-format:

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Nästa steg
* [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)

