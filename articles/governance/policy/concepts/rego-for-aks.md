---
title: Lär dig Azure-princip för Azure Kubernetes-tjänsten
description: Lär dig hur Azure Policy använder Rego och Open Policy Agent för att hantera kluster på Azure Kubernetes Service.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372646"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Förstå Azure Policy för Azure Kubernetes Service

Azure Policy integreras med [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) för att tillämpa tvingande efterlevnad och skyddsåtgärder i dina kluster i centraliserade, konsekventa sätt.
Genom att utöka användningen av [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, en _webhook_ för [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), gör Azure Policy det möjligt att hantera och rapportera om efterlevnadstillståndet för dina Azure-resurser och AKS-kluster från ett ställe.

> [!IMPORTANT]
> Azure Policy för AKS finns i förhandsversion och stöder endast inbyggda principdefinitioner. Inbyggda principer finns i kategorin **Kubernetes.** **Effekten EnforceRegoPolicy** och relaterade **kubernetes-tjänstkategoriprinciper** är _inaktuella_. Använd i stället den uppdaterade [Effekten EnforceOPAConstraint.](./effects.md#enforceopaconstraint)

> [!WARNING]
> Den här funktionen är ännu inte tillgänglig i alla regioner. En status för distributionen finns i [AKS-problem - Breaking Change for Policy Add-on](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Översikt

Så här aktiverar och använder du Azure Policy för AKS med AKS-klustret:

- [Anmäl dig till förhandsversionsfunktioner](#opt-in-for-preview)
- [Installera Azure Policy-tillägget](#installation-steps)
- [Tilldela en principdefinition för AKS](#built-in-policies)
- [Vänta på validering](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Anmäl dig till förhandsgranskning

Innan du installerar Azure Policy-tillägget eller aktiverar någon av tjänstfunktionerna måste prenumerationen aktivera **microsoft.ContainerService-resursleverantören** och **Microsoft.PolicyInsights-resursleverantören** och sedan godkännas för att gå med i förhandsversionen. Om du vill gå med i förhandsversionen följer du dessa steg i Azure-portalen eller med Azure CLI:

- Azure Portal:

  1. Registrera resursleverantörerna **Microsoft.ContainerService** och **Microsoft.PolicyInsights.** Steg för steg finns i [Resursleverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

     ![Sök efter policy i alla tjänster](../media/rego-for-aks/search-policy.png)

  1. Välj **Koppla förhandsgranskning** till vänster på sidan Azure Policy.

     ![Gå med i förhandsversionen av principen för AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Markera raden för den prenumeration som du vill lägga till i förhandsgranskningen.

  1. Välj **opt-in-knappen** högst upp i listan över prenumerationer.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure-principtillägg

_Azure Policy-tillägget_ för Kubernetes ansluter Azure Policy-tjänsten till gatekeeper-åtkomstkontrollanten. Tillägget, som installeras i _kube-systemets_ namnområde, antar följande funktioner:

- Kontrollerar med Azure Policy-tjänsten för tilldelningar till klustret.
- Distribuerar principer i klustret som [villkorsmall](https://github.com/open-policy-agent/gatekeeper#constraint-templates) och [villkor](https://github.com/open-policy-agent/gatekeeper#constraints) anpassade resurser.
- Rapporter granskning och efterlevnad information tillbaka till Azure Policy-tjänsten.

### <a name="installing-the-add-on"></a>Installera tillägget

#### <a name="prerequisites"></a>Krav

Innan du installerar tillägget i AKS-klustret måste förhandsgranskningstillägget installeras. Det här steget görs med Azure CLI:

1. Om Gatekeeper v2-principer installerades tar du bort tillägget med knappen **Inaktivera** i AKS-klustret under sidan **Principer (förhandsversion).**

1. Du behöver Azure CLI version 2.0.62 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

1. AKS-klustret måste vara version _1.14_ eller högre. Använd följande skript för att validera AKS-klusterversionen:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installera version _0.4.0_ av azure CLI-förhandstillägget för AKS: `aks-preview`

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Om du tidigare har installerat _tillägget aks-preview_ installerar `az extension update --name aks-preview` du eventuella uppdateringar med kommandot.

#### <a name="installation-steps"></a>Installationssteg

När förutsättningarna är klara installerar du Azure Policy-tillägget i AKS-klustret som du vill hantera.

- Azure Portal

  1. Starta AKS-tjänsten i Azure-portalen genom att klicka på **Alla tjänster**och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ett av dina AKS-kluster.

  1. Välj **Principer (förhandsgranskning)** till vänster på kubernetes-tjänstsidan.

     ![Principer från AKS-klustret](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Välj knappen **Aktivera tillägg på** huvudsidan.

     ![Aktivera Azure Policy för AKS-tillägget](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Om **knappen Aktivera är** nedtonad har prenumerationen ännu inte lagts till i förhandsgranskningen. Se [Opt-in för förhandsversion](#opt-in-for-preview) för de steg som krävs. Om det finns en **knappen Inaktivera** är Gatekeeper v2 fortfarande installerad och måste tas bort.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validerings- och rapporteringsfrekvens

Tillägget checkar in med Azure Policy-tjänsten för ändringar i principtilldelningar var 15:e minut.
Under den här uppdateringscykeln söker tillägget efter ändringar. Utlösaren för dessa ändringar skapar, uppdaterar eller tar bort begränsningsmallarna och begränsningarna.

> [!NOTE]
> En klusteradministratör kan ha behörighet att skapa och uppdatera villkorsmallar och begränsningsresurser, men dessa stöds inte scenarier eftersom manuella uppdateringar skrivs över.

Var 15:e minut efterlyser tilläggssamtalen för en fullständig genomsökning av klustret. Efter att ha samlat in information om den fullständiga genomsökningen och eventuella realtidsutvärderingar av Gatekeeper av försöksändringar i klustret, rapporterar tillägget resultaten tillbaka till Azure Policy-tjänsten för att inkludera [i efterlevnadsinformation](../how-to/get-compliance-data.md#portal) som alla Azure-principtilldelning. Endast resultat för aktiva principtilldelningar returneras under granskningscykeln. Granskningsresultat kan också ses som [överträdelser](https://github.com/open-policy-agent/gatekeeper#audit) som anges i statusfältet för den misslyckade begränsningen.

## <a name="policy-language"></a>Politiskt språk

Språkstrukturen i Azure-princip för hantering av Kubernetes följer befintliga principer. Effekten _EnforceOPAConstraint_ används för att hantera dina Kubernetes-kluster och tar detaljerade egenskaper som är specifika för att arbeta med [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) och Gatekeeper v3. Mer information och exempel finns i effekten [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
  
Som en del av egenskaperna _details.constraintTemplate_ och _details.constraint_ i principdefinitionen skickar Azure Policy URI:erna för dessa [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att validera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy det möjligt att återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig molnefterlevnadsrapporteringsupplevelse. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Inbyggda principer

Så här hittar du de inbyggda principerna för hantering av klustret med Azure-portalen:

1. Starta Azure Policy-tjänsten i Azure-portalen. Välj Alla tjänster i den vänstra rutan och sök sedan efter och välj **Princip**.

1. Välj **Definitioner**i den vänstra rutan på sidan Azure-princip .

1. I listrutan Kategori använder du Markera alla för att rensa filtret och väljer sedan **Kubernetes**.

1. Markera principdefinitionen och välj sedan knappen **Tilldela.**

1. Ange **scopet** till hanteringsgruppen, prenumerationen eller resursgruppen för Kubernetes-klustret där principtilldelningen ska gälla.

   > [!NOTE]
   > När du tilldelar Azure-principen för **AKS-definitionen** måste scopet innehålla AKS-klusterresursen.

1. Ge principtilldelningen ett **namn** och **en beskrivning** som du enkelt kan använda för att identifiera den.

1. Ange [tvingande princip](./assignment-structure.md#enforcement-mode) till ett av värdena nedan.

   - **Aktiverad** - Framtvinga principen i klustret. Kubernetes antagningsförfrågningar med överträdelser nekas.
   
   - **Inaktiverad** - Tillämpa inte principen i klustret. Kubernetes antagningsförfrågningar med överträdelser nekas inte. Resultaten av bedömning av efterlevnaden är fortfarande tillgängliga. När du distribuerar nya principer till kluster som körs kan alternativet _Inaktivera inaktiverat_ vara användbart för att testa principerna eftersom antagningsbegäranden med överträdelser inte nekas.

1. Välj **Nästa**.

1. Ange **parametervärden**
   
   - Om du vill utesluta Kubernetes namnområden från principutvärderingen anger du listan över namnområden i **parameternamnområdesundantag**. Det rekommenderas att utesluta: _kube-system_

1. Välj **Granska + skapa**.

Alternativt kan du använda snabbstarten [Tilldela en princip - Portal](../assign-policy-portal.md) för att hitta och tilldela en AKS-princip. Sök efter en Kubernetes principdefinition i stället för exemplet "granska vms".

> [!IMPORTANT]
> Inbyggda principer i kategorin **Kubernetes** är endast för användning med AKS. En lista över inbyggda principer finns i [Kubernetes exempel](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>Loggning

### <a name="azure-policy-add-on-logs"></a>Azure Princip-tilläggsloggar

Som kubernetes-styrenhet/behållare håller både Azure Policy Add-on och Gatekeeper loggar i AKS-klustret. Loggarna visas på sidan **Insikter** i AKS-klustret. Mer information finns i [Förstå AKS-klusterprestanda med Azure Monitor för behållare](../../../azure-monitor/insights/container-insights-analyze.md).

## <a name="remove-the-add-on"></a>Ta bort tillägget

Om du vill ta bort Azure Policy-tillägget från AKS-klustret använder du antingen Azure-portalen eller Azure CLI:

- Azure Portal

  1. Starta AKS-tjänsten i Azure-portalen genom att klicka på **Alla tjänster**och sedan söka efter och välja **Kubernetes-tjänster**.

  1. Välj ditt AKS-kluster där du vill inaktivera Azure Policy-tillägget.

  1. Välj **Principer (förhandsgranskning)** till vänster på kubernetes-tjänstsidan.

     ![Principer från AKS-klustret](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. På huvudsidan väljer du knappen **Inaktivera tillägg.**

     ![Inaktivera Azure-principen för AKS-tillägget](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostikdata som samlas in av Azure Policy Add-on

Azure Policy-tillägget för Kubernetes samlar in begränsade klusterdiagnostiska data. Dessa diagnostiska data är viktiga tekniska data relaterade till programvara och prestanda. Den används på följande sätt:

- Håll Azure Policy-tillägg uppdaterade
- Skydda Azure Policy-tillägget säkert, tillförlitligt, högpresterande
- Förbättra Azure Policy Add-on - genom den samlade analysen av användningen av tillägget

Den information som samlas in av tillägget är inte personuppgifter. Följande uppgifter samlas för närvarande in:

- Azure Policy-tilläggsagentversion
- Klustertyp
- Klusterregion
- Klusterresursgrupp
- Klusterresurs-ID
- ID för klusterprenumeration
- Cluster OS (Exempel: Linux)
- Klusterstad (Exempel: Seattle)
- Klusterstat eller provins (Exempel: Washington)
- Klusterland eller -region (exempel: USA)
- Undantag/fel som påträffas av Azure Policy Add-on under agentinstallation på principutvärdering
- Antal gatekeeperprinciper som inte har installerats av Azure Policy Add-on

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](definition-structure.md).
- Granska [Förstå policy-effekter](effects.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
