---
title: Azure Container registret webhooks
description: Lär dig hur du använder webhooks utlösaren händelser när vissa åtgärder sker i registret-databaser.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: cf71deda9ec9053a91596c17e18fc797995754e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-container-registry-webhooks"></a>Med hjälp av Azure-behållare registret webhooks

En Azure-behållaren registret lagrar och hanterar privata Docker behållare bilder, ungefär samma sätt som Docker-hubb lagrar offentliga Docker-avbildningar. Du kan använda webhooks att utlösa händelser när vissa åtgärder utföras i en av dina databaser i registret. Webhooks kan svara på händelser på nivån registret eller de kan begränsas av en viss databas-tagg.

Mer information om webhook begäranden finns [Schemareferens för Azure-behållare registret webhook](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-behållaren registret - skapa en behållare registret i din Azure-prenumeration. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* Docker CLI - att konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installera [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Skapa webhook Azure-portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Navigera till behållaren registret som du vill skapa en webhook.
1. Under **SERVICES**väljer **Webhooks**.
1. Välj **Lägg till** i webhook-verktygsfältet.
1. Slutför den *skapa webhook* formuläret med följande information:

| Värde | Beskrivning |
|---|---|
| Namn | Namnet som du vill ge webhooken. Det får bara innehålla gemena bokstäver och siffror och måste vara 5 50 tecken. |
| Tjänstens URI | Den URI där webhooken ska skicka efter meddelanden. |
| Anpassade huvuden | Huvuden som du vill skicka tillsammans med POST-begäran. De måste vara i ”nyckel: värde” format. |
| Utlösaråtgärder | Åtgärder som utlöser webhooken. Webhooks för närvarande kan utlösas av avbildningen push och/eller ta bort åtgärder. |
| Status | Status för webhook när den har skapats. Den är aktiverad som standard. |
| Omfång | Omfattningen som webhooken fungerar. Omfånget är som standard för alla händelser i registret. Det kan anges för en databas eller en tagg i formatet ”databas: taggen”. |

Exempelformulär för webhook:

![ACR webhook skapa Användargränssnittet i Azure-portalen](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Skapa webhook Azure CLI

Om du vill skapa en webhook med hjälp av Azure CLI, använder den [az acr webhook skapa](/cli/azure/acr/webhook#az_acr_webhook_create) kommando.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Test-webhook

### <a name="azure-portal"></a>Azure Portal

Tidigare med hjälp av webhook för behållaren image push- och borttagningsåtgärder, du kan testa den med den **Ping** knappen. Ping skickar en allmän POST-begäran till den angivna slutpunkten och loggar svaret. Med hjälp av ping-funktionen kan hjälpa dig att kontrollera att du har korrekt konfigurerat webhooken.

1. Välj webhooken som du vill testa.
2. I det översta verktygsfältet väljer **Ping**.
3. Kontrollera slutpunktens svar i den **HTTP-STATUS** kolumn.

![ACR webhook skapa Användargränssnittet i Azure-portalen](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR webhook med Azure CLI, använder den [az acr webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping) kommando.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Om du vill se resultatet av [az acr webhook lista-händelser](/cli/azure/acr/webhook#list-events) kommando.

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

[Azure Container registret webhook-Schemareferens](container-registry-webhook-reference.md)