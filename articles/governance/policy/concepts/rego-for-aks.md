---
title: Lär dig att granska innehållet i en virtuell dator
description: Lär dig hur Azure Policy använder Rego och öppna principagent för att hantera kluster i Azure Kubernetes Service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453910"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Förstå Azure Policy för Azure Kubernetes Service

Azure Policy kan integreras med den [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) för att använda i skala enforcements och säkerhetsåtgärder på ditt kluster på ett centraliserat och konsekvent sätt.
Genom att utöka användningen av [GateKeeper](https://github.com/open-policy-agent/gatekeeper), en _åtkomst controller webhook_ för [öppna Principagent](https://www.openpolicyagent.org/) (OPA), Azure Policy gör det möjligt att hantera och rapportera om kompatibilitet tillståndet för dina Azure-resurser och AKS-kluster från ett och samma ställe.

> [!NOTE]
> Azure Policy för AKS är i en begränsad förhandsversion och endast stöd för inbyggda principdefinitioner.

## <a name="overview"></a>Översikt

Vidta följande åtgärder för att aktivera och använda Azure Policy för AKS med AKS-klustret:

- [Anmäl dig till förhandsversionsfunktioner](#opt-in-for-preview)
- [Installera tillägget Azure Policy](#installation-steps)
- [Tilldela en principdefinition för AKS](#built-in-policies)
- [Vänta på verifiering](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Anmäl dig till förhandsversionen

Innan du installerar tillägget för Azure-princip eller om du använder något av service-funktioner, din prenumeration måste aktivera den **Microsoft.ContainerService** resursprovidern och **Microsoft.PolicyInsights**resursprovidern, och sedan godkännas för att ta del av förhandsversionen. Följ dessa steg i Azure portal eller med Azure CLI för att använda förhandsgranskningen:

- Azure-portalen:

  1. Registrera den **Microsoft.ContainerService** och **Microsoft.PolicyInsights** resursprovidrar. Anvisningar finns i [resursproviders och resurstyper](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

     ![Sök efter princip i alla tjänster](../media/rego-for-aks/search-policy.png)

  1. Välj **delta förhandsversion** till vänster på sidan för Azure Policy.

     ![Anslut princip för AKS förhandsgranskning](../media/rego-for-aks/join-aks-preview.png)

  1. Välj raden i den prenumeration som ska läggas till förhandsversionen.

  1. Välj den **valbar** knappen överst i listan över prenumerationer.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Azure Policy-tillägg

Den _Azure princip tillägget_ för Kubernetes ansluter Azure Policy-tjänsten till kontrollanten GateKeeper åtkomst. Tillägg, som är installerad i den _azure-policy_ namnområdet, utfärdar följande funktioner:

- Kontroller med Azure Policy för tilldelningar till AKS-kluster
- Hämtar och cachelagrar principinformation, inklusive den _rego_ principdefinition som **configmaps**
- Kör en fullständig genomsökning kompatibilitetskontrollen på AKS-kluster
- Rapporter granskning och information om kompatibilitet tillbaka till Azure Policy

### <a name="installing-the-add-on"></a>Installera tillägget

#### <a name="prerequisites"></a>Förutsättningar

Innan du installerar tillägget AKS-klustret måste vara installerat tillägget förhandsversion. Detta görs med Azure CLI:

1. Du behöver Azure CLI version 2.0.62 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. AKS-klustret måste vara version _1.10_ eller högre. Använd följande skript för att verifiera ditt AKS-kluster av version:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installera version _0.4.0_ av Azure CLI Förhandsgranska tillägg för AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Om du tidigare har installerat den _förhandsversionen av aks_ tillägg, installera alla uppdateringar med hjälp av den `az extension update --name aks-preview` kommando.

#### <a name="installation-steps"></a>Installationssteg

När kraven har slutförts, kan du installera tillägget Azure Policy i AKS-kluster som du vill hantera.

- Azure Portal

  1. Starta AKS-tjänsten i Azure portal genom att klicka på **alla tjänster**, söka efter och välja **Kubernetes-tjänster**.

  1. Välj en av dina AKS-kluster.

  1. Välj **principer (förhandsversion)** på vänster sida av Kubernetes service-sidan.

     ![Principer för AKS-kluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. På huvudsidan, Välj den **aktivera tillägg** knappen.

     ![Aktivera princip för Azure för AKS-tillägg](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Om den **aktivera tillägg** knappen är nedtonad, prenumerationen har ännu inte lagts till förhandsversionen. Se [valbar för förhandsversionen](#opt-in-for-preview) för steg som krävs.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validering och telemetrirapportering

Tillägget checkar in med Azure Policy för ändringar i principtilldelningar var femte minut. Under den här uppdateringscykeln tillägget tar bort alla _configmaps_ i den _azure-policy_ namnområde återskapas den _configmaps_ för GateKeeper-användning.

> [!NOTE]
> När en _kluster admin_ kanske har behörighet att den _azure-policy_ namnområdet, det är inte rekommenderas eller stöds om du vill göra ändringar i namnområdet. Alla manuella ändringar går förlorade vid uppdateringsprocess.

Var femte minut, anropar tillägg för en fullständig skanning av klustret. När du samlar in information om av en fullständig genomsökning och eventuella i realtid utvärderingar av GateKeeper försök har gjorts ändringar i klustret, tillägg rapporterar resultaten tillbaka till Azure Policy för inkludering i [efterlevnadsinformation](../how-to/get-compliance-data.md) som alla Azure-Policy tilldelningen. Endast returneras för aktiv principtilldelningar under granskning cykel.

## <a name="policy-language"></a>Principspråket

Struktur för Azure Policy-språk för att hantera AKS följer som befintliga principer. Effekten _EnforceRegoPolicy_ används för att hantera dina AKS-kluster och tar _information_ egenskaper som är specifika för att arbeta med OPA och GateKeeper. Mer information och exempel, i den [EnforceRegoPolicy](effects.md#enforceregopolicy) effekt.

Som en del av den _details.policy_ -egenskapen i principdefinitionen Azure Policy skickar URI för en princip för rego till tillägget. Rego är det språk som OPA och GateKeeper stöd för att verifiera eller mutera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för hantering av Kubernetes, gör Azure Policy det möjligt att återanvända befintliga regler och koppla ihop dem med Azure Policy för en enhetlig molnet kompatibilitet rapporteringsfunktioner. Mer information finns i [vad är Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Inbyggda principer

Följ dessa steg för att hitta de inbyggda principerna för att hantera AKS med Azure portal:

1. Starta Azure Policy-tjänsten i Azure-portalen. Välj **alla tjänster** i vänstra fönstret och Sök efter och välj **princip**.

1. I den vänstra rutan på sidan för Azure Policy, Välj **definitioner**.

1. Använd från rutan kategori listrutan **Markera alla** att ta bort filtret och välj sedan **Kubernetes-tjänst**.

1. Välj principdefinitionen och välj sedan den **tilldela** knappen.

> [!NOTE]
> När du tilldelar Azure-principen för AKS-definition, den **omfång** måste innehålla AKS-klusterresursen.

Alternativt kan använda den [och tilldela en princip – Portal](../assign-policy-portal.md) Snabbstart för att hitta och tilldela en princip för AKS. Sök efter en principdefinition för Kubernetes i stället för på exemplet ”granska virtuella datorer”.

## <a name="logging"></a>Loggning

### <a name="azure-policy-add-on-logs"></a>Azure loggar för tillägg

Som behållare/Kubernetes controller behåller Azure Policy-tillägget loggar i AKS-kluster. Loggarna visas i den **Insights** för AKS-klustret. Mer information finns i [förstå AKS kluster prestanda med Azure Monitor för behållare](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>GateKeeper-loggar

Om du vill aktivera GateKeeper loggar för den nya resursen förfrågningar, följer du stegen i [aktivera och granska Kubernetes huvudnoden loggar i AKS](../../../aks/view-master-logs.md).
Här är en exempelfråga visa nekade aktiviteter i den nya resursbegäranden:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Om du vill visa loggar från behållare GateKeeper, följer du stegen i [aktivera och granska Kubernetes huvudnoden loggar i AKS](../../../aks/view-master-logs.md) och kontrollera den _kube apiserver_ alternativet i den **diagnostikinställningar** fönstret.

## <a name="remove-the-add-on"></a>Ta bort tillägget

Ta bort Azure Policy-tillägget från ditt AKS-kluster med Azure portal eller Azure CLI:

- Azure Portal

  1. Starta AKS-tjänsten i Azure portal genom att klicka på **alla tjänster**, söka efter och välja **Kubernetes-tjänster**.

  1. Välj AKS-klustret där du vill inaktivera tillägget för Azure Policy.

  1. Välj **principer (förhandsversion)** på vänster sida av Kubernetes service-sidan.

     ![Principer för AKS-kluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. På huvudsidan, Välj den **inaktivera tillägget** knappen.

     ![Inaktivera Azure-principen för AKS-tillägg](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [skapa principer programmässigt](../how-to/programmatically-create.md).
- Lär dig hur du [hämta kompatibilitetsdata](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vilka en hanteringsgrupp är med [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/index.md).