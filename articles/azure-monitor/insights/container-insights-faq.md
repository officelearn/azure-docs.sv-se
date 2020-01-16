---
title: Azure Monitor för behållare vanliga frågor och svar | Microsoft Docs
description: Azure Monitor for containers är en lösning som övervakar hälso tillståndet för dina AKS-kluster och Container Instances i Azure. I den här artikeln besvaras vanliga frågor.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: b0d2be8b573dbbf047f4a27ae9ac9f611b76dc51
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977753"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Azure Monitor för behållare vanliga frågor och svar

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor för behållare. Om du har ytterligare frågor om lösningen går du till [diskussions forumet](https://feedback.azure.com/forums/34192--general-feedback) och publicerar dina frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Jag ser inte bild-och namn egenskaps värden ifyllda när jag frågar ContainerLog-tabellen.

För agent version ciprod12042019 och senare fylls dessa två egenskaper i som standard för varje loggnings rad för att minimera kostnaderna för loggdata som samlas in. Det finns två alternativ för att fråga tabellen som innehåller dessa egenskaper med sina värden:

### <a name="option-1"></a>Alternativ 1 

Gå med i andra tabeller för att inkludera dessa egenskaps värden i resultaten.

Ändra dina frågor för att inkludera bild-och ImageTag-egenskaper från tabellen ```ContainerInventory``` genom att koppla till egenskapen ContainerID. Du kan inkludera egenskapen namn (som tidigare fanns i ```ContainerLog``` tabellen) från KubepodInventory-tabellens ContaineName-fält genom att koppla till egenskapen ContainerID. Detta är det rekommenderade alternativet.

Följande exempel är ett exempel på en detaljerad fråga som förklarar hur du hämtar dessa fält värden med kopplingar.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>Alternativ 2

Återaktivera insamling för de här egenskaperna för varje behållar logg rad.

Om det första alternativet inte är bekvämt på grund av frågor som har inaktiverats kan du återaktivera insamling av de här fälten genom att aktivera inställningen ```log_collection_settings.enrich_container_logs``` i agentens konfigurations mappning enligt beskrivningen i [konfigurations inställningarna för data insamling](./container-insights-agent-config.md).

> [!NOTE]
> Det andra alternativet rekommenderas inte med stora kluster som har fler än 50 noder, eftersom det genererar API-Server anrop från varje nod > i klustret för att utföra denna anrikning. Det här alternativet ökar också data storleken för varje logg rad som samlas in.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Kan jag visa mått som samlats in i Grafana?

Azure Monitor for containers stöder visning av mått som lagras i din Log Analytics arbets yta i Grafana-instrumentpaneler. Vi har angett en mall som du kan ladda ned från Grafana för [instrument panelen](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) för att komma igång och referera till att hjälpa dig att fråga efter ytterligare data från de övervakade klustren för att visualisera anpassade Grafana-instrumentpaneler. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kan jag övervaka mitt AKS-kluster med Azure Monitor för behållare?

Azure Monitor för behållare stöder övervakning av arbets belastningar som distribueras till AKS-motorn (tidigare ACS-motor) som finns på Azure. Mer information och en översikt över de steg som krävs för att aktivera övervakning för det här scenariot finns i [använda Azure Monitor för behållare för AKS-Engine](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Varför visas inte data i min Log Analytics-arbetsyta?

Om du inte kan se några data i Log Analytics arbets ytan vid en viss tid varje dag, kan du ha uppnått standard gränsen på 500 MB eller den dagliga begränsningen för att styra mängden data som ska samlas in dagligen. När gränsen uppfylls för dagen stoppas data insamlingen och återupptas bara på nästa dag. Information om hur du granskar din data användning och uppdaterar till en annan pris nivå baserat på dina förväntade användnings mönster finns i [logga data användning och kostnad](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Vilka är behållar tillstånden angivna i ContainerInventory-tabellen?

Tabellen ContainerInventory innehåller information om både stoppade och pågående behållare. Tabellen fylls i av ett arbets flöde i agenten som frågar Docker efter alla behållare (körs och stoppas) och vidarebefordrar dessa data till Log Analytics arbets ytan.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hur gör jag för att lösa *saknade prenumerations registrerings* fel?

Om du får ett fel meddelande om att **prenumerations registrering saknas för Microsoft. OperationsManagement**kan du lösa det genom att registrera resurs leverantören **Microsoft. OperationsManagement** i prenumerationen där arbets ytan definieras. Dokumentationen för hur du kan göra detta finns [här](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Finns det stöd för RBAC-aktiverade AKS-kluster?

Lösningen för övervakning av behållare stöder inte RBAC, men den stöds med Azure Monitor för behållare. Sidan lösnings information visar kanske inte rätt information i bladet som visar data för de här klustren.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hur gör jag för att aktivera logg insamling för behållare i namn området Kube-system via Helm?

Logg samlingen från behållare i Kube-systemets namnrymd är inaktive rad som standard. Logg insamling kan aktive ras genom att ställa in en miljö variabel på omsagent. Mer information finns på sidan [Azure Monitor for containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hur gör jag för att du uppdatera omsagent till den senaste versionen?

Information om hur du uppgraderar agenten finns i [agent hantering](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Hur gör jag för att aktivera loggning på flera rader?

För närvarande har Azure Monitor för behållare inte stöd för flera rader, men det finns lösningar som är tillgängliga. Du kan konfigurera alla tjänster som ska skrivas i JSON-format och sedan Docker/Moby att skriva dem som en enda rad.

Du kan till exempel figursätta loggen som ett JSON-objekt, som du ser i exemplet nedan för ett exempel på Node. js-program:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Dessa data kommer att se ut som i följande exempel i Azure Monitor för loggar när du frågar efter den:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

En detaljerad översikt över problemet finns i följande [GitHub-länk](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hur gör jag för att lösa Azure AD-fel när jag aktiverar Live-loggar? 

Följande fel kan visas: svars-URL: en som **anges i begäran matchar inte de svars-URL: er som har kon figurer ATS för programmet: < program-ID\>** . Lösningen för att lösa problemet finns i artikeln [så här visar du behållar data i real tid med Azure Monitor för behållare](container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Varför kan jag inte uppgradera klustret efter onboarding?

Om du när du har aktiverat Azure Monitor för behållare för ett AKS-kluster tar du bort arbets ytan Log Analytics som klustret skickade data till, vid försök att uppgradera klustret. För att undvika detta måste du inaktivera övervakning och sedan återaktivera den till en annan giltig arbets yta i din prenumeration. När du försöker utföra kluster uppgraderingen igen bör den bearbetas och slutföras.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Vilka portar och domäner behöver jag öppna/vitlista för agenten?

Se [nätverks brand Väggs kraven](container-insights-onboard.md#network-firewall-requirements) för proxy-och brand Väggs konfigurations information som krävs för behållarens agent med Azure, Azure amerikanska myndigheter och moln i Azure Kina.

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster, granska [hur att publicera Azure övervakar för behållare](container-insights-onboard.md) att förstå de krav och tillgängliga metoder för att aktivera övervakning. 
