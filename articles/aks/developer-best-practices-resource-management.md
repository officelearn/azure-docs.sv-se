---
title: Metodtips för resurshantering
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för programutvecklare för resurshantering i Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: 0052657c947f8a9ff9c9d6aef86ff16d9a22adae
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803491"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Metodtips för programutvecklare att hantera resurser i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) finns det några viktiga områden att tänka på. Hur du hanterar dina programdistributioner kan påverka slutanvändarens upplevelse av tjänster som du tillhandahåller negativt. För att hjälpa dig att lyckas bör du tänka på några metodtips som du kan följa när du utvecklar och kör program i AKS.

Den här metodtipsartikeln fokuserar på hur du kör ditt kluster och arbetsbelastningar ur ett programutvecklareperspektiv. Information om metodtips för administrativa metoder finns i [metodtips för klusteroperatör för isolering och resurshantering i Azure Kubernetes Service (AKS).][operator-best-practices-isolation] I den här artikeln lär du dig:

> [!div class="checklist"]
> * Vad är pod resursförfrågningar och gränser
> * Olika sätt att utveckla och distribuera program med Dev Spaces och Visual Studio Code
> * Så här `kube-advisor` använder du verktyget för att söka efter problem med distributioner

## <a name="define-pod-resource-requests-and-limits"></a>Definiera begäranden och gränser för pod-resurs

**Vägledning för bästa praxis** – Ange pod-begäranden och begränsningar för alla poddar i YAML-manifesten. Om AKS-klustret använder *resurskvoter*kan distributionen avvisas om du inte definierar dessa värden.

Ett primärt sätt att hantera beräkningsresurserna i ett AKS-kluster är att använda pod-begäranden och begränsningar. Dessa begäranden och begränsningar låter Kubernetes schemaläggaren veta vilka beräkningsresurser en pod ska tilldelas.

* **Pod CPU/Minne begäranden** definierar en viss mängd CPU och minne som pod behöver regelbundet.
    * När Kubernetes-schemaläggaren försöker placera en pod på en nod används pod-begäranden för att avgöra vilken nod som har tillräckliga resurser tillgängliga för schemaläggning.
    * Om du inte anger en pod-begäran kommer den att vara den gräns som definierats som standard.
    * Det är mycket viktigt att övervaka programmets prestanda för att justera dessa begäranden. Om otillräckliga begäranden görs kan ditt program få försämrade prestanda på grund av att en nod schemaläggers. Om begäranden överskattas kan ditt program ha svårare att schemalägga.
* **Pod CPU / Minne gränser** är den maximala mängden CPU och minne som en pod kan använda. Dessa gränser hjälper till att definiera vilka poddar som ska dödas i händelse av nod instabilitet på grund av otillräckliga resurser. Utan ordentliga gränser som pods kommer att dödas tills resurstrycket lyfts.
    * Pod gränser hjälpa till att definiera när en pod har förlorat kontrollen över resursförbrukning. När en gräns överskrids prioriteras podden för avlivning för att upprätthålla nodhälsan och minimera påverkan på poddar som delar noden.
    * Om du inte anger en pod-gräns kan den inte ha det högsta tillgängliga värdet på en viss nod.
    * Ange inte en pod gräns högre än dina noder kan stödja. Varje AKS-nod reserverar en viss mängd CPU och minne för kubernetes-kärnkomponenterna. Programmet kan försöka använda för många resurser på noden för att andra poddar ska kunna köras.
    * Återigen är det mycket viktigt att övervaka hur din ansökan fungerar vid olika tidpunkter under dagen eller veckan. Bestäm när den högsta efterfrågan är och justera pod-gränserna till de resurser som krävs för att uppfylla programmets maximala behov.

I pod-specifikationerna är det **bästa praxis och mycket viktigt** att definiera dessa begäranden och gränser baserat på ovanstående information. Om du inte inkluderar dessa värden kan Kubernetes-schemaläggaren inte ta hänsyn till de resurser som dina program behöver för att underlätta schemaläggningsbeslut.

Om schemaläggaren placerar en pod på en nod med otillräckliga resurser försämras programprestanda. Det rekommenderas starkt att klusteradministratörer anger *resurskvoter* för ett namnområde som kräver att du anger resursbegäranden och begränsningar. Mer information finns i [resurskvoter för AKS-kluster][resource-quotas].

När du definierar en CPU-begäran eller gräns mäts värdet i CPU-enheter. 
* *1,0* CPU motsvarar en underliggande virtuell CPU-kärna på noden. 
* Samma mätning används för GPU:er.
* Du kan definiera bråk som mäts i millicore. Till exempel är *100m* *0,1* av en underliggande vCPU kärna.

