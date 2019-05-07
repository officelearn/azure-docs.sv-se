---
title: Manuellt stoppa eller starta en behållare i Azure Container Instances
description: Lär dig hur du manuellt stoppa och starta en behållargrupp i Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 8e62d106a42dfbec897e5e14cf68fd3d7fd823c4
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65070815"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Manuellt stoppa eller starta en behållare i Azure Container Instances

Den [omstartsprincip](container-instances-restart-policy.md) inställningen för en behållargrupp avgör hur behållarinstanser starta eller stoppa som standard. Du kan åsidosätta standardinställningen genom att stoppa eller starta en behållargrupp manuellt.

## <a name="stop"></a>Stoppa

Stoppa en behållargrupp som körs – till exempel med hjälp av den [az container stoppa] [ az-container-stop] kommando eller Azure-portalen. För vissa arbetsbelastningar, kan du stoppa en tidskrävande behållargrupp när du har en definierad period att sänka kostnaderna. 

*När en behållargrupp anger tillståndet Stoppad, avslutas och återanvänds alla behållare i gruppen. Behållartillstånd bevaras inte.*

När behållarna har återvunnits, den [resurser](container-instances-container-groups.md#resource-allocation) frigörs och fakturering stoppas för behållargruppen.

Stop-åtgärd har ingen effekt om behållargruppen avslutas redan (är i tillståndet för en lyckades eller misslyckades). Till exempel avbryter en behållargrupp med uppgifter som körs en gång behållare som har körts med tillståndet lyckades. Försöker stoppa gruppen eftersom tillståndet inte ändrar tillståndet. 

## <a name="start"></a>Start

När en behållargrupp har stoppats - kan antingen eftersom behållarna avbröts på egen hand eller manuellt stoppad grupp – du börja behållarna. Till exempel använda den [az container start] [ az-container-start] kommando eller Azure-portalen för att manuellt starta behållarna i gruppen. Om behållaravbildning för alla behållare uppdateras, hämtas en ny avbildning. 

Starta en behållargrupp börjar en ny distribution med samma behållarkonfiguration för. Med hjälp av den här åtgärden kan du snabbt återanvända en grupp kända container-konfiguration som fungerar som förväntat. Du behöver att skapa en ny behållargrupp som kör samma arbetsbelastning.

Alla behållare i en behållargrupp har startats av den här åtgärden. Du kan inte starta en specifik behållare i gruppen.

Starta om principen när du manuellt starta eller starta om en behållargrupp behållare grupp körs enligt det konfigurerade.
  
## <a name="restart"></a>Starta om

Du kan starta om en behållargrupp när den körs – till exempel med hjälp av den [az container omstart] [ az-container-restart] kommando. Den här åtgärden startar om alla behållare i behållargruppen. Om behållaravbildning för alla behållare uppdateras, hämtas en ny avbildning. 

Starta om en behållargrupp är användbart när du vill felsöka ett distributionsproblem. Till exempel om en temporär begränsningen förhindrar att dina behållare körs, kan startar om gruppen lösa problemet.

Alla behållare i en behållargrupp startas om av den här åtgärden. Du kan inte starta om en specifik behållare i gruppen.

När du manuellt startar om en behållargrupp omstartsprincip behållare grupp körs enligt det konfigurerade.

## <a name="next-steps"></a>Nästa steg

Läs mer om [starta om principinställningar](container-instances-restart-policy.md) i Azure Container Instances.

Utöver manuellt stoppa och starta en behållargrupp med den befintliga konfigurationen, kan du [uppdatera inställningarna för](container-instances-update.md) för en behållargrupp som körs.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
