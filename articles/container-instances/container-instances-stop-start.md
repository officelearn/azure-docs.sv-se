---
title: Stoppa eller starta behållargrupp manuellt
description: Lär dig hur du stoppar eller startar en behållargrupp manuellt i Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533427"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Stoppa eller starta behållare manuellt i Azure Container Instances

Inställningen [för omstartsprincip](container-instances-restart-policy.md) för en behållargrupp avgör hur behållarinstansar startar eller stoppas som standard. Du kan åsidosätta standardinställningen genom att manuellt stoppa eller starta en behållargrupp.

## <a name="stop"></a>Stoppa

Stoppa en behållargrupp som körs manuellt, till exempel med kommandot [az container stop][az-container-stop] eller Azure-portalen. För vissa behållararbetsbelastningar kanske du vill stoppa en tidskrävande behållargrupp efter en definierad period för att spara på kostnaderna. 

*När en behållargrupp går in i tillståndet Stoppad avslutas och återvinns alla behållare i gruppen. Det bevarar inte behållartillstånd.*

När behållarna återvinns [frigörs resurserna](container-instances-container-groups.md#resource-allocation) och faktureringsstopp för behållargruppen.

Stoppåtgärden har ingen effekt om behållargruppen redan har avslutats (är i ett lyckades eller misslyckat tillstånd). Till exempel avslutas en behållargrupp med run-once-behållaraktiviteter som kördes i tillståndet Efterföljande. Försök att stoppa gruppen i det tillståndet ändrar inte tillståndet. 

## <a name="start"></a>Start

När en behållargrupp stoppas - antingen för att behållarna avslutas på egen hand eller om du manuellt stoppade gruppen - kan du starta behållarna. Använd till exempel [kommandot az container start][az-container-start] eller Azure-portalen för att manuellt starta behållarna i gruppen. Om behållaravbildningen för en behållare uppdateras hämtas en ny avbildning. 

Starta en behållargrupp börjar en ny distribution med samma behållarkonfiguration. Den här åtgärden kan hjälpa dig att snabbt återanvända en känd konfiguration av behållargrupper som fungerar som förväntat. Du behöver inte skapa en ny behållargrupp för att köra samma arbetsbelastning.

Alla behållare i en behållargrupp startas av den här åtgärden. Du kan inte starta en viss behållare i gruppen.

När du har startat eller starta om en behållargrupp manuellt körs behållargruppen enligt den konfigurerade omstartsprincipen.
  
## <a name="restart"></a>Starta om

Du kan starta om en behållargrupp medan den körs, till exempel genom att använda kommandot [az container restart.][az-container-restart] Den här åtgärden startar om alla behållare i behållargruppen. Om behållaravbildningen för en behållare uppdateras hämtas en ny avbildning. 

Det är praktiskt att starta om en behållargrupp när du vill felsöka ett distributionsproblem. Om till exempel en tillfällig resursbegränsning förhindrar att behållarna körs kan det lösa problemet genom att starta om gruppen.

Alla behållare i en behållargrupp startas om av den här åtgärden. Du kan inte starta om en viss behållare i gruppen.

När du har startat om en behållargrupp manuellt körs behållargruppen enligt den konfigurerade omstartsprincipen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [att starta om principinställningar i](container-instances-restart-policy.md) Azure Container Instances.

Förutom att manuellt stoppa och starta en behållargrupp med den befintliga konfigurationen kan du [uppdatera inställningarna för](container-instances-update.md) en behållare som körs.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
