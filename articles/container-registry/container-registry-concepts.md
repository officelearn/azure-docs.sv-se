---
title: Om databaser och avbildningar i Azure Container Registry
description: Introduktion till viktiga begrepp för Azure-behållarregister, databaser och behållaravbildningar.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800333"
---
# <a name="about-registries-repositories-and-images"></a>Om register, databaser och bilder

Den här artikeln introducerar nyckelbegreppen för behållarregister, databaser, och behållaravbildningar och relaterade artefakter. 

## <a name="registry"></a>Registret

En behållare *registret* är en tjänst som lagrar och distribuerar behållaravbildningar. Docker Hub är en offentlig behållarregister som stöder communityn för öppen källkod och fungerar som en allmän katalog med bilder. Azure Container Registry ger användarna direkt kontroll av deras bilder med integrerad autentisering [georeplikering](container-registry-geo-replication.md) support global distribution och tillförlitlighet för nätverksnära distributioner, [ konfiguration av virtuell nätverks- och brandväggsinställningar](container-registry-vnet.md), [taggen låsning](container-registry-image-lock.md), och många andra förbättrad funktioner. 

Förutom att Docker-behållaravbildningar, har stöd för Azure Container Registry relaterade [innehåll artefakter](container-registry-image-formats.md) inklusive öppna behållare initiativ OCI ()-bildformat.

## <a name="content-addressable-elements-of-an-artifact"></a>Adresserbara innehållselement av en artefakt

Adressen för en artefakt i ett Azure container registry innehåller följande element. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -fullständigt kvalificerade namnet på registret-värden. Register-värden i ett Azure container registry är i formatet *myregistry*. azurecr.io (endast gemener). Du måste ange loginUrl när du använder Docker eller andra artefakter för pull- eller push-klientverktyg till ett Azure container registry. 
* **namnområdet** - avgränsade med snedstreck logisk gruppering av relaterade bilder eller artefakter – till exempel för en arbetsgrupp eller en app
* **artefakten** -namnet på en lagringsplats för en viss avbildning eller en artefakt
* **taggen** – en viss version av en bild eller en artefakt som lagras i en databas


Det fullständiga namnet på en bild i en Azure container registry kan till exempel se ut:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

Se följande avsnitt för information om de här elementen.

## <a name="repository-name"></a>Databasnamn

Hantera behållarregister *databaser*, samlingar av behållaravbildningar eller andra artefakter med samma namn men olika taggar. Till exempel finns följande tre avbildningar i databasen ”acr-helloworld”:

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Databasnamn kan även inkludera [namnområden](container-registry-best-practices.md#repository-namespaces). Namnområden kan du till gruppen avbildningar med vanlig avgränsade med snedstreck databasnamn, till exempel:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

En behållaravbildning eller andra artefakter i ett register är associerad med en eller flera taggar, har ett eller flera lager och identifieras med ett manifest. Förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att effektivt hantera ditt register.

### <a name="tag"></a>Tagga

Den *taggen* för en avbildning eller andra artefakter anger dess version. En enda artefakt i en databas kan du tilldela en eller flera taggar och kan även vara ”ej taggade”. Det vill säga kan du ta bort alla taggar från en avbildning, medan avbildningsdata (lager) är i registret.

Databasen (eller databasen och namnområde) plus en tagg definierar en avbildningens namn. Du kan skicka och hämta en avbildning genom att ange dess namn i sändning eller hämtning igen.

Hur du tagga behållaravbildningar leds av dina scenarier för att utveckla eller distribuera dem. Till exempel rekommenderas stabil taggar för att underhålla dina Källavbildningen och unika taggar för distribution av avbildningar. Mer information finns i [rekommendationer för taggning och versionshantering behållaravbildningar](container-registry-image-tag-version.md).

### <a name="layer"></a>Lager

Behållaravbildningar består av en eller flera *lager*, varje motsvarar en rad i den Dockerfile som definierar avbildningen. Bilder i ett register dela vanliga lager, öka lagringseffektiviteten. Till exempel flera bilder i olika databaser kan dela samma grundläggande Alpine Linux lager, men bara en kopia av skiktet lagras i registret.

Layer delning optimerar dessutom layer-distribution till noder med flera avbildningar som delar vanliga lager. Om en avbildning som redan finns på en nod innehåller Alpine Linux-lagret som bas, överföra inte den efterföljande pull av en annan avbildning som refererar till samma lager till exempel lagret till noden. I stället den refererar till det lagret som redan finns på noden.

För att ge säker isolering och skydd mot potentiella layer manipulering, delas inte lager mellan register.

### <a name="manifest"></a>Manifest

Varje behållaravbildning eller artefakt som skickas till ett behållarregister är associerad med en *manifest*. Manifestet, som genereras av registret när avbildningen skickas, identifierar bilden och anger dess lager. Du kan visa manifest för en databas med Azure CLI-kommando [az acr databasen show-manifest][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Till exempel lista manifestet Överför sammanfattningar för ”acr-helloworld”-lagringsplatsen:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest sammanfattad

Manifest identifieras med en unik SHA-256-hash eller *manifest sammanfattad*. Varje avbildning eller en artefakt--identifieras om taggade eller inte – med dess sammanfattad. Sammanfattad värdet är unikt, även om bildens lagerdata är identisk med en annan bild. Den här mekanismen är vad låter dig upprepade gånger identiskt taggade bilder till ett register. Exempel: du kan skicka flera gånger `myimage:latest` till registret felfritt eftersom varje avbildning har identifierats av dess unika sammanfattad.

Du kan hämta en avbildning från ett register genom att ange dess sammanfattad i pull-åtgärd. Vissa system kan konfigureras för att hämta av sammanfattad eftersom det garanterar versionsnumret för avbildningen som hämtas, även om ett identiskt taggade avbildningen skickas sedan till registret.

Exempel: hämta en avbildning från lagringsplatsen ”acr-helloworld” av manifest sammanfattad:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Om du överför flera gånger ändrade avbildningar med identiska taggar, kan du skapa överblivna bilder--bilder som är ej taggade, men fortfarande förbruka utrymme i registret. Ej taggade bilder visas inte i Azure CLI eller i Azure-portalen när du listar eller visa bilder efter tagg. Men lagren fortfarande finns och förbruka utrymme i registret. Information om hur du frigör utrymme som används av otaggade avbildningar finns i [ta bort avbildningar i Azure Container Registry](container-registry-delete.md).


## <a name="next-steps"></a>Nästa steg

Läs mer om [bildlagring](container-registry-storage.md) och [stöds format](container-registry-image-formats.md) i Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


