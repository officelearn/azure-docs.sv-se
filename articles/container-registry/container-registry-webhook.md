---
title: Azure Container Registry-webhookar
description: Lär dig hur du använder webhooks för att utlösande händelser när vissa åtgärder sker i registret-databaser.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 0a3d2d0e858dc052095c0a58287970d10c06f0ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787282"
---
# <a name="using-azure-container-registry-webhooks"></a>Med Azure Container Registry-webhookar

En Azure-behållarregister lagrar och hanterar privata Docker-behållaravbildningar, ungefär på samma sätt som Docker Hub lagrar offentliga Docker-avbildningar. Det kan också vara värd för databaser för [Helm diagram](container-registry-helm-repos.md) (förhandsversion), en paketering formatera för att distribuera program till Kubernetes. Du kan använda webhooks för att utlösande händelser när vissa åtgärder ägde rum i någon av dina databaser i registret. Webhooks kan reagera på händelser på nivån registret eller de kan begränsas av en specifik lagringsplats-tagg.

Mer information om webhook-begäranden finns [Schemareferens i Azure Container Registry webhook](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

* Azure container registry – skapa ett behållarregister i Azure-prenumerationen. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). Den [Azure Container Registry SKU: er](container-registry-skus.md) har olika webhooks kvoter.
* Docker CLI - att konfigurera din lokala dator som Docker-värd och komma åt Docker CLI-kommandona installera [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Skapa webhook - Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Gå till behållarregistret som du vill skapa en webhook.
1. Under **Services**väljer **Webhooks**.
1. Välj **Lägg till** i webhook-verktygsfältet.
1. Slutför den *skapa webhook* formuläret med följande information:

| Värde | Beskrivning |
|---|---|
| Namn | Namnet som du vill ge till webhooken. Det får innehålla endast bokstäver och siffror och måste vara 5 – 50 tecken långt. |
| Tjänstens URI | URI: N där webhooken ska skicka POST-meddelanden. |
| Anpassade huvuden | Rubriker som du vill skicka den tillsammans med POST-begäran. De bör vara i ”key: value” format. |
| Utlösaråtgärder | Åtgärder som utlöser webhooken. Åtgärder omfattar bild push, image delete, Helm-diagram-push, ta bort Helm-diagrammet och bild i karantän. Du kan välja en eller flera åtgärder för att utlösa webhooken. |
| Status | Status för webhook när den har skapats. Det är aktiverat som standard. |
| Scope | Omfånget då webhooken fungerar. Om inte anges är omfånget för alla händelser i registret. Det kan anges för en databas eller en tagg i formatet ”-lagringsplatsen: tagg” eller ”lagringsplats: *” för alla taggar i en databas. |

Exempelformulär för webhook:

![ACR webhook-skapandet UI i Azure portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Skapa webhook – Azure CLI

Skapa en webhook som använder Azure CLI för att använda den [az acr webhook skapa](/cli/azure/acr/webhook#az-acr-webhook-create) kommando. Följande kommando skapar en webhook för alla bild ta bort händelser i registret *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testa webhook

### <a name="azure-portal"></a>Azure Portal

Innan du börjar använda webhooken, kan du testa den med den **Ping** knappen. Ping skickar en allmän POST-begäran till den angivna slutpunkten och loggar svaret. Med hjälp av ping-funktionen kan hjälpa dig att kontrollera att du har korrekt konfigurerat webhooken.

1. Välj webhooken som du vill testa.
2. I det översta verktygsfältet väljer **Ping**.
3. Kontrollera slutpunkternas svar i den **HTTP-STATUS** kolumn.

![ACR webhook-skapandet UI i Azure portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR webhook med Azure CLI, använder den [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) kommando.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Du kan se resultaten genom att använda den [az acr webhook lista-händelser](/cli/azure/acr/webhook) kommando.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Ta bort webhook

### <a name="azure-portal"></a>Azure Portal

Varje webhook kan tas bort genom att välja webhooken och sedan den **ta bort** knappen i Azure-portalen.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Nästa steg

### <a name="webhook-schema-reference"></a>Schemareferens för Webhook

Information om format och egenskaper i JSON-event-nyttolaster som orsakats av Azure Container Registry finns i webhook-Schemareferens:

[Schemareferens i Azure Container Registry webhook](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid-händelser

Förutom inbyggda registret webhook händelserna som beskrivs i den här artikeln, kan Azure Container Registry Generera händelser till Event Grid:

[Snabbstart: Skicka container registerhändelser till Event Grid](container-registry-event-grid-quickstart.md)
