---
title: Webhooks att svara på registeråtgärder
description: Lär dig hur du använder webhooks för att utlösa händelser när push- eller pull-åtgärder inträffar i registerdatabaserna.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454370"
---
# <a name="using-azure-container-registry-webhooks"></a>Använda webhooks för Azure Container-behållarregister

Ett Azure-containerregister lagrar och hanterar privata Docker-containeravbildningar, på samma sätt som Docker Hub lagrar offentliga Docker-avbildningar. Det kan också vara värd för databaser för [Helm-diagram](container-registry-helm-repos.md) (förhandsversion), ett förpackningsformat för att distribuera program till Kubernetes. Du kan använda webhooks för att utlösa händelser när vissa åtgärder utförs i en av dina registerdatabaser. Webhooks kan svara på händelser på registernivå eller så kan de begränsas till en viss databastagg. Med ett [georeplikerat](container-registry-geo-replication.md) register konfigurerar du varje webhook för att svara på händelser i en viss regional replik.

Mer information om webhook-begäranden finns i [Azure Container Registry webhook schemareferens](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Krav

* Azure-containerregister – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). [Azure Container Registry SKU:er](container-registry-skus.md) har olika webhooks-kvoter.
* Docker CLI – Om du vill konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installerar du [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Skapa webhook - Azure portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till behållarregistret där du vill skapa en webhook.
1. Under **Tjänster**väljer du **Webhooks**.
1. Välj **Lägg till** i verktygsfältet webhook.
1. Fyll i formuläret *Skapa webhook* med följande information:

| Värde | Beskrivning |
|---|---|
| Namn på Webhook | Namnet du vill ge till webhook. Den får bara innehålla bokstäver och siffror och måste vara 5-50 tecken lång. |
| Location | För ett [geo-replikerat](container-registry-geo-replication.md) register anger du Azure-regionen för registerrepliken. 
| Tjänst URI | Uri där webhook ska skicka POST-meddelanden. |
| Anpassade rubriker | Rubriker som du vill skicka tillsammans med POST-begäran. De bör vara i "nyckel: värde" format. |
| Utlösa åtgärder | Åtgärder som utlöser webhooken. Åtgärderna omfattar bildtryck, bildborttagning, tryck på Helm-diagram, borttagning av Helm-diagram och bildkarantän. Du kan välja en eller flera åtgärder för att utlösa webhooken. |
| Status | Statusen för webhooken när den har skapats. Det är aktiverat som standard. |
| Omfång | Omfattningen där webhook fungerar. Om inget anges är omfånget för alla händelser i registret. Det kan anges för en databas eller en tagg med formatet "repository:tag" eller "repository:*" för alla taggar under en databas. |

Exempel på webhook-formulär:

![ACR webhook skapa användargränssnitt i Azure-portalen](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Skapa webhook - Azure CLI

Om du vill skapa en webhook med hjälp av Azure CLI använder du kommandot [az acr webhook create.](/cli/azure/acr/webhook#az-acr-webhook-create) Följande kommando skapar en webhook för alla avbildningsborttagningshändelser i registret *mycontainerregistry:*

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testa webhook

### <a name="azure-portal"></a>Azure Portal

Innan du använder webhooken kan du **Ping** testa den med Ping-knappen. Ping skickar en allmän POST-begäran till den angivna slutpunkten och loggar svaret. Med ping-funktionen kan du kontrollera att du har konfigurerat webhooken korrekt.

1. Välj den webhook du vill testa.
2. Välj **Ping**i det övre verktygsfältet .
3. Kontrollera slutpunktens svar i kolumnen **HTTP STATUS.**

![ACR webhook skapa användargränssnitt i Azure-portalen](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR webhook med Azure CLI använder du kommandot [az acr webhook ping.](/cli/azure/acr/webhook#az-acr-webhook-ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Om du vill se resultaten använder du kommandot [az acr webhook list-events.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Ta bort webhook

### <a name="azure-portal"></a>Azure Portal

Varje webhook kan tas bort genom att välja webhook och sedan **knappen Ta bort** i Azure-portalen.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Nästa steg

### <a name="webhook-schema-reference"></a>Webbkrokschemareferens

Mer information om formatet och egenskaperna för JSON-händelsenyttolaster som avges av Azure Container Registry finns i webhook-schemareferensen:

[Azure Container Registry webhook schemareferens](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Händelser i händelserutnätet

Förutom de inbyggda registerwebbkhändelser som beskrivs i den här artikeln kan Azure Container Registry avge händelser till Event Grid:

[Snabbstart: Skicka behållarregisterhändelser till Händelserutnät](container-registry-event-grid-quickstart.md)
