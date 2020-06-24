---
title: Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar övervakning av ett Kubernetes-kluster med Azure Monitor som finns i Azure Red Hat OpenShift version 4 och högre.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 872d842f02e19313940dfeba5258feb7d3799547
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888451"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare

Azure Monitor for containers innehåller omfattande övervaknings funktioner för AKS-och AKS-motorns kluster. Den här artikeln beskriver hur du aktiverar övervakning av Kubernetes-kluster som finns i [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4. x för att uppnå en liknande övervaknings upplevelse.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
>

Azure Monitor för behållare kan aktive ras för en eller flera befintliga distributioner av Azure Red Hat OpenShift v4. x med följande metoder som stöds:

- För ett befintligt kluster med det tillhandahållna bash-skriptet och körs i [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och som inte stöds

Azure Monitor for containers stöder övervakning av Azure Red Hat OpenShift v4. x enligt beskrivningen i [översikts](container-insights-overview.md) artikeln, förutom följande funktioner:

- Real tids data (förhands granskning)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor Metrics-databasen

## <a name="prerequisites"></a>Krav

- Azure CLI-version 2.0.72 eller senare

- [Helm 3](https://helm.sh/docs/intro/install/) CLI-verktyg

- [Bash-version 4](https://www.gnu.org/software/bash/)

- Kommando rads verktyget [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Om du vill aktivera och komma åt funktionerna i Azure Monitor för behållare måste du minst vara medlem i Azure *Contributor* -rollen i Azure-prenumerationen och en medlem i rollen [*Log Analytics Contributor*](../platform/manage-access.md#manage-access-using-azure-permissions) för den Log Analytics arbets yta som kon figurer ATS med Azure Monitor för behållare.

- Om du vill visa övervaknings data är du medlem i behörighets rollen [*Log Analytics läsare*](../platform/manage-access.md#manage-access-using-azure-permissions) med den Log Analytics arbets yta som kon figurer ats med Azure Monitor för behållare.

## <a name="enable-for-an-existing-cluster"></a>Aktivera för ett befintligt kluster

Utför följande steg för att aktivera övervakning av en Azure Red Hat OpenShift-version 4 och högre kluster som distribueras i Azure med hjälp av det tillhandahållna bash-skriptet.

1. Logga in i Azure

    ```azurecli
    az login
    ```

2. Hämta och Spara skriptet till en lokal mapp som konfigurerar klustret med övervaknings tillägget med följande kommandon:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Om du vill identifiera **Kube-kontexten** för klustret, efter att ha lyckats `oc login` på klustret, kör du kommandot `kubectl config current-context` och kopierar värdet.

### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbets yta

Följande steg aktiverar övervakning av klustret med hjälp av bash-skriptet som du laddade ned tidigare. Om du vill integrera med en befintlig Log Analytics arbets yta utför du följande steg för att först identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta som krävs för `workspaceResourceId` parametern och kör sedan kommandot för att aktivera övervaknings tillägget mot den angivna arbets ytan. Om du inte har en arbets yta att ange kan du gå vidare till avsnittet [integrera med standard arbets ytan](#integrate-with-default-workspace) och låta skriptet skapa en ny arbets yta åt dig.

1. Lista alla prenumerationer som du har åtkomst till med hjälp av följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata ser ut ungefär så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Kopiera värdet för **SubscriptionId**.

2. Växla till den prenumeration som är värd för Log Analytics arbets ytan med hjälp av följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. I följande exempel visas listan över arbets ytor i dina prenumerationer i standardformatet JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

4. Kör följande kommando för att aktivera övervakning och Ersätt värdet för `workspaceResourceId` `azureAroV4ResourceIdparameter` parametrarna och: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Exempel:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="integrate-with-default-workspace"></a>Integrera med standard arbets ytan

Följande steg aktiverar övervakning av ditt Azure Red Hat OpenShift v4. x-kluster med bash-skriptet som du laddade ned. I det här exemplet behöver du inte per-skapa eller ange en befintlig arbets yta. Det här kommandot fören klar processen åt dig genom att skapa en standard arbets yta i kluster prenumerationens standard resurs grupp om det inte redan finns en sådan i regionen. Standard arbets ytan som skapats liknar formatet *DefaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Ett exempel:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

Vyn multi-cluster i Azure Monitor för behållare visar dina Azure Red Hat OpenShift-kluster som inte har övervakning aktiverat under fliken **icke-övervakade kluster** . Alternativet **Aktivera** bredvid klustret initierar inte registrering av övervakning från portalen. Du omdirigeras till den här artikeln för att manuellt aktivera övervakning enligt stegen ovan i den här artikeln.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. På Azure Portal-menyn eller på Start sidan väljer du **Azure Monitor**. Under avsnittet **insikter** väljer du **behållare**.

3. På sidan **Monitor-containers** väljer du **icke-övervakade kluster**.

4. I listan över icke-övervakade kluster letar du reda på klustret i listan och klickar på **Aktivera**. Du kan identifiera resultaten i listan genom att leta efter värdet **Aro** under kolumn **kluster typen**. När du har klickat på **Aktivera**omdirigeras du till den här artikeln.

## <a name="next-steps"></a>Nästa steg

- När övervakning har Aktiver ATS för att samla in hälso-och resursutnyttjande för ditt RedHat OpenShift-kluster och arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Som standard samlar den behållareade agenten STDOUT/stderr-behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera samling av behållar logg samlingar som är specifika för en viss namnrymd eller namnrymder granskar du [konfiguration av container Insights](container-insights-agent-config.md) för att konfigurera önskade inställningar för data insamling till ConfigMap-konfigurations filen.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått-kassationing](container-insights-prometheus-integration.md)

- Information om hur du stoppar övervakningen av klustret med Azure Monitor för behållare finns i [så här slutar du övervaka ditt Azure Red Hat OpenShift-kluster](container-insights-optout-openshift.md).
