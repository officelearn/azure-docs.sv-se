---
title: Konfigurera Azure Monitor för behållare agent datainsamling | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan konfigurera Azure Monitor för behållare agent för att styra stdout / stderr och miljövariabler loggsamling.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933027"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurera agentdatainsamling för Azure Monitor för behållare

Azure Monitor för behållare samlar upp stdout-, stderr- och miljövariabler från behållararbetsbelastningar som distribueras till hanterade Kubernetes-kluster från den behållaren. Du kan konfigurera inställningar för insamling av agentdata genom att skapa en anpassad Kubernetes ConfigMaps för att styra den här upplevelsen. 

Den här artikeln visar hur du skapar ConfigMap och konfigurerar datainsamling baserat på dina krav.

>[!NOTE]
>För Azure Red Hat OpenShift skapas en konfigurationsfil för mall i namnområdet *openshift-azure-loggning.* 
>

## <a name="configmap-file-settings-overview"></a>Översikt över inställningar för ConfigMap-filer

En mall ConfigMap fil tillhandahålls som gör att du enkelt kan redigera den med dina anpassningar utan att behöva skapa den från grunden. Innan du börjar bör du granska Kubernetes-dokumentationen om [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) och bekanta dig med hur du skapar, konfigurerar och distribuerar ConfigMaps. På så sätt kan du filtrera stderr och stdout per namnområde eller över hela klustret och miljövariabler för alla behållare som körs över alla poddar/noder i klustret.

