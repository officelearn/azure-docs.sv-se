---
title: Lär dig Azure Policy för AKS-motorn
description: Lär dig hur Azure Policy använder CustomResourceDefinitions och öppna Policy Agent från Gatekeeper v3 för att hantera kluster med AKS-motorn.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436430"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Förstå Azure Policy för AKS-motorn

Azure Policy integreras med [AKS-motorn](https://github.com/Azure/aks-engine/blob/master/docs/README.md), ett system som ger bekväm verktyg för att snabbt kunna starta ett självhanterat Kubernetes-kluster på Azure. Den här integrationen möjliggör vid skalnings-och säkerhets åtgärder i AKS-motorns självhanterade kluster på ett centraliserat, konsekvent sätt. Genom att utöka användningen av OPA ( [Open Policy Agent](https://www.openpolicyagent.org/) ) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), en _webhook för Admission controller_ för Kubernetes, gör Azure policy det möjligt att hantera och rapportera om kompatibilitetstillstånd för dina Azure-resurser och AKS-motorns självhanterade kluster från en och samma plats.

> [!NOTE]
> Azure Policy för AKS-motorn är i offentlig för hands version och har inget service avtal. Gatekeeper v3 är i beta och stöds av communityn för öppen källkod. Tjänsten stöder bara inbyggda princip definitioner och ett enda AKS motor kluster för varje resurs grupp som kon figurer ATS med ett huvud namn för tjänsten.

> [!IMPORTANT]
> För att få support för Azure Policy-motorn, AKS-motorn eller Gatekeeper v3 skapar du ett [nytt problem](https://github.com/Azure/aks-engine/issues/new/choose) i AKS-AKS-motorn GitHub-lagringsplatsen.

## <a name="overview"></a>Översikt

Utför följande åtgärder för att aktivera och använda Azure Policy för AKS-motor med ditt självhanterade Kubernetes-kluster på Azure:

- [Krav](#prerequisites)
- [Installera Azure Policy-tillägget](#installing-the-add-on)
- [Tilldela en princip definition för AKS-motorn](#built-in-policies)
- [Vänta på verifiering](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Krav

Innan du installerar Azure Policy tillägg eller aktiverar någon av tjänst funktionerna måste prenumerationen aktivera **Microsoft. PolicyInsights** Resource Provider och skapa en roll tilldelning för kluster tjänstens huvud namn. 

1. Om du vill aktivera resurs leverantören följer du stegen i [resurs leverantörer och typer](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) eller kör antingen Azure CLI-eller Azure PowerShell-kommandot:

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

1. Skapa en roll tilldelning för kluster tjänstens huvud namn

   - Om du inte känner till app-ID för kluster tjänstens huvud namn, kan du se det med följande kommando.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Tilldela roll tilldelningen princip insikts data skrivare (förhands granskning) till appens huvud namn för kluster tjänst (värdet _aadClientID_ från föregående steg) med Azure CLI. Ersätt `<subscriptionId>` med ditt PRENUMERATIONS- `<aks engine cluster resource group>` ID och med resurs gruppen som Kubernetes-AKS--klustret är i.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure Policy tillägg

_Azure policy-tillägget_ för Kubernetes ansluter tjänsten Azure policy till Gatekeeper-åtkomstkontroll. Tillägget, som installeras i _Kube-systemets_ namnrymd, agerar i följande funktioner:

- Söker med Azure Policy för tilldelningar till AKS-motor klustret
- Hämtar och installerar princip information, constraint-mallar och begränsningar
- Kör en fullständig kompatibilitetskontroll i AKS-motorns kluster
- Rapporterar gransknings-och kompatibilitetsinformation tillbaka till Azure Policy

### <a name="installing-the-add-on"></a>Installerar tillägget

När nödvändiga komponenter har slutförts kan du installera Azure Policy-tillägget. Installationen kan ske under skapandet eller uppdaterings cykeln för en AKS-motor eller som en oberoende åtgärd i ett befintligt kluster.

- Installera vid skapande eller uppdaterings cykel

  Om du vill aktivera Azure Policy-tillägget under skapandet av ett nytt självhanterat kluster eller som en uppdatering av ett befintligt kluster, ska du ta med egenskaps kluster definitionen **addon** för AKS-motorn.

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

  Mer information om finns i kluster definitionen för den externa guiden [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installera i befintligt kluster med Helm-diagram

  Använd följande steg för att förbereda klustret och installera tillägget:

  1. Installera Gatekeeper till _Gatekeeper-systemets_ namnrymd.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Lägg till _Control-plan-_ etiketten i _Kube-system_. Den här etiketten omfattar inte granskning av _Kube-systemets_ poddar och tjänster av Gatekeeper och Azure policy-tillägget.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synkronisera Kubernetes-data (namnrymd, pod, ingress, tjänst) med OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Mer information finns i [OPA-replikera data](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Lägg till Azure Policy lagrings platsen till Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Mer information finns i [Helm Chart – snabb starts guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installera tillägget med ett Helm-diagram. Ersätt `<subscriptionId>` med ditt PRENUMERATIONS- `<aks engine cluster resource group>` ID och med resurs gruppen som Kubernetes-AKS--klustret är i.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Mer information om vilka Helm-diagram som installeras finns i [Azure policy-tillägget Helm Chart Definition](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) på GitHub.

     > [!NOTE]
     > På grund av förhållandet mellan Azure Policy-tillägg och resurs grupps-ID: t har Azure Policy endast stöd för ett AKS motor kluster för varje resurs grupp.

Kör följande kommando för att kontrol lera att installationen av tillägget lyckades och att _Azure-principens_ Pod körs:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Validering och rapporterings frekvens

Tillägget checkar in med Azure Policy för ändringar i princip tilldelningarna var 5: e minut. Under den här uppdaterings cykeln söker tillägget efter ändringar. Dessa ändringar genererar, uppdaterar eller tar bort villkoret och begränsningarna.

> [!NOTE]
> En _kluster administratör_ kan ha behörighet att göra ändringar i constraint och begränsningar, men det rekommenderas inte eller stöds inte för att göra ändringar i constraint eller begränsningar som skapats av Azure policy. Eventuella manuella ändringar som gjorts går förlorade under uppdaterings cykeln.

Var 5: e minut anropar tillägget för en fullständig genomsökning av klustret. Efter att ha samlat in information om den fullständiga genomsökningen och eventuella utvärderingar i real tid i samband med ändringar i klustret, rapporterar tillägget tillbaka resultatet till Azure Policy för att inkludera [information om kompatibilitet](../how-to/get-compliance-data.md) , t. ex. Azure policy tilldelning. Endast resultat för aktiva princip tilldelningar returneras under gransknings cykeln. Gransknings resultat kan också visas som överträdelser som anges i fältet status för den misslyckade begränsningen.

## <a name="policy-language"></a>Princip språk

Den Azure Policy språk strukturen för att hantera AKS-motorn följer befintliga principer. _EnforceOPAConstraint_ används för att hantera dina AKS-motor kluster och använder _information_ som är särskilt för att arbeta med [OPA constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) och Gatekeeper v3. Mer information och exempel finns i [EnforceOPAConstraint](effects.md#enforceopaconstraint) -effekter.

Som en del av egenskaperna _details. constraintTemplate_ och _information. constraint_ i princip definitionen, Azure policy skickar URI: erna för dessa [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) till tillägget. Rego är det språk som OPA och Gatekeeper stöder för att verifiera en begäran till Kubernetes-klustret. Genom att stödja en befintlig standard för Kubernetes-hantering gör Azure Policy att du kan återanvända befintliga regler och koppla dem till Azure Policy för en enhetlig rapporterings upplevelse för moln efterlevnad. Mer information finns i [Vad är Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Inbyggda principer

Följ dessa steg om du vill hitta de inbyggda principerna för att hantera AKS-motor klustret med hjälp av Azure Portal:

1. Starta tjänsten Azure Policy i Azure Portal. Välj **alla tjänster** i den vänstra rutan och Sök sedan efter och välj **princip**.

1. I den vänstra rutan på sidan Azure Policy väljer du **definitioner**.

1. I list rutan Kategori använder du **Markera alla** för att ta bort filtret och väljer sedan **Kubernetes**.

1. Välj princip definitionen och välj sedan knappen **tilldela** .

> [!NOTE]
> När du tilldelar Azure Policy motor definitionen för AKS måste **omfånget** vara resurs gruppen för AKS-motorns kluster.

Du kan också använda snabb start för att [tilldela en princip-Portal](../assign-policy-portal.md) för att hitta och tilldela en AKS-motor princip. Sök efter en princip definition för AKS-motorn i stället för exemplet på granska virtuella datorer.

> [!IMPORTANT]
> Inbyggda principer i kategorin **Kubernetes** är endast för användning med AKS-motorn.

## <a name="logging"></a>Loggning

### <a name="azure-policy-add-on-logs"></a>Azure Policy tilläggs loggar

Som en Kubernetes kontrollant/container behåller Azure Policy-tillägget loggar i AKS-motorns kluster.

Om du vill visa Azure Policy tilläggs loggar använder `kubectl`du:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gatekeeper-loggar

Gatekeeper-pod, _Gatekeeper-Controller Manager-0_, finns vanligt vis i namn `gatekeeper-system` området `kube-system` eller, men kan finnas i en annan namnrymd beroende på hur den distribueras.

Om du vill visa Gatekeeper-loggarna använder `kubectl`du:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Mer information finns i [fel söknings Gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) i OPA-dokumentationen.

## <a name="remove-the-add-on"></a>Ta bort tillägget

Om du vill ta bort Azure Policy-tillägget och Gatekeeper från ditt AKS-motor kluster använder du metoden som överensstämmer med hur tillägget installerades:

- Om det är installerat genom att ange egenskapen **addons** i kluster definitionen för AKS-motorn:

  Distribuera om kluster definitionen till AKS-motorn när du har ändrat egenskapen **addons** för _Azure-policyn_ till false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Om det installeras med Helm-diagram:

  1. Ta bort gamla begränsningar

     För närvarande tar avinstallations mekanismen bara bort Gatekeeper-systemet, det tar inte bort några _ConstraintTemplate_-, _begränsnings_-eller _konfigurations_ resurser som har skapats av användaren eller tar bort sina tillhör ande _CRDs_.

     När Gatekeeper körs är det möjligt att ta bort oönskade begränsningar genom att:

     - Tar bort alla instanser av begränsnings resursen
     - Tar bort _ConstraintTemplate_ -resursen som ska rensa _CRD_ automatiskt
     - Om du tar bort _konfigurations_ resursen tas slutförarna bort från synkroniserade resurser

  1. Avinstallera Azure Policy-tillägg
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Avinstallera gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostikdata som samlats in av Azure Policy-tillägget

Azure Policy-tillägget för Kubernetes samlar in begränsade kluster diagnostikdata. Dessa diagnostikdata är viktiga tekniska data relaterade till program vara och prestanda. Den används på följande sätt:

- Behåll Azure Policy-tillägget uppdaterad
- Behåll Azure Policy-tillägget säkert, tillförlitligt, presterande
- Förbättra Azure Policy-tillägg genom den sammanställda analysen av användningen av tillägget

Informationen som samlas in av tillägget är inte personlig information. Följande information samlas in för närvarande:

- Azure Policy tilläggs agent version
- Kluster typ
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
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).