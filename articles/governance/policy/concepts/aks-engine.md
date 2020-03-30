---
title: Lär dig Azure-princip för AKS-motorn
description: Lär dig hur Azure Policy använder CustomResourceDefinitions och Open Policy Agent från Gatekeeper v3 för att hantera kluster med AKS Engine.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436430"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Förstå Azure-principen för AKS-motorn

Azure Policy integreras med [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), ett system som ger praktiska verktyg för att snabbt bootstrap ett självhanterade Kubernetes-kluster på Azure. Den här integreringen möjliggör konsekventa efterlevnadar och skyddsåtgärder på dina AKS-motor självhanterade kluster på ett centraliserat och konsekvent sätt. Genom att utöka användningen av [OPA (Open Policy Agent)](https://www.openpolicyagent.org/) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), en _webhook_ för Kubernetes, gör Azure Policy det möjligt att hantera och rapportera om efterlevnadstillståndet för dina Azure-resurser och AKS Engine självhanterade kluster från ett ställe.

> [!NOTE]
> Azure Policy för AKS Engine är i offentlig förhandsversion och har inget serviceavtal. Gatekeeper v3 är i Beta och stöds av öppen källkod. Tjänsten stöder endast inbyggda principdefinitioner och ett enda AKS-motorkluster för varje resursgrupp som konfigurerats med ett tjänsthuvudnamn.

> [!IMPORTANT]
> Skapa ett [nytt problem](https://github.com/Azure/aks-engine/issues/new/choose) i AKS Engine GitHub-databasen om du vill få support för Azure Policy för AKS Engine, AKS Engine eller Gatekeeper v3.

## <a name="overview"></a>Översikt

Så här aktiverar och använder du Azure Policy för AKS Engine med ditt självhanterade Kubernetes-kluster på Azure:

- [Krav](#prerequisites)
- [Installera Azure Policy-tillägget](#installing-the-add-on)
- [Tilldela en principdefinition för AKS Engine](#built-in-policies)
- [Vänta på validering](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Krav

Innan du installerar Azure Policy-tillägget eller aktiverar någon av tjänstfunktionerna måste din prenumeration aktivera **Microsoft.PolicyInsights-resursprovidern** och skapa en rolltilldelning för huvud huvudobjektet för klustertjänsten. 

1. Så här aktiverar du resursleverantören genom att följa stegen i [Resursleverantörer och -typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller köra kommandot Azure CLI eller Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Skapa en rolltilldelning för huvudhuvudbeloppet för klustertjänsten

   - Om du inte känner till klustertjänstens huvudapp-ID slår du upp det med följande kommando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Tilldela rolltilldelningen för Policy Insights Data Writer (förhandsversion)till klustertjänstens huvudapp-ID (värde _aadClientID_ från föregående steg) med Azure CLI. Ersätt `<subscriptionId>` med ditt prenumerations-ID och `<aks engine cluster resource group>` med resursgruppen aks-motorn självhanterade Kubernetes-klustret är i.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure-principtillägg

_Azure Policy-tillägget_ för Kubernetes ansluter Azure Policy-tjänsten till gatekeeper-åtkomstkontrollanten. Tillägget, som installeras i _kube-systemets_ namnområde, antar följande funktioner:

- Kontrollerar med Azure-princip för tilldelningar till AKS Engine-klustret
- Hämtar och installerar principinformation, villkorsmallar och begränsningar
- Kör en fullständig kontroll av genomsökningskompatibilitet på AKS Engine-klustret
- Rapportera gransknings- och efterlevnadsinformation tillbaka till Azure Policy

### <a name="installing-the-add-on"></a>Installera tillägget

När förutsättningarna har slutförts kan Azure Policy-tillägget installeras. Installationen kan vara under skapandet eller uppdateringscykeln för en AKS-motor eller som en oberoende åtgärd på ett befintligt kluster.

- Installera under skapande- eller uppdateringscykeln

  Om du vill aktivera Azure Policy-tillägget under skapandet av ett nytt självhanterade kluster eller som en uppdatering till ett befintligt kluster, inkluderar **du tilläggsklusterdefinitionen** för AKS Engine.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Mer information om finns i den externa guiden [AKS Engine klusterdefinition](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installera i befintligt kluster med Helm-diagram

  Använd följande steg för att förbereda klustret och installera tillägget:

  1. Installera Gatekeeper till _gatekeeper-systemet_ namnområde.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Lägg till etikett för _styrplan_ _i kube-systemet_. Den här etiketten utesluter granskning av _kube-system poddar_ och tjänster av Gatekeeper och Azure Policy Add-on.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synkronisera Kubernetes-data (namnområde, pod, ingående, tjänst) med OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Mer information finns i [OPA - Replikera data](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Lägg till Azure-principåterpoet till Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Mer information finns i [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installera tillägget med ett Helm-diagram. Ersätt `<subscriptionId>` med ditt prenumerations-ID och `<aks engine cluster resource group>` med resursgruppen aks-motorn självhanterade Kubernetes-klustret är i.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Mer information om vad tillägget Helm Chart installerar finns i [Azure Policy Add-on Helm Chart definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) på GitHub.

     > [!NOTE]
     > På grund av relationen mellan Azure Policy Add-on och resursgrupp-ID stöder Azure Policy endast ett AKS-motorkluster för varje resursgrupp.

Om du vill verifiera att tilläggsinstallationen lyckades och att _azure-policy-pod_ körs kör du följande kommando:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Validerings- och rapporteringsfrekvens

Tillägget checkar in med Azure Policy för ändringar i principtilldelningar var femte minut. Under den här uppdateringscykeln söker tillägget efter ändringar. Utlösaren för dessa ändringar skapar, uppdaterar eller tar bort begränsningsmallarna och begränsningarna.

> [!NOTE]
> En _klusteradministratör_ kan ha behörighet att göra ändringar i villkorsmallar och begränsningar, men det rekommenderas inte eller stöds inte att göra ändringar i begränsningsmallar eller begränsningar som skapats av Azure Policy. Alla manuella ändringar som görs går förlorade under uppdateringscykeln.

Var femte minut kräver tilläggssamtalen en fullständig genomsökning av klustret. Efter att ha samlat in information om den fullständiga genomsökningen och eventuella realtidsutvärderingar av Gatekeeper av försöksändringar i klustret, rapporterar tillägget resultaten tillbaka till Azure Policy för att inkluderas i [efterlevnadsinformation](../how-to/get-compliance-data.md) som alla Azure-principtilldelningar. Endast resultat för aktiva principtilldelningar returneras under granskningscykeln. Granskningsresultat kan också ses som överträdelser som anges i statusfältet för den misslyckade begränsningen.

## <a name="policy-language"></a>Politiskt språk

Språkstrukturen i Azure Policy för hantering av AKS Engine följer befintliga principer. Effekten _EnforceOPAConstraint_ används för att hantera aks-motorkluster och tar _detaljerade_ egenskaper som är specifika för att arbeta med [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) och Gatekeeper v3. Mer information och exempel finns i effekten [EnforceOPAConstraint.](effects.md#enforceopaconstraint)

Som en del av egenskaperna _details.constraintTemplate_ och _details.constraint_ i principdefinitionen skickar Azure Policy URI:erna för dessa [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att validera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy det möjligt att återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig molnefterlevnadsrapporteringsupplevelse. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Inbyggda principer

Så här hittar du de inbyggda principerna för hantering av AKS Engine-kluster med Azure-portalen:

1. Starta Azure Policy-tjänsten i Azure-portalen. Välj **Alla tjänster** i den vänstra rutan och sök sedan efter och välj **Princip**.

1. Välj **Definitioner**i den vänstra rutan på sidan Azure-princip .

1. I listrutan Kategori använder du **Markera alla** för att rensa filtret och väljer sedan **Kubernetes**.

1. Markera principdefinitionen och välj sedan knappen **Tilldela.**

> [!NOTE]
> När du tilldelar Azure-principen för AKS Engine-definitionen måste **scopet** vara resursgruppen för AKS Engine-klustret.

Alternativt kan du använda snabbstarten [Tilldela en princip - Portal](../assign-policy-portal.md) för att hitta och tilldela en AKS-motorprincip. Sök efter en AKS Engine principdefinition i stället för exemplet "audit vms".

> [!IMPORTANT]
> Inbyggda principer i kategorin **Kubernetes** är endast för användning med AKS Engine.

## <a name="logging"></a>Loggning

### <a name="azure-policy-add-on-logs"></a>Azure Princip-tilläggsloggar

Som kubernetes-styrenhet/behållare håller Azure Policy Add-on loggar i AKS Engine-klustret.

Om du vill visa Azure Policy-tilläggsloggarna använder du `kubectl`:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gatekeeper stockar

Gatekeeper pod, _gatekeeper-controller-manager-0_, är `gatekeeper-system` `kube-system` vanligtvis i eller namnområdet, men kan vara i ett annat namnområde beroende på hur den distribueras.

Om du vill visa Gatekeeper-loggarna använder du: `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Mer information finns i [Felsökning gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) i OPA-dokumentationen.

## <a name="remove-the-add-on"></a>Ta bort tillägget

Om du vill ta bort Azure Policy Add-on och Gatekeeper från AKS Engine-klustret använder du den metod som stämmer överens med hur tillägget installerades:

- Om den installeras genom att ange egenskapen **addons** i klusterdefinitionen för AKS Engine:

  Distribuera om klusterdefinitionen till AKS Engine efter att ha ändrat egenskapen **addons** för _azure-policy_ till false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Om det installeras med Helm-diagram:

  1. Ta bort gamla villkor

     För närvarande avinstallationsmekanismen tar bara bort Gatekeeper-systemet, det tar inte bort några _ConstraintTemplate-_, _Constraint_eller _Config-resurser_ som har skapats av användaren, inte heller tar bort deras medföljande _CRD:er_.

     När Gatekeeper körs är det möjligt att ta bort oönskade begränsningar genom att:

     - Ta bort alla instanser av begränsningsresursen
     - Ta bort _ConstraintTemplate-resursen,_ som automatiskt ska rensa _crd_
     - Om du tar bort _Config-resursen_ tas finalizers bort på synkroniserade resurser

  1. Avinstallera Azure-principtillägg
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Avinstallera Gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
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