---
title: Konfigurera liveness avsökningar i Azure Container Instances
description: Lär dig hur du konfigurerar liveness avsökningar för att starta om feltillstånd behållare i Azure Container Instances
services: container-instances
author: jluk
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: juluk
ms.openlocfilehash: 1582f0d7ec688bc72cc9d1aa6ae0ddb0a6ad3a17
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213079"
---
# <a name="configure-liveness-probes"></a>Konfigurera liveavsökningar

Köra program i behållare under längre tid, vilket resulterar i bruten tillstånd som kan behöva repareras genom att starta om behållaren. Azure Container Instances stöder liveness avsökningar för att inkludera konfigurationer så att din behållare kan starta om vid kritiska funktioner inte fungerar.

Den här artikeln förklarar hur du distribuerar en behållargrupp som innehåller en hälsoavsökningen visar automatisk omstart av en simulerad feltillstånd behållare.

## <a name="yaml-deployment"></a>YAML-distribution

Skapa en `liveness-probe.yaml` fil med följande kodavsnitt. Den här filen definierar en behållargrupp som består av en NGNIX-behållare som så småningom blir ohälsosamt.

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

Kör följande kommando för att distribuera den här behållargruppen med ovanstående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Startkommandot

Distributionen definierar ett från kommando som ska köras när behållaren startas första gången som körs, definieras av den `command` egenskapen som accepterar en matris med strängar. I det här exemplet ska det starta en bash-session och skapa en fil med namnet `healthy` inom den `/tmp` katalogen genom att skicka det här kommandot:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

 Det kommer sedan att vila i 30 sekunder innan du tar bort filen och anger sedan en 10 minuter strömsparläge.

### <a name="liveness-command"></a>Liveness kommando

Den här distributionen definierar en `livenessProbe` som har stöd en `exec` liveness kommando som fungerar som liveness kontrollen. Om det här kommandot avslutas med ett annat värde än noll, behållaren ska avslutas och startas om, signalering den `healthy` filen kunde inte hittas. Om det här kommandot har avslutas med slutkoden 0, åtgärd ingen.

Den `periodSeconds` egenskapen betyder liveness kommandot ska köras var femte sekund.

## <a name="verify-liveness-output"></a>Verifiera liveness utdata

Inom de första 30 sekunderna den `healthy` filen som skapats av startkommandot finns. När kommandot liveness söker efter den `healthy` filens förekomst statuskoden returnerar noll, signalering lyckades, så ingen omstart sker.

Efter 30 sekunder, den `cat /tmp/healthy` börjar misslyckas, orsakar feltillstånd och avlivas händelser inträffar.

Dessa händelser kan ses från Azure portal eller Azure CLI.

![Portalen ohälsosam händelse][portal-unhealthy]

Genom att visa händelser i Azure-portalen, händelser av typen `Unhealthy` att utlösas vid liveness kommandot misslyckas. Efterföljande händelsen ska vara av typen `Killing`, vilket tyder på en borttagning av behållare så att en omstart kan börja. Antalet omstarter för behållaren ökar varje gång som det här inträffar.

Omstarter utförs på plats så att resurser som offentliga IP-adresser och nodspecifik innehållet kommer att bevaras.

![Portalen starta om räknare][portal-restart]

Om hälsoavsökningen kontinuerligt misslyckas och utlöser för många omstarter, anger din behållare en exponentiell backoff-fördröjning.

## <a name="liveness-probes-and-restart-policies"></a>Liveness avsökningar och principer och omstart

Starta om principer ersätter omstartsbeteende som utlöses av liveness avsökningar. Exempel: Om du ställer in en `restartPolicy = Never` *och* en hälsoavsökningen behållargruppen startar inte om i händelse av en misslyckad liveness-kontroll. Behållargruppen ska i stället följa den behållargruppen omstartsprincip av `Never`.

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier kan kräva en hälsoavsökningen att aktivera automatiska omstarter om en funktion som krävs inte fungerar korrekt. Mer information om hur du kör uppgiftsbaserade behållare finns i [köra behållarbaserade uppgifter i Azure Container Instances](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