>[!IMPORTANT]
>Den minsta agentversion som stöds för att samla in stdout-, stderr- och miljövariabler från behållararbetsbelastningar är ciprod06142019 eller senare. Om du vill verifiera agentversionen väljer du en nod på fliken **Nod** och i egenskapsfönstrets anteckningsvärde för egenskapen **Agent Image Tag.** Mer information om agentversionerna och vad som ingår i varje version finns i [agentreleaseanteckningar](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Inställningar för datainsamling

Följande är de inställningar som kan konfigureras för att styra datainsamlingen.

|Nyckel |Datatyp |Värde |Beskrivning |
|----|----------|------|------------|
|`schema-version` |Sträng (skiftlägeskänslig) |v1 (på) |This is the schema version used by the agent when parsing this ConfigMap. Schemaversion som stöds är v1. Det går inte att ändra det här värdet och avvisas när ConfigMap utvärderas.|
|`config-version` |String | | Stöder möjligheten att hålla reda på den här konfigurationsfilens version i källkontrollsystemet/-databasen. Högsta tillåtna tecken är 10 och alla andra tecken trunkeras. |
|`[log_collection_settings.stdout] enabled =` |Boolean | sant eller falskt | Detta styr om stdout containerloggsamling är aktiverad. När du `true` är inställd på och inga namnområden utesluts för stdout log collection (`log_collection_settings.stdout.exclude_namespaces` inställningen nedan), kommer stdout loggar samlas in från alla behållare över alla poddar / noder i klustret. Om det inte anges i ConfigMaps `enabled = true`är standardvärdet . |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Kommaavgränsad matris |Matris med Kubernetes namnområden för vilka stdout-loggar inte samlas in. Den här inställningen `log_collection_settings.stdout.enabled` är endast `true`effektiv om den är inställd på . Om det inte anges i ConfigMap `exclude_namespaces = ["kube-system"]`är standardvärdet .|
|`[log_collection_settings.stderr] enabled =` |Boolean | sant eller falskt |Detta styr om stderr container loggsamling är aktiverad. När du `true` är inställd på och inga namnområden utesluts för stdout log collection (`log_collection_settings.stderr.exclude_namespaces` inställning), kommer stderr loggar samlas in från alla behållare över alla poddar / noder i klustret. Om det inte anges i ConfigMaps `enabled = true`är standardvärdet . |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Kommaavgränsad matris |Matris med Kubernetes namnområden för vilka stderr loggar inte kommer att samlas in. Den här inställningen `log_collection_settings.stdout.enabled` är endast `true`effektiv om den är inställd på . Om det inte anges i ConfigMap `exclude_namespaces = ["kube-system"]`är standardvärdet . |
| `[log_collection_settings.env_var] enabled =` |Boolean | sant eller falskt | Den här inställningen styr miljövariabelsamlingen för alla poddar/noder i klustret och som standard `enabled = true` när de inte anges i ConfigMaps. Om insamling av miljövariabler är globalt aktiverad kan du inaktivera den `AZMON_COLLECT_ENV` för en viss behållare genom att ange miljövariabeln till **False** antingen med en Dockerfile-inställning eller i [konfigurationsfilen för pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) under avsnittet **Env:** . Om insamling av miljövariabler är globalt inaktiverad kan du inte aktivera insamling för en viss behållare (det vill säga den enda åsidosättningen som kan tillämpas på behållarnivå är att inaktivera samlingen när den redan är aktiverad globalt.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Boolean | sant eller falskt | Den här inställningen styr behållarloggens anrikning om att fylla i egenskapsvärdena Namn och bild för varje loggpost som skrivs till tabellen ContainerLog för alla behållarloggar i klustret. Det är `enabled = false` standard när det inte anges i ConfigMap. |

ConfigMaps är en global lista och det kan bara finnas en ConfigMap som tillämpas på agenten. Du kan inte låta en annan ConfigMaps åsidosätta samlingarna.

## <a name="configure-and-deploy-configmaps"></a>Konfigurera och distribuera ConfigMaps

Utför följande steg för att konfigurera och distribuera konfigurationsfilen för ConfigMap till klustret.

1. [Ladda ner](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) filen ConfigMap yaml och spara den som container-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Det här steget krävs inte när du arbetar med Azure Red Hat OpenShift eftersom ConfigMap-mallen redan finns i klustret.

2. Redigera ConfigMap yaml-filen med dina anpassningar för att samla in stdout-, stderr- och/eller miljövariabler. Om du redigerar ConfigMap yaml-filen för Azure Red `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Hat OpenShift kör du först kommandot för att öppna filen i en textredigerare.

    - Om du vill utesluta specifika namnområden för stdout-loggsamling konfigurerar `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`du nyckeln/värdet med följande exempel: .
    
    - Om du vill inaktivera miljövariabelsamling för `[log_collection_settings.env_var] enabled = true` en viss behållare anger du nyckeln/värdet så att variabelinsamlingen aktiveras globalt och följer sedan stegen [här](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) för att slutföra konfigurationen för den specifika behållaren.
    
    - Om du vill inaktivera kluster för stderr-loggsamling konfigurerar `[log_collection_settings.stderr] enabled = false`du nyckeln/värdet med följande exempel: .

3. För andra kluster än Azure Red Hat OpenShift skapar du ConfigMap `kubectl apply -f <configmap_yaml_file.yaml>` genom att köra följande kubectl-kommando: på andra kluster än Azure Red Hat OpenShift. 
    
    Exempel: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Spara ändringarna i redigeraren för Azure Red Hat OpenShift.

Konfigurationsändringen kan ta några minuter att slutföra innan den börjar gälla, och alla omsagent pods i klustret startar om. Omstarten är en rullande omstart för alla omsagent pods, inte alla startas samtidigt. När omstarterna är klara visas ett meddelande som liknar följande och som `configmap "container-azm-ms-agentconfig" created`innehåller resultatet: .

## <a name="verify-configuration"></a>Verifiera konfiguration

Om du vill kontrollera att konfigurationen har tillämpats på ett annat kluster än Azure Red `kubectl logs omsagent-fdf58 -n=kube-system`Hat OpenShift använder du följande kommando för att granska loggarna från en agentpod: . Om det finns konfigurationsfel från omsagentkapslarna visas fel som liknar följande:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Fel relaterade till att tillämpa konfigurationsändringar är också tillgängliga för granskning. Följande alternativ är tillgängliga för ytterligare felsökning av konfigurationsändringar:

- Från en agent pod loggar `kubectl logs` med samma kommando. 

    >[!NOTE]
    >Det här kommandot gäller inte azure red hat openshift-kluster.
    > 

- Från Live loggar. Liveloggar visar fel som liknar följande:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Från tabellen **KubeMonAgentEvents** i logganalysarbetsytan. Data skickas varje timme med *Fel* allvarlighetsgrad för konfigurationsfel. Om det inte finns några fel kommer posten i tabellen att ha data med allvarlighetsgrad *info*, som inte rapporterar några fel. Egenskapen **Tags** innehåller mer information om pod- och behållar-ID:t där felet uppstod och även den första förekomsten, den senaste förekomsten och antalet under den senaste timmen.

- Med Azure Red Hat OpenShift kontrollerar du omsagentloggarna genom att söka i **tabellen ContainerLog** för att verifiera om loggsamling av openshift-azure-logging är aktiverad.

NÃ¤r du har korrigerat felen i ConfigMap fÃ¤r klustrade fÃ¤r kÃ¤r än `kubectl apply -f <configmap_yaml_file.yaml`Azure Red Hat OpenShift sparar du yaml-filen och anpÃ¤nder de uppdaterade ConfigMaps genom att kÃ¤gga kommandot: . För Azure Red Hat OpenShift redigerar och sparar du de uppdaterade ConfigMaps-kommandona genom att köra kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Tillämpa uppdaterad ConfigMap

Om du redan har distribuerat en ConfigMap på andra kluster än Azure Red Hat OpenShift och vill uppdatera den med en nyare konfiguration, kan `kubectl apply -f <configmap_yaml_file.yaml`du redigera ConfigMap-filen som du tidigare har använt och sedan använda samma kommando som tidigare . För Azure Red Hat OpenShift redigerar och sparar du de uppdaterade ConfigMaps-kommandona genom att köra kommandot:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Konfigurationsändringen kan ta några minuter att slutföra innan den börjar gälla, och alla omsagent pods i klustret startar om. Omstarten är en rullande omstart för alla omsagent pods, inte alla startas samtidigt. När omstarterna är klara visas ett meddelande som liknar följande och som `configmap "container-azm-ms-agentconfig" updated`innehåller resultatet: .

## <a name="verifying-schema-version"></a>Verifiera schemaversion

Konfigurationsschemaversioner som stöds är tillgängliga som pod-anteckning (schemaversioner) på omsagentpodden. Du kan se dem med följande kubectl-kommando:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Utdata visas ungefär som följande med anteckningsschema-versionerna:

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

- Azure Monitor för behållare innehåller inte en fördefinierad uppsättning aviseringar. Granska [skapa prestandaaviseringar med Azure Monitor för behållare för](container-insights-alerts.md) att lära dig hur du skapar rekommenderade aviseringar för hög CPU- och minnesanvändning för att stödja dina DevOps eller operativa processer och procedurer.

- När övervakning är aktiverad för att samla in hälso- och resursanvändning för ditt AKS- eller hybridkluster och arbetsbelastningar som körs på dem kan du lära dig [hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Visa [exempel på loggfrågor](container-insights-log-search.md#search-logs-to-analyze-data) om du vill se fördefinierade frågor och exempel som kan utvärderas eller anpassas för aviseringar, visualisering eller analys av kluster.
