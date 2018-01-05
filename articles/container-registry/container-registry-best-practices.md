---
title: "Bästa metoderna i Azure Container Registry"
description: "Lär dig att använda Azure Container Registry på ett effektivt sätt genom att följa dessa bästa metoder."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: d94c6801f96ce684ebb912667dc4aa381c171216
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Bästa metoder för Azure Container Registry

Genom att följa dessa bästa metoder kan du bidra till att maximera prestandan och den kostnadseffektiva användningen av det privata Docker-registret i Azure.

## <a name="network-close-deployment"></a>Nätverksnära distribution

Skapa behållarregistret i samma Azure-region som du distribuerar behållare i. Genom att placera registret i en region som är nätverksnära till behållarvärdarna kan du bidra till att sänka både svarstid och kostnader.

Nätverksnära distribution är en av de främsta orsakerna till att använda ett privat behållarregister. Docker-avbildningar har en effektiv [lagerkonstruktion](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) som möjliggör inkrementella distributioner. Nya noder måste dock hämta alla lager som krävs för en viss avbildning. Den första `docker pull` kan snabbt växa till flera gigabyte. Med ett privat register nära distributionen minimeras nätverkssvarstiden.
Dessutom implementerar alla offentliga moln, inklusive Azure, avgifter för nätverksöverskridande. Om avbildningar hämtas från ett datacenter till ett annat läggs avgifter för nätverksöverskridande till, utöver svarstiden.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replikering av distributioner över flera regioner

Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Azure Container Registry om du distribuerar behållare till flera regioner. Oavsett om du arbetar med globala kunder från lokala datacenter eller om utvecklingsteamet finns på olika platser kan du förenkla registerhanteringen och minimera svarstiden genom geo-replikering i registret. Den här funktionen finns för närvarande i en förhandsversion i [Premium](container-registry-skus.md)-register.

Information om hur du använder geo-replikering finns i självstudien [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md) (Geo-replikering i Azure Container Registry) i tre delar.

## <a name="repository-namespaces"></a>Namnrymder för lagringsplatser

Genom att använda namnrymder för lagringsplatser går det att dela ett enda register över flera grupper i din organisation. Registren kan delas mellan distributioner och team. Azure Container Registry har stöd för kapslade namnrymder, vilket möjliggör isolering av grupper.

Du kan till exempel överväga följande taggar för behållaravbildningar. Avbildningar som används i hela företaget, t.ex. `aspnetcore`, placeras i rotnamnrymden, medan behållaravbildningar som ägs av produktions- och marknadsföringsgrupperna använder sina egna namnrymder.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Dedikerad resursgrupp

Eftersom behållarregister är resurser som används i flera behållarvärdar bör ett register finnas i en egen resursgrupp.

Även om du kan experimentera med en särskild värdtyp, t.ex. Azure Container Instances, vill du troligtvis ta bort behållarinstansen när du är klar. Du kan dock även vilja behålla avbildningssamlingen som du överförde till Azure Container Registry. Genom att placera registret i en egen resursgrupp minimerar du risken för att oavsiktligen ta bort avbildningssamlingen i registret när du tar bort behållarinstansens resursgrupp.

## <a name="authentication"></a>Autentisering

Vid autentisering med ett Azure-behållarregister finns det två huvudsakliga scenarier: individuell autentisering och tjänstautentisering (eller "fjärradministrering"). Följande tabell innehåller en kort översikt över dessa scenarier och den rekommenderade metoden för autentisering av dem.

| Typ | Exempel på ett scenario | Rekommenderad metod |
|---|---|---|
| Individuell identitet | En utvecklare hämtar avbildningar till eller skickar bilder från sin egen utvecklingsdator. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Fjärradministrerad/tjänstidentitet | Bygg- och distributionsledningar där användaren inte är direkt inblandad. | [Tjänstens huvud](container-registry-authentication.md#service-principal) |

Mer djupgående information om autentisering i Azure Container Registry finns i [Authenticate with an Azure container registry](container-registry-authentication.md) (Autentisera med ett Azure-behållarregister).

## <a name="manage-registry-size"></a>Hantera registerstorlek

Lagringsbegränsningarna för varje [SKU för Container Registry][container-registry-skus] är avsedda för olika scenarier: **Basic** för att komma igång, **Standard** för de flesta produktionsappar och **Premium** för storskaliga prestanda och [geo-replikering][container-registry-geo-replication]. Du bör hantera registrets storlek genom att regelbundet ta bort innehåll som inte används under hela registrets livslängd.

Aktuell användning av registret finns i **Översikt** för behållarregistret i Azure Portal:

![Information om registeranvändning i Azure Portal][registry-overview-quotas]

Du kan hantera storleken på registret med hjälp av [Azure CLI][azure-cli] eller [Azure Portal][azure-portal]. Endast hanterade SKU: er (Basic, Standard och Premium) har stöd för borttagning av lagringsplatser och avbildningar. Du kan inte ta bort lagringsplatser, avbildningar eller taggar i ett klassiskt register.

### <a name="delete-in-azure-cli"></a>Borttagning i Azure CLI

Använd kommandot [az acr repository delete][az-acr-repository-delete] för att ta bort en lagringsplats eller innehåll på en lagringsplats.

Om du vill ta bort en lagringsplats, inklusive alla taggar och avbildningslagerdata på lagringsplatsen, anger du endast namnet på lagringsplatsen när du kör [az acr repository delete][az-acr-repository-delete]. I följande exempel tar vi bort lagringsplatsen *myapplication* och alla taggar och avbildningslagerdata på lagringsplatsen:

```azurecli
az acr repository delete --name myregistry --repository myapplication
```

Du kan också ta bort avbildningsdata från en lagringsplats med argumenten `--tag` och `--manifest`. Mer information om de här argumenten finns i [referensen för kommandot az acr repository delete][az-acr-repository-delete].

### <a name="delete-in-azure-portal"></a>Ta bort i Azure Portal

Om du vill ta bort en lagringsplats från ett register i Azure Portal navigerar du först till ditt behållarregister. Välj sedan **Lagringsplatser** under **TJÄNSTER** och högerklicka på den lagringsplats som du vill ta bort. Välj **Ta bort** för att ta bort lagringsplatsen och de Docker-avbildningar som finns på lagringsplatsen.

![Ta bort en lagringsplats i Azure Portal][delete-repository-portal]

Du kan också ta bort taggar från en lagringsplats på ett liknande sätt. Navigera till lagringsplatsen, högerklicka på taggen du vill ta bort under **TAGGAR** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Azure Container Registry finns i flera nivåer, som kallas SKU:er, som var och en ger olika funktioner. Mer information om tillgängliga SKU:er finns i [SKU:er för Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[azure-cli]: /cli/azure/overview
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
