---
title: Använd Application Insights för att övervaka din Azure Kubernetes service (AKS) eller andra Kubernetes värdbaserade program – Azure Monitor | Microsoft Docs
description: Azure Monitor använder nät verks teknik, Istio, på ditt Kubernetes-kluster för att tillhandahålla program övervakning för alla Kubernetes värdbaserade program. På så sätt kan du samla in Application Insights telemetri som rör inkommande och utgående begär anden till och från poddar som körs i klustret.
ms.topic: conceptual
author: tokaplan
ms.author: alkaplan
ms.date: 04/25/2019
ms.openlocfilehash: 3cd43963175594fcdc1c3c67d6b2493ce1ccd313
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321929"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes-hosted-applications-with-istio---deprecated"></a>Övervakning av program vara utan instrumentering för Kubernetes-värdbaserade program med Istio-inaktuell

> [!IMPORTANT]
> Den här funktionen är för närvarande inaktuell och kommer inte längre att stödjas efter den 1 augusti 2020.
> För närvarande går det bara att aktivera den kodbaserade övervakningen för [Java via fristående agent](./java-in-process-agent.md). För andra språk använder du SDK: er för att övervaka dina appar på AKS: [ASP.net Core](./asp-net-core.md), [ASP.net](./asp-net.md), [Node.js](./nodejs.md), [Java Script](./javascript.md)och [python](./opencensus-python.md).

Azure Monitor använder nu teknisk teknik för service nät på ditt Kubernetes-kluster för att tillhandahålla program övervakning för alla Kubernetes appar som körs. Med standardinställningar för program insikter som [program karta](./app-map.md) för att modellera dina beroenden [Live Metrics Stream](./live-stream.md) för real tids övervakning, kraftfulla visualiseringar med [standard instrument panelen](./overview-dashboard.md), [Metric Explorer](../platform/metrics-getting-started.md)och [arbets böcker](../platform/workbooks-overview.md). Med den här funktionen kan användarna upptäcka Flask halsar i prestanda och fel hotspots i alla sina Kubernetes-arbetsbelastningar inom ett valt Kubernetes-namnområde. Genom att dra nytta av dina befintliga service nät investeringar med tekniker som Azure Monitor Istio aktive ras automatiskt instrumenterad app Monitoring utan någon ändring av programmets kod.

> [!NOTE]
> Det här är ett av många sätt att utföra program övervakning på Kubernetes.Du kan också instrumentera alla appar som finns i Kubernetes med hjälp av [Application Insights SDK](../azure-monitor-app-hub.yml) utan att det behövs något service nät. Om du vill övervaka Kubernetes utan att instrumentera programmet med ett SDK kan du använda metoden nedan.

## <a name="prerequisites"></a>Krav

- Ett [Kubernetes-kluster](../../aks/concepts-clusters-workloads.md).
- Konsol åtkomst till klustret för att köra *kubectl*.
- En [program Insight-resurs](create-new-resource.md)
- Ha ett tjänst nät. Om klustret inte har Istio distribuerat kan du lära dig hur du [installerar och använder Istio i Azure Kubernetes-tjänsten](../../aks/servicemesh-istio-install.md).

## <a name="capabilities"></a>Funktioner

Genom att använda program övervakning utan Instrumentation för Kubernetes värdbaserade appar kan du använda:

