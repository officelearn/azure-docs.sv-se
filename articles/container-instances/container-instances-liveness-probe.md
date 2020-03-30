---
title: Konfigurera liveness-avsökning på behållarinstans
description: Lär dig hur du konfigurerar liveness-avsökningar för att starta om felaktiga behållare i Azure Container Instances
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 11c6c9d39067c536bf4325f74eb24b2ab64ef515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934167"
---
# <a name="configure-liveness-probes"></a>Konfigurera liveavsökningar

Containeriserade program kan köras under längre tidsperioder, vilket resulterar i brutna tillstånd som kan behöva repareras genom att starta om behållaren. Azure Container Instances stöder liveness-avsökningar så att du kan konfigurera dina behållare i din behållargrupp för att starta om om kritiska funktioner inte fungerar. Den liveness sonden beter sig som en [Kubernetes liveness sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

I den här artikeln beskrivs hur du distribuerar en behållargrupp som innehåller en liveness-avsökning, vilket visar en automatisk omstart av en simulerad felbehållsbehållare.

Azure Container Instances stöder också [beredskapsavsökningar](container-instances-readiness-probe.md), som du kan konfigurera för att säkerställa att trafiken når en behållare endast när den är klar för den.

> [!NOTE]
> För närvarande kan du inte använda en liveness-avsökning i en behållargrupp som distribueras till ett virtuellt nätverk.

## <a name="yaml-deployment"></a>YAML-distribution

Skapa `liveness-probe.yaml` en fil med följande utdrag. Den här filen definierar en behållargrupp som består av en NGNIX-behållare som så småningom blir felaktig.

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

Kör följande kommando för att distribuera den här behållargruppen med ovanstående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Kommandot Start

Distributionen innehåller `command` en egenskap som definierar ett startkommando som körs när behållaren börjar köras. Den här egenskapen accepterar en matris med strängar. Det här kommandot simulerar behållaren som går in i ett feltillstånd.

Först startar en bash session och skapar `healthy` en `/tmp` fil som kallas i katalogen. Den försätts sedan i 30 sekunder innan du tar bort filen och anger sedan en 10-minuters viloläge:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Kommandot Liveness

Den här distributionen definierar en `livenessProbe` som stöder ett `exec` liveness-kommando som fungerar som liveness-kontrollen. Om det här kommandot avslutas med ett värde som inte är `healthy` noll, dödas behållaren och startas om, vilket signalerar att filen inte kunde hittas. Om det här kommandot avslutas med avslutningskod 0 vidtas ingen åtgärd.

Egenskapen `periodSeconds` anger att kommandot liveness ska köras var femte sekund.

## <a name="verify-liveness-output"></a>Verifiera liveness-utdata

Inom de första 30 `healthy` sekunderna finns filen som skapats av startkommandot. När liveness-kommandot söker `healthy` efter filens existens returnerar statuskoden 0, vilket signalerar lyckad, så ingen omstart sker.

Efter 30 sekunder `cat /tmp/healthy` börjar kommandot att misslyckas, vilket gör att ohälsosamma och dödande händelser inträffar.

Dessa händelser kan visas från Azure-portalen eller Azure CLI.

![Felhändelse för portalen][portal-unhealthy]

Genom att visa händelserna i Azure-portalen utlöses händelser av typen `Unhealthy` när liveness-kommandot misslyckas. Den efterföljande händelsen `Killing`är av typen , vilket innebär en borttagning av behållare så att en omstart kan påbörjas. Antalet omstarter för behållaren ökar varje gång den här händelsen inträffar.

Omstarter slutförs på plats så att resurser som offentliga IP-adresser och nodspecifikt innehåll bevaras.

![Räknare för omstart av portal][portal-restart]

Om liveness-avsökningen kontinuerligt misslyckas och utlöser för många omstarter går behållaren in i en exponentiell back-off-fördröjning.

## <a name="liveness-probes-and-restart-policies"></a>Liveness-avsökningar och omstartsprinciper

Omstartsprinciper ersätter omstartsbeteendet som utlöses av liveness-avsökningar. Om du till exempel `restartPolicy = Never` anger en *och* en liveness-avsökning startar inte behållargruppen om på grund av en misslyckad liveness-kontroll. Behållargruppen följer i stället behållargruppens `Never`omstartsprincip för .

## <a name="next-steps"></a>Nästa steg

Aktivitetsbaserade scenarier kan kräva en liveness-avsökning för att aktivera automatiska omstarter om en förbeställd funktion inte fungerar som den ska. Mer information om hur du kör uppgiftsbaserade behållare finns [i Köra behållaruppgifter i Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
