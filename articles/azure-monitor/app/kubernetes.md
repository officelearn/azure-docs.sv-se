---
title: Använd Application Insights för att övervaka din Azure Kubernetes Service (AKS) eller andra Kubernetes-värdprogram – Azure Monitor | Microsoft-dokument
description: Azure Monitor använder service mesh-teknik, Istio, på ditt Kubernetes-kluster för att tillhandahålla programövervakning för alla Kubernetes-värdprogram. På så sätt kan du samla in programstatistik telemetri som gäller inkommande och utgående begäranden till och från poddar som körs i klustret.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 56a0cb66f5b54c817067970ab369d7ca471a1696
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132350"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications"></a>Övervakning av nollinstrumenteringsprogram för Kubernetes värdprogram

> [!IMPORTANT]
> Den här funktionen är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor utnyttjar nu service mesh-teknik i ditt Kubernetes-kluster för att tillhandahålla övervakning utanför boxen för alla Kubernetes-värdappar. Med standardfunktioner i Application Insight som [Programmappning](../../azure-monitor/app/app-map.md) för att modellera dina beroenden, Live Metrics Stream för [realtidsövervakning,](../../azure-monitor/app/live-stream.md) kraftfulla visualiseringar med [standardinstrumentpanelen,](../../azure-monitor/app/overview-dashboard.md) [Måttutforskaren](../../azure-monitor/platform/metrics-getting-started.md)och [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md). Den här funktionen hjälper användare att upptäcka flaskhalsar och felhotspots för alla sina Kubernetes-arbetsbelastningar i ett valt Kubernetes-namnområde. Genom att dra nytta av dina befintliga investeringar i tjänstnät med tekniker som Istio möjliggör Azure Monitor automatisk instrumentbaserad appövervakning utan att ändra programmets kod.

> [!NOTE]
> Detta är ett av många sätt att utföra programövervakning på Kubernetes.Du kan också instrumentera alla appar som finns i Kubernetes med hjälp av [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.yml) utan att behöva ett servicenät. För att övervaka Kubernetes utan att instrumentera programmet med en SDK kan du använda nedanstående metod.

## <a name="prerequisites"></a>Krav

