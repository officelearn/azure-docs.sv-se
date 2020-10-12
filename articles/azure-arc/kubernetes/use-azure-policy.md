---
title: Använd Azure Policy om du vill använda klusterkonfigurationer i skala (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använd Azure Policy för att tillämpa klusterkonfigurationer i stor skala
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: e4279f3d89376320116067bf191e3196271918ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050045"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Använd Azure Policy om du vill använda klusterkonfigurationer i skala (för hands version)

## <a name="overview"></a>Översikt

Använd Azure Policy för att framtvinga att varje `Microsoft.Kubernetes/connectedclusters` resurs eller Git-Ops aktive rad `Microsoft.ContainerService/managedClusters` resurs har en speciell `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tillämpning. Om du vill använda Azure Policy väljer du en befintlig princip definition och skapar en princip tilldelning. När du skapar princip tilldelningen anger du omfånget för tilldelningen: det här är en Azure-resurs grupp eller-prenumeration. Du anger också parametrarna för `sourceControlConfiguration` som ska skapas. När tilldelningen har skapats kommer principmodulen att identifiera alla `connectedCluster` eller `managedCluster` resurser som finns inom omfånget och som ska gälla `sourceControlConfiguration` för var och en.

Om du använder flera git-databaser som källor till sanningen för varje kluster (till exempel en lagrings platsen för central IT/kluster operatör och andra databaser för program team), kan du aktivera detta genom att använda flera princip tilldelningar, varje princip tilldelning som kon figurer ATS för att använda en annan git-lagrings platsen.

## <a name="prerequisite"></a>Förutsättning

Se till att du har `Microsoft.Authorization/policyAssignments/write` behörighet för det omfång (prenumeration eller resurs grupp) där du vill skapa den här princip tilldelningen.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

1. I Azure Portal navigerar du till princip och i avsnittet **redigering** på sid panelen väljer du **definitioner**.
2. Välj den inbyggda principen "distribuera GitOps till Kubernetes-kluster" i kategorin "Kubernetes" och klicka på **tilldela**.
3. Ange **omfattningen** för hanterings gruppen, prenumerationen eller resurs gruppen där princip tilldelningen ska gälla.
4. Ange **undantag**om du vill undanta resurser från princip omfånget.
5. Ge princip tilldelningen ett **namn** och en **Beskrivning** som du kan använda för att identifiera det enkelt.
6. Se till att **princip tillämpning** är *aktive rad*.
7. Välj **Nästa**.
8. Ange parameter värden som ska användas när du skapar `sourceControlConfiguration` .
9. Välj **Nästa**.
10. Aktivera **skapa en reparations uppgift**.
11. Försäkra dig om att **skapa en hanterad identitet** kontrol leras och att identiteten har **deltagar** behörighet. Se [det här dokumentet](../../governance/policy/assign-policy-portal.md) och [kommentaren i det här dokumentet](../../governance/policy/how-to/remediate-resources.md) för mer information om de behörigheter som du behöver.
12. Välj **Granska + skapa**.

När princip tilldelningen har skapats, för alla nya `connectedCluster` resurser (eller `managedCluster` resurser med GitOps-agenterna installerade) som finns inom tilldelnings omfånget, `sourceControlConfiguration` tillämpas. För befintliga kluster måste du köra en reparations uppgift manuellt. Det tar vanligt vis från 10-20 minuter innan princip tilldelningen börjar gälla.

## <a name="verify-a-policy-assignment"></a>Verifiera en princip tilldelning

1. I Azure Portal navigerar du till en av dina `connectedCluster` resurser och i avsnittet **Inställningar** på sid panelen väljer du **principer**. (UX för AKS-kluster är inte implementerat ännu, men kommer.)
2. I listan bör du se princip tilldelningen som du skapade ovan, och **kompatibilitetstillstånd** bör vara *kompatibelt*.
3. I avsnittet **Inställningar** på sid panelen väljer du **konfigurationer**.
4. I listan bör du se `sourceControlConfiguration` att princip tilldelningen har skapats.
5. Använd **kubectl** för att gå med i klustret: du bör se namn området och artefakterna som har skapats av `sourceControlConfiguration` .
6. Inom 5 minuter bör du se i klustret de artefakter som beskrivs i manifesten i den konfigurerade git-lagrings platsen.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure Monitor för behållare med ARC-aktiverade Kubernetes-kluster](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
