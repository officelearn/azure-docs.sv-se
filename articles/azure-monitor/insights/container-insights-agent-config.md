---
title: Konfigurera Azure Monitor för datainsamling för behållare agent | Microsoft Docs
description: Den här artikeln beskriver hur du kan konfigurera i Azure Monitor för behållare agenten ska styra stdout/stderr och miljövariabler logga samling.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341650"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurera agenten datainsamling för Azure Monitor för behållare

Azure Monitor för behållare samlar in stdout, stderr och miljövariabler från behållararbetsbelastningar som distribueras till hanterade Kubernetes-kluster som finns på Azure Kubernetes Service (AKS) från behållare agenten. Du kan konfigurera inställningar för insamling av agenten genom att skapa en anpassad Kubernetes ConfigMaps att styra den här upplevelsen. Den här artikeln visar hur du skapar ConfigMap och konfigurerar insamling av data baserat på dina krav.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Konfigurera ditt kluster med anpassade inställningar för insamling

En mallfil ConfigMap har angetts som gör att du kan enkelt redigera den med dina anpassningar utan att behöva skapa från grunden. Innan du börjar bör du granska Kubernetes-dokumentationen om [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) och bekanta dig med hur du skapar, konfigurerar och distribuerar ConfigMaps. Detta kan du filtrera stderr och stdout per namnområde eller i hela klustret, och miljövariabler för alla behållare som körs i alla poddar/noder i klustret.

>[!IMPORTANT]
>Den lägsta agentversion som stöds av den här funktionen är microsoft / oms:ciprod06142019 eller senare. 

### <a name="overview-of-configurable-data-collection-settings"></a>Översikt över konfigurerbara inställningar för insamling

Följande är de inställningar som kan konfigureras för att styra insamling av data.

|Nyckel |Datatyp |Value |Beskrivning |
|----|----------|------|------------|
|`schema-version` |Sträng (skiftlägeskänsligt) |v1 |Det här är den schemaversionen som används av agenten vid parsning av den här ConfigMap. För närvarande stöds schemaversionen är v1. Ändra det här värdet stöds inte och kommer att avvisas när ConfigMap utvärderas.|
|`config-version` |String | | Har stöd för möjligheten att hålla reda på den här konfigurationsfilen versionen i system/centrallagret för källkontroll. Högsta tillåtna antalet tecken är 10 och alla andra tecken trunkeras. |
|`[log_collection_settings.stdout] enabled =` |Boolean | SANT eller FALSKT | Detta styr om Logginsamling för stdout behållare är aktiverad. När värdet `true` och inga namnområden undantas för stdout Logginsamling (`log_collection_settings.stdout.exclude_namespaces` inställning nedan), stdout loggar samlas in från alla behållare i alla poddar/noder i klustret. Om inte har angetts i ConfigMaps standardvärdet är `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | CSV-matris |Matris med Kubernetes-namnområden som STDOUT loggar inte kommer att samlas in. Den här inställningen gäller bara om `log_collection_settings.stdout.enabled` är inställd på `true`. Om inte har angetts i ConfigMap standardvärdet är `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | SANT eller FALSKT |Detta styr om stderr behållare Logginsamling är aktiverad. När värdet `true` och inga namnområden undantas för stdout Logginsamling (`log_collection_settings.stderr.exclude_namespaces` inställningen), stderr-loggar samlas in från alla behållare i alla poddar/noder i klustret. Om inte har angetts i ConfigMaps standardvärdet är `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |CSV-matris |Matris med Kubernetes-namnområden för vilka stderr loggar inte kommer att samlas in. Den här inställningen gäller bara om `log_collection_settings.stdout.enabled` är inställd på `true`. Om inte har angetts i ConfigMap standardvärdet är `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | SANT eller FALSKT | Detta styr om miljön variabelsamlingen är aktiverad. När värdet `false`, inga miljövariabler samlas in för alla behållare som körs i alla poddar/noder i klustret. Om inte har angetts i ConfigMap standardvärdet är `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera och distribuera ConfigMap konfigurationsfilen till klustret.

1. [Ladda ned](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) mall ConfigMap yaml och spara den som container-azm-ms-agentconfig.yaml.  
1. Redigera ConfigMap yaml-fil med dina anpassningar. 

    - Om du vill exkludera specifika namnområden för stdout Logginsamling kan du konfigurera nyckel/värde-enligt följande exempel: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Ange nyckel/värde för att inaktivera miljö variabelsamlingen för en specifik behållare, `[log_collection_settings.env_var] enabled = true` att aktivera variabelsamlingen globalt och följ sedan stegen [här](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) att slutföra konfigurationen för den specifika behållaren.
    - Om du vill inaktivera stderr Logginsamling hela klustret, du konfigurerar nyckel/värde-enligt följande exempel: `[log_collection_settings.stderr] enabled = false`.

1. Skapa ConfigMap genom att köra följande kommando för kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Konfigurationsändringen kan ta några minuter att slutföra innan du börjar gälla och startar om alla omsagent poddar i klustret. Omstarten är löpande startas om för att alla omsagent poddar, inte alla startas om samtidigt. När startas om är klar visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" created`.

Använder du följande kommando för att verifiera konfigurationen tillämpades, för att granska loggar från en agent pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Om det finns konfigurationsfel från osmagent poddarna, visar utdata fel liknar följande:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel förhindrar att omsagent parsa filen, vilket gör att den startar du om och använder standardkonfigurationen. När du har korrigerat felen i ConfigMap spara yaml-fil och använda den uppdaterade ConfigMaps genom att köra kommandot: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Tillämpa uppdateras ConfigMap

Om du redan har distribuerat en ConfigMap för klustret och du vill uppdatera det med en nyare konfiguration, kan du helt enkelt redigera filen ConfigMap du tidigare har använt och sedan installerar med hjälp av samma kommando som tidigare `kubectl apply -f <configmap_yaml_file.yaml`.

Konfigurationsändringen kan ta några minuter att slutföra innan du börjar gälla och startar om alla omsagent poddar i klustret. Omstarten är löpande startas om för att alla omsagent poddar, inte alla startas om samtidigt. När startas om är klar visas ett meddelande som liknar följande och som innehåller resultatet: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Verifiera schemaversion

Stöds config schemat versioner är tillgängliga som pod anteckning (schema-versioner) på omsagent pod. Du kan se dem med följande kubectl-kommando: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Dessa utdata visar liknande denna med anteckning schema-versioner:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig hur du använder Azure Monitor och övervaka andra aspekter av AKS-klustret går [visa Azure Kubernetes Service health](container-insights-analyze.md).
- Visa [logga fråga exempel](container-insights-log-search.md#search-logs-to-analyze-data) att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisera och analysera dina kluster.