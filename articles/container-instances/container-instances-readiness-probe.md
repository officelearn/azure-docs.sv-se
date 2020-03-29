---
title: Konfigurera beredskapsavsökning på behållarinstans
description: Lär dig hur du konfigurerar en avsökning för att säkerställa att behållare i Azure Container Instances endast tar emot begäranden när de är klara
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 64bb4a3e429ce820835abbf8e235600e592f7868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935681"
---
# <a name="configure-readiness-probes"></a>Konfigurera beredskapsavsökning

För behållare program som betjänar trafik, kanske du vill kontrollera att din behållare är redo att hantera inkommande begäranden. Azure Container Instances stöder beredskapsavsökningar för att inkludera konfigurationer så att din behållare inte kan nås under vissa förhållanden. Readinesssonden uppför något liknande en [Kubernetes readinesssond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). En behållarapp kan till exempel behöva läsa in en stor datauppsättning under start och du vill inte att den ska ta emot begäranden under den här tiden.

I den här artikeln beskrivs hur du distribuerar en behållargrupp som innehåller en beredskapsavsökning, så att en behållare endast tar emot trafik när avsökningen lyckas.

Azure Container Instances stöder också [liveness-avsökningar](container-instances-liveness-probe.md), som du kan konfigurera för att orsaka en felaktig behållare för att automatiskt starta om.

> [!NOTE]
> För närvarande kan du inte använda en beredskapsavsökning i en behållargrupp som distribueras till ett virtuellt nätverk.

## <a name="yaml-configuration"></a>YAML-konfiguration

Skapa till exempel `readiness-probe.yaml` en fil med följande kodavsnitt som innehåller en beredskapsavsökning. Den här filen definierar en behållargrupp som består av en behållare som kör en liten webbapp. Appen distribueras från `mcr.microsoft.com/azuredocs/aci-helloworld` den offentliga avbildningen. Den här behållarappen visas också i [Distribuera en behållarinstans i Azure med Hjälp av Azure CLI](container-instances-quickstart.md) och andra snabbstarter.

```yaml
apiVersion: 2018-10-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Kommandot Start

Distributionen innehåller `command` en egenskap som definierar ett startkommando som körs när behållaren börjar köras. Den här egenskapen accepterar en matris med strängar. Det här kommandot simulerar en tid när webbappen körs men behållaren inte är klar. 

Först startar en skalsession och `node` ett kommando körs för att starta webbappen. Det startar också ett kommando för att sova i 240 `ready` sekunder, varefter det skapar en fil som kallas i katalogen: `/tmp`

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Kommandot Beredskap

Den här YAML-filen definierar en `readinessProbe` som stöder ett `exec` beredskapskommando som fungerar som beredskapskontroll. Det här exemplet beredskap kommando `ready` tester för `/tmp` förekomsten av filen i katalogen.

När `ready` filen inte finns avslutas beredskapskommandot med ett värde som inte är noll. behållaren fortsätter att köras men kan inte nås. När kommandot avslutas med avslutningskod 0 är behållaren klar att nås. 

Egenskapen `periodSeconds` anger att beredskapskommandot ska köras var femte sekund. Beredskapsavsökningen körs under containergruppens livstid.

## <a name="example-deployment"></a>Exempel på distribution

Kör följande kommando för att distribuera en behållargrupp med föregående YAML-konfiguration:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Visa beredskapskontroller

I det här exemplet misslyckas kommandot beredskap under de första 240 sekunderna när det söker efter `ready` filens existens. Statuskoden som returneras signalerar att behållaren inte är klar.

Dessa händelser kan visas från Azure-portalen eller Azure CLI. Portalen visar till exempel `Unhealthy` att händelser av typen utlöses när kommandot beredskap misslyckas. 

![Felhändelse för portalen][portal-unhealthy]

## <a name="verify-container-readiness"></a>Verifiera behållarens beredskap

När du har startat behållaren kan du kontrollera att den inte är tillgänglig från början. Efter etablering hämtar du IP-adressen för behållargruppen:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Försök att komma åt platsen medan beredskapsavsökningen misslyckas:

```bash
wget <ipAddress>
```

Utdata visar att webbplatsen inte är tillgänglig från början:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Efter 240 sekunder lyckas beredskapskommandot och signalen är klar. Nu, när du `wget` kör kommandot, lyckas det:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

När behållaren är klar kan du också komma åt webbappen genom att bläddra till IP-adressen med hjälp av en webbläsare.

> [!NOTE]
> Beredskapsavsökningen fortsätter att köras under containergruppens livstid. Om beredskapskommandot misslyckas vid ett senare tillfälle blir behållaren åter otillgänglig. 
> 

## <a name="next-steps"></a>Nästa steg

En beredskapsavsökning kan vara användbar i scenarier som involverar grupper med flera behållare som består av beroende behållare. Mer information om scenarier med flera behållare finns [i Behållargrupper i Azure Container Instances](container-instances-container-groups.md).

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
