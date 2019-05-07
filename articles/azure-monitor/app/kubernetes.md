---
title: Azure Monitor - noll instrumentation programövervakning för Kubernetes värdbaserade appar | Microsoft Docs
description: Noll instrumentation programövervakning för Kubernetes som värd för appar är en lösning för övervakning som gör det möjligt att samla in Application Insights telemetry som hör till inkommande och utgående förfrågningar till och från poddar som körs i Kubernetes-klustret genom att använda tjänsten nät teknik kallas Istio.
services: application-insights
author: rishabjolly
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: rijolly
ms.openlocfilehash: 73f95ab75b49fb8ec5b61f6e30080f8f6d474c16
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149881"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Noll instrumentering av program för Kubernetes-värdappar

> [!IMPORTANT]
> Den här funktionen finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Monitor använder nu service nät teknisk på Kubernetes-kluster för att ge ut från box-program som övervakning av Kubernetes som värd för appar. Med Application Insights-funktioner som [Programkartan](../../azure-monitor/app/app-map.md) att utforma dina beroenden [Live Metrics Stream](../../azure-monitor/app/live-stream.md) för övervakning i realtid, kraftfulla visualiseringar med den [standard instrumentpanelen](../../azure-monitor/app/overview-dashboard.md), [Metric Explorer](../../azure-monitor/platform/metrics-getting-started.md), och [arbetsböcker](../../azure-monitor/app/usage-workbooks.md). Den här funktionen kan användare upptäcka flaskhalsar och fel-anslutningar för alla sina Kubernetes arbetsbelastningar inom en vald Kubernetes-namnrymd. Genom att dra nytta av dina befintliga service nät investeringar med teknik som Istio, aktiverar Azure Monitor automatisk instrumenterats appövervakning utan någon ändring av programkoden.

