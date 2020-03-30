---
title: Skala en app automatiskt som körs i Azure Service Fabric Mesh
description: Lär dig hur du konfigurerar principer för automatisk skalning för tjänsterna i ett Service Fabric Mesh-program.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461975"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Skapa principer för automatisk skalning för ett Service Fabric Mesh-program
En av de största fördelarna med att distribuera program till Service Fabric Mesh är möjligheten för dig att enkelt skala dina tjänster in eller ut. Detta bör användas för att hantera varierande mängd belastning på dina tjänster, eller förbättra tillgängligheten. Du kan skala dina tjänster manuellt in eller ut eller ställa in principer för automatisk skalning.

[Med automatisk skalning](service-fabric-mesh-scalability.md#autoscaling-service-instances) kan du dynamiskt skala antalet tjänstinstanser (vågrät skalning). Automatisk skalning ger stor elasticitet och möjliggör etablering eller borttagning av tjänstinstanser baserat på CPU- eller minnesanvändning.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Alternativ för att skapa en automatisk skalningsprincip, utlösare och mekanism
En princip för automatisk skalning har definierats för varje tjänst som du vill skala. Principen definieras antingen i YAML-tjänstresursfilen eller JSON-distributionsmallen. Varje skalningsprincip består av två delar: en utlösare och en skalningsmekanism.

Utlösaren definierar när en princip för automatisk skalning anropas.  Ange vilken typ av utlösare (genomsnittlig belastning) och måttet som ska övervakas (CPU eller minne).  Övre och nedre belastningströsklar som anges som en procentsats. Skalintervallet definierar hur ofta (i sekunder) det angivna utnyttjandet (t.ex. genomsnittlig CPU-belastning) ska kontrolleras (i sekunder) för alla tjänstinstanser som för närvarande distribueras.  Mekanismen utlöses när det övervakade måttet sjunker under det nedre tröskelvärdet eller ökar över det övre tröskelvärdet.  

Skalningsmekanismen definierar hur skalningsåtgärden ska utföras när principen utlöses.  Ange typ av mekanism (lägg till/ta bort replik), det lägsta och högsta antalet repliker (som heltal).  Antalet tjänstrepliker skalas aldrig under minimiantalet eller över det maximala antalet.  Ange också skalningssteget som ett heltal, vilket är antalet repliker som ska läggas till eller tas bort i en skalningsåtgärd.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definiera en princip för automatisk skalning i en JSON-mall

I följande exempel visas en princip för automatisk skalning i en JSON-distributionsmall.  Principen för automatisk skalning deklareras i en egenskap för den tjänst som ska skalas.  I det här exemplet definieras en processormedelvärdebelastningsutlösare.  Mekanismen utlöses om den genomsnittliga CPU-belastningen för alla distribuerade instanser sjunker under 0,2 (20%) eller går över 0,8 (80%).  CPU-belastningen kontrolleras var 60:e sekund.  Skalningsmekanismen har definierats för att lägga till eller ta bort instanser om principen utlöses.  Tjänstinstanser läggs till eller tas bort i steg om en.  Ett minsta instansantal på en och ett maximalt instansantal på 40 definieras också.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definiera en princip för automatisk skalning i en resource-fil för tjänst.yaml
I följande exempel visas en princip för automatisk skalning i en YAML-fil (Service Resource).  Principen för automatisk skalning deklareras som en egenskap för den tjänst som ska skalas.  I det här exemplet definieras en processormedelvärdebelastningsutlösare.  Mekanismen utlöses om den genomsnittliga CPU-belastningen för alla distribuerade instanser sjunker under 0,2 (20%) eller går över 0,8 (80%).  CPU-belastningen kontrolleras var 60:e sekund.  Skalningsmekanismen har definierats för att lägga till eller ta bort instanser om principen utlöses.  Tjänstinstanser läggs till eller tas bort i steg om en.  Ett minsta instansantal på en och ett maximalt instansantal på 40 definieras också.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skalar en tjänst manuellt](service-fabric-mesh-tutorial-template-scale-services.md)