- Ett [Kubernetes-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konsolåtkomst till klustret för att köra *kubectl*.
- En [application insight-resurs](create-new-resource.md)
- Ha ett servicenät. Om klustret inte har distribuerat Istio kan du lära dig hur du [installerar och använder Istio i Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funktioner

Genom att använda noll instrumenteringsprogram övervakning för Kubernetes värd apps, kommer du att kunna använda:

- [Programkarta](../../azure-monitor/app/app-map.md)
- [Mått för livestream](../../azure-monitor/app/live-stream.md)
- [Instrumentpaneler](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Spårning av distribuerad](../../azure-monitor/app/distributed-tracing.md)
- [Övervakning av transaktioner från slutna till](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installationssteg

För att aktivera lösningen kommer vi att utföra följande steg:
- Distribuera programmet (om det inte redan har distribuerats).
- Se till att programmet är en del av servicenätet.
- Observera insamlad telemetri.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurera appen så att den fungerar med ett servicenät

Istio stöder två sätt [att instrumentera en pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
I de flesta fall är det enklast att markera kubernetes namnområde som innehåller ditt program med *istio-injektionsetiketten:*

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Eftersom servicenät lyfter data från tråden, kan vi inte avlyssna den krypterade trafiken. För trafik som inte lämnar klustret använder du ett okrypterat protokoll (till exempel HTTP). För extern trafik som måste krypteras bör du överväga att [konfigurera TLS-avslutningen](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) vid ingångsstyrenheten.

Program som körs utanför tjänstnätet påverkas inte.

### <a name="deploy-your-application"></a>Distribuera appen

- Distribuera programmet till namnområdet *för min-app-namnområde.* Om programmet redan är distribuerat, och du har följt den automatiska sidovagnsinjektionsmetoden som beskrivs ovan, måste du återskapa poddar för att säkerställa att Istio injicerar sin sidovagn. antingen initiera en rullande uppdatering eller ta bort enskilda poddar och vänta på att de ska återskapas.
- Se till att din ansökan uppfyller [Istio-kraven](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuera övervakning av noll instrumenteringsprogram för Kubernetes värdappar

1. Hämta och extrahera en [ *Application Insights-kortutgåva* ](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navigera till */src/kubernetes/* inuti utgivningsmappen.
3. Redigera *program-insights-istio-mixer-adapter-deployment.yaml*
    - redigera värdet *för ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* miljövariabel för att innehålla instrumenteringsnyckeln för Application Insights-resursen i Azure-portalen för att innehålla telemetrin.
    - Om det behövs redigerar du värdet *för ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* miljövariabeln så att den innehåller en kommaavgränsad lista över namnområden som du vill aktivera övervakning för. Lämna den tom för att övervaka alla namnområden.
4. Använd *varje* YAML-fil som hittas under *src/kubernetes/* genom att köra följande (du måste fortfarande vara inne *i /src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verifiera distributionen

- Kontrollera att application insights-kortet har distribuerats:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> I vissa fall krävs finjustering. Om du vill inkludera eller utesluta telemetri för en enskild pod från att samlas in använder du *etiketten appinsights/monitoring.enabled* på den podden. Detta kommer att ha prioritet över alla namnområdesbaserade konfigurationer. Ange *appinsights/monitoring.enabled* till *true* för att inkludera podden och *false* för att utesluta den.

### <a name="view-application-insights-telemetry"></a>Visa telemetri för programinsikter

- Generera en exempelbegäran mot ditt program för att bekräfta att övervakningen fungerar som den ska.
- Inom 3-5 minuter bör du börja se telemetri visas i Azure-portalen. Var noga med att kolla in *avsnittet Programöversikt* i din Application Insights-resurs i portalen.

## <a name="troubleshooting"></a>Felsökning

Nedan visas felsökningsflödet som ska användas när telemetri inte visas i Azure-portalen som förväntat.

1. Kontrollera att programmet är under belastning och skickar/tar emot begäranden i vanlig HTTP. Eftersom telemetri lyfts bort från kabeln stöds inte krypterad trafik. Om det inte finns några inkommande eller utgående begäranden kommer det inte heller att finnas någon telemetri.
2. Kontrollera att rätt instrumenteringsnyckel finns i *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* miljövariabeln i *application-insights-istio-mixer-adapter-deployment.yaml*. Instrumenteringsnyckeln finns på fliken *Översikt för* application insights-resursen i Azure-portalen.
3. Kontrollera att rätt Kubernetes namnområde finns i *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* miljövariabel i *application-insights-istio-mixer-adapter-deployment.yaml*. Lämna den tom för att övervaka alla namnområden.
4. Se till att programmets kapslar har injicerats av Istio. Kontrollera att Istios sidovagn finns på varje pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Kontrollera att det finns en behållare med namnet *istio-proxy* som körs på podden.

5. Visa programstatistikkortets spårningar.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Antalet mottagna telemetriartiklar uppdateras en gång i minuten. Om det inte växer minut över minut - ingen telemetri skickas till adaptern av Istio.
   Leta efter eventuella fel i loggen.
6. Om det har fastställts att *Application Insight for Kubernetes-kortet* inte matas telemetri, kontrollera Istio's Mixer loggar för att ta reda på varför det inte skickar data till adaptern:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Leta efter eventuella fel, särskilt när det gäller kommunikation med *applicationinsightsadapter* adapter.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Den senaste informationen om hur projektet går framåt finns i [application insights-kortet för Istio Mixer-projektets GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Avinstallera

Så här avinstallerar du produkten för *varje* YAML-fil som hittas under *src/kubernetes/* kör:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur Azure Monitor och behållare fungerar tillsammans besöker du [Översikt över Azure Monitor för behållare](../../azure-monitor/insights/container-insights-overview.md)
