---
title: Lär dig Azure Policy för Azure Kubernetes service
description: Lär dig hur Azure Policy använder Rego och öppna princip agenten för att hantera kluster i Azure Kubernetes-tjänsten.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 248f96b4385e97605986b53bd94fd83236ec8f08
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480896"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Förstå Azure Policy för Azure Kubernetes-tjänsten

Azure Policy integreras med [Azure Kubernetes service](../../../aks/intro-kubernetes.md) (AKS) för att tillämpa skalningar och skydd i klustren på ett centraliserat, konsekvent sätt.
Genom att utöka användningen av [Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2, en OPA- _webhook_ för [Open Policy Agent](https://www.openpolicyagent.org/) (), gör Azure policy det möjligt att hantera och rapportera efterlevnaden av dina Azure-resurser och AKS-kluster från en och samma plats.

> [!NOTE]
> Azure Policy för AKS är i begränsad för hands version och stöder bara inbyggda princip definitioner.

## <a name="overview"></a>Översikt

Utför följande åtgärder för att aktivera och använda Azure Policy för AKS med ditt AKS-kluster:

- [Anmäl dig till för hands versions funktioner](#opt-in-for-preview)
- [Installera Azure Policy-tillägget](#installation-steps)
- [Tilldela en princip definition för AKS](#built-in-policies)
- [Vänta på verifiering](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Anmäl dig till för hands version

Innan du installerar Azure Policy tillägg eller aktiverar någon av tjänst funktionerna måste prenumerationen aktivera **Microsoft. container service** Resource Provider och **Microsoft. PolicyInsights** Resource Provider och sedan godkännas till delta i förhands granskningen. Om du vill delta i förhands granskningen följer du de här stegen i antingen Azure Portal eller med Azure CLI:

- Azure-portalen:

  1. Registrera resurs leverantörerna **Microsoft. container service** och **Microsoft. PolicyInsights** . Anvisningar finns i [resurs leverantörer och typer](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

     ![Sök efter princip i alla tjänster](../media/rego-for-aks/search-policy.png)

  1. Välj **koppla förhands granskning** till vänster på sidan Azure policy.

     ![Delta i principen för för hands versionen av AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Välj den rad i prenumerationen som du vill lägga till i förhands granskningen.

  1. Välj knappen **för att välja högst** upp i listan över prenumerationer.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure Policy tillägg

_Azure policy-tillägget_ för Kubernetes ansluter tjänsten Azure policy till Gatekeeper-åtkomstkontroll. Tillägget, som installeras i _Azure-principens_ namnrymd, agerar i följande funktioner:

- Söker med Azure Policy för tilldelningar till AKS-klustret
- Hämtar och cachelagrar princip information, inklusive _Rego_ princip definition som **configmaps**
- Kör en fullständig kompatibilitetskontroll i AKS-klustret
- Rapporterar gransknings-och kompatibilitetsinformation tillbaka till Azure Policy

### <a name="installing-the-add-on"></a>Installerar tillägget

#### <a name="prerequisites"></a>Förutsättningar

Innan du installerar tillägget i ditt AKS-kluster måste du installera för hands versions tillägget. Det här steget görs med Azure CLI:

1. Du behöver Azure CLI-versionen 2.0.62 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. AKS-klustret måste vara version _1,10_ eller högre. Använd följande skript för att verifiera din AKS-kluster version:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installera version _0.4.0_ av Azure CLI Preview-tillägget för AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Om du tidigare har installerat _AKS-Preview-_ tillägget installerar du eventuella uppdateringar med hjälp av kommandot `az extension update --name aks-preview`.

#### <a name="installation-steps"></a>Installations steg

När förutsättningarna har slutförts installerar du Azure Policy-tillägget i det AKS-kluster som du vill hantera.

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att klicka på **alla tjänster**och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ett av dina AKS-kluster.

  1. Välj **principer (förhands granskning)** på vänster sida av Kubernetes-tjänst sidan.

     ![Principer från AKS-klustret](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. På huvud sidan väljer du knappen **aktivera tillägg** .

     ![Aktivera Azure Policy för AKS-tillägg](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Om knappen **aktivera tillägg** är nedtonad, har prenumerationen ännu inte lagts till i för hands versionen. Se [opt-in för för hands version](#opt-in-for-preview) för de steg som krävs.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validering och rapporterings frekvens

Tillägget checkar in med Azure Policy för ändringar i princip tilldelningarna var 5: e minut. Under den här uppdaterings cykeln tar tillägget bort alla _configmaps_ i namn området för _Azure-principer_ och återskapar sedan _configmaps_ för användning av gatekeeper.

> [!NOTE]
> En _kluster administratör_ kan ha behörighet till namn området för _Azure-principer_ , men det rekommenderas inte eller stöds inte för att göra ändringar i namn området. Eventuella manuella ändringar som gjorts går förlorade under uppdaterings cykeln.

Var 5: e minut anropar tillägget för en fullständig genomsökning av klustret. Efter att ha samlat in information om den fullständiga genomsökningen och eventuella utvärderingar i real tid i samband med ändringar i klustret, rapporterar tillägget tillbaka resultatet till Azure Policy för att inkludera [information om kompatibilitet](../how-to/get-compliance-data.md) , t. ex. Azure policy tilldelning. Endast resultat för aktiva princip tilldelningar returneras under gransknings cykeln.

## <a name="policy-language"></a>Princip språk

Azure Policy språk strukturen för att hantera AKS följer de befintliga principerna. _EnforceRegoPolicy_ används för att hantera dina AKS-kluster och har _information om information_ som är speciell för att arbeta med OPA och Gatekeeper v2. Mer information och exempel finns i [EnforceRegoPolicy](effects.md#enforceregopolicy) -effekter.

Som en del av egenskapen _information. policy_ i princip definitionen, Azure policy skickar en URI för en Rego-princip till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att verifiera eller undersöka en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy att du kan återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig rapporterings upplevelse för moln efterlevnad. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Inbyggda principer

Följ dessa steg om du vill hitta de inbyggda principerna för att hantera AKS med hjälp av Azure Portal:

1. Starta tjänsten Azure Policy i Azure Portal. Välj **alla tjänster** i den vänstra rutan och Sök sedan efter och välj **princip**.

1. I den vänstra rutan på sidan Azure Policy väljer du **definitioner**.

1. I list rutan Kategori använder du **Markera alla** för att rensa filtret och sedan välja **Kubernetes-tjänst**.

1. Välj princip definitionen och välj sedan knappen **tilldela** .

> [!NOTE]
> När du tilldelar en Azure Policy för AKS-definitionen måste **omfånget** innehålla kluster resursen AKS.

Du kan också använda snabb start för att [tilldela en princip-Portal](../assign-policy-portal.md) för att hitta och tilldela en AKS-princip. Sök efter en Kubernetes-princip definition i stället för exemplet på granska virtuella datorer.

> [!IMPORTANT]
> Inbyggda principer i Category **Kubernetes-tjänsten** kan bara användas med AKS.

## <a name="logging"></a>Loggning

### <a name="azure-policy-add-on-logs"></a>Azure Policy tilläggs loggar

Som en Kubernetes kontrollant/container behåller Azure Policy-tillägget loggar i AKS-klustret. Loggarna visas på sidan **Insights** i AKS-klustret. Mer information finns i [förstå AKS kluster prestanda med Azure Monitor for containers](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Gatekeeper-loggar

Om du vill aktivera Gatekeeper-loggar för nya resurs begär Anden följer du stegen i [Aktivera och granska Kubernetes huvud Node-loggar i AKS](../../../aks/view-master-logs.md).
Här är en exempel fråga som visar nekade händelser på nya resurs begär Anden:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Om du vill visa loggar från Gatekeeper-behållare följer du stegen i [Aktivera och granska Kubernetes huvud Node-loggar i AKS](../../../aks/view-master-logs.md) och markerar alternativet _Kube-Apiserver_ i fönstret **diagnostiska inställningar** .

## <a name="remove-the-add-on"></a>Ta bort tillägget

Om du vill ta bort Azure Policy-tillägget från AKS-klustret använder du antingen Azure Portal eller Azure CLI:

- Azure Portal

  1. Starta AKS-tjänsten i Azure Portal genom att klicka på **alla tjänster**och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ditt AKS-kluster där du vill inaktivera Azure Policy-tillägget.

  1. Välj **principer (förhands granskning)** på vänster sida av Kubernetes-tjänst sidan.

     ![Principer från AKS-klustret](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. På huvud sidan väljer du knappen **Inaktivera tillägg** .

     ![Inaktivera Azure Policy för AKS-tillägg](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostikdata som samlats in av Azure Policy-tillägget

Azure Policy-tillägget för Kubernetes samlar in begränsade kluster diagnostikdata. Dessa diagnostikdata är viktiga tekniska data relaterade till program vara och prestanda. Den används på följande sätt:

- Behåll Azure Policy-tillägget uppdaterat
- Behåll Azure Policy-tillägget säkert, tillförlitligt, presterande
- Förbättra Azure Policy-tillägg genom den sammanställda analysen av användningen av tillägget

Informationen som samlas in av tillägget är inte personlig information. Följande information samlas in för närvarande:

- Azure Policy tilläggs agent version
- Klustertyp
- Kluster region
- Kluster resurs grupp
- Resurs-ID för kluster
- ID för kluster prenumeration
- Kluster-OS (exempel: Linux)
- Kluster ort (exempel: Seattle)
- Kluster status eller provins (exempel: Washington)
- Klustrets land eller region (exempel: USA)
- Undantag/fel som påträffats av Azure Policy tillägg under agent installation vid princip utvärdering
- Antal Gatekeeper-principer som inte har installerats av Azure Policy tillägg

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).