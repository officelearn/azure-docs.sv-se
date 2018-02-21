---
title: Montera en hemlig volym i Azure Container instanser
description: "Lär dig hur du monterar en hemlig volym för att lagra känslig information för åtkomst av behållarinstanser"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 6f8e1b6faac11b668a143f8013a198831a428c51
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montera en hemlig volym i Azure Container instanser

Lär dig hur du monterar en *hemlighet* volym i dina behållarinstanser för lagring och hämtning av behållare i behållargrupper känslig information.

> [!NOTE]
> Montera en *hemlighet* volym är för närvarande begränsad till Linux-behållare. Under tiden som vi arbetar för att göra alla funktioner tillgängliga för Windows-behållare kan du se de nuvarande skillnaderna mellan plattformarna i informationen om [kvoter och regional tillgänglighet för Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>Hemlig volym

Du kan använda en *hemlighet* volymen för att tillhandahålla känslig information till behållare i en grupp i behållaren. Den *hemlighet* volym lagrar dina angivna hemligheter i filer på volymen behållare i behållargruppen kan sedan komma åt den. Med hjälp av hemligheter i en *hemlighet* volym, undvika att placera känsliga data som SSH-nycklar eller Databasautentiseringsuppgifter i programkoden.

Alla *hemlighet* volymer backas upp av [tmpfs][tmpfs], ett filsystem som backas upp av RAM; innehållet skrivs aldrig till beständigt minne.

## <a name="mount-a-secret-volume"></a>Montera en hemlig volym

Montera en *hemlighet* volym i en behållare instans, måste du distribuera med hjälp av en [Azure Resource Manager-mall](/azure/templates/microsoft.containerinstance/containergroups).

Först fylla i `volumes` matris i behållargruppen `properties` avsnitt i mallen. I varje behållare i behållargruppen där du vill montera den *hemlighet* volym, fylla i `volumeMounts` matris i den `properties` avsnitt i definition av behållare.

Till exempel skapas följande Resource Manager-mall en behållare grupp bestående av en enskild behållare. Behållaren monteringar en *hemlighet* volym som består av två Base64-kodad hemligheter.

[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Ett exempel på distribution av behållare-instans med en Azure Resource Manager-mall finns i [distribuera flera behållare grupper i Azure Behållarinstanser](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du monterar andra volymtyper av i Azure Container instanser:

* [Montera en Azure-filresursen i Azure Container instanser](container-instances-volume-azure-files.md)
* [Montera en emptyDir volymen i Azure Container instanser](container-instances-volume-emptydir.md)
* [Montera en gitRepo volym i Azure Container instanser](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs