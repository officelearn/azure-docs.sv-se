---
title: Konfigurera liveness avsökningar i Azure Container instanser
description: Lär dig hur du konfigurerar liveness avsökningar att starta om feltillstånd behållare i Azure Container instanser
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 76ca4db28d99702532ae656a19f0d54b479a13fe
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35249382"
---
# <a name="configure-liveness-probes"></a>Konfigurera liveness avsökningar

Container program köra under långa perioder som resulterar i brutna tillstånd som kan behöva repareras genom att starta om behållaren. Azure Behållarinstanser stöder liveness avsökningar om du vill inkludera konfigurationer så att din behållare kan starta om om viktiga funktioner inte fungerar. 

Den här artikeln förklarar hur du distribuerar en behållare-grupp som innehåller en liveness avsökning visar automatisk omstart av en simulerad ohälsosamt behållare.

## <a name="yaml-deployment"></a>YAML-distribution

Skapa en `liveness-probe.yaml` fil med följande kodavsnitt. Den här filen definierar en grupp i behållaren som består av en NGNIX-behållare som slutligen blir ohälsosamt. 

```yaml
apiVersion: 2018-06-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command: 
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Never
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera till konfigurationen ovan YAML behållargruppen:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Starta kommando

Distributionen definierar ett första kommando som ska köras när behållaren först startas körs, definieras av den `command` -egenskap som accepterar en matris med strängar. I det här exemplet startar en bash-session den och skapa en fil med namnet `healthy` inom den `/tmp` katalogen genom att skicka det här kommandot:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Den kommer sedan vila i 30 sekunder innan du tar bort filen och sedan försätts i strömsparläge en 10 minuter.

### <a name="liveness-command"></a>Liveness kommando

Den här distributionen definierar en `livenessProbe` som har stöd en `exec` liveness kommando som fungerar som liveness-kontrollen. Om det här kommandot avslutas med ett värde som inte är noll, behållaren avslutats och startas om, signalering den `healthy` filen kunde inte hittas. Om det här kommandot har avslutas med slutkoden 0, vidtas ingen åtgärd.

Den `periodSeconds` egenskapen anger liveness kommandot ska köras var femte sekund.

## <a name="verify-liveness-output"></a>Kontrollera liveness utdata

I de första 30 sekunderna den `healthy` filen som skapas av startkommandot finns. När kommandot liveness söker efter den `healthy` filens existens statuskoden returnerar ett noll-signalering lyckades, så ingen omstart sker.

Efter 30 sekunder det `cat /tmp/healthy` börjar misslyckas, orsakar feltillstånd och avlivas händelser inträffar. 

Dessa händelser kan visas från Azure-portalen eller Azure CLI 2.0.

![Portalen ohälsosam händelse][portal-unhealthy]

Genom att visa händelser i Azure-portalen händelser av typen `Unhealthy` ska utlösas vid liveness kommandot misslyckas. Efterföljande händelsen ska vara av typen `Killing`, så att en omstart kan börja som betecknar en behållare för borttagning. Antalet omstarter för behållaren ökar varje gång detta inträffar.

Omstart är slutförda på plats så att resurser som offentliga IP-adresser och nodspecifik innehållet kommer att bevaras.

![Portalen omstart räknare][portal-restart]

Om liveness avsökningen misslyckas kontinuerligt och utlöser för många omstarter, anges din behållare en exponentiell tillbaka av fördröjning.

## <a name="liveness-probes-and-restart-policies"></a>Liveness-avsökningar och starta om principer

Starta om principer ersätter omstartsbeteende som utlöses av liveness avsökningar. Till exempel om du ställer in en `restartPolicy = Never` *och* en liveness avsökning behållargruppen startar inte om vid en misslyckad liveness-kontroll. Behållargruppen kommer att i stället följa principen för behållargruppen omstart av `Never`.

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier kan kräva en liveness avsökningen tom aktivera automatiska omstarter om en funktion som krävs inte fungerar korrekt. Mer information om hur du kör uppgiftsbaserade behållare finns [köra av uppgifter i Azure Behållarinstanser](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png