---
title: Om databaser & avbildningar
description: Introduktion till viktiga begrepp för Azure Container register,-databaser och behållar avbildningar.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455285"
---
# <a name="about-registries-repositories-and-images"></a>Om register, databaser och avbildningar

Den här artikeln beskriver viktiga begrepp för behållar register, lagrings platser och behållar avbildningar och relaterade artefakter. 

## <a name="registry"></a>Register

Ett behållar *register* är en tjänst som lagrar och distribuerar behållar avbildningar. Docker Hub är ett offentligt behållar register som stöder communityn för öppen källkod och fungerar som en allmän katalog med avbildningar. Azure Container Registry ger användare direkt kontroll över sina avbildningar, med integrerad autentisering, [geo-replikering](container-registry-geo-replication.md) stöder global distribution och tillförlitlighet för nätverks nära distributioner, [konfiguration av virtuella nätverk och brand väggar](container-registry-vnet.md), [tagg låsning](container-registry-image-lock.md)och många andra förbättrade funktioner. 

Förutom Docker-behållar avbildningar stöder Azure Container Registry relaterade [innehålls artefakter](container-registry-image-formats.md) , inklusive OCI-bildformat (Open container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Innehålls adresser bara element i en artefakt

Adressen till en artefakt i ett Azure Container Registry innehåller följande element. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** – det fullständigt kvalificerade namnet på register värden. Register värden i ett Azure Container Registry har formatet *register*. azurecr.IO (alla gemener). Du måste ange loginUrl när du använder Docker eller andra klient verktyg för att hämta eller push-artefakter till ett Azure Container Registry. 
* **namnrymd** -avgränsad logisk gruppering av relaterade bilder eller artefakter – till exempel för en arbets grupp eller app
* **artefakt** – namnet på en lagrings plats för en viss bild eller artefakt
* **tagga** – en angiven version av en bild eller artefakt som lagras i en lagrings plats


Till exempel kan det fullständiga namnet på en avbildning i ett Azure Container Registry se ut så här:

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

I följande avsnitt finns mer information om de här elementen.

## <a name="repository-name"></a>Namn på databas

Behållar register hanterar *databaser*, samlingar med behållar avbildningar eller andra artefakter med samma namn, men olika taggar. Följande tre avbildningar finns till exempel i databasen "ACR-HelloWorld":

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

Namn på databaser kan även innehålla [namn områden](container-registry-best-practices.md#repository-namespaces). Med namn rymder kan du gruppera bilder med hjälp av snedstreck-avgränsade lagrings namn, till exempel:

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Bild

En behållar avbildning eller en annan artefakt i ett register är kopplad till en eller flera taggar, har ett eller flera skikt, och identifieras av ett manifest. Att förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att hantera registret effektivt.

### <a name="tag"></a>Tagg

*Taggen* för en bild eller en annan artefakt anger dess version. En enda artefakt i en lagrings plats kan tilldelas en eller flera taggar, och kan också vara "omärkt". Det innebär att du kan ta bort alla Taggar från en bild, medan bildens data (dess lager) finns kvar i registret.

Lagrings platsen (eller lagrings platsen och namn området) plus en tagg definierar namnet på en avbildning. Du kan push-överföra och hämta en avbildning genom att ange dess namn i push-eller pull-åtgärden.

Hur du taggar behållar avbildningar vägleds av dina scenarier för att utveckla eller distribuera dem. Till exempel rekommenderas stabila taggar för att underhålla dina bas avbildningar och unika taggar för att distribuera avbildningar. Mer information finns i [rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md).

### <a name="layer"></a>Lager

Behållar avbildningar består av ett eller flera *lager*, var och en som motsvarar en linje i Dockerfile som definierar bilden. Avbildningar i ett register delar gemensamma lager, vilket ökar lagrings effektiviteten. Till exempel kan flera avbildningar i olika lagrings platser dela samma Alpine Linux-basadress, men endast en kopia av det lagret lagras i registret.

Lager delning optimerar också lager distribution till noder med flera bilder som delar gemensamma lager. Om en avbildning som redan finns på en nod t. ex. inkluderar Alpine Linux-lagret som bas, överförs inte lagret till noden när den efterföljande hämtningen av en annan bild som refererar till samma lager. I stället refererar den till det lager som redan finns på noden.

För att tillhandahålla säker isolering och skydd från potentiell lager hantering delas inte lagren mellan register.

### <a name="manifest"></a>Manifest

Varje behållar avbildning eller artefakt som flyttas till ett behållar register är associerad med ett *manifest*. Manifestet, som genereras av registret när avbildningen skickas, identifierar avbildningen unikt och anger dess lager. Du kan visa manifesten för en lagrings plats med Azure CLI [-kommandot AZ ACR-lagringsplats show-manifest][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ange till exempel manifesten för databasen "ACR-HelloWorld":

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

### <a name="manifest-digest"></a>Manifest Sammanfattning

Manifest identifieras av en unik SHA-256-hash eller *manifest sammandrag*. Varje bild eller artefakt – om Taggad eller inte--har identifierats av sammanfattningen. Digest-värdet är unikt även om avbildningens lager data är identiska med en annan bild. Den här metoden gör det möjligt att upprepade gånger skicka identiska märkta bilder till ett register. Du kan till exempel skicka `myimage:latest` flera gånger till registret utan fel eftersom varje avbildning identifieras med en unik Sammanfattning.

Du kan hämta en avbildning från ett register genom att ange dess Digest i pull-åtgärden. Vissa system kan konfigureras att hämta genom sammandrag eftersom det garanterar att avbildnings versionen hämtas, även om en identiskt taggad bild sedan skickas till registret.

Hämta till exempel en bild från lagrings platsen "ACR-HelloWorld" genom manifest sammandrag:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Om du ofta skickar ändrade bilder med identiska taggar kan du skapa överblivna bilder – bilder som är otaggade, men som fortfarande använder utrymme i registret. Otaggade bilder visas inte i Azure CLI eller i Azure Portal när du listar eller visar bilder efter tagg. Men deras lager finns fortfarande kvar och tar upp utrymme i registret. När du tar bort en otaggade bild frigörs register utrymmet när manifestet är det enda, eller det sista, som pekar på ett visst lager. Information om hur du frigör utrymme som används av otaggade bilder finns i [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [bild lagring](container-registry-storage.md) och [innehålls format som stöds](container-registry-image-formats.md) i Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


