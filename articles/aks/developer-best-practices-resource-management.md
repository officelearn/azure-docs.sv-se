---
title: Metodtips för Developer - resurshantering i Azure Kubernetes Services (AKS)
description: Läs application developer metodtipsen för resurshantering i Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: zarhoads
ms.openlocfilehash: aebade14f3a8a1095925d17325ce99b78031dc32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466654"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Metodtips för programutvecklare att hantera resurser i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS), finns det några viktiga områden att tänka på. Hur du hanterar din distribution av program kan ge försämrade slutanvändarens upplevelse av tjänster som du anger. För att hjälpa dig att lyckas, Tänk på några av metodtipsen du kan följa när du utvecklar och kör program i AKS.

Den här bästa praxis-artikeln fokuserar på hur du kör ditt kluster och arbetsbelastningar från en utvecklares perspektiv för programmet. Information om administrativa metodtips finns i [kluster operatorn Metodtips för isolering och resurshantering i Azure Kubernetes Service (AKS)][operator-best-practices-isolation]. I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vad är pod resursbegäranden och gränser
> * Sätt att utveckla och distribuera program med utveckling blanksteg och Visual Studio Code
> * Hur du använder den `kube-advisor` verktyg för att söka efter problem med distributioner

## <a name="define-pod-resource-requests-and-limits"></a>Definiera pod resursbegäranden och begränsningar

**Bästa praxis riktlinjer** – Ställ in pod begäranden och gränser för alla poddar i YAML-manifest. Om AKS-kluster använder *resurskvoter*, din distribution kan avvisas om du inte definierar dessa värden.

En primär sätt att hantera beräkningsresurser i ett AKS-kluster är att använda pod-begäranden och begränsningar. Dessa begäranden och begränsningar kan Kubernetes scheduler vet vad beräkningsresurser som en pod ska tilldelas.

* **Pod begäranden** definiera en viss mängd CPU och minne som krävs för din pod. Dessa begäranden ska vara mycket av beräkningsresurser som en pod som behövs för att tillhandahålla en godtagbar nivå av prestanda.
    * När scheduler Kubernetes försöker placera en pod på en nod, används pod-begäranden till att avgöra vilken nod har tillräckligt med resurser som är tillgängliga.
    * Övervaka prestanda för programmet och justera dessa begäranden för att se till att du inte definierar färre resurser som krävs för att upprätthålla en godtagbar nivå av prestanda.
* **Pod gränser** är längsta CPU och minne som en pod kan använda. Dessa gränser förhindra att en eller två skenande poddar tar för mycket Processortid och minnesresurser från noden. Det här scenariot skulle minska prestandan för noden och andra poddar som körs på den.
    * Om du inte ange en pod-gräns som är högre än vad som har stöd för noderna. Varje nod i AKS reserverar en viss mängd CPU och minne för Kubernetes kärnkomponenter. Ditt program kan prova att använda för många resurser på noden för andra poddarna för att köra.
    * Igen, övervaka prestanda för ditt program vid olika tidpunkter under dagens eller veckans lopp. Avgöra när behovstoppen är och justera pod-gränser för de resurser som krävs för att uppfylla programmets behov.

I din pod-specifikationer är det bäst att definiera dessa begäranden och begränsningar. Om du inte anger dessa värden, Förstå Kubernetes scheduler inte vilka resurser som krävs. Scheduler kan schemalägga pod på en nod utan tillräckligt med resurser för att ge acceptabel prestanda. Klusteradministratören kan ställa *resurskvoter* för ett namnområde som du måste ange resursbegäranden och begränsningar. Mer information finns i [resurskvoter på AKS-kluster][resource-quotas].

När du definierar en CPU-begäran eller begränsa mäts värdet i CPU-enheter. *1.0* CPU motsvarar en underliggande virtuella processorkärna på noden. Samma måttet används för GPU: er. Du kan också definiera en bråkdelar begäran eller en gräns, vanligtvis i millicpu. Till exempel *100 miljoner* är *0.1* av en underliggande virtuella CPU-kärna.

