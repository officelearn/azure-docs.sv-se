---
title: Om databaser & bilder
description: Introduktion till viktiga begrepp för Azure-behållarregister, databaser och behållaravbildningar.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247064"
---
# <a name="about-registries-repositories-and-images"></a>Om register, databaser och bilder

Den här artikeln introducerar de viktigaste begreppen för behållarregister, databaser och behållaravbildningar och relaterade artefakter. 

## <a name="registry"></a>Register

Ett *behållarregister* är en tjänst som lagrar och distribuerar behållaravbildningar. Docker Hub är ett offentligt behållarregister som stöder communityn med öppen källkod och fungerar som en allmän katalog med avbildningar. Azure Container Registry ger användarna direkt kontroll över sina avbildningar, med integrerad autentisering, [geo-replikering](container-registry-geo-replication.md) som stöder global distribution och tillförlitlighet för nätverksanslutningar, [virtuell nätverks- och brandväggskonfiguration,](container-registry-vnet.md) [tagglåsning](container-registry-image-lock.md)och många andra förbättrade funktioner. 

Förutom Docker-behållaravbildningar stöder Azure Container Registry relaterade [innehållsartefakter,](container-registry-image-formats.md) inklusive OCI-avbildningsformat (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Adresserbara innehållselement i en artefakt

Adressen till en artefakt i ett Azure-behållarregister innehåller följande element. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - Det fullständigt kvalificerade namnet på registervärden. Registervärden i ett Azure-behållarregister är i det format *som mitt register*.azurecr.io (alla gemener). Du måste ange loginUrl när du använder Docker eller andra klientverktyg för att hämta eller skicka artefakter till ett Azure-behållarregister. 
* **namnområde** - Slash-avgränsad logisk gruppering av relaterade bilder eller artefakter - till exempel för en arbetsgrupp eller app
* **artefakt** - Namnet på en databas för en viss bild eller artefakt
* **tag** - En specifik version av en bild eller artefakt som lagras i en databas


Det fullständiga namnet på en avbildning i ett Azure-behållarregister kan till exempel se ut så här:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Mer information om dessa element finns i följande avsnitt.

## <a name="repository-name"></a>Databasnamn

Behållarregister hanterar *databaser,* samlingar av behållaravbildningar eller andra artefakter med samma namn, men olika taggar. Följande tre bilder finns till exempel i databasen "acr-helloworld":


- *acr-helloworld:senaste*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Databasnamn kan också innehålla [namnområden](container-registry-best-practices.md#repository-namespaces). Med namnområden kan du gruppera bilder med hjälp av framåtskurna snedstrecksavgränsade databasnamn, till exempel:

- *marknadsföring/kampanj10-18/web:v2*
- *marknadsföring/kampanj10-18/api:v3*
- *marknadsföring/kampanj10-18/e-postavsändare:v2*
- *produkt-returer/webb-inlämning:20180604*
- *produkt-returer/äldre-integratör:20180715*

## <a name="image"></a>Bild

En behållaravbildning eller annan artefakt i ett register är associerad med en eller flera taggar, har ett eller flera lager och identifieras av ett manifest. Att förstå hur dessa komponenter relaterar till varandra kan hjälpa dig att hantera registret effektivt.

### <a name="tag"></a>Tagga

*Taggen* för en bild eller annan artefakt anger dess version. En enskild artefakt i en databas kan tilldelas en eller flera taggar och kan också vara "otaggad". Det vill säga du kan ta bort alla taggar från en bild, medan bildens data (dess lager) finns kvar i registret.

Databasen (eller databasen och namnområdet) plus en tagg definierar en bilds namn. Du kan trycka på och hämta en bild genom att ange dess namn i push- eller pull-åtgärden.

Hur du taggar behållaravbildningar styrs av dina scenarier för att utveckla eller distribuera dem. Till exempel rekommenderas stabila taggar för att underhålla dina basavbildningar och unika taggar för att distribuera avbildningar. Mer information finns i [Rekommendationer för taggning och versionshantering av behållaravbildningar](container-registry-image-tag-version.md).

### <a name="layer"></a>Lager

Behållaravbildningar består av ett eller flera *lager*, var och en motsvarar en linje i Dockerfile som definierar bilden. Avbildningar i ett register delar gemensamma lager, vilket ökar lagringseffektiviteten. Flera avbildningar i olika databaser kan till exempel dela samma alpina Linux-baslager, men bara en kopia av lagret lagras i registret.

Lagerdelning optimerar också lagerfördelningen till noder med flera bilder som delar vanliga lager. Om till exempel en avbildning som redan finns på en nod innehåller alpine Linux-lagret som bas, överförs inte lagret till noden efterföljande dragningskraft. I stället refereras det lager som redan finns på noden.

För att ge säker isolering och skydd mot potentiell lagermanipulering delas lager inte mellan register.

### <a name="manifest"></a>Manifest

Varje behållaravbildning eller artefakt som skjuts till ett behållarregister är associerad med ett *manifest*. Manifestet, som genereras av registret när avbildningen trycks, identifierar bilden unikt och anger dess lager. Du kan lista manifest för en databas med Azure [CLI-kommandot az acr-databasshow-manifest:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Lista till exempel manifesten för databasen "acr-helloworld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
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

### <a name="manifest-digest"></a>Manifestsammandrag

Manifest identifieras med en unik SHA-256 hash eller *manifestsammandrag.* Varje bild eller artefakt - om taggade eller inte - identifieras av dess sammanfattning. Digestvärdet är unikt även om bildens lagerdata är identiska med den i en annan bild. Denna mekanism är vad som gör att du upprepade gånger kan skicka identiskt taggade bilder till ett register. Du kan till exempel `myimage:latest` skicka till registret flera gånger utan fel eftersom varje avbildning identifieras av dess unika sammanfattning.

Du kan hämta en avbildning från ett register genom att ange dess sammanfattning i pull-åtgärden. Vissa system kan konfigureras för att dra genom sammandrag eftersom det garanterar att avbildningsversionen dras, även om en identiskt taggad avbildning därefter skickas till registret.

Hämta till exempel en bild från databasen "acr-helloworld" genom att visa sammanfattning:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Om du upprepade gånger skickar ändrade bilder med identiska taggar kan du skapa överblivna avbildningar – bilder som inte har taggats, men som fortfarande förbrukar utrymme i registret. Otaggade avbildningar visas inte i Azure CLI eller i Azure-portalen när du listar eller visar avbildningar efter tagg. Deras lager finns dock fortfarande kvar och förbrukar utrymme i registret. Om du tar bort en otaggad avbildning frigörs registerutrymmet när manifestet är det enda, eller det sista, som pekar på ett visst lager. Information om hur du frigör utrymme som används av otaggade [avbildningar finns i Ta bort behållaravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [avbildningslagring](container-registry-storage.md) och [innehållsformat som stöds](container-registry-image-formats.md) i Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