> [!NOTE]
> Detta är en av många sätt att utföra av program på Kubernetes. Du kan även instrumentera alla appar finns i Kubernetes med hjälp av den [Application Insights SDK](../../azure-monitor/azure-monitor-app-hub.md) utan att behöva ett tjänst-nät. Du övervakar Kubernetes utan instrumentering av programmet med ett SDK som du kan använda den nedan metod.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En [Kubernetes-kluster](https://docs.microsoft.com/azure/aks/concepts-clusters-workloads).
- Konsolen åtkomst till klustret för att köra *kubectl*.
- En [Application Insights-resurs](create-new-resource.md)
- Ha ett tjänst-nät. Om klustret inte har distribuerats Istio, kan du lära dig hur du [installera och använda Istio i Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/istio-install).

## <a name="capabilities"></a>Funktioner

Med hjälp av noll instrumentation programövervakning för Kubernetes värdbaserade appar, kommer du att kunna använda:

- [Programkarta](../../azure-monitor/app/app-map.md)
- [Live Metrics Stream](../../azure-monitor/app/live-stream.md)
- [Instrumentpaneler](../../azure-monitor/app/overview-dashboard.md)
- [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)
- [Distribuerad spårning](../../azure-monitor/app/distributed-tracing.md)
- [Transaktionsövervakning för slutpunkt till slutpunkt](../../azure-monitor/learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installationssteg

Om du vill aktivera lösningen kommer vi att utföra följande steg:
- Distribuera programmet (om det inte redan har distribuerats).
- Se till att programmet är en del av tjänsten-nät.
- Se telemetri som samlas in.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurera din app att fungera med ett tjänst-nät

Istio stöder två olika sätt att [instrumentering av en pod](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
I de flesta fall är det enklast att markera Kubernetes-namnområde som innehåller ditt program med den *istio inmatning* etikett:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Eftersom tjänsten nät hissar data av kabeln, kan vi komma åt krypterad trafik. För trafik som inte lämnar klustret, använda ett okrypterat protokoll (till exempel HTTP). Extern trafik som måste krypteras, Överväg [hur du konfigurerar SSL-avslutning](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) på ingress-kontrollant.

Program som körs utanför service-nät påverkas inte.

### <a name="deploy-your-application"></a>Distribuera appen

- Distribuera programmet till *min app namnområde* namnområde. Om programmet redan har distribuerats och du har följt metoden automatisk sidovagn inmatning som beskrivs ovan, måste du återskapa poddarna för att säkerställa Istio lägger in dess sidovagn; initiera en rullande uppdatering eller ta bort enskilda poddar och vänta tills de återskapas.
- Se till att ditt program som uppfyller [Istio krav](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuera noll instrumentation programövervakning för Kubernetes värdbaserade appar

1. Ladda ned och extrahera en [ *Application Insights-adapter* viktig](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Gå till */src/kubernetes/* inuti mappen som versionen.
3. Edit *application-insights-istio-mixer-adapter-deployment.yaml*
    - redigera värdet för *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* miljövariabeln som innehåller instrumenteringsnyckeln för Application Insights-resurs i Azure portal för att innehålla telemetri.
    - Om det behövs kan du redigera värdet för *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* miljövariabeln ska innehålla en kommaavgränsad lista över namnområden som du vill aktivera övervakning. Lämna det tomt om du vill övervaka alla namnområden.
4. Tillämpa *varje* YAML-fil som finns under *src/kubernetes/* genom att köra följande (du måste fortfarande vara inuti */src/kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verifiera distributionen

- Kontrollera att Application Insights-kort har distribuerats:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> I vissa fall kan krävs finjustering justering. Om du vill inkludera eller exkludera telemetri för en enskild pod från som samlas in, använder *appinsights/monitoring.enabled* etikett på den pod. Detta har prioritet under alla namnområde-baserad konfiguration. Ange *appinsights/monitoring.enabled* till *SANT* att inkludera en pod och *FALSKT* undantas.

### <a name="view-application-insights-telemetry"></a>Visa Application Insights telemetry

- Skapa en exempelbegäran mot ditt program för att bekräfta att övervakning fungerar korrekt.
- Du bör börja Se telemetri som visas i Azure-portalen inom 3-5 minuter. Se till att ta en titt på *Programkartan* delen av Application Insights-resursen i portalen.

## <a name="troubleshooting"></a>Felsökning

Nedan är felsökning flödet ska användas när telemetri inte visas i Azure-portalen som förväntat.

1. Se till att programmet är under belastning och skicka och ta emot begäranden i vanlig HTTP. Eftersom telemetri lyfts från ledningen, stöds inte krypterad trafik. Om det finns inga inkommande eller utgående förfrågningar, debiteras ser du ingen telemetri antingen.
2. Se till att rätt instrumenteringsnyckeln finns i den *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* miljövariabeln i *application-insights-istio-mixer-adapter-deployment.yaml*. Instrumenteringsnyckeln finns på den *översikt* fliken Application Insights-resurs i Azure-portalen.
3. Se till att rätt Kubernetes-namnområdet har angetts i den *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* miljövariabeln i *application-insights-istio-mixer-adapter-deployment.yaml*. Lämna det tomt om du vill övervaka alla namnområden.
4. Se till att ditt programs poddar har sidovagn införs av Istio. Kontrollera att Istios sidovagn finns på varje pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Kontrollera att det finns en behållare med namnet *istio proxy* som körs på din pod.

5. Visa Application Insights-adapterns spårningar.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Antal mottagna telemetrin objekt uppdateras en gång i minuten. Om det inte växa minut under minut - som ser du ingen telemetri skickas till nätverkskortet genom Istio.
   Sök efter eventuella fel i loggen.
6. Om det har fastställts som *Application Insights för Kubernetes* nätverkskort matas inte telemetri, kontrollera Istio's Mixer-loggfilerna för att ta reda på varför det inte skickar data till nätverkskortet:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Leta efter fel, särskilt om kommunikation med *applicationinsightsadapter* nätverkskort.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Den senaste informationen för status för det här projektet, finns det [Application Insights-adapter för Istio Mixer projektets GitHub](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Avinstallera

Avinstallera produkten, för *varje* YAML-fil som finns under *src/kubernetes/* kör:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Nästa steg

Mer information om hur Azure Monitor och behållare tillsammans besök [Azure Monitor för översikt över behållare](../../azure-monitor/insights/container-insights-overview.md)