- [Programkarta](./app-map.md)
- [Live Stream mått](./live-stream.md)
- [Instrumentpaneler](./overview-dashboard.md)
- [Metrics Explorer](../platform/metrics-getting-started.md)
- [Distribuerad spårning](./distributed-tracing.md)
- [Transaktions övervakning från slut punkt till slut punkt](../learn/tutorial-performance.md#identify-slow-server-operations)

## <a name="installation-steps"></a>Installationssteg

För att aktivera lösningen utför vi följande steg:
- Distribuera programmet (om det inte redan har distribuerats).
- Se till att programmet är en del av tjänst nätet.
- Observera insamlad telemetri.

### <a name="configure-your-app-to-work-with-a-service-mesh"></a>Konfigurera din app så att den fungerar med ett service nät

Istio stöder två sätt att [instrumentera en POD](https://istio.io/docs/setup/kubernetes/additional-setup/sidecar-injection/).
I de flesta fall är det enklast att markera Kubernetes-namnområdet som innehåller ditt program med *istio-insprutning-* etiketten:

```console
kubectl label namespace <my-app-namespace> istio-injection=enabled
```

> [!NOTE]
> Eftersom tjänst nätet lyfter ut data från kabeln kan vi inte avlyssna den krypterade trafiken. Använd ett okrypterat protokoll (till exempel HTTP) för trafik som inte lämnar klustret. För extern trafik som måste krypteras bör du överväga att konfigurera [TLS-avslutning på ingångs](https://kubernetes.io/docs/concepts/services-networking/ingress/#tls) styrenheten.

Program som körs utanför tjänst nätet påverkas inte.

### <a name="deploy-your-application"></a>Distribuera appen

- Distribuera ditt program till namn områdes namn området *My-app-namespace* . Om programmet redan har distribuerats och du har följt den automatiska metoden för automatisk insprutning som beskrivs ovan, måste du återskapa poddar för att säkerställa att Istio injicerar sin sidvagn. Starta antingen en rullande uppdatering eller ta bort enskilda poddar och vänta tills de har återskapats.
- Se till att programmet uppfyller [Istio-kraven](https://istio.io/docs/setup/kubernetes/prepare/requirements/).

### <a name="deploy-zero-instrumentation-application-monitoring-for-kubernetes-hosted-apps"></a>Distribuera program övervakning utan Instrumentation för Kubernetes-värdbaserade appar

1. Hämta och extrahera en [ *Application Insights korts* version](https://github.com/Microsoft/Application-Insights-Istio-Adapter/releases/).
2. Navigera till */src/Kubernetes/* i mappen release.
3. Redigera *Application-Insights-istio-mixer-adapter-Deployment. yaml*
    - redigera värdet för *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* -miljövariabeln som innehåller Instrumentation-nyckeln för den Application Insights resursen i Azure Portal som ska innehålla Telemetrin.
    - Om det behövs redigerar du värdet för *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* -miljövariabeln så att den innehåller en kommaavgränsad lista över namn områden som du vill aktivera övervakning för. Lämna det tomt om du vill övervaka alla namn områden.
4. Använd *varje* yaml-fil som finns under *src/Kubernetes/* genom att köra följande (du måste fortfarande vara inuti */src/Kubernetes/*):

   ```console
   kubectl apply -f .
   ```

### <a name="verify-deployment"></a>Verifiera distributionen

- Se till att Application Insights adapter har distribuerats:

  ```console
  kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
  ```
> [!NOTE]
> I vissa fall krävs fin justering. Om du vill ta med eller undanta telemetri för en enskild Pod från att samlas in använder du *appinsights/Monitoring. enabled* Label på den pod. Detta kommer att ha prioritet över all namnrymds-baserad konfiguration. Ange *appinsights/övervakning. aktiverat* till *Sant* om du vill inkludera Pod och till *false* för att utesluta det.

### <a name="view-application-insights-telemetry"></a>Visa Application Insights telemetri

- Generera en exempel förfrågan mot ditt program för att bekräfta att övervakningen fungerar korrekt.
- Inom 3-5 minuter bör du börja se telemetri visas i Azure Portal. Se avsnittet *program karta* i Application Insights resursen i portalen.

## <a name="troubleshooting"></a>Felsökning

Nedan visas fel söknings flödet som används när telemetri inte visas i Azure Portal som förväntat.

1. Se till att programmet håller på att läsas in och skickar/tar emot förfrågningar i vanlig HTTP. Eftersom telemetri lyfts bort från kabeln stöds inte krypterad trafik. Om det inte finns några inkommande eller utgående begär Anden, kommer det inte att finnas någon telemetri.
2. Kontrol lera att rätt Instrumentation-nyckel finns i *ISTIO_MIXER_PLUGIN_AI_INSTRUMENTATIONKEY* -miljövariabeln i *Application-Insights-ISTIO-mixer-adapter-Deployment. yaml*. Instrumentation-nyckeln finns på fliken *Översikt* i Application Insights resursen i Azure Portal.
3. Se till att rätt Kubernetes-namnrymd finns i *ISTIO_MIXER_PLUGIN_WATCHLIST_NAMESPACES* -miljövariabeln i *Application-Insights-ISTIO-mixer-adapter-Deployment. yaml*. Lämna det tomt om du vill övervaka alla namn områden.
4. Se till att programmets poddar har sidvagn med Istio. Kontrol lera att Istio finns på varje pod.

   ```console
   kubectl describe pod -n <my-app-namespace> <my-app-pod-name>
   ```
   Kontrol lera att det finns en behållare med namnet *istio-proxy* som körs på pod.

5. Visa Application Insights kortets spår.

   ```console
   kubectl get pods -n istio-system -l "app=application-insights-istio-mixer-adapter"
   kubectl logs -n istio-system application-insights-istio-mixer-adapter-<fill in from previous command output>
   ```

   Antalet mottagna telemetridata uppdateras en gång i minuten. Om det inte växer Minute över minut – ingen telemetri skickas till kortet av Istio.
   Leta efter eventuella fel i loggen.
6. Om det har kon figurer ATS att *program insikter för Kubernetes* -adapter inte används för telemetri, kontrol lera Istios mixer loggar för att ta reda på varför det inte skickar data till kortet:

   ```console
   kubectl get pods -n istio-system -l "istio=mixer,app=telemetry"
   kubectl logs -n istio-system istio-telemetry-<fill in from previous command output> -c mixer
   ```
   Sök efter eventuella fel, särskilt för kommunikation med *applicationinsightsadapter* -adaptern.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Den senaste informationen om förloppet för det här projektet finns på [Application Insights adapter för GitHub för Istio-mixer](https://github.com/Microsoft/Application-Insights-Istio-Adapter/blob/master/SETUP.md#faq).

## <a name="uninstall"></a>Avinstallera

För att avinstallera produkten, för *varje* yaml-fil som hittas under *src/Kubernetes/* Run:

```console
kubectl delete -f <filename.yaml>
```


## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur Azure Monitor och behållare arbetar tillsammans besöker [Azure Monitor för behållare översikt](../insights/container-insights-overview.md)

