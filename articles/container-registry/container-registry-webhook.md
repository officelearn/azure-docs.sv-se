---
title: Azure Container Registry-webhookar
description: Lär dig hur du använder webhooks för att utlösande händelser när vissa åtgärder sker i registret-databaser.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/20/2017
ms.author: danlep
ms.openlocfilehash: 350ae16aa66276e7e64c5c35718dca74a70f499e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854107"
---
# <a name="using-azure-container-registry-webhooks"></a>Med Azure Container Registry-webhookar

En Azure-behållarregister lagrar och hanterar privata Docker-behållaravbildningar, ungefär på samma sätt som Docker Hub lagrar offentliga Docker-avbildningar. Du kan använda webhooks för att utlösande händelser när vissa åtgärder ägde rum i någon av dina databaser i registret. Webhooks kan reagera på händelser på nivån registret eller de kan begränsas av en specifik lagringsplats-tagg.

Mer information om webhook-begäranden finns [Schemareferens i Azure Container Registry webhook](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure container registry – skapa ett behållarregister i Azure-prenumerationen. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* Docker CLI - att konfigurera din lokala dator som Docker-värd och komma åt Docker CLI-kommandona installera [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Skapa webhook Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Gå till behållarregistret som du vill skapa en webhook.
1. Under **SERVICES**väljer **Webhooks**.
1. Välj **Lägg till** i webhook-verktygsfältet.
1. Slutför den *skapa webhook* formuläret med följande information:

| Värde | Beskrivning |
|---|---|
| Namn | Namnet som du vill ge till webhooken. Det kan bara innehålla gemena bokstäver och siffror och måste vara 5 – 50 tecken långt. |
| Tjänstens URI | URI: N där webhooken ska skicka POST-meddelanden. |
| Anpassade huvuden | Rubriker som du vill skicka den tillsammans med POST-begäran. De bör vara i ”key: value” format. |
| Utlösaråtgärder | Åtgärder som utlöser webhooken. Webhooks för närvarande kan utlösas av avbildningen push och/eller ta bort åtgärder. |
| Status | Status för webhook när den har skapats. Det är aktiverat som standard. |
| Omfång | Omfånget då webhooken fungerar. Som standard är omfånget för alla händelser i registret. Det kan anges för en databas eller en tagg i formatet ”-lagringsplatsen: tagg”. |

Exempelformulär för webhook:

![ACR webhook-skapandet UI i Azure portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Skapa webhook Azure CLI

Skapa en webhook som använder Azure CLI för att använda den [az acr webhook skapa](/cli/azure/acr/webhook#az-acr-webhook-create) kommando.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testa webhook

### <a name="azure-portal"></a>Azure Portal

Tidigare med hjälp av webhook behållaren bild push och ta bort åtgärder, du kan testa den med den **Ping** knappen. Ping skickar en allmän POST-begäran till den angivna slutpunkten och loggar svaret. Med hjälp av ping-funktionen kan hjälpa dig att kontrollera att du har korrekt konfigurerat webhooken.

1. Välj webhooken som du vill testa.
2. I det översta verktygsfältet väljer **Ping**.
3. Kontrollera slutpunkternas svar i den **HTTP-STATUS** kolumn.

![ACR webhook-skapandet UI i Azure portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR webhook med Azure CLI, använder den [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) kommando.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Du kan se resultaten genom att använda den [az acr webhook lista-händelser](/cli/azure/acr/webhook#list-events) kommando.

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