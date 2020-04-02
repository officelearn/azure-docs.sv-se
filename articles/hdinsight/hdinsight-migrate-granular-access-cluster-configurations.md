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
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546344"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrera till detaljerad rollbaserad åtkomst för klusterkonfigurationer

Vi inför några viktiga förändringar för att stödja mer detaljerad rollbaserad tillgång för att få känslig information. Som en del av dessa ändringar kan vissa åtgärder krävas **senast den 3 september 2019** om du använder någon av de [berörda entiteterna/scenarierna](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Vad förändras?

Tidigare kunde hemligheter erhållas via HDInsight API av klusteranvändare som har [rollerna](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)Ägare, Deltagare eller `*/read` Läsare, eftersom de var tillgängliga för alla med behörighet. Hemligheter definieras som värden som kan användas för att få mer förhöjd åtkomst än en användares roll bör tillåta. Dessa inkluderar värden som HTTP-autentiseringsuppgifter för klustergateway, lagringskontonycklar och databasautentiseringsuppgifter.

Från och med den 3 september 2019, `Microsoft.HDInsight/clusters/configurations/action` tillgång till dessa hemligheter kommer att kräva tillstånd, vilket innebär att de inte längre kan nås av användare med reader-rollen. Rollerna som har den här behörigheten är rollen Deltagare, Ägare och den nya HDInsight Cluster Operator -rollen (mer om den nedan).

Vi introducerar också en ny [HDInsight Cluster Operator-roll](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) som kan hämta hemligheter utan att beviljas administratörsbehörighetens eller ägarens administrativa behörigheter. Sammanfattning:

| Roll                                  | Tidigare                                                                                       | Framöver       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Läsare                                | - Läs åtkomst, inklusive hemligheter                                                                   | - Läsåtkomst, **exklusive** hemligheter |           |   |   |
| Klusteroperatör för HDInsight<br>(Ny roll) | Ej tillämpligt                                                                                              | - Läs/skriv åtkomst, inklusive hemligheter         |   |   |
| Deltagare                           | - Läs/skriv åtkomst, inklusive hemligheter<br>- Skapa och hantera alla typer av Azure-resurser.     | Ingen ändring |
| Ägare                                 | - Läs/skriv åtkomst inklusive hemligheter<br>- Full tillgång till alla resurser<br>- Delegera åtkomst till andra | Ingen ändring |

Information om hur du lägger till rolltilldelningen för HDInsight Cluster Operator till en användare för att ge dem läs-/skrivåtkomst till klusterhemligheter finns i avsnittet [Nedan, Lägg till rolltilldelningen för HDInsight Cluster Operator till en användare](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Påverkas jag av dessa förändringar?

Följande entiteter och scenarier påverkas:

- [API](#api): Användare `/configurations` `/configurations/{configurationName}` som använder slutpunkterna eller.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) version 1.1.1 eller nedan.
- [Azure Toolkit för IntelliJ](#azure-toolkit-for-intellij) version 3.20.0 eller lägre.
- [Azure Data Lake and Stream Analytics Tools för Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) nedan version 2.3.9000.1.
- [Azure Toolkit för Eclipse](#azure-toolkit-for-eclipse) version 3.15.0 eller lägre.
- [SDK för .NET](#sdk-for-net)
    - [versioner 1.x eller 2.x](#versions-1x-and-2x) `GetClusterConfigurations`: `GetConnectivitySettings` `ConfigureHttpSettings`Användare `EnableHttp` `DisableHttp` som använder klassen , , eller metoder från klassen ConfigurationsOperationsExtensions.
    - [versioner 3.x och uppåt:](#versions-3x-and-up) `Update`Användare `EnableHttp`som `DisableHttp` använder `ConfigurationsOperationsExtensions` `Get`, , eller metoder från klassen.
- [SDK för Python](#sdk-for-python): `get` `update` Användare som `ConfigurationsOperations` använder eller metoder från klassen.
- [SDK för Java](#sdk-for-java): `update` `get` Användare som `ConfigurationsInner` använder eller metoder från klassen.
- [SDK för go:](#sdk-for-go) `Get` Användare `Update` som `ConfigurationsClient` använder eller metoder från struct.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) nedan version 2.0.0.
Se nedanstående avsnitt (eller använd länkarna ovan) för att se migreringsstegen för ditt scenario.

### <a name="api"></a>API

Följande API:er ändras eller är inaktuella:

- [**GET /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (känslig information har tagits bort)
    - Tidigare använts för att hämta enskilda konfigurationstyper (inklusive hemligheter).
    - Från och med den 3 september 2019 returnerar det här API-anropet nu enskilda konfigurationstyper med utelämnade hemligheter. Om du vill hämta alla konfigurationer, inklusive hemligheter, använder du det nya POST /configurations-anropet. Om du bara vill hämta gatewayinställningar använder du det nya POST /getGatewaySettings-anropet.
- [**GET /konfigurationer**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (inaktuella)
    - Tidigare använts för att hämta alla konfigurationer (inklusive hemligheter)
    - Från och med den 3 september 2019 kommer det här API-anropet att vara inaktuellt och stöds inte längre. Om du vill hämta alla konfigurationer framöver använder du det nya POST /configurations-anropet. Om du vill hämta konfigurationer med utelämnade känsliga parametrar använder du anropet GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (inaktuellt)
    - Tidigare använts för att uppdatera gateway-autentiseringsuppgifter.
    - Från och med den 3 september 2019 kommer det här API-anropet att vara inaktuellt och stöds inte längre. Använd de nya POST /updateGatewaySettings istället.

Följande ersättnings-API:er har lagts till:</span>

- [**POST /konfigurationer**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Använd det här API:et för att hämta alla konfigurationer, inklusive hemligheter.
- [**POST /getGatewaySettings POST /getGatewayE**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Använd det här API:et för att hämta gatewayinställningar.
- [**POST /updateGatewaySettings POST /updateGatewayE**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Använd det här API:et för att uppdatera gatewayinställningar (användarnamn och/eller lösenord).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight-verktyg för Visual Studio-kod

Om du använder version 1.1.1 eller lägre uppdaterar du till den [senaste versionen av Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) för att undvika avbrott.

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

Om du använder version 3.20.0 eller lägre uppdaterar du till den [senaste versionen av Azure Toolkit for IntelliJ-insticksprogrammet för](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) att undvika avbrott.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Analysverktyg för Azure Data Lake och Stream för Visual Studio

Uppdatera till version 2.3.9000.1 eller senare av [Azure Data Lake och Stream Analytics Tools för Visual Studio för](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) att undvika avbrott.  Hjälp med att uppdatera finns i vår [dokumentation, Update Data Lake Tools för Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

Om du använder version 3.15.0 eller lägre uppdaterar du till den [senaste versionen av Azure Toolkit for Eclipse för](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) att undvika avbrott.

### <a name="sdk-for-net"></a>SDK för .NET

#### <a name="versions-1x-and-2x"></a>Versionerna 1.x och 2.x

Uppdatera till [version 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) av HDInsight SDK för .NET. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ClusterOperationsExtensions.GetClusterConfigurations`returnerar **inte längre känsliga parametrar** som lagringsnycklar (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, använder du `ClusterOperationsExtensions.ListConfigurations` framöver.  Observera att användare med rollen "Läsare" inte kommer att kunna använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster.
    - Om du bara vill `ClusterOperationsExtensions.GetGatewaySettings`hämta HTTP-gateway-autentiseringsuppgifter använder du .

- `ClusterOperationsExtensions.GetConnectivitySettings`är nu inaktuella och har `ClusterOperationsExtensions.GetGatewaySettings`ersatts av .

- `ClusterOperationsExtensions.ConfigureHttpSettings`är nu inaktuella och har `ClusterOperationsExtensions.UpdateGatewaySettings`ersatts av .

- `ConfigurationsOperationsExtensions.EnableHttp`och `DisableHttp` är nu inaktuella. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

#### <a name="versions-3x-and-up"></a>Version 3.x och uppåt

Uppdatera till [version 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) eller senare av HDInsight SDK för .NET. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)returnerar **inte längre känsliga parametrar** som lagringsnycklar (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, använder du [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) framöver.Observera att användare med rollen "Läsare" inte kommer att kunna använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)hämta HTTP-gateway-autentiseringsuppgifter använder du . 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)är nu inaktuella och har [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet)ersatts av . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)och [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) är nu inaktuella. HTTP är nu alltid aktiverat, så dessa metoder behövs inte längre.

### <a name="sdk-for-python"></a>SDK för Python

Uppdatera till [version 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) eller senare av HDInsight SDK för Python. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)returnerar **inte längre känsliga parametrar** som lagringsnycklar (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, använder du [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) framöver.Observera att användare med rollen "Läsare" inte kommer att kunna använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)hämta HTTP-gateway-autentiseringsuppgifter använder du .
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)är nu inaktuella och har [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)ersatts av .

### <a name="sdk-for-java"></a>SDK för Java

Uppdatera till [version 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) eller senare av HDInsight SDK för Java. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- `ConfigurationsInner.get`returnerar **inte längre känsliga parametrar** som lagringsnycklar (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
- `ConfigurationsInner.update`är nu föråldrad.

### <a name="sdk-for-go"></a>SDK för go

Uppdatera till [version 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) eller senare av HDInsight SDK for Go. Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)returnerar **inte längre känsliga parametrar** som lagringsnycklar (kärnplats) eller HTTP-autentiseringsuppgifter (gateway).
    - Om du vill hämta alla konfigurationer, inklusive känsliga parametrar, använder du [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) framöver.Observera att användare med rollen "Läsare" inte kommer att kunna använda den här metoden. Detta möjliggör detaljerad kontroll över vilka användare som kan komma åt känslig information för ett kluster. 
    - Om du bara vill [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)hämta HTTP-gateway-autentiseringsuppgifter använder du .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)är nu inaktuella och har [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings)ersatts av .

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Uppdatera till [Az PowerShell version 2.0.0](https://www.powershellgallery.com/packages/Az) eller senare för att undvika avbrott.  Minimala kodändringar kan krävas om du använder en metod som påverkas av dessa ändringar.
- `Grant-AzHDInsightHttpServicesAccess`är nu föråldrad och har ersatts `Set-AzHDInsightGatewayCredential` av den nya cmdlet.
- `Get-AzHDInsightJobOutput`har uppdaterats för att stödja detaljerad rollbaserad åtkomst till lagringsnyckeln.
    - Användare med rollerna	HDInsight-klusteroperator, Deltagare och Ägare påverkas inte.
    - Användare med endast rollen Läsare måste `DefaultStorageAccountKey` uttryckligen ange parametern.
- `Revoke-AzHDInsightHttpServicesAccess`är nu föråldrad. HTTP är nu alltid aktiverat, så den här cmdleten behövs inte längre.
 Se [az. HDInsight migration guide](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) för mer information.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Lägga till rolltilldelningen för HDInsight Cluster Operator till en användare

En användare med [rollen Ägare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) kan tilldela rollen [HDInsight Cluster Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) till användare som du vill ha läs-/skrivåtkomst till känsliga hdinsight-klusterkonfigurationsvärden (till exempel autentiseringsuppgifter för klustergateway och lagringskontonycklar).

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Det enklaste sättet att lägga till den `az role assignment create` här rolltilldelningen är att använda kommandot i Azure CLI.

> [!NOTE]
> Det här kommandot måste köras av en användare med rollen Ägare, eftersom endast de kan bevilja dessa behörigheter. Namnet `--assignee` på tjänstens huvudnamn eller e-postadress för den användare som du vill tilldela rollen HDInsight Cluster Operator. Om du får ett otillräckligt behörighetsfel läser du vanliga frågor och svar nedan.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Bevilja roll på resursnivå (klusternivå)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Bevilja roll på resursgruppsnivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Bevilja roll på prenumerationsnivå

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Du kan alternativt använda Azure-portalen för att lägga till hdinsight cluster operator-rolltilldelningen till en användare. Se dokumentationen, [Hantera åtkomst till Azure-resurser med RBAC och Azure-portalen - Lägg till en rolltilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Varför ser jag felet 403 när jag har uppdaterat mina API-förfrågningar och/eller API-verktyg?

Klusterkonfigurationer ligger nu bakom detaljerad rollbaserad åtkomstkontroll och kräver `Microsoft.HDInsight/clusters/configurations/*` behörighet att komma åt dem. Om du vill få den här behörigheten tilldelar du rollen HDInsight Cluster Operator, Contributor eller Owner till användarens eller tjänstens huvudnamn som försöker komma åt konfigurationer.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Varför visas "Otillräckliga privilegier för att slutföra åtgärden" när jag kör Azure CLI-kommandot för att tilldela hdinsight-klusteroperatörsrollen till en annan användare eller tjänsthuvudnamn?

Förutom att ha ägarrollen måste användaren eller tjänstens huvudnamn som kör kommandot ha tillräcklig Azure AD-behörighet för att slå upp objekt-ID:t för förvärvaren. Det här meddelandet anger otillräckliga Azure AD-behörigheter. Prova att `-–assignee` ersätta `–assignee-object-id` argumentet med och ange objekt-ID för förvärvaren som parameter i stället för namnet (eller huvud-ID i fråga om en hanterad identitet). Mer information finns i avsnittet valfria parametrar i [den az-rolltilldelningen](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) som skapar dokumentation.

Om detta fortfarande inte fungerar kontaktar du din Azure AD-administratör för att hämta rätt behörigheter.

### <a name="what-will-happen-if-i-take-no-action"></a>Vad händer om jag inte vidtar några åtgärder?

Från och med den 3 september `GET /configurations` `POST /configurations/gateway` 2019 och samtal `GET /configurations/{configurationName}` kommer inte längre att returnera någon information och samtalet returnerar inte längre känsliga parametrar, till exempel lagringskontonycklar eller klusterlösenordet. Detsamma gäller för motsvarande SDK-metoder och PowerShell-cmdlets.

Om du använder en äldre version av något av verktygen för Visual Studio, VSCode, IntelliJ eller Eclipse som nämns ovan, kommer de inte längre att fungera förrän du uppdaterar.

Mer detaljerad information finns i motsvarande avsnitt i det här dokumentet för ditt scenario.
