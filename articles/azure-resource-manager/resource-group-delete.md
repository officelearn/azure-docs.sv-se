---
title: Ta bort resursgruppen och resurser – Azure Resource Manager
description: Beskriver hur Azure Resource Manager beställningar borttagningen av resurser när en tar bort en resursgrupp. Beskriver svarskoder samt hur Resource Manager hanterar dem för att fastställa om borttagningen lyckades.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b8c4fdc942af291e912a4c1e74d1292279cf9f8c
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132337"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Azure Resource Manager resource borttagning av

Den här artikeln beskriver hur Azure Resource Manager beställningar borttagningen av resurser när du tar bort en resursgrupp.

## <a name="determine-order-of-deletion"></a>Fastställa ordningen för borttagning

När du tar bort en resursgrupp, anger Resource Manager den för att ta bort resurser. Den använder följande ordning:

1. Alla underordnade (kapslade) resurser tas bort.

2. Resurser som hanterar andra resurser tas bort nästa. En resurs kan ha den `managedBy` egenskapen in för att ange att en annan resurs hanterar den. När den här egenskapen anges tas den resurs som hanterar andra resursen bort innan de övriga resurserna.

3. De återstående resurserna tas bort efter de föregående två kategorierna.

## <a name="resource-deletion"></a>Resursborttagning

När ordningen bestäms utfärdar en borttagningsåtgärd för varje resurs i Resource Manager. Det väntar några beroenden som ska slutföras innan du fortsätter.

För synkrona åtgärder är förväntade lyckat svar koderna:

* 200
* 204
* 404

För asynkrona åtgärder är det förväntade lyckat svaret 202. Resource Manager spårar location-huvudet eller azure-asynkrona åtgärden huvudet att bestämma status för asynkron borttagningen.
  
### <a name="errors"></a>Fel

När en borttagningsåtgärd returnerar ett fel, försöker Resource Manager ta bort anropet. Återförsök sker för 5xx, 429 och 408 statuskoder. Som standard är tidsperioden för återförsök 15 minuter.

## <a name="after-deletion"></a>Efter borttagningen

Resource Manager skickar en GET-anrop för varje resurs som den försökte ta bort. Svaret på GET-anrop anses vara 404. När Resource Manager hämtar 404, som de anser att borttagningen har slutförts. Resource Manager tar bort resursen från sin cache.

Om GET-anrop på resursen returnerar 200 eller 201, återskapas Resource Manager resursen.

### <a name="errors"></a>Fel

Om åtgärden GET returnerar ett fel, försöker Resource Manager GET för följande felkod:

* Mindre än 100
* 408
* 429
* Större än 500

För andra felkoder inte Resource Manager borttagningen av resursen.

## <a name="next-steps"></a>Nästa steg

* Information om Resource Manager-begrepp finns i [översikt över Azure Resource Manager](resource-group-overview.md).
* Ta bort kommandon finns i [PowerShell](/powershell/module/azurerm.resources/Remove-AzureRmResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete), och [REST API](/rest/api/resources/resourcegroups/delete).