I följande grundläggande exempel för en enda NGINX-pod poden begär *100 miljoner* processortid och *128Mi* minne. Resursgränser för poden är inställda på *250 miljoner* CPU och *256Mi* minne:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Läs mer om resource mätning av faktisk användning och tilldelningar [hantera beräkningsresurser för behållare][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Utveckla och felsöka program mot ett AKS-kluster

**Bästa praxis riktlinjer** -utvecklingsteam ska distribuera och felsöka mot ett AKS-kluster med utveckling blanksteg. Den här modellen utveckling ser till att rollbaserade åtkomstkontroller, nätverk eller lagringsbehov implementeras innan appen har distribuerats till produktion.

Med Azure Dev blanksteg, utveckla, felsöka och testa program direkt mot ett AKS-kluster. Utvecklare i ett team arbetar tillsammans för att skapa och testa under hela programmets livscykel. Du kan fortsätta att använda befintliga verktyg som Visual Studio eller Visual Studio Code. Ett tillägg har installerats för Dev blanksteg som ger ett alternativ för att köra och felsöka programmet i ett AKS-kluster:

![Felsök program i ett AKS-kluster med utveckling blanksteg](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Integrerad utvecklings- och processen Dev blanksteg minskar behovet av lokala testmiljöer, till exempel [minikube][minikube]. Däremot du utvecklar och testar mot ett AKS-kluster. Det här klustret kan skyddas och isolerade som anges i föregående avsnitt om användning av namnområden logiskt isolera ett kluster. När dina appar är redo att distribuera till produktion, kan du tryggt distribuera som din utveckling har utförts mot ett verkligt AKS-kluster.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Använd Visual Studio Code-tillägget för Kubernetes

**Bästa praxis riktlinjer** – installera och använda VS Code-tillägg för Kubernetes när du skriver YAML manifest. Du kan också använda tillägget för integrerad distributionslösning och kan bidra programägare som sällan interagerar med AKS-klustret.

Den [Visual Studio Code-tillägg för Kubernetes] [ vscode-kubernetes] hjälper dig att utveckla och distribuera program till AKS. Tillägget visar intellisense för Kubernetes-resurser och Helm-diagram och mallar. Du kan också bläddra, distribuera och redigera Kubernetes-resurser från VS Code. Tillägget också innehåller en kontroll av intellisense för resursbegäranden eller begränsar anges i pod-specifikationer:

![VS Code-tillägg för Kubernetes varning om saknade minnesbegränsningar](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Regelbundet söka efter problem med kube-advisor

**Bästa praxis riktlinjer** -regelbundet kör den senaste versionen av `kube-advisor` verktyg med öppen källkod att identifiera problem i klustret. Om du använder resurskvoter i ett befintligt AKS-kluster, kör `kube-advisor` först för att hitta poddar som inte har resursbegäranden och gränser som definierats.

Den [kube-advisor] [ kube-advisor] verktyget är ett projekt med associerade AKS öppen källkod som genomsöker ett Kubernetes-kluster och rapporter på problem som hittas. En användbar check är att identifiera poddar som inte har resursbegäranden och begränsningar på plats.

I ett AKS-kluster som är värd för många utvecklingsteam och program, kan det vara svårt att spåra poddar utan dessa resurs-begäranden och begränsar uppsättningen. Som bästa praxis, regelbundet kör `kube-advisor` på AKS-kluster.

## <a name="next-steps"></a>Nästa steg

Den här bästa praxis-artikeln fokuserar på hur du kör ditt kluster och arbetsbelastningar från ett kluster operatorn perspektiv. Information om administrativa metodtips finns i [kluster operatorn Metodtips för isolering och resurshantering i Azure Kubernetes Service (AKS)][operator-best-practices-isolation].

Om du vill implementera vissa av dessa metodtips, finns i följande artiklar:

* [Utveckla med utveckling blanksteg][dev-spaces]
* [Om problem med kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
