---
title: Visa Azure Monitor för behållare distributioner (för hands version) | Microsoft Docs
description: Den här artikeln beskriver real tids visningen av Kubernetes-distributioner utan att använda kubectl i Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75404780"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Visa distributioner (för hands version) i real tid

Med Azure Monitor för behållare emulerar funktionen Visa distributioner (för hands version) direkt åtkomst till Kubernetes-distributions objekt i real tid genom att `kubeclt get deployments` exponera `kubectl describe deployment {your deployment}` -och-kommandon. 

>[!NOTE]
>AKS-kluster som är aktiverade som [privata kluster](https://azure.microsoft.com/updates/aks-private-cluster/) stöds inte med den här funktionen. Den här funktionen använder direkt åtkomst till Kubernetes-API: et via en proxyserver från din webbläsare. Om du aktiverar nätverks säkerhet för att blockera Kubernetes-API: et från den här proxyn blockeras trafiken. 

>[!NOTE]
>Den här funktionen är tillgänglig i alla Azure-regioner, inklusive Azure Kina. Den är för närvarande inte tillgänglig i Azure amerikanska myndigheter.

Läs mer i Kubernetes-dokumentationen om [distributioner](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). 

## <a name="how-it-works"></a>Hur det fungerar

Funktionen Live data (för hands version) har direkt åtkomst till Kubernetes-API: et och ytterligare information om Authentication Model finns [här](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Funktionen distributioner (för hands version) utför en enda tidpunkt (uppdaterings bar) belastning mot distributions slut `/apis/apps/v1/deployments`punkten. Det gör att du kan välja en specifik distribution och läsa in Beskriv information för den aktuella distributionen mot distributions `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`slut punkten. 

Om du väljer **Uppdatera** högst upp till vänster på sidan uppdateras distributions listan. Detta simulerar att köra kommandot på `kubectl` nytt. 

>[!IMPORTANT]
>Inga data lagras permanent under den här funktionens funktion. All information som registreras under sessionen tas bort när du stänger webbläsaren eller navigerar bort från den.  

>[!NOTE]
>Du kan inte fästa Live data-data (för hands version) från-konsolen till en Azure-instrumentpanel.

## <a name="deployments-describe"></a>Distributioner beskriver

Om du vill visa mer information om en distribution, vilket är detsamma `kubectl describe deployment`som, utför du följande steg.

1. I Azure Portal bläddrar du till kluster resurs gruppen AKS och väljer din AKS-resurs.

2. På instrument panelen för AKS under **övervakning** till vänster väljer du **insikter**. 

3. Välj fliken **distributioner (för hands version)** .

    ![Vyn distributioner i Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

Vyn visar en lista över alla distributioner som körs tillsammans med namn området och annan detaljerad information, som emulerar körningen av kommandot `kubectl get deployments –all-namespaces`. Du kan sortera resultaten genom att markera någon av kolumnerna. 

![Information om rutan Egenskaper för distribution](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

När du väljer en distribution i listan visas ett egenskaps fönster automatiskt till höger på sidan. Den visar information som rör den valda distributionen som du skulle visa om du körde kommandot `kubectl describe deployment {deploymentName}`. Du kanske har lagt märke till att information saknas i beskrivningen. Framför allt är **mallen** saknas. Genom att välja fliken **RAW** kan du navigera till den avparsade informationen.  

![Information om fönstret distributions egenskaper rå data](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

När du granskar distributions information kan du se behållar loggar och händelser i real tid. Välj konsol fönstret **Visa Live-konsolen** och live data (förhands granskning) visas under data rutnätet distributioner där du kan visa Live-loggdata i en kontinuerlig data ström. Om status indikatorn för hämtningen visar en grön bock markering, som är längst till höger i fönstret, innebär det att data kan hämtas och att det börjar strömma till konsolen.

Du kan också filtrera efter namn område eller kluster nivå händelser. Mer information om hur du visar data i real tid i-konsolen finns i [Visa real tids data (för hands version) med Azure Monitor för behållare](container-insights-livedata-overview.md). 

![Distributioner Visa real tids data i-konsolen](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Nästa steg

- Om du vill fortsätta lära dig hur du använder Azure Monitor och övervakar andra aspekter av ditt AKS-kluster kan du läsa mer i [Visa Azure Kubernetes service Health](container-insights-analyze.md).

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att skapa aviseringar, visualiseringar eller utföra ytterligare analyser av klustren.
