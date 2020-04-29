---
title: Detaljerad rollbaserad åtkomst till Azure HDInsight-klusterkonfigurationer
description: Lär dig mer om de ändringar som krävs som en del av migreringen till detaljerad rollbaserad åtkomst för HDInsight-klusterkonfigurationer.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: bb78d84aa0f9a2832b6599edeac9d50e0e226437
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80546344"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrera till detaljerad rollbaserad åtkomst för klusterkonfigurationer

Vi introducerar några viktiga ändringar för att ge stöd för mer detaljerad rollbaserad åtkomst för att få känslig information. Som en del av dessa ändringar kan en del åtgärd krävas **senast den 3 September 2019** om du använder en av de [berörda entiteterna/scenarierna](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Vad ändras?

Tidigare kunde hemligheter erhållas via HDInsight-API: t av kluster användare som har rollen ägare, deltagare eller Reader [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), som de var tillgängliga för alla med `*/read` behörigheten. Hemligheter definieras som värden som kan användas för att få mer utökad åtkomst än en användares roll ska tillåtas. Detta inkluderar värden som till exempel kluster-gatewayens HTTP-autentiseringsuppgifter, lagrings konto nycklar och autentiseringsuppgifter för databasen.

Från och med den 3 september 2019 krävs `Microsoft.HDInsight/clusters/configurations/action` behörigheten för att komma åt dessa hemligheter, vilket innebär att de inte längre kan nås av användare med rollen läsare. Rollerna som har den här behörigheten är deltagare, ägare och den nya rollen för HDInsight-klustret (mer information nedan).

Vi introducerar också en ny roll för [HDInsight-kluster](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) som kommer att kunna hämta hemligheter utan att ha behörighet som deltagares eller ägares administrativa behörigheter. Sammanfattning:

| Roll                                  | Tidigare                                                                                       | Gå framåt       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Läsare                                | – Läs behörighet, inklusive hemligheter                                                                   | -Läs behörighet, **förutom** hemligheter |           |   |   |
| HDInsight-kluster operator<br>(Ny roll) | Ej tillämpligt                                                                                              | -Läs-/Skriv behörighet, inklusive hemligheter         |   |   |
| Deltagare                           | -Läs-/Skriv behörighet, inklusive hemligheter<br>– Skapa och hantera alla typer av Azure-resurser.     | Ingen ändring |
| Ägare                                 | -Läs-/skriv åtkomst inklusive hemligheter<br>-Fullständig åtkomst till alla resurser<br>– Delegera åtkomst till andra | Ingen ändring |

Information om hur du lägger till roll tilldelningen HDInsight-rollen till en användare för att ge dem Läs-och skriv åtkomst till kluster hemligheter finns i avsnittet nedan, [Lägg till roll tilldelningen HDInsight-kluster till en användare](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Påverkas jag av de här ändringarna?

Följande entiteter och scenarier påverkas:

- [API](#api): användare som `/configurations` använder sig `/configurations/{configurationName}` av-eller-slut punkter.
- [Azure HDInsight-verktyg för Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 eller lägre.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 eller senare.
- [Azure Data Lake-och Stream Analytics-verktyg för Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) -versionen av 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) version 3.15.0 eller senare.
- [SDK för .NET](#sdk-for-net)
    - [version 1. x eller 2. x](#versions-1x-and-2x) `GetClusterConfigurations`: användare som använder metoderna `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` , `DisableHttp` eller från klassen ConfigurationsOperationsExtensions.
    - [version 3. x och uppåt](#versions-3x-and-up) `Get`: användare som använder metoderna `Update`, `EnableHttp`,, `DisableHttp` eller från `ConfigurationsOperationsExtensions` klassen.
- [SDK för python](#sdk-for-python): användare som `get` använder metoderna `update` eller från- `ConfigurationsOperations` klassen.
- [SDK för Java](#sdk-for-java): användare som `update` använder metoderna `get` eller från- `ConfigurationsInner` klassen.
- [SDK för go](#sdk-for-go): användare som `Get` använder- `Update` eller-metoderna `ConfigurationsClient` från struct.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) under version 2.0.0.
Se de följande avsnitten (eller Använd länkarna ovan) om du vill se migreringen för ditt scenario.

### <a name="api"></a>API

Följande API: er kommer att ändras eller föråldras:

- [**Hämta/Configurations/{ConfigurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (känslig information tas bort)
    - Användes tidigare för att hämta enskilda konfigurations typer (inklusive hemligheter).
    - Från och med den 3 september 2019 kommer det här API-anropet att returnera enskilda konfigurations typer med hemligheter utelämnade. Om du vill hämta alla konfigurationer, inklusive hemligheter, använder du det nya inlägget/Configurations-anropet. Om du bara vill ha Gateway-inställningar använder du det nya inlägget/getGatewaySettings-anropet.
- [**Hämta/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (inaktuellt)
    - Användes tidigare för att hämta alla konfigurationer (inklusive hemligheter)
    - Från och med den 3 september 2019 är API-anropet inaktuellt och stöds inte längre. Om du vill hämta alla konfigurationer som går framåt använder du det nya inlägget/Configurations-anropet. Om du vill hämta konfigurationer med känsliga parametrar utelämnade använder du GET/configurations/{configurationName}-anropet.
- [**Publicera/Configurations/{ConfigurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (inaktuellt)
    - Användes tidigare för att uppdatera Gateway-autentiseringsuppgifter.
    - Från och med den 3 september 2019 är API-anropet inaktuellt och stöds inte längre. Använd det nya inlägget/updateGatewaySettings i stället.

Följande ersättnings-API: er har lagts till:</span>

- [**PUBLICERA/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Använd det här API: et för att hämta alla konfigurationer, inklusive hemligheter.
- [**PUBLICERA/getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Använd det här API: et för att hämta Gateway-inställningar.
- [**PUBLICERA/updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Använd det här API: et för att uppdatera Gateway-inställningar (användar namn och/eller lösen ord).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight-verktyg för Visual Studio Code

Om du använder version 1.1.1 eller tidigare kan du uppdatera till den [senaste versionen av Azure HDInsight Tools för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) för att undvika avbrott.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Om du använder version 3.20.0 eller lägre kan du uppdatera till den [senaste versionen av Azure Toolkit for IntelliJ-plugin-programmet](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) för att undvika avbrott.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake-och Stream Analytics-verktyg för Visual Studio

Uppdatera till version 2.3.9000.1 eller senare av [Azure Data Lake och Stream Analytics verktyg för Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) för att undvika avbrott.  Hjälp med att uppdatera finns i vår dokumentation, [uppdatera data Lake verktyg för Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Om du använder version 3.15.0 eller lägre kan du uppdatera till den [senaste versionen av Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) för att undvika avbrott.

### <a name="sdk-for-net"></a>SDK för .NET

#### <a name="versions-1x-and-2x"></a>Version 1. x och 2. x

Uppdatera till [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) av HDInsight SDK för .net. Minimala kod ändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ClusterOperationsExtensions.GetClusterConfigurations`kommer **inte längre att returnera känsliga parametrar** som lagrings nycklar (Core-site) eller http-autentiseringsuppgifter (Gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar `ClusterOperationsExtensions.ListConfigurations` , använder du framåt.  Observera att användare med rollen läsare inte kommer att kunna använda den här metoden. Detta ger detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster.
    - Använd `ClusterOperationsExtensions.GetGatewaySettings`för att hämta bara HTTP Gateway-autentiseringsuppgifter.

- `ClusterOperationsExtensions.GetConnectivitySettings`är nu föråldrad och har ersatts `ClusterOperationsExtensions.GetGatewaySettings`av.

- `ClusterOperationsExtensions.ConfigureHttpSettings`är nu föråldrad och har ersatts `ClusterOperationsExtensions.UpdateGatewaySettings`av.

- `ConfigurationsOperationsExtensions.EnableHttp`och `DisableHttp` är nu föråldrade. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

#### <a name="versions-3x-and-up"></a>Version 3. x och uppåt

Uppdatera till [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) eller senare av HDInsight SDK för .net. Minimala kod ändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)kommer **inte längre att returnera känsliga parametrar** som lagrings nycklar (Core-site) eller http-autentiseringsuppgifter (Gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) , använder du framåt.Observera att användare med rollen läsare inte kommer att kunna använda den här metoden. Detta ger detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Använd [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)för att hämta bara HTTP Gateway-autentiseringsuppgifter. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)är nu föråldrad och har ersatts [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)av. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)och [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) är nu föråldrade. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

### <a name="sdk-for-python"></a>SDK för Python

Uppdatera till [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) eller senare av HDInsight SDK för python. Minimala kod ändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)kommer **inte längre att returnera känsliga parametrar** som lagrings nycklar (Core-site) eller http-autentiseringsuppgifter (Gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) , använder du framåt.Observera att användare med rollen läsare inte kommer att kunna använda den här metoden. Detta ger detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Använd [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)för att hämta bara HTTP Gateway-autentiseringsuppgifter.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)är nu föråldrad och har ersatts [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)av.

### <a name="sdk-for-java"></a>SDK för Java

Uppdatera till [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) eller senare av HDInsight SDK för Java. Minimala kod ändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ConfigurationsInner.get`kommer **inte längre att returnera känsliga parametrar** som lagrings nycklar (Core-site) eller http-autentiseringsuppgifter (Gateway).
- `ConfigurationsInner.update`är nu föråldrad.

### <a name="sdk-for-go"></a>SDK för go

Uppdatera till [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) eller senare av HDInsight SDK för go. Minimala kod ändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)kommer **inte längre att returnera känsliga parametrar** som lagrings nycklar (Core-site) eller http-autentiseringsuppgifter (Gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) , använder du framåt.Observera att användare med rollen läsare inte kommer att kunna använda den här metoden. Detta ger detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Använd [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)för att hämta bara HTTP Gateway-autentiseringsuppgifter.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)är nu föråldrad och har ersatts [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)av.

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Uppdatera till [AZ PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az) eller senare för att undvika avbrott.  Minimala kod ändringar kan krävas om du använder en metod som påverkas av ändringarna.
- `Grant-AzHDInsightHttpServicesAccess`är nu föråldrad och har ersatts av den `Set-AzHDInsightGatewayCredential` nya cmdleten.
- `Get-AzHDInsightJobOutput`har uppdaterats för att ge stöd åt detaljerad rollbaserad åtkomst till lagrings nyckeln.
    - Användare med rollerna	HDInsight-klusteroperator, Deltagare och Ägare påverkas inte.
    - Användare med endast rollen läsare behöver ange `DefaultStorageAccountKey` parametern explicit.
- `Revoke-AzHDInsightHttpServicesAccess`är nu föråldrad. HTTP är nu alltid aktiverat, så denna cmdlet behövs inte längre.
 Se [AZ. Guide för HDInsight-migrering](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) för mer information.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Lägg till roll tilldelningen HDInsight-kluster för en användare

En användare med rollen [ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) kan tilldela rollen [HDInsight kluster operatör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) till användare som du vill ha läs-/skriv behörighet till känsliga kluster konfigurations värden för HDInsight (till exempel autentiseringsuppgifter för kluster-gateway och lagrings konto nycklar).

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Det enklaste sättet att lägga till den här roll tilldelningen är `az role assignment create` genom att använda kommandot i Azure CLI.

> [!NOTE]
> Det här kommandot måste köras av en användare med ägar rollen, eftersom bara de kan bevilja dessa behörigheter. `--assignee` Är namnet på tjänstens huvud namn eller e-postadress till den användare till vilken du vill tilldela rollen HDInsight-kluster operatör. Läs vanliga frågor och svar nedan om du får ett fel meddelande om otillräcklig behörighet.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Bevilja roll på resurs nivån (kluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Bevilja roll på resurs grupps nivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Bevilja rollen på prenumerations nivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Du kan också använda Azure Portal för att lägga till roll tilldelningen HDInsight-kluster för en användare. Se dokumentationen, [Hantera åtkomst till Azure-resurser med RBAC och Azure Portal – Lägg till en roll tilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Varför ser jag felet 403 när jag har uppdaterat mina API-förfrågningar och/eller API-verktyg?

Klusterkonfigurationer är nu bakom detaljerad rollbaserad åtkomst kontroll och kräver `Microsoft.HDInsight/clusters/configurations/*` behörighet att komma åt dem. Du kan hämta den här behörigheten genom att tilldela HDInsight-klustrets operatör, deltagare eller ägar roll till användaren eller tjänstens huvud namn för att få åtkomst till konfigurationer.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Varför visas "otillräcklig behörighet för att slutföra åtgärden" när du kör Azure CLI-kommandot för att tilldela rollen HDInsight-kluster operatör till en annan användare eller tjänstens huvud namn?

Förutom att ha ägar rollen måste användaren eller tjänstens huvud namn som kör kommandot ha tillräckliga Azure AD-behörigheter för att leta upp objekt-ID: n för den tilldelade personen. Det här meddelandet anger otillräckliga behörigheter för Azure AD. Försök att ersätta `-–assignee` argumentet med `–assignee-object-id` och ange objekt-ID: t för den tilldelade personen som parameter i stället för namnet (eller ägar-ID: t i händelse av en hanterad identitet). Se avsnittet valfria parametrar i [AZ roll tilldelning skapa dokumentation](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) för mer information.

Om det fortfarande inte fungerar kontaktar du din Azure AD-administratör för att få rätt behörighet.

### <a name="what-will-happen-if-i-take-no-action"></a>Vad händer om jag inte vidtar någon åtgärd?

Från den 3 september 2019 `GET /configurations` och `POST /configurations/gateway` anrop kommer inte längre att returnera någon information och `GET /configurations/{configurationName}` anropet kommer inte längre att returnera känsliga parametrar, t. ex. lagrings konto nycklar eller kluster lösen ord. Samma sak gäller för motsvarande SDK-metoder och PowerShell-cmdletar.

Om du använder en äldre version av ett av verktygen för Visual Studio, VSCode, IntelliJ eller Sol förmörkelse som nämns ovan, fungerar de inte längre förrän du uppdaterar.

Mer detaljerad information finns i motsvarande avsnitt i det här dokumentet för ditt scenario.
