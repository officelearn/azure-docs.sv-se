---
title: Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du konfigurerar övervakning för ett Kubernetes-kluster med Azure Monitor som finns i Azure Red Hat OpenShift version 4 eller senare.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 7eee7ba6ba01679f72d1249058e4101b38d8461d
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85508018"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurera Azure Red Hat OpenShift v4. x med Azure Monitor för behållare

Azure Monitor för behållare ger en omfattande övervaknings upplevelse för Azure Kubernetes service-och AKS-motorns kluster. Den här artikeln beskriver hur du uppnår en liknande övervaknings upplevelse genom att aktivera övervakning av Kubernetes-kluster som finns i [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4. x.

>[!NOTE]
>Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
>

Du kan aktivera Azure Monitor för behållare för en eller flera befintliga distributioner av Azure Red Hat OpenShift v4. x med de metoder som beskrivs i den här artikeln.

För ett befintligt kluster kör du det här [bash-skriptet i Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Funktioner som stöds och som inte stöds

Azure Monitor for containers stöder övervakning av Azure Red Hat OpenShift v4. x enligt beskrivningen i [Azure Monitor for containers Overview](container-insights-overview.md), förutom följande funktioner:

- Real tids data (förhands granskning)
- [Samla in mått](container-insights-update-metrics.md) från klusternoder och poddar och lagra dem i Azure Monitor Metrics-databasen

## <a name="prerequisites"></a>Krav

- Azure CLI-version 2.0.72 eller senare  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI-verktyget

- [Bash-version 4](https://www.gnu.org/software/bash/)

- Kommando rads verktyget [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Om du vill aktivera och få åtkomst till funktionerna i Azure Monitor för behållare måste du ha minst en Azure *Contributor* -roll i Azure-prenumerationen och en [*Log Analytics deltagar*](../platform/manage-access.md#manage-access-using-azure-permissions) roll i arbets ytan Log Analytics som kon figurer ATS med Azure Monitor för behållare.

- Om du vill visa övervaknings data måste du ha [*Log Analytics läsar*](../platform/manage-access.md#manage-access-using-azure-permissions) roll på arbets ytan Log Analytics som kon figurer ats med Azure Monitor för behållare.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Aktivera övervakning för ett befintligt kluster

Gör så här för att aktivera övervakning av ett Azure Red Hat OpenShift-kluster med version 4 eller senare som har distribuerats i Azure med hjälp av det tillhandahållna bash-skriptet:

1. Logga in på Azure genom att köra följande kommando:

    ```azurecli
    az login
    ```

1. Ladda ned och spara till en lokal mapp det skript som konfigurerar klustret med övervaknings tillägget genom att köra följande kommando:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

1. Om du vill identifiera *Kube-kontexten* för klustret efter en lyckad *oc-inloggning* till klustret kör du följande kommando:

    `kubectl config current-context`
    
1. Kopiera värdet för senare användning.

### <a name="integrate-with-an-existing-workspace"></a>Integrera med en befintlig arbets yta

I det här avsnittet aktiverar du övervakning av klustret med hjälp av bash-skriptet som du laddade ned tidigare. Om du vill integrera med en befintlig Log Analytics arbets yta börjar du med att identifiera det fullständiga resurs-ID: t för din Log Analytics arbets yta som krävs för `workspaceResourceId` parametern och kör sedan kommandot för att aktivera övervaknings tillägget mot den angivna arbets ytan. 

Om du inte har en arbets yta att ange kan du gå till avsnittet [integrera med standard arbets ytan](#integrate-with-the-default-workspace) och låta skriptet skapa en ny arbets yta åt dig.

1. Lista alla prenumerationer som du har åtkomst till genom att köra följande kommando:

    ```azurecli
    az account list --all -o table
    ```

    Utdata kommer att se ut så här:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Kopiera värdet för **SubscriptionId**.

1. Växla till den prenumeration som är värd för Log Analytics arbets ytan genom att köra följande kommando:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Visa listan över arbets ytor i dina prenumerationer i standardvärdet JSON-format genom att köra följande kommando:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. I utdata letar du reda på arbets ytans namn och kopierar sedan det fullständiga resurs-ID: t för den Log Analytics arbets ytan under fält **-ID: t**.

1. Kör följande kommando för att aktivera övervakning. Ersätt värdena för `azureAroV4ResourceId` `workspaceResourceId` parametrarna och. 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Exempel:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso måtten för klustret.

### <a name="integrate-with-the-default-workspace"></a>Integrera med standard arbets ytan

I det här avsnittet aktiverar du övervakning för ditt Azure Red Hat OpenShift v4. x-kluster med hjälp av det bash-skript som du laddade ned. 

I det här exemplet är du inte tvungen att skapa eller ange en befintlig arbets yta. Det här kommandot fören klar processen åt dig genom att skapa en standard arbets yta i kluster prenumerationens standard resurs grupp, om det inte redan finns en sådan i regionen. 

Standard arbets ytan som skapas har formatet *DefaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Till exempel:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

När du har aktiverat övervakning kan det ta ungefär 15 minuter innan du kan visa hälso mått för klustret.

### <a name="from-the-azure-portal"></a>Från Azure-portalen

Vyn multi-cluster i Azure Monitor för behållare visar dina Azure Red Hat OpenShift-kluster som inte har övervakning aktiverat under fliken **icke-övervakade kluster** . Alternativet **Aktivera** bredvid klustret initierar inte registrering av övervakning från portalen. Du omdirigeras till den här artikeln för att aktivera övervakning manuellt genom att följa anvisningarna som beskrivs tidigare i den här artikeln.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I den vänstra rutan eller på Start sidan väljer du **Azure Monitor**. 

1. I avsnittet **insikter** väljer du **behållare**.

1. På sidan **Monitor-containers** väljer du **icke-övervakade kluster**.

1. I listan över icke-övervakade kluster väljer du klustret och väljer sedan **Aktivera**. 

    Du kan identifiera resultaten i listan genom att leta efter värdet **Aro** i kolumnen **kluster typ** . När du har valt **Aktivera**omdirigeras du till den här artikeln.

## <a name="next-steps"></a>Nästa steg

- Nu när du har aktiverat övervakning för att samla in hälso-och resursutnyttjande för ditt RedHat OpenShift version 4. x-kluster och de arbets belastningar som körs på dem, lär [du dig hur du använder](container-insights-analyze.md) Azure Monitor för behållare.

- Som standard samlar den behållareade agenten *STDOUT* -och *stderr* -behållar loggarna för alla behållare som körs i alla namn områden utom Kube-system. Om du vill konfigurera en behållar logg samling som är specifik för en viss namnrymd eller namnrymd, granskar du [agent konfigurationen för container Insights](container-insights-agent-config.md) för att konfigurera de data insamlings inställningar som du vill använda för din *ConfigMap* konfigurations fil.

- Om du vill ta bort och analysera Prometheus-mått från klustret kan du läsa [Konfigurera Prometheus mått](container-insights-prometheus-integration.md).

- Information om hur du stoppar övervakningen av klustret med hjälp av Azure Monitor för behållare finns i [så här slutar du övervaka ditt Azure Red Hat OpenShift-kluster](container-insights-optout-openshift.md).
