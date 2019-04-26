---
title: Ta bort avbildningen resurser i Azure Container Registry
description: Information om hur du effektivt hantera registrets storlek genom att ta bort behållaren bilddata.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/04/2019
ms.author: danlep
ms.openlocfilehash: 1e496002c869c5d2c072773d37ed5fd5d4a5841e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430820"
---
# <a name="delete-container-images-in-azure-container-registry"></a>Ta bort avbildningar i Azure Container Registry

För att underhålla storleken på Azure container registry, bör du regelbundet ta bort inaktuella bilddata. Medan vissa behållaravbildningar som distribuerats i produktionen kan kräva mer långsiktiga lagring, kan andra vanligtvis tas bort snabbare. Till exempel i en automatisk build och Testscenario, kan registret snabbt fylla med avbildningar som aldrig kan distribueras och kan rensas strax efter att du har slutfört passet bygge och test.

Eftersom du kan ta bort avbildningsdata på flera olika sätt, är det viktigt att förstå hur varje borttagningsåtgärd påverkar lagringsanvändning. Den här artikeln kan du först introducerar komponenterna i en Docker-avbildningar i registret och behållare och sedan omfattar flera metoder för att ta bort avbildningsdata. Exempelskript tillhandahålls för att automatisera delete-åtgärder.

## <a name="registry"></a>Register

En behållare *registret* är en tjänst som lagrar och distribuerar behållaravbildningar. Docker Hub är en offentlig Docker-behållarregister, medan Azure Container Registry ger privata Docker-behållarregister i Azure.

## <a name="repository"></a>Lagringsplats

Hantera behållarregister *databaser*, samlingar av behållaravbildningar med samma namn men olika taggar. Till exempel finns följande tre avbildningar i databasen ”acr-helloworld”:

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

## <a name="components-of-an-image"></a>Komponenterna i en bild

En behållaravbildning i ett register är associerad med en eller flera taggar, har ett eller flera lager och identifieras med ett manifest. Förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att avgöra den bästa metoden för att frigöra utrymme i registret.

### <a name="tag"></a>Tagga

En bild *taggen* anger dess version. En enda avbildning i en databas kan tilldelas en eller flera taggar och kan också vara ”ej taggade”. Det vill säga kan du ta bort alla taggar från en avbildning, medan avbildningsdata (lager) är i registret.

Databasen (eller databasen och namnområde) plus en tagg definierar en avbildningens namn. Du kan skicka och hämta en avbildning genom att ange dess namn i sändning eller hämtning igen.

Avbildningens namn innehåller också det fullständigt kvalificerade namnet för registret värden i ett privat register som Azure Container Registry. Registret värden för bilder i ACR är i formatet *acrname.azurecr.io* (endast gemener). Till exempel blir det fullständiga namnet på den första bilden i namnområdet ”marknadsföring” i föregående avsnitt:

```
myregistry.azurecr.io/marketing/campaign10-18/web:v2
```

En beskrivning på avbildningen taggning metodtips finns i den [Docker-märkning: Bästa metoder för taggning och versionshantering docker-avbildningar] [ tagging-best-practices] blogginlägget på MSDN.

### <a name="layer"></a>Lager

Bilder består av en eller flera *lager*, varje motsvarar en rad i den Dockerfile som definierar avbildningen. Bilder i ett register dela vanliga lager, öka lagringseffektiviteten. Till exempel flera bilder i olika databaser kan dela samma grundläggande Alpine Linux lager, men bara en kopia av skiktet lagras i registret.

Layer delning optimerar dessutom layer-distribution till noder med flera avbildningar som delar vanliga lager. Om en avbildning som redan finns på en nod innehåller Alpine Linux-lagret som bas, överföra inte den efterföljande pull av en annan avbildning som refererar till samma lager till exempel lagret till noden. I stället den refererar till det lagret som redan finns på noden.

### <a name="manifest"></a>Manifest

