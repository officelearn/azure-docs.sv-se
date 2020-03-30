---
title: Metodtips för operatör – hantering av behållaravbildning i Azure Kubernetes Services (AKS)
description: Lär dig metodtips för klusteroperatören för hur du hanterar och skyddar behållaravbildningar i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594756"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för hantering av behållaravbildning och säkerhet i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) är säkerheten för dina behållare och behållaravbildningar en viktig faktor. Behållare som innehåller inaktuella basavbildningar eller oacascherade programkörningar medför en säkerhetsrisk och eventuell attackvektor. För att minimera dessa risker bör du integrera verktyg som söker efter och åtgärdar problem i dina behållare vid byggtid samt körning. Ju tidigare i processen säkerhetsproblem eller inaktuella basavbildning fångas, desto säkrare är klustret. I den här artikeln betyder *behållare* både behållaravbildningar som lagras i ett behållarregister och de behållare som körs.

Den här artikeln fokuserar på hur du skyddar dina behållare i AKS. Lär dig att:

> [!div class="checklist"]
> * Söka efter och åtgärda bildsäkerhetsproblem
> * Utlöser och distribuerar om behållaravbildningar automatiskt när en basavbildning uppdateras

Du kan också läsa metodtipsen för [klustersäkerhet][best-practices-cluster-security] och [pod-säkerhet][best-practices-pod-security].

Du kan också använda [containersäkerhet i Security Center][security-center-containers] för att söka igenom dina behållare efter säkerhetsproblem.  Det finns också [Azure Container Registry integration][security-center-acr] med Security Center för att skydda dina avbildningar och register från sårbarheter.

## <a name="secure-the-images-and-run-time"></a>Säkra avbildningarna och körtiden

**Vägledning för bästa praxis** – Sök igenom behållaravbildningar efter säkerhetsproblem och distribuera endast avbildningar som har klarat valideringen. Uppdatera regelbundet basavbildningar och programkörning och distribuera sedan om arbetsbelastningar i AKS-klustret.

Ett problem med antagandet av behållarbaserade arbetsbelastningar är att verifiera säkerheten för avbildningar och körning som används för att skapa egna program. Hur ser du till att du inte inför säkerhetsproblem i dina distributioner? Distributionsarbetsflödet bör innehålla en process för att skanna behållaravbildningar med hjälp av verktyg som [Twistlock][twistlock] eller [Aqua][aqua]och sedan endast tillåta att verifierade avbildningar distribueras.

![Skanna och åtgärda behållaravbildningar, validera och distribuera](media/operator-best-practices-container-security/scan-container-images-simplified.png)

I ett verkligt exempel kan du använda en pipeline för kontinuerlig integrering och kontinuerlig distribution (CI/CD) för att automatisera avbildningssökningar, verifiering och distributioner. Azure Container Registry innehåller dessa funktioner för skanning av sårbarheter.

## <a name="automatically-build-new-images-on-base-image-update"></a>Skapa automatiskt nya bilder på basavbildningsuppdatering

**Vägledning för bästa praxis** – När du använder basavbildningar för programavbildningar använder du automatisering för att skapa nya avbildningar när basavbildningen uppdateras. Eftersom dessa basavbildningar vanligtvis innehåller säkerhetskorrigeringar uppdaterar du alla nedströms programbehållareavbildningar.

Varje gång en basavbildning uppdateras bör även alla nedströmsbehållaresavbildningar uppdateras. Den här byggprocessen bör integreras i validerings- och distributionspipelor som [Azure Pipelines][azure-pipelines] eller Jenkins. Dessa pipelines ser till att dina program fortsätter att köras på de uppdaterade baserade avbildningarna. När dina programbehållareavbildningar har validerats kan AKS-distributionerna sedan uppdateras för att köra de senaste, säkra avbildningarna.

Azure Container Registry Tasks kan också automatiskt uppdatera behållaravbildningar när basavbildningen uppdateras. Med den här funktionen kan du skapa ett litet antal basavbildningar och regelbundet hålla dem uppdaterade med bugg- och säkerhetskorrigeringar.

Mer information om grundläggande avbildningsuppdateringar finns i [Automatisera avbildningsversioner på basavbildningsuppdatering med Azure Container Registry Tasks][acr-base-image-update].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserade på hur du skyddar dina behållare. Information om hur du implementerar några av dessa områden finns i följande artiklar:

* [Automatisera avbildningen bygger på basavbildningsuppdatering med Azure Container Registry Tasks][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration