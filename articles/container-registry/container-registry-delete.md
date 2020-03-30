---
title: Ta bort bildresurser
description: Information om hur du effektivt hanterar registerstorlek genom att ta bort behållaravbildningsdata med Azure CLI-kommandon.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403346"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Ta bort behållaravbildningar i Azure Container Registry med Azure CLI

Om du vill behålla storleken på ditt Azure-behållarregister bör du regelbundet ta bort inaktuella avbildningsdata. Vissa behållaravbildningar som distribueras i produktion kan kräva mer långsiktig lagring, men andra kan vanligtvis tas bort snabbare. I ett automatiskt bygg- och testscenario kan registret till exempel snabbt fyllas med avbildningar som kanske aldrig distribueras och kan rensas strax efter att bygg- och testpasset har slutförts.

Eftersom du kan ta bort bilddata på flera olika sätt är det viktigt att förstå hur varje borttagning påverkar lagringsanvändningen. Den här artikeln innehåller flera metoder för att ta bort bilddata:

* Ta bort en [databas](#delete-repository): Tar bort alla bilder och alla unika lager i databasen.
* Ta bort efter [tagg:](#delete-by-tag)Tar bort en bild, taggen, alla unika lager som refereras av bilden och alla andra taggar som är associerade med bilden.
* Ta bort efter [manifestsammanfattning:](#delete-by-manifest-digest)Tar bort en bild, alla unika lager som refereras av bilden och alla taggar som är associerade med bilden.

Exempelskript tillhandahålls för att automatisera borttagningsåtgärder.

En introduktion till dessa begrepp finns i [Om register, databaser och bilder](container-registry-concepts.md).

## <a name="delete-repository"></a>Ta bort databas

Om du tar bort en databas tas alla bilder bort i databasen, inklusive alla taggar, unika lager och manifest. När du tar bort en databas återställer du lagringsutrymmet som används av bilderna som refererar till unika lager i databasen.

Följande Azure CLI-kommando tar bort databasen "acr-helloworld" och alla taggar och manifest i databasen. Om lager som refereras av de borttagna manifesten inte refereras av några andra avbildningar i registret, tas även deras lagerdata bort, vilket återställer lagringsutrymmet.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Ta bort efter tagg

Du kan ta bort enskilda bilder från en databas genom att ange databasens namn och tagg i borttagningsåtgärden. När du tar bort efter tagg återställer du lagringsutrymmet som används av unika lager i avbildningen (lager som inte delas av andra avbildningar i registret).

Om du vill ta bort efter tagg använder du `--image` ta bort [az-akr-databasen][az-acr-repository-delete] och anger bildnamnet i parametern. Alla lager som är unika för bilden och alla andra taggar som är associerade med bilden tas bort.

Till exempel ta bort "acr-helloworld: senaste" bild från registret "mitt register":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Ta bort *av taggen* bör inte förväxlas med att ta bort en tagg (untagging). Du kan ta bort en tagg med azure CLI-kommandot [az acr repository untag][az-acr-repository-untag]. Inget utrymme frigörs när du avtaggar en avbildning eftersom [dess manifest-](container-registry-concepts.md#manifest) och lagerdata finns kvar i registret. Endast själva taggreferensen tas bort.

## <a name="delete-by-manifest-digest"></a>Ta bort efter manifestsammandrag

En [manifestsammanfattning](container-registry-concepts.md#manifest-digest) kan associeras med en, ingen eller flera taggar. När du tar bort efter sammanfattning tas alla taggar som refereras av manifestet bort, liksom lagerdata för alla lager som är unika för bilden. Delade lagerdata tas inte bort.

Om du vill ta bort efter sammanfattning listar du först manifestsammandraget för databasen som innehåller de bilder som du vill ta bort. Ett exempel:

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
  }
]
```

Ange sedan den sammanfattning som du vill ta bort i kommandot [az acr-databasborttagning.][az-acr-repository-delete] Formatet för kommandot är:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Om du till exempel vill ta bort det sista manifestet som visas i föregående utdata (med taggen "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

Avbildningen `acr-helloworld:v2` tas bort från registret, liksom alla lagerdata som är unika för den avbildningen. Om ett manifest är associerat med flera taggar tas även alla associerade taggar bort.

## <a name="delete-digests-by-timestamp"></a>Ta bort sammanfattningar efter tidsstämpel

Om du vill behålla storleken på en databas eller ett register kan du behöva ta bort manifestsammanfattningar som är äldre än ett visst datum.

Följande Azure CLI-kommando listar alla manifestsammanfattning i en databas som är äldre än en angiven tidsstämpel i stigande ordning. Ersätt `<acrName>` `<repositoryName>` och med värden som är lämpliga för din miljö. Tidsstämpeln kan vara ett fullständigt datumtidsuttryck eller ett datum, som i det här exemplet.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

När du har identifierat inaktuella manifestsammanfattningar kan du köra följande Bash-skript för att ta bort manifestsammanfattningar som är äldre än en angiven tidsstämpel. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra `ENABLE_DELETE` värdet `true` så att bildborttagningen kan aktiveras.

> [!WARNING]
> Använd följande exempelskript med försiktighet - borttagna bilddata är oåterkallelig. Om du har system som hämtar bilder genom manifestsammandrag (i motsats till bildnamn) bör du inte köra dessa skript. Om du tar bort manifestsammanfattningar förhindras att dessa system drar avbildningarna från registret. Istället för att dra genom manifest, överväga att anta ett *unikt taggningsschema,* en [rekommenderad bästa praxis](container-registry-image-tag-version.md). 

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

## <a name="delete-untagged-images"></a>Ta bort otaggade bilder

Som nämnts i [manifestsammandraget](container-registry-concepts.md#manifest-digest) avsnitt, trycka en modifierad bild med hjälp av en befintlig tagg **untags** den tidigare skjuts bilden, vilket resulterar i en övergiven (eller "dinglande") bild. Den tidigare pushade avbildningens manifest – och dess lagerdata – finns kvar i registret. Tänk på följande händelseförlopp:

1. Push bild *acr-helloworld* med tagg **senaste:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för repository *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
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
1. Push bild *acr-helloworld* med tagg **senaste:**`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Kontrollera manifest för repository *acr-helloworld:*

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
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

Som du kan se i utdata från det sista steget i `"tags"` sekvensen finns det nu ett övergivet manifest vars egenskap är en tom lista. Det här manifestet finns fortfarande i registret, tillsammans med alla unika lagerdata som refereras till. **Om du vill ta bort sådana överblivna bilder och deras lagerdata måste du ta bort genom manifestsammandrag .**

## <a name="delete-all-untagged-images"></a>Ta bort alla otaggade bilder

Du kan lista alla otaggade avbildningar i databasen med följande Azure CLI-kommando. Ersätt `<acrName>` `<repositoryName>` och med värden som är lämpliga för din miljö.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Med det här kommandot i ett skript kan du ta bort alla otaggade bilder i en databas.

> [!WARNING]
> Använd följande exempelskript med försiktighet - borttagna bilddata är oåterkalleliga. Om du har system som hämtar bilder genom manifestsammandrag (i motsats till bildnamn) bör du inte köra dessa skript. Om du tar bort otaggade avbildningar hindras dessa system från att dra bilderna från registret. Istället för att dra genom manifest, överväga att anta ett *unikt taggningsschema,* en [rekommenderad bästa praxis](container-registry-image-tag-version.md).

**Azure CLI i Bash**

Följande Bash-skript tar bort alla otaggade bilder från en databas. Det kräver Azure CLI och **xargs**. Som standard utför skriptet ingen borttagning. Ändra `ENABLE_DELETE` värdet `true` så att bildborttagningen kan aktiveras.

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
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Azure CLI i PowerShell**

Följande PowerShell-skript tar bort alla otaggade bilder från en databas. Det kräver PowerShell och Azure CLI. Som standard utför skriptet ingen borttagning. Ändra `$enableDelete` värdet `$TRUE` så att bildborttagningen kan aktiveras.

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
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Rensa automatiskt taggar och manifest (förhandsversion)

Som ett alternativ till skript azure CLI-kommandon kör du en on-demand- eller schemalagd ACR-aktivitet för att ta bort alla taggar som är äldre än en viss varaktighet eller matchar ett angivet namnfilter. Mer information finns i [Rensa avbildningar automatiskt från ett Azure-behållarregister](container-registry-auto-purge.md).

Du kan också ange en [bevarandeprincip](container-registry-retention-policy.md) för varje register för att hantera otaggade manifest. När du aktiverar en bevarandeprincip tas avbildningsmanifest i registret som inte har några associerade taggar och de underliggande lagerdata automatiskt bort efter en viss period.

## <a name="next-steps"></a>Nästa steg

Mer information om avbildningslagring i Azure Container Registry finns [i Lagringsavbildningslagring i Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
