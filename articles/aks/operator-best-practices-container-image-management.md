---
title: Operatorn metodtips – bildhantering för behållare i Azure Kubernetes Services (AKS)
description: Läs kluster operatorn metodtipsen att hantera och säkra behållaravbildningar i Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 3feadaca361950df2a09f8da33fe380fc3763763
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614820"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för hantering av behållare avbildningen och säkerhet i Azure Kubernetes Service (AKS)

När du utvecklar och kör program i Azure Kubernetes Service (AKS) är en nyckelfaktor säkerheten för behållare och behållaravbildningar. Behållare som innehåller inaktuella basera bilder eller okorrigerade programmet körningar medföra en säkerhetsrisk och möjliga angreppsvinkel. För att minimera riskerna, bör du integrera verktyg som kan söka efter och åtgärda problem i dina behållare vid Byggtiden samt runtime. Den tidigare i processen sårbarhet eller inaktuella basavbildning har fastnat, säkrare desto klustret. I den här artikeln *behållare* innebär att båda de behållaravbildningar som lagras i ett behållarregister och behållarna som körs.

Den här artikeln handlar om hur du skyddar dina behållare i AKS. Lär dig att:

> [!div class="checklist"]
> * Söka efter och åtgärda sårbarheter i bild
> * Automatiskt utlösa och distribuera om avbildningar när en basavbildning har uppdaterats

Du kan också läsa metod tips för [kluster säkerhet][best-practices-cluster-security] och Pod- [säkerhet][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Skydda bilderna och körtid

**Bästa praxis riktlinjer** – söka igenom dina behållaravbildningar för säkerhetsrisker och bara distribuera avbildningar som har klarat verifieringen. Regelbundet uppdatera Källavbildningen och körning av program och sedan distribuera igen arbetsbelastningar i AKS-klustret.

Ett problem med införandet av behållarbaserade arbetsbelastningar verifierar säkerheten för avbildningar och runtime som används för att skapa dina egna program. Hur du se till att du inte orsakar säkerhetsrisker i dina distributioner? Distributions arbets flödet bör innehålla en process för att skanna behållar avbildningar med hjälp av verktyg som [twistlock][twistlock] eller [turkos][aqua], och sedan bara tillåta att verifierade avbildningar distribueras.

![Skanna och åtgärda behållaravbildningar, verifiera och distribuera](media/operator-best-practices-container-security/scan-container-images-simplified.png)

I ett verkliga exempel använda du en kontinuerlig integrering och en pipeline för kontinuerlig distribution (CI/CD) för att automatisera den avbildningen genomsökningar, verifiering och distributioner. Azure Container Registry innehåller säkerhetsproblemen genomsökning funktioner.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatiskt skapa nya avbildningar på grundläggande uppdateringar

**Bästa praxis riktlinjer** – som du använder när Källavbildningen programavbildningar, Använd automation för att skapa nya avbildningar när basavbildningen uppdateras. Eftersom dessa Källavbildningen innehåller vanligtvis säkerhetskorrigeringar, uppdatera alla nedströms program-behållaravbildningar.

Varje gång en basavbildning uppdateras bör alla underordnade behållaravbildningar också uppdateras. Den här bygg processen bör integreras i pipeline för validering och distribution, till exempel [Azure][azure-pipelines] -pipeliner eller Jenkins. Dessa pipelines ser till att dina program fortsätter att köras på de uppdaterade baserat bilderna. När dina program behållaravbildningar verifieras uppdateras AKS-distributioner sedan för att köra de senaste, säker avbildningarna.

Azure Container Registry uppgifter kan också automatiskt uppdatera behållaravbildningar när basavbildningen uppdateras. Den här funktionen kan du skapa ett litet antal Källavbildningen och hålla dem uppdaterade med fel och säkerhet redigeringar regelbundet.

Mer information om uppdateringar av bas avbildningar finns i avsnittet [om att automatisera avbildningar på bas avbildnings uppdatering med Azure Container Registry uppgifter][acr-base-image-update].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar dina behållare. Om du vill implementera vissa av dessa områden, finns i följande artiklar:

* [Automatisera avbildning bygger på en bas avbildnings uppdatering med Azure Container Registry uppgifter][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