Varje behållaravbildning som skickas till ett behållarregister är associerad med en *manifest*. Manifestet, som genereras av registret när avbildningen skickas, identifierar bilden och anger dess lager. Du kan visa manifest för en databas med Azure CLI-kommando [az acr databasen show-manifest][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Till exempel visa en lista över manifestet Överför sammanfattningar för ”acr-helloworld”-lagringsplatsen:

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

Manifestet som beskrivs här skiljer sig från avbildningsfest som du kan visa i Azure portal eller med [docker manifest inspektera][docker-manifest-inspect]. I följande avsnitt, ”manifest sammanfattad” avser sammanfattad som genererats av push-åtgärden inte den *config.digest* i manifestet bild. Du kan hämta och ta bort avbildningar från **manifest sammanfattad**, inte config.digest. Följande bild illustrerar de två typerna av sammandrag.

![Manifest sammanfattad och config.digest i Azure portal][manifest-digest]

### <a name="manifest-digest"></a>Manifest sammanfattad

Manifest identifieras med en unik SHA-256-hash eller *manifest sammanfattad*. Varje avbildning--identifieras om taggade eller inte – av dess sammanfattad. Sammanfattad värdet är unikt, även om bildens lagerdata är identisk med en annan bild. Den här mekanismen är vad låter dig upprepade gånger identiskt taggade bilder till ett register. Exempel: du kan skicka flera gånger `myimage:latest` till registret felfritt eftersom varje avbildning har identifierats av dess unika sammanfattad.

Du kan hämta en avbildning från ett register genom att ange dess sammanfattad i pull-åtgärd. Vissa system kan konfigureras för att hämta av sammanfattad eftersom det garanterar versionsnumret för avbildningen som hämtas, även om ett identiskt taggade avbildningen skickas sedan till registret.

Till exempel hämta en avbildning från lagringsplatsen ”acr-helloworld” av manifest sammanfattad:

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> Om du överför flera gånger ändrade avbildningar med identiska taggar, kan du skapa överblivna bilder--bilder som är ej taggade, men fortfarande förbruka utrymme i registret. Ej taggade bilder visas inte i Azure CLI eller i Azure-portalen när du listar eller visa bilder efter tagg. Men lagren fortfarande finns och förbruka utrymme i registret. Den [ta bort ej taggade bilder](#delete-untagged-images) i den här artikeln beskriver frigöra utrymme som används av ej taggade bilder.

## <a name="delete-image-data"></a>Ta bort avbildningsdata

Du kan ta bort avbildningsdata från behållarregistret på flera olika sätt:

* Ta bort en [databasen](#delete-repository): Tar bort alla avbildningar och alla unika lager i databasen.
* Ta bort genom att [taggen](#delete-by-tag): Tar bort en avbildning, taggen, alla unika lager som refereras av avbildningen och alla andra taggar som är kopplade till avbildningen.
* Ta bort genom att [manifest sammanfattad](#delete-by-manifest-digest): Tar bort en avbildning, alla unika lager som refereras av avbildningen och alla taggar som är kopplade till avbildningen.

## <a name="delete-repository"></a>Ta bort lagringsplats

Tar bort en lagringsplats alla avbildningar i databasen, inklusive alla taggar, unika lager och manifest. När du tar bort en databas kan du återställa det lagringsutrymme som används av avbildningar som fanns i databasen.

I följande Azure CLI kommando tar bort ”acr-helloworld”-databasen och alla taggar och manifest i databasen. Om lager som refererar till borttagna manifest inte refereras av några andra bilder i registret, raderas också sina layer-data.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Ta bort efter tagg

Du kan ta bort enskilda bilder från en databas genom att ange Lagringsplatsens namn och tagg i åtgärden ta bort. När du tar bort efter tagg kan återställa du lagringsutrymmet som används av alla unika lager i avbildningen (lager som inte delas med andra avbildningar i registret).

Ta bort efter tagg genom att använda [az acr databasen delete] [ az-acr-repository-delete] och ange avbildningens namn i den `--image` parametern. Alla lager som är unika för avbildningen och eventuella andra taggar som är kopplade till avbildningen tas bort.

Till exempel tar bort den ”acr-helloworld:latest” avbildningen från registret ”myregistry”:

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> Tar bort *efter tagg* bör inte förväxlas med att ta bort en tagg (tar bort taggen). Du kan ta bort en tagg med Azure CLI-kommando [az acr databasen taggen][az-acr-repository-untag]. Inget utrymme har frigjorts när du ta bort taggen en bild eftersom dess [manifest](#manifest) och lagerdata är kvar i registret. Bara själva referensen tagg har tagits bort.

## <a name="delete-by-manifest-digest"></a>Ta bort av manifest sammanfattad

En [manifest sammanfattad](#manifest-digest) kan associeras med en, ingen eller flera taggar. När du tar bort av sammanfattad raderas alla taggar som refereras av manifestet, eftersom lagerdata för alla lager som är unika för avbildningen. Delade layer data inte tas bort.

Om du vill ta bort genom att sammanfattad, överför första listan manifestet sammanfattningar för den lagringsplats som innehåller bilder som du vill ta bort. Exempel:

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
  }
]
```

Ange sedan den samling som du vill ta bort i den [az acr databasen delete] [ az-acr-repository-delete] kommando. Formatet för kommandot är:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Exempelvis kan du ta bort senaste manifestet visas i föregående utdata (med taggen ”v2”):

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

Den `acr-helloworld:v2` avbildningen tas bort från registret, som är alla lagerdata som är unika för avbildningen. Om ett manifest är associerad med flera taggar, raderas också alla tillhörande taggar.

### <a name="list-digests-by-timestamp"></a>Lista sammandrag av tidsstämpel

Om du vill underhålla storleken på en lagringsplats eller registret kan behöva du regelbundet ta bort manifestet sammandrag som är äldre än ett visst datum.

Följande Azure CLI-kommando visar en lista över alla manifest sammanfattad i en databas som är äldre än en angiven tidsstämpel, i stigande ordning. Ersätt `<acrName>` och `<repositoryName>` med värden som är lämpliga för din miljö. Tidsstämpeln kan vara ett fullständigt datum / tid-uttryck eller ett datum, som i följande exempel.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

### <a name="delete-digests-by-timestamp"></a>Ta bort sammandrag av tidsstämpel

När du har identifierat inaktuella manifest sammandrag, kan du köra följande Bash-skript för att ta bort manifestet sammandrag som är äldre än en angiven tidsstämpel. Det krävs Azure CLI och **xargs**. Skriptet utför som standard inga borttagning. Ändra den `ENABLE_DELETE` värde att `true` att aktivera avbildningen tas bort.

> [!WARNING]
> Använd följande exempelskript med försiktighet--har tagits bort avbildningsdata är UNRECOVERABLE. Om du har system som hämta avbildningar av manifest sammanfattad (i stället för avbildningsnamn) kan köra du inte skripten. Tar bort manifestet sammandrag hindrar dessa system från att hämta avbildningar från ditt register. Överväg att använda i stället för att hämta av manifest, en *unika taggning* schema, en [rekommenderad bästa praxis][tagging-best-practices]. 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Ta bort ej taggade bilder

Som vi nämnde i den [Manifest sammanfattad](#manifest-digest) avsnittet push-överför en ändrad avbildning med en befintlig tagg **untags** tidigare publicerade avbildningen, vilket resulterar i en bild av överblivna (eller ”överflödiga”). Tidigare publicerade avbildningens manifest-- och dess lagerdata--finns kvar i registret. Överväg följande sekvens med händelser:

1. Push-överför avbildningen *acr-helloworld* med tagg **senaste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för lagringsplats *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Ändra *acr-helloworld* Dockerfile
1. Push-överför avbildningen *acr-helloworld* med tagg **senaste**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för lagringsplats *acr-helloworld*:

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Som du ser i utdata från det sista steget i sekvensen har nu en överblivna manifest vars `"tags"` egenskapen är en tom lista. Den här manifestet finns fortfarande i registret, tillsammans med unika layer data som den refererar till. **Att ta bort, till exempel frånkopplade avbildningar och deras layer, måste du ta bort av manifest sammanfattad**.

### <a name="list-untagged-images"></a>Lista över ej taggade bilder

Du kan visa alla ej taggade bilder i databasen med hjälp av följande Azure CLI-kommando. Ersätt `<acrName>` och `<repositoryName>` med värden som är lämpliga för din miljö.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

### <a name="delete-all-untagged-images"></a>Ta bort alla ej taggade bilder

> [!WARNING]
> Använd följande exempelskript med försiktighet--bort avbildningsdata är UNRECOVERABLE. Om du har system som hämta avbildningar av manifest sammanfattad (i stället för avbildningsnamn) kan köra du inte skripten. Ta bort ej taggade bilder hindrar dessa system från att hämta avbildningar från ditt register. Överväg att använda i stället för att hämta av manifest, en *unika taggning* schema, en [rekommenderad bästa praxis][tagging-best-practices].

**Azure CLI i Bash**

Följande Bash-skript tar bort alla ej taggade bilder från en databas. Det krävs Azure CLI och **xargs**. Skriptet utför som standard inga borttagning. Ändra den `ENABLE_DELETE` värde att `true` att aktivera avbildningen tas bort.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted. Set ENABLE_DELETE=true to enable image deletion."
fi
```

**Azure CLI i PowerShell**

Följande PowerShell-skript tar bort alla ej taggade bilder från en databas. Det krävs PowerShell och Azure CLI. Skriptet utför som standard inga borttagning. Ändra den `$enableDelete` värde att `$TRUE` att aktivera avbildningen tas bort.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted. Set `$enableDelete = `$TRUE to enable image deletion."
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om bildlagring i Azure Container Registry [bildlagring för behållare i Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
