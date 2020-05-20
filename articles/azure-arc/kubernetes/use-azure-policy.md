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
ms.openlocfilehash: 64be872e4e76545c015378417410cbbfad95ebfd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680669"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Använd Azure Policy om du vill använda klusterkonfigurationer i skala (för hands version)

## <a name="overview"></a>Översikt

Använd Azure Policy för att framtvinga att varje `Microsoft.Kubernetes/connectedclusters` eller git-Ops-aktiverad `Microsoft.ContainerService/managedClusters` resurs har en speciell `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tillämpning.  Om du vill använda Azure Policy väljer du en befintlig princip definition och skapar en princip tilldelning.  När du skapar princip tilldelningen anger du omfånget för tilldelningen: det här är en Azure-resurs grupp eller-prenumeration.  Du anger också parametrarna för `sourceControlConfiguration` som ska skapas.  När tilldelningen har skapats kommer principmodulen att identifiera alla `connectedCluster` eller `managedCluster` resurser som finns inom omfånget och som ska gälla `sourceControlConfiguration` för var och en.

Om du använder flera git-databaser som källor till sanningen för varje kluster (till exempel en lagrings platsen för central IT/kluster operatör och andra databaser för program team), kan du aktivera detta genom att använda flera princip tilldelningar, varje princip tilldelning som kon figurer ATS för att använda en annan git-lagrings platsen.

## <a name="create-a-custom-policy-definition"></a>Skapa en anpassad principdefinition

1. I Azure Portal navigerar du till princip och i avsnittet **redigering** på sid panelen väljer du **definitioner**.
2. Välj **+ princip definition**.
3. Ange **definitions platsen** för din prenumeration eller hanterings grupp.  Detta fastställer det bredaste omfånget där princip definitionen kan användas.
4. Ge principen ett **namn** och en **Beskrivning**.
5. Under kategori väljer du **Skapa nytt**och skriv *Kubernetes-kluster – Azure-båge*
6. I redigerings rutan **princip regel** kopierar/klistrar du in innehållet i den här [exempel princip definitionen](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Spara**.

Det här steget för att skapa en anpassad princip definition behövs inte när arbetet har slutförts för att göra detta till en inbyggd princip.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

1. I Azure Portal navigerar du till princip och i avsnittet **redigering** på sid panelen väljer du **definitioner**.
2. Hitta definitionen som du nyss skapade och markera den.
3. I sidan åtgärder väljer du **tilldela**.
4. Ange **omfattningen** för hanterings gruppen, prenumerationen eller resurs gruppen där princip tilldelningen ska gälla.
5. Ange **undantag**om du vill undanta resurser från princip omfånget.
6. Ge princip tilldelningen ett **namn** och en **Beskrivning** som du kan använda för att identifiera det enkelt.
7. Se till att **princip tillämpning** är *aktive rad*.
8. Välj **Nästa**.
9. Ange parameter värden som ska användas när du skapar `sourceControlConfiguration` .
10. Välj **Nästa**.
11. Aktivera **skapa en reparations uppgift**.
12. Försäkra dig om att **skapa en hanterad identitet** kontrol leras och att identiteten har **deltagar** behörighet.  Se [det här dokumentet](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) och [kommentaren i det här dokumentet](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources) för mer information om de behörigheter som du behöver.
13. Välj **Granska + skapa**.

När princip tilldelningen har skapats, för alla nya `connectedCluster` resurser (eller `managedCluster` resurser med GitOps-agenterna installerade) som finns inom tilldelnings omfånget, `sourceControlConfiguration` tillämpas.  För befintliga kluster måste du köra en reparations uppgift manuellt.  Det tar vanligt vis från 10-20 minuter innan princip tilldelningen börjar gälla.

## <a name="verify-a-policy-assignment"></a>Verifiera en princip tilldelning

1. I Azure Portal navigerar du till en av dina `connectedCluster` resurser och i avsnittet **Inställningar** på sid panelen väljer du **principer**. (UX för AKS-hanterat kluster är inte implementerat ännu, men kommer.)
2. I listan bör du se princip tilldelningen som du skapade ovan, och **kompatibilitetstillstånd** bör vara *kompatibelt*.
3. I avsnittet **Inställningar** på sid panelen väljer du **konfigurationer**.
4. I listan bör du se `sourceControlConfiguration` att princip tilldelningen har skapats.
5. Använd **kubectl** för att gå med i klustret: du bör se namn området och artefakterna som har skapats av `sourceControlConfiguration` .
6. Inom 5 minuter bör du se i klustret de artefakter som beskrivs i manifesten i den konfigurerade git-lagrings platsen.

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
