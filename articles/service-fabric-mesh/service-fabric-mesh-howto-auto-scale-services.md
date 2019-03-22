---
title: Autoskala en app som körs i Azure Service Fabric nät | Microsoft Docs
description: Lär dig hur du konfigurerar automatisk skalning för tjänster i ett Service Fabric-nät program.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 2233dffabd7c76ca55cf215f8bc04e66134f5799
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338810"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Skapa principer för automatisk skalning för ett program med Service Fabric-nät
En av de största fördelarna med att distribuera program till Service Fabric Mesh är att du enkelt kan skala in eller ut dina tjänster. Detta hjälper dig att hantera varierande belastningsstorlekar på dina tjänster eller att förbättra tillgängligheten. Du kan manuellt skala dina tjänster in eller ut eller konfigurera principer för automatisk skalning.

[Automatisk skalning](service-fabric-mesh-scalability.md#autoscaling-service-instances) kan du dynamiskt skala antalet dina service-instanser (horisontell skalning). Automatisk skalning ger bra elasticitet och möjliggör etablering eller borttagning av instanser av tjänsten baserat på användning av CPU eller minne.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Alternativ för att skapa en automatisk skalning principer, utlösare och mekanism
Ett automatiskt skalningsprincip har definierats för varje tjänst som du vill skala. Principen har definierats i resursfilen YAML-tjänsten eller JSON-Distributionsmall. Varje skalningsprincip består av två delar: en utlösare och en mekanism för skalning.

Utlösaren definierar när en princip för automatisk skalning har anropats.  Ange vilken typ av utlösare (Genomsnittlig belastning) och mått som ska övervaka (CPU eller minne).  Övre och nedre belastningen tröskelvärden angetts som en procentandel. Skala intervallet definierar hur ofta (i sekunder) den angivna användningen (till exempel genomsnittliga CPU-belastningen) över alla instanser som för tillfället distribuerade tjänsten.  Mekanismen för utlöses när övervakade mått sjunker under lägre tröskelvärde eller ökar övre tröskelvärdet.  

Mekanismen för skalning definierar hur du utför åtgärden skalning när principen har utlösts.  Ange typ av mekanism (Lägg till/ta bort replik), lägsta och högsta repliken räknas (som heltal).  Antalet repliker för tjänsten skalas aldrig under det minsta antalet eller över det maximala antalet.  Ange också skala stegen som ett heltal, vilket är antalet repliker som ska läggas till eller tas bort i en åtgärd för skalning.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definiera ett automatiskt skalningsprincip i en JSON-mall

I följande exempel visas en princip för automatisk skalning i en JSON-Distributionsmall.  Princip för automatisk skalning har deklarerats i en egenskap för tjänsten kan skalas.  I det här exemplet definieras en genomsnittlig belastning CPU-utlösare.  Mekanismen aktiveras om den genomsnittliga CPU-belastningen för alla distribuerade instanser faller under 0,2 (20%) eller börjar ovan 0,8 (80%).  CPU-belastningen kontrolleras var 60: e sekund.  Mekanismen för skalning har definierats för att lägga till eller ta bort instanser om principen utlöses.  Instanser av tjänsten kommer läggas till eller tas bort i steg om en.  Ett minsta instansantal av en och en högsta instansantal 40 definieras också.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definiera en princip för automatisk skalning i en resursfil för service.yaml
I följande exempel visas en princip för automatisk skalning i en tjänst-resursfil (YAML).  Princip för automatisk skalning har deklarerats som en egenskap för tjänsten kan skalas.  I det här exemplet definieras en genomsnittlig belastning CPU-utlösare.  Mekanismen aktiveras om den genomsnittliga CPU-belastningen för alla distribuerade instanser faller under 0,2 (20%) eller börjar ovan 0,8 (80%).  CPU-belastningen kontrolleras var 60: e sekund.  Mekanismen för skalning har definierats för att lägga till eller ta bort instanser om principen utlöses.  Instanser av tjänsten kommer läggas till eller tas bort i steg om en.  Ett minsta instansantal av en och en högsta instansantal 40 definieras också.

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
Lär dig hur du [manuellt skala en tjänst](service-fabric-mesh-tutorial-template-scale-services.md)
