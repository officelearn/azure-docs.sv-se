---
title: Bästa metoderna i Azure Container Registry
description: Lär dig att använda Azure Container Registry på ett effektivt sätt genom att följa dessa bästa metoder.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/27/2018
ms.author: danlep
ms.openlocfilehash: e22acc6e698d9b14a55145d8f23f5f773e6c39fd
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857711"
---
# <a name="best-practices-for-azure-container-registry"></a>Bästa metoder för Azure Container Registry

Genom att följa dessa bästa metoder kan du bidra till att maximera prestandan och den kostnadseffektiva användningen av det privata Docker-registret i Azure.

## <a name="network-close-deployment"></a>Nätverksnära distribution

Skapa containerregistret i samma Azure-region som du distribuerar containrar i. Genom att placera registret i en region som är nätverksnära till containervärdarna kan du bidra till att sänka både svarstid och kostnader.

Nätverksnära distribution är en av de främsta orsakerna till att använda ett privat containerregister. Docker-avbildningar har en effektiv [lagerkonstruktion](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) som möjliggör inkrementella distributioner. Nya noder måste dock hämta alla lager som krävs för en viss avbildning. Den första `docker pull` kan snabbt växa till flera gigabyte. Med ett privat register nära distributionen minimeras nätverkssvarstiden.
Dessutom implementerar alla offentliga moln, inklusive Azure, avgifter för nätverksöverskridande. Om avbildningar hämtas från ett datacenter till ett annat läggs avgifter för nätverksöverskridande till, utöver svarstiden.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replikering av distributioner över flera regioner

Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Azure Container Registry om du distribuerar behållare till flera regioner. Oavsett om du arbetar med globala kunder från lokala datacenter eller om utvecklingsteamet finns på olika platser kan du förenkla registerhanteringen och minimera svarstiden genom geo-replikering i registret. Geo-replikering är endast tillgänglig med [Premium](container-registry-skus.md)-register.

Information om hur du använder geo-replikering finns i självstudien [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md) (Geo-replikering i Azure Container Registry) i tre delar.

## <a name="repository-namespaces"></a>Namnrymder för lagringsplatser

Genom att använda namnrymder för lagringsplatser går det att dela ett enda register över flera grupper i din organisation. Registren kan delas mellan distributioner och team. Azure Container Registry har stöd för kapslade namnrymder, vilket möjliggör isolering av grupper.

Du kan till exempel överväga följande taggar för containeravbildningar. Avbildningar som används i hela företaget, t.ex. `aspnetcore`, placeras i rotnamnrymden, medan containeravbildningar som ägs av produktions- och marknadsföringsgrupperna använder sina egna namnrymder.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedikerad resursgrupp

Eftersom containerregister är resurser som används i flera containervärdar bör ett register finnas i en egen resursgrupp.

Även om du kan experimentera med en särskild värdtyp, t.ex. Azure Container Instances, vill du troligtvis ta bort behållarinstansen när du är klar. Du kan dock även vilja behålla avbildningssamlingen som du överförde till Azure Container Registry. Genom att placera registret i en egen resursgrupp minimerar du risken för att oavsiktligen ta bort avbildningssamlingen i registret när du tar bort containerinstansens resursgrupp.

## <a name="authentication"></a>Autentisering

Vid autentisering med ett Azure-containerregister finns det två huvudsakliga scenarier: individuell autentisering och tjänstautentisering (eller "fjärradministrering"). Följande tabell innehåller en kort översikt över dessa scenarier och den rekommenderade metoden för autentisering av dem.

| Typ | Exempel på ett scenario | Rekommenderad metod |
|---|---|---|
| Individuell identitet | En utvecklare hämtar avbildningar till eller skickar bilder från sin egen utvecklingsdator. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Fjärradministrerad/tjänstidentitet | Bygg- och distributionsledningar där användaren inte är direkt inblandad. | [Tjänstens huvud](container-registry-authentication.md#service-principal) |

Mer djupgående information om autentisering i Azure Container Registry finns i [Authenticate with an Azure container registry](container-registry-authentication.md) (Autentisera med ett Azure-behållarregister).

## <a name="manage-registry-size"></a>Hantera registerstorlek

Lagringsbegränsningarna för varje [SKU för Container Registry][container-registry-skus] är avsedda för olika scenarier: **Basic** för att komma igång, **Standard** för de flesta produktionsappar och **Premium** för storskaliga prestanda och [geo-replikering][container-registry-geo-replication]. Du bör hantera registrets storlek genom att regelbundet ta bort innehåll som inte används under hela registrets livslängd.

Använda Azure CLI-kommando [az acr show-usage] [ az-acr-show-usage] att visa den aktuella storleken på ditt register:

```console
$ az acr show-usage --resource-group myResourceGroup --name myregistry --output table
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Du kan också hitta den aktuella lagring som används i den **översikt** på ditt register i Azure portal:

![Information om registeranvändning i Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Ta bort avbildningsdata

Azure Container Registry har stöd för flera metoder för att ta bort avbildningsdata från ditt behållarregister. Du kan ta bort avbildningar efter tagg eller manifest sammanfattad, eller ta bort en hel databas.

Mer information om att ta bort avbildningsdata från ditt register, inklusive omärkt (kallas ibland ”överflödiga” eller ”ägarlösa”)-avbildningar, se [ta bort avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Azure Container Registry finns i flera nivåer, som kallas SKU:er, som var och en ger olika funktioner. Mer information om tillgängliga SKU:er finns i [SKU:er för Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
