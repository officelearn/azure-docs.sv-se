---
title: Konfigurera direktmigreringens avsökning på behållar instansen
description: Lär dig hur du konfigurerar direktmigreringens avsökningar för att starta om felaktiga behållare i Azure Container Instances
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934167"
---
# <a name="configure-liveness-probes"></a>Konfigurera liveavsökningar

Program i behållare kan köras under längre tids perioder, vilket resulterar i brutna tillstånd som kan behöva repare ras genom att starta om behållaren. Azure Container Instances stöder stöd för direktmigrering så att du kan konfigurera dina behållare i din behållar grupp för omstart om kritiska funktioner inte fungerar. Direktmigreringen avsökningen fungerar som en Kubernetes för [Direktmigrering](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

I den här artikeln förklaras hur du distribuerar en behållar grupp som innehåller en livemigrering, som demonstrerar den automatiska omstarten av en simulerad ej hälso behållar behållare.

Azure Container Instances också stöd för [beredskaps avsökningar](container-instances-readiness-probe.md), som du kan konfigurera för att säkerställa att trafiken når en behållare endast när den är redo för den.

> [!NOTE]
> För närvarande kan du inte använda en direktmigreringens avsökning i en behållar grupp som distribuerats till ett virtuellt nätverk.

## <a name="yaml-deployment"></a>YAML-distribution

Skapa en `liveness-probe.yaml`-fil med följande kodfragment. Den här filen definierar en behållar grupp som består av en NGNIX-behållare som slutligen blir skadad.

```yaml
apiVersion: 2018-10-01
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
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera den här behållar gruppen med ovanstående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start kommando

Distributionen innehåller en `command`-egenskap som definierar ett start kommando som körs när behållaren först börjar köras. Den här egenskapen accepterar en sträng mat ris. Det här kommandot simulerar att behållaren inträder i ett ohälsosamt tillstånd.

Först startar den en bash-session och skapar en fil med namnet `healthy` i katalogen `/tmp`. Sedan försätts den i 30 sekunder innan filen tas bort, och sedan anges en 10 minuters vilo läge:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Lives-kommandot

Den här distributionen definierar ett `livenessProbe` som stöder ett `exec` Live-kommando som fungerar som en Live-kontroll. Om det här kommandot avslutas med ett värde som inte är noll stoppas behållaren och startas om, vilket innebär att det inte gick att hitta den `healthy` filen. Om kommandot avslutas med slut koden 0 vidtas ingen åtgärd.

Egenskapen `periodSeconds` anger att kommandot Lives ska köras var femte sekund.

## <a name="verify-liveness-output"></a>Verifiera Live-utdata

Under de första 30 sekunderna finns `healthy`-filen som skapats av Start kommandot. När Live-kommandot söker efter den `healthy` filen returnerar status koden 0, signalering lyckades, så att ingen omstart sker.

Efter 30 sekunder börjar `cat /tmp/healthy` kommandot att fungera, vilket orsakar att det uppstår fel och att dödande händelser inträffar.

Dessa händelser kan visas från Azure Portal eller Azure CLI.

![Felaktig händelse för portalen][portal-unhealthy]

Genom att visa händelserna i Azure Portal utlöses händelser av typen `Unhealthy` när Live-kommandot avbryts. Den efterföljande händelsen är av typen `Killing`och betecknar en behållar borttagning så att en omstart kan påbörjas. Antalet omstarter för containern ökar varje tidpunkt som den här händelsen inträffar.

Omstarter slutförs på plats så att resurser som offentliga IP-adresser och Node-/regionsspecifika innehåll bevaras.

![Räknare för att starta om portalen][portal-restart]

Om den kontinuerliga avsökningen Miss lyckas och utlöser för många omstarter, kommer din behållare att övergå till en exponentiell säkerhets fördröjning.

## <a name="liveness-probes-and-restart-policies"></a>Direktmigreringar och principer för omstart

Restart-principer ersätter omstarts beteendet som utlöses av direktmigreringar. Om du till exempel ställer in en `restartPolicy = Never` *och* en Direktmigrering, kommer behållar gruppen inte att starta om på grund av en misslyckad direktmigreringens kontroll. Behållar gruppen följer i stället behållar gruppens omstarts princip för `Never`.

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier kan kräva en Direktmigrering för att aktivera automatiska omstarter om en förutsättnings funktion inte fungerar korrekt. Mer information om att köra uppgiftsbaserade behållare finns [i köra aktiviteter i behållare i Azure Container instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
