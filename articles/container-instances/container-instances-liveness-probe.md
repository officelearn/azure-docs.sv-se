---
title: Konfigurera direktmigreringens avsökningar i Azure Container Instances
description: Lär dig hur du konfigurerar direktmigreringens avsökningar för att starta om felaktiga behållare i Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.openlocfilehash: 28205d6db85d7a5051f283445d95dd2375e174c8
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325869"
---
# <a name="configure-liveness-probes"></a>Konfigurera liveavsökningar

Program i behållare kan köras under längre tids perioder som resulterar i brutna tillstånd som kan behöva repare ras genom att du startar om behållaren. Azure Container Instances stöder direktmigreringar för att inkludera konfigurationer så att din behållare kan startas om om kritiska funktioner inte fungerar.

I den här artikeln förklaras hur du distribuerar en behållar grupp som innehåller en livemigrering, som demonstrerar den automatiska omstarten av en simulerad ej hälso behållar behållare.

## <a name="yaml-deployment"></a>YAML-distribution

Skapa en `liveness-probe.yaml` fil med följande kodfragment. Den här filen definierar en behållar grupp som består av en NGNIX-behållare som slutligen blir skadad.

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
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Kör följande kommando för att distribuera den här behållar gruppen med ovanstående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Start kommando

Distributionen definierar ett start kommando som ska köras när behållaren först börjar köras, som definieras av `command` egenskapen som accepterar en sträng mat ris. I det här exemplet startar en bash-session och skapar en fil som heter `healthy` `/tmp` i katalogen genom att skicka det här kommandot:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Den kommer sedan att gå i vilo läge i 30 sekunder innan filen tas bort, och anger sedan en 10 minuters vilo läge.

### <a name="liveness-command"></a>Lives-kommandot

Den här distributionen definierar `livenessProbe` ett som stöder `exec` ett Live-kommando som fungerar som en Live-kontroll. Om det här kommandot avslutas med ett värde som inte är noll stoppas behållaren och startas om, vilket innebär att det inte gick `healthy` att hitta någon signal om att det går att hitta filen. Om kommandot avslutas med slut koden 0 utförs ingen åtgärd.

`periodSeconds` Egenskapen anger att Live-kommandot ska köras var femte sekund.

## <a name="verify-liveness-output"></a>Verifiera Live-utdata

Inom de första 30 sekunderna `healthy` finns filen som skapats av Start kommandot. När Live-kommandot söker efter `healthy` filens existens returnerar status koden noll, signaleringen lyckades, så att ingen omstart sker.

Efter 30 sekunder börjar den `cat /tmp/healthy` Miss lyckas, vilket innebär att det uppstår fel i händelse av fel och avlivning.

Dessa händelser kan visas från Azure Portal eller Azure CLI.

![Felaktig händelse för portalen][portal-unhealthy]

Genom att visa händelser i Azure Portal utlöses händelser av typen `Unhealthy` när Live-kommandot Miss växlar. Den efterföljande händelsen är av typen `Killing`och betecknar en container borttagning så att en omstart kan påbörjas. Antalet omstarter för behållaren ökar varje stund då detta inträffar.

Omstarter slutförs på plats så att resurser som offentliga IP-adresser och Node-/regionsspecifika innehåll bevaras.

![Räknare för att starta om portalen][portal-restart]

Om den kontinuerliga avsökningen Miss lyckas och utlöser för många omstarter, kommer din behållare att ange en exponentiell säkerhets fördröjning.

## <a name="liveness-probes-and-restart-policies"></a>Direktmigreringar och principer för omstart

Restart-principer ersätter omstarts beteendet som utlöses av direktmigreringar. Om du till exempel ställer in en `restartPolicy = Never` *och* en Direktmigrering, kommer behållar gruppen inte att starta om vid en misslyckad direktmigreringens kontroll. Behållar gruppen följer i stället behållar gruppens omstarts princip `Never`.

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier kan kräva en Direktmigrering för att aktivera automatiska omstarter om en förutsättnings funktion inte fungerar korrekt. Mer information om att köra uppgiftsbaserade behållare finns [i köra aktiviteter i behållare i Azure Container instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
