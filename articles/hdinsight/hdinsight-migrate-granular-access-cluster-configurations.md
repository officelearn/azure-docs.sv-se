---
title: Migrera till detaljerade rollbaserad åtkomst för klusterkonfigurationer - Azure HDInsight
description: Läs mer om de ändringar som krävs som en del av migreringen till detaljerade rollbaserad åtkomst för konfigurationer med HDInsight-kluster.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/26/2019
ms.openlocfilehash: 8bcb20ec5c85c3cfa2e481a4a5848f404a2fb4eb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685464"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrera till detaljerad rollbaserad åtkomst för klusterkonfigurationer

Vi introducerar några viktiga förändringar för att stödja mer detaljerad rollbaserad åtkomst för att få känslig information. Som en del av dessa ändras, vissa **åtgärd kan krävas** om du använder en av de [påverkas entiteter/scenarier för](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Vad ändras?

Tidigare hemligheter kan hämtas via HDInsight-API av kluster-användare som har den ägare, deltagare eller läsare [RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
Framöver kommer längre dessa hemligheter inte tillgänglig för användare med rollen läsare. Hemligheter är definierade som värden som kan användas för att få mer utökad åtkomst än en användarroll ska tillåta. Dessa inkluderar värden som HTTP-gateway på klusternivå, lagringskontonycklar och autentiseringsuppgifter på databasen.

Lanserar vi även en ny [Hdinsight-kluster operatorn](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) roll som kommer att kunna hämta hemligheter utan beviljas administratörsbehörighet för deltagare eller ägare. Sammanfattningsvis:

| Roll                                  | Tidigare                                                                                       | Vi rekommenderar att       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Läsare                                | -Läs-åtkomst, inklusive hemligheter                                                                   | -Läsbehörighet, **exklusive** hemligheter |           |   |   |
| HDInsight-kluster-Operator<br>(Ny roll) | Gäller inte                                                                                              | Läs-/ skrivåtkomst, inklusive hemligheter         |   |   |
| Deltagare                           | Läs-/ skrivåtkomst, inklusive hemligheter<br>– Skapa och hantera alla typer av Azure-resurser.     | Ingen förändring |
| Ägare                                 | Läs-/ skrivåtkomst inklusive hemligheter<br>– Fullständig åtkomst till alla resurser<br>-Delegera åtkomst till andra | Ingen förändring |

Information om hur du lägger till HDInsight-kluster operatorn rolltilldelning till en användare att ge dem läs/skrivbehörighet till klustret hemligheter, finns i den under avsnitt, [Lägg till rolltilldelning Operator för HDInsight-kluster till en användare](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Påverkas jag av dessa ändringar?

Följande entiteter och scenarier som påverkas:

- [API](#api): Användare som använder den `/configurations` eller `/configurations/{configurationName}` slutpunkter.
- [Azure HDInsight Tools för Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 eller lägre.
- [Azure Toolkit för IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 eller lägre.
- [SDK för .NET](#sdk-for-net)
    - [versioner 1.x och 2.x](#versions-1x-and-2x): Användare som använder den `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` eller `DisableHttp` metoderna i klassen ConfigurationsOperationsExtensions.
    - [versioner 3.x och upp](#versions-3x-and-up): Användare som använder den `Get`, `Update`, `EnableHttp`, eller `DisableHttp` metoder från den `ConfigurationsOperationsExtensions` klass.
- [SDK för Python](#sdk-for-python): Användare som använder den `get` eller `update` metoder från den `ConfigurationsOperations` klass.
- [SDK för Java](#sdk-for-java): Användare som använder den `update` eller `get` metoder från den `ConfigurationsInner` klass.
- [SDK för Go](#sdk-for-go): Användare som använder den `Get` eller `Update` metoder från den `ConfigurationsClient` Struct-datatypen.

Läs avsnitten nedan (eller använd länkarna ovan) om du vill se migreringsanvisningarna för ditt scenario.

### <a name="api"></a>API

Följande API: er kan ändras eller inaktuell:

- [**GET-/configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (känslig information tas bort)
    - Tidigare används för att hämta enskilda konfigurationerna (inklusive hemligheter).
    - Den här API-anrop returnerar nu enskilda konfigurationerna med hemligheter utelämnas. Hämta alla inställningar, till exempel hemligheter, med nya INLÄGG /configurations anropet. Hämta bara gateway-inställningar med det nya INLÄGG /getGatewaySettings anropet.
- [**GET-/configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (inaktuell)
    - Användes för att hämta alla konfigurationer (inklusive hemligheter)
    - Den här API-anrop kommer inte längre att stödjas. Hämta alla konfigurationer framöver med nya INLÄGG /configurations anropet. Hämta konfigurationer med känsliga parametrar utelämnas med GET /configurations/ {configurationName}-anrop.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (inaktuell)
    - Tidigare används för att uppdatera gateway-autentiseringsuppgifter.
    - Den här API-anrop kommer föråldrad och inte längre stöds. Använd den nya INLÄGG /updateGatewaySettings i stället.

Följande ersättningen API: er har lagts till:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Du kan använda detta API för att hämta alla inställningar, till exempel hemligheter.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Du kan använda detta API för att hämta gateway-inställningar.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Använd detta API om du vill uppdatera gateway-inställningar (användarnamn och/eller lösenord).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools för Visual Studio Code

Om du använder version 1.1.1 eller nedan uppdaterar till den [senaste versionen av Azure HDInsight Tools för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) att undvika avbrott i verksamheten.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit för IntelliJ

Om du använder version 3.20.0 eller nedan uppdaterar till den [senaste versionen av Azure Toolkit för IntelliJ plugin-programmet](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) att undvika avbrott i verksamheten.

### <a name="sdk-for-net"></a>SDK för .NET

#### <a name="versions-1x-and-2x"></a>Versioner 1.x och 2.x

Uppdatera till [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) av HDInsight SDK för .NET. Minimal kodändringar kan krävas om du använder en metod som påverkas av ändringarna:

- `ClusterOperationsExtensions.GetClusterConfigurations` kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar `ClusterOperationsExtensions.ListConfigurations` framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster.
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` Nu är föråldrade och har ersatts av `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` Nu är föråldrade och har ersatts av `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` och `DisableHttp` nu är föråldrade. HTTP är nu alltid aktiverad, så dessa metoder är inte längre behövs.

#### <a name="versions-3x-and-up"></a>Versioner 3.x och senare

Uppdatera till [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) eller senare av HDInsight SDK för .NET. Minimal kodändringar kan krävas om du använder en metod som påverkas av ändringarna:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster. 
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Nu är föråldrade och har ersatts av [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) och [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) nu är föråldrade. HTTP är nu alltid aktiverad, så dessa metoder är inte längre behövs.

### <a name="sdk-for-python"></a>SDK för Python

Uppdatera till [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) eller senare av HDInsight SDK för Python. Minimal kodändringar kan krävas om du använder en metod som påverkas av ändringarna:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster. 
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) Nu är föråldrade och har ersatts av [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK för Java

Uppdatera till [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) eller senare av HDInsight SDK för Java. Minimal kodändringar kan krävas om du använder en metod som påverkas av ändringarna:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster. 
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) Nu är föråldrade och har ersatts av [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK för Go

Uppdatera till [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) eller senare av HDInsight SDK för Go. Minimal kodändringar kan krävas om du använder en metod som påverkas av ändringarna:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) kommer **inte längre returneras känsliga parametrar** som lagringsnycklar (core-plats) eller HTTP-autentiseringsuppgifter (gateway).
    - Använd för att hämta alla inställningar, till exempel känsliga parametrar [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) framöver.  Observera att användare med rollen ”läsare” inte kommer att kunna använda den här metoden. Det möjliggör detaljerad kontroll över vilka användare kan komma åt känslig information för ett kluster. 
    - Använd för att hämta bara HTTP-gateway-autentiseringsuppgifter, [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) Nu är föråldrade och har ersatts av [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Lägg till rolltilldelning Operator för HDInsight-kluster till en användare

En användare med den [deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) eller [ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rollen kan tilldela den [Hdinsight-kluster operatorn](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) roll till användare som du vill ha läs-/ skrivåtkomst till känsliga HDInsight-kluster-konfigurationsvärden (till exempel autentiseringsuppgifter för kluster-gateway och lagringskontonycklar).

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Det enklaste sättet att lägga till den här rolltilldelningen är med hjälp av den `az role assignemnt create` i Azure CLI.

> [!NOTE]
> Det här kommandot måste köras av en användare med rollerna deltagare eller ägare som endast de kan bevilja dessa behörigheter. Den `--assignee` är e-postadressen för användaren som du vill tilldela rollen operatör för HDInsight-kluster.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Ge rollen på resursnivån (kluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Ge rollen på resursgruppsnivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Ge rollen på prenumerationsnivån

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Du kan också använda Azure-portalen för att lägga till HDInsight-kluster operatorn rolltilldelning till en användare. Finns i dokumentationen för [hantera åtkomst till Azure-resurser med RBAC och Azure portal – Lägg till en rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
