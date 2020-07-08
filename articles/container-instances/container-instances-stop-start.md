---
title: Stoppa eller starta behållar gruppen manuellt
description: Lär dig hur du stoppar eller startar en behållar grupp manuellt i Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74533427"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Stoppa eller starta containrar manuellt i Azure Container Instances

Inställningen för att [starta om principen](container-instances-restart-policy.md) för en behållar grupp avgör hur behållar instanser startar eller stoppar som standard. Du kan åsidosätta standardinställningen genom att manuellt stoppa eller starta en behållar grupp.

## <a name="stop"></a>Stoppa

Stoppa en behållare som körs manuellt, till exempel genom att använda kommandot [AZ container Stop][az-container-stop] eller Azure Portal. För vissa behållar arbets belastningar kanske du vill stoppa en långvarig behållar grupp efter en definierad period för att spara pengar. 

*När en behållar grupp går in i stoppat tillstånd, avslutar den och återvinner alla behållare i gruppen. Den bevarar inte behållar tillstånd.*

När behållarna återvinns frigörs [resurserna](container-instances-container-groups.md#resource-allocation) och faktureringen stoppas för behållar gruppen.

Åtgärden stoppa har ingen verkan om behållar gruppen redan har avslut ATS (är i ett tillstånd där det lyckades eller misslyckades). Till exempel en behållar grupp med uppgifter för kör en och samma behållare som har slutförts i läget lyckades. Försök att stoppa gruppen i detta tillstånd ändrar inte statusen. 

## <a name="start"></a>Start

När en behållar grupp stoppas – antingen på grund av att behållarna avbröts på egen hand eller om du stoppade gruppen manuellt, kan du starta behållarna. Använd exempelvis [Start kommandot AZ container][az-container-start] eller Azure Portal för att starta behållarna manuellt i gruppen. Om behållar avbildningen för en behållare uppdateras, hämtas en ny avbildning. 

Om du startar en behållar grupp påbörjas en ny distribution med samma behållar konfiguration. Den här åtgärden kan hjälpa dig att snabbt återanvända en känd container Group-konfiguration som fungerar som förväntat. Du behöver inte skapa en ny behållar grupp för att köra samma arbets belastning.

Alla behållare i en behållar grupp startas av den här åtgärden. Det går inte att starta en speciell behållare i gruppen.

När du manuellt startar eller startar om en behållar grupp körs behållar gruppen enligt den konfigurerade principen för omstart.
  
## <a name="restart"></a>Starta om

Du kan starta om en behållar grupp medan den körs – till exempel genom att använda kommandot [AZ container restart][az-container-restart] . Den här åtgärden startar om alla behållare i behållar gruppen. Om behållar avbildningen för en behållare uppdateras, hämtas en ny avbildning. 

Att starta om en behållar grupp är användbart när du vill felsöka ett distributions problem. Om till exempel en temporär resurs begränsning förhindrar att behållarna körs kan det lösa problemet genom att starta om gruppen.

Alla behållare i en behållar grupp startas om med den här åtgärden. Du kan inte starta om en speciell behållare i gruppen.

När du startar om en behållar grupp manuellt körs behållar gruppen enligt den konfigurerade principen för omstart.

## <a name="next-steps"></a>Nästa steg

Läs mer om att [starta om princip inställningar](container-instances-restart-policy.md) i Azure Container instances.

Förutom att manuellt stoppa och starta en behållar grupp med den befintliga konfigurationen kan du [uppdatera inställningarna](container-instances-update.md) för en behållar grupp som körs.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