I följande grundläggande exempel för en enda NGINX-pod begär *podden 100 m* CPU-tid och *128Mi* minne. Resursgränserna för pod är inställda på *250m* CPU och *256Mi* minne:

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

Mer information om resursmätningar och tilldelningar finns i [Hantera beräkningsresurser för behållare][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Utveckla och felsöka program mot ett AKS-kluster

**Vägledning för bästa praxis** – utvecklingsteam bör distribuera och felsöka mot ett AKS-kluster med Dev Spaces. Den här utvecklingsmodellen ser till att rollbaserade åtkomstkontroller, nätverks- eller lagringsbehov implementeras innan appen distribueras till produktion.

Med Azure Dev Spaces utvecklar, felsöker och testar du program direkt mot ett AKS-kluster. Utvecklare inom ett team arbetar tillsammans för att bygga och testa under hela programmets livscykel. Du kan fortsätta att använda befintliga verktyg som Visual Studio eller Visual Studio Code. Ett tillägg installeras för Dev Spaces som ger möjlighet att köra och felsöka programmet i ett AKS-kluster:

![Felsöka program i ett AKS-kluster med Dev Spaces](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Denna integrerade utveckling och testprocess med Dev Spaces minskar behovet av lokala testmiljöer, till exempel [minikube][minikube]. I stället utvecklar och testar du mot ett AKS-kluster. Det här klustret kan skyddas och isoleras enligt föregående avsnitt om användningen av namnområden för att logiskt isolera ett kluster. När dina appar är redo att distribueras till produktion kan du med tillförsikt distribuera eftersom din utveckling gjordes mot ett riktigt AKS-kluster.

Azure Dev Spaces är avsett att användas med program som körs på Linux-poddar och noder.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Använda tillägget Visual Studio-kod för Kubernetes

**Vägledning för bästa praxis** - Installera och använd VS-kodtillägget för Kubernetes när du skriver YAML-manifest. Du kan också använda tillägget för integrerad distributionslösning, vilket kan hjälpa programägare som sällan interagerar med AKS-klustret.

[Tillägget Visual Studio-kod för Kubernetes][vscode-kubernetes] hjälper dig att utveckla och distribuera program till AKS. Tillägget ger intellisense för Kubernetes-resurser och Helm-diagram och mallar. Du kan också bläddra bland, distribuera och redigera Kubernetes-resurser inifrån VS-kod. Tillägget ger också en intellisense-kontroll för resursbegäranden eller begränsningar som anges i pod-specifikationerna:

![VS-kodtillägg för Kubernetes varnar för saknade minnesgränser](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Kontrollera regelbundet om det finns problem med kube-advisor

**Vägledning för bästa praxis** – Kör `kube-advisor` regelbundet den senaste versionen av open source-verktyget för att identifiera problem i klustret. Om du använder resurskvoter på ett `kube-advisor` befintligt AKS-kluster kör du först för att hitta poddar som inte har resursbegäranden och begränsningar definierade.

[Kube-advisor-verktyget][kube-advisor] är ett associerat AKS-projekt med öppen källkod som söker igenom ett Kubernetes-kluster och rapporterar om problem som hittas. En användbar kontroll är att identifiera poddar som inte har resursbegäranden och begränsningar på plats.

Verktyget kube-advisor kan rapportera om resursbegäran och gränser som saknas i PodSpecs för Windows-program samt Linux-program, men själva kube-advisor-verktyget måste schemaläggas på en Linux-pod. Du kan schemalägga en pod så att den körs på en nodpool med ett visst operativsystem med hjälp av en [nodväljare][k8s-node-selector] i podns konfiguration.

I ett AKS-kluster som är värd för många utvecklingsteam och program kan det vara svårt att spåra poddar utan att dessa resursbegäranden och begränsningar har angetts. Det bästa är att `kube-advisor` köras regelbundet på AKS-kluster.

## <a name="next-steps"></a>Nästa steg

Den här artikeln med metodtips fokuserade på hur du kör ditt kluster och arbetsbelastningar från ett klusteroperatorperspektiv. Information om metodtips för administrativa metoder finns i [metodtips för klusteroperatör för isolering och resurshantering i Azure Kubernetes Service (AKS).][operator-best-practices-isolation]

Information om hur du implementerar några av de här metodtipsen finns i följande artiklar:

* [Utveckla med Dev Spaces][dev-spaces]
* [Sök efter problem med kube-advisor][aks-kubeadvisor]

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
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
