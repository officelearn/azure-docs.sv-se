---
title: "Integrera externa övervakningslösning med Azure-stacken | Microsoft Docs"
description: "Lär dig mer om att integrera Azure stacken med en extern övervakningslösning i ditt datacenter."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3435ada40afb9f1c6e57be64d1b9086d0cdaefd9
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrera externa övervakningslösning med Azure-stacken

Du behöver övervaka Azure Stack-programvara, fysiska datorer och fysiska nätverksväxlar för övervakning av externa Azure Stack-infrastrukturen. Dessa områden ger en metod för att hämta information om hälsa och avisering:

- Azure Stack programvara erbjuder ett REST-baserad API för att hämta hälsotillstånd och aviseringar. (Med hjälp av programvarudefinierade tekniker som Storage Spaces Direct lagring hälsotillstånd och aviseringar är en del av programvaran övervakning.).
- Fysiska datorer kan göra hälsa och aviseringsinformation tillgängliga via huvudkortshanteringskontroller (bmc).
- Fysiska nätverksenheter kan göra hälsa och aviseringsinformation tillgängliga via SNMP-protokollet.

Varje Azure Stack-lösning levereras med en maskinvara livscykel värd. Den här värden körs OEM-tillverkaren (OEM) maskinvaruleverantören övervakningsprogram för fysiska servrar och nätverksenheter. Om du vill kan du kringgå övervakningsinställningarna lösningar och integrera med befintliga övervakning direkt i ditt datacenter.

> [!IMPORTANT]
> Den externa övervakningslösning som du använder måste vara utan Agent. Du kan inte installera agenter från tredje part i Azure Stack-komponenter.

Följande diagram visar trafikflödet mellan en Azure-stacken integrerat system, maskinvara livscykel värden, en extern övervakningslösning och ett system för insamling av externa biljetter/data.

![Diagram över trafik mellan Azure stacken, övervakning och biljetter lösning.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Den här artikeln förklarar hur du integrerar Azure stacken med externa övervakningslösningar, till exempel System Center Operations Manager och Nagios. Den omfattar också hur du arbetar med aviseringar via programmering med hjälp av PowerShell eller via REST API-anrop.

## <a name="integrate-with-operations-manager"></a>Integrera med Operations Manager

Du kan använda Operations Manager för externa övervakning av Azure-stacken. System Center Management Pack för Microsoft Azure-Stack kan du övervaka flera Azure-stacken distributioner med en Operations Manager-instans. Management pack använder hälsa resursprovidern och uppdatera resursprovidern REST API: er för att kommunicera med Azure-stacken. Om du planerar att kringgå OEM övervakning av program som körs på värden för maskinvara livscykel, kan du installera leverantörshanteringspaket för övervakning av fysiska servrar. Du kan också använda Operations Manager identifiering av nätverksenheter för att övervaka nätverksväxlar.

Management pack för Azure-stacken innehåller följande funktioner:

- Du kan hantera flera Azure Stack-distributioner
- Det finns stöd för Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS)
- Du kan hämta och stänga aviseringar
- Det finns en hälsa och en instrumentpanel kapacitet
- Innehåller Underhållsläge för automatisk identifiering för när korrigeringar och uppdateringar (P & U) pågår
- Innehåller framtvinga en uppdatering uppgifter för distribution och region
- Du kan lägga till anpassad information till en region
- Har stöd för meddelanden och rapportering

Du kan hämta System Center Management Pack för Microsoft Azure-stacken och den associerade [användarhandboken](https://www.microsoft.com/en-us/download/details.aspx?id=55184), eller direkt från Operations Manager.

Du kan integrera med Service Manager i System Center Operations Manager för en loggnings-lösning. Integrerad product connector kan dubbelriktad kommunikation som gör att du kan stänga en avisering i Azure-stacken och Operations Manager när du har löst tjänstbegäran i Service Manager.

Följande diagram visar integrering av Azure-stacken med en befintlig distribution av System Center. Du kan automatisera Service Manager ytterligare med System Center Orchestrator eller Service Management Automation (SMA) för att köra åtgärder i Azure-stacken.

![Diagram över integrering med OM, Service Manager och SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrera med Nagios

En Nagios övervakning plugin-programmet har utvecklats tillsammans med Cloudbase partnerlösningar som är tillgängliga under licensen Tillåtande kostnadsfri programvara – MIT (Massachusetts Institute of Technology).

Plugin-programmet har skrivits i Python och utnyttjar resursprovidern hälsa REST API. Den erbjuder grundläggande funktioner för att hämta och Stäng varningar i Azure-stacken. Den låter dig lägga till flera Azure Stack-distributioner och skicka meddelanden som System Center management pack.

Plugin-programmet fungerar med Nagios Enterprise och Nagios kärnor. Du kan ladda ned det [här](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Hämtningsplatsen innehåller också information om installation och konfiguration.

### <a name="plugin-parameters"></a>Plugin-parametrar

Konfigurera plugin-fil ”Azurestack_plugin.py” med följande parametrar:

| Parameter | Beskrivning | Exempel |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (administratör) slutpunkt |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (administratör) slutpunkt  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Admin prenumerations-ID | Hämta via administratörsportal eller PowerShell |
| *User_name* | Operatorn prenumeration användarnamn | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operatorn prenumeration lösenord | mypassword |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Namn på Azure Stack område | lokal |
|  |  |

* PowerShell GUID som tillhandahålls är universal. Du kan använda den för varje distribution.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Använda PowerShell för att övervaka hälsotillstånd och aviseringar

Om du inte använder Operations Manager, Nagios eller en Nagios-baserad lösning kan använda du PowerShell för att aktivera ett brett spektrum av övervakning av lösningar som kan integreras med Azure-stacken.
 
1. Om du vill använda PowerShell, se till att du har [PowerShell installeras och konfigureras](azure-stack-powershell-configure-quickstart.md) för en Azure-stacken operator-miljö. Installera PowerShell på en lokal dator som kan nå slutpunkten för Resource Manager (administratör)\(https://adminmanagement.[Region].[External_FQDN]).

2. Kör följande kommandon för att ansluta till Azure Stack-miljö som operatör Azure Stack:

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. Ändra till den katalog där du installerade den [Azure Stack verktyg](https://github.com/Azure/AzureStack-Tools) som en del av PowerShell-installationen, till exempel c:\azurestack-tools-master. Sedan ändra till katalogen infrastruktur och kör följande kommando för att importera modulen infrastruktur:

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. Använd kommandon, till exempel i följande exempel för att arbeta med aviseringar:
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>Använda REST-API för att övervaka hälsotillstånd och aviseringar

Du kan använda REST API-anrop för att få aviseringar, stänga aviseringar och få hälsotillståndet för resursprovidrar.

### <a name="get-alert"></a>Hämta avisering

**Förfrågan**

Begäran hämtar alla aktiva och stängda aviseringar för providern standardabonnemang. Det finns ingen brödtext i begäran.


|Metod  |Förfrågans URI  |
|---------|---------|
|HÄMTA     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**Argument**

|Argumentet  |Beskrivning  |
|---------|---------|
|armendpoint     |  Azure Resource Manager-slutpunkten för din miljö för Azure-stacken, i formatet https://adminmanagement.{RegionName}.{Externa FQDN}. Om den externa FQDN är till exempel *azurestack.external* och regionnamn är *lokala*, och sedan Resource Manager-slutpunkten är https://adminmanagement.local.azurestack.external.       |
|subid     |   Prenumerations-ID för den användare som har att göra anropet. Du kan använda den här API för att fråga endast med en användare som har behörighet att providern standardabonnemang.      |
|RegionName     |    Regionnamn med Azure Stack-distributionen.     |
|api-version     |  Version av det protokoll som används för att göra denna begäran. Du måste använda 2016-05-01.      |
|     |         |

**Svar**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**Svarsinformation**


|  Argumentet  |Beskrivning  |
|---------|---------|
|*id*     |      Unikt ID för aviseringen.   |
|*Namn*     |     Internt namn för aviseringen.   |
|*Typ*     |     Resursdefinitionen.    |
|*Plats*     |       Namn på område.     |
|*tagg*     |   Resurstaggar.     |
|*closedtimestamp*    |  UTC-tid då aviseringen stängdes.    |
|*createdtimestamp*     |     UTC-tid då aviseringen skapades.   |
|*Beskrivning*     |    Beskrivning av aviseringen.     |
|*faultid*     | Komponent som berörs.        |
|*alertid*     |  Unikt ID för aviseringen.       |
|*faulttypeid*     |  Unik typ av felaktiga komponenten.       |
|*lastupdatedtimestamp*     |   UTC-tid då aviseringsinformation senast uppdaterades.    |
|*healthstate*     | Övergripande hälsostatus.        |
|*Namn*     |   Namnet på den specifika aviseringen.      |
|*fabricname*     |    Registrerade fabric namnet på den felaktiga komponenten.   |
|*Beskrivning*     |  Beskrivning av registrerade fabric-komponent.   |
|*ServiceType*     |   Typ av registrerade fabrikstjänsten.   |
|*Reparation*     |   Rekommenderade steg.    |
|*Typ*     |   Typ av avisering.    |
|*resourceRegistrationid*    |     ID för den registrerade resursen som påverkas.    |
|*resourceProviderRegistrationID*   |    ID för registrerad resursprovider för komponenten som påverkas.  |
|*serviceregistrationid*     |    ID för en registrerad service.   |
|*Allvarlighetsgrad*     |     Allvarlighetsgrad för aviseringen.  |
|*state*     |    Tillstånd för avisering.   |
|*Rubrik*     |    Aviseringen rubrik.   |
|*impactedresourceid*     |     ID för påverkas resurs.    |
|*ImpactedresourceDisplayName*     |     Namnet på resursen påverkas.  |
|*closedByUserAlias*     |   Användaren som stängde aviseringen.      |

### <a name="close-alert"></a>Stäng avisering 

**Förfrågan**

Begäran stänger en avisering med ett unikt ID.

|Metod    |Förfrågans URI  |
|---------|---------|
|PLACERA     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**Argument**


|Argumentet  |Beskrivning  |
|---------|---------|
|*armendpoint*     |   Resource Manager-slutpunkten för din miljö för Azure-stacken, i formatet https://adminmanagement.{RegionName}.{Externa FQDN}. Om den externa FQDN är till exempel *azurestack.external* och regionnamn är *lokala*, och sedan Resource Manager-slutpunkten är https://adminmanagement.local.azurestack.external.      |
|*subid*     |    Prenumerations-ID för den användare som har att göra anropet. Du kan använda den här API för att fråga endast med en användare som har behörighet att providern standardabonnemang.     |
|*RegionName*     |   Regionnamn med Azure Stack-distributionen.      |
|*api-version*     |    Version av det protokoll som används för att göra denna begäran. Du måste använda 2016-05-01.     |
|*alertid*     |    Unikt ID för aviseringen.     |

**Brödtext**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**Svar**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**Svarsinformation**


|  Argumentet  |Beskrivning  |
|---------|---------|
|*id*     |      Unikt ID för aviseringen.   |
|*Namn*     |     Internt namn för aviseringen.   |
|*Typ*     |     Resursdefinitionen.    |
|*Plats*     |       Namn på område.     |
|*tagg*     |   Resurstaggar.     |
|*closedtimestamp*    |  UTC-tid då aviseringen stängdes.    |
|*createdtimestamp*     |     UTC-tid då aviseringen skapades.   |
|*Beskrivning*     |    Beskrivning av aviseringen.     |
|*faultid*     | Komponent som berörs.        |
|*alertid*     |  Unikt ID för aviseringen.       |
|*faulttypeid*     |  Unik typ av felaktiga komponenten.       |
|*lastupdatedtimestamp*     |   UTC-tid då aviseringsinformation senast uppdaterades.    |
|*healthstate*     | Övergripande hälsostatus.        |
|*Namn*     |   Namnet på den specifika aviseringen.      |
|*fabricname*     |    Registrerade fabric namnet på den felaktiga komponenten.   |
|*Beskrivning*     |  Beskrivning av registrerade fabric-komponent.   |
|*ServiceType*     |   Typ av registrerade fabrikstjänsten.   |
|*Reparation*     |   Rekommenderade steg.    |
|*Typ*     |   Typ av avisering.    |
|*resourceRegistrationid*    |     ID för den registrerade resursen som påverkas.    |
|*resourceProviderRegistrationID*   |    ID för registrerad resursprovider för komponenten som påverkas.  |
|*serviceregistrationid*     |    ID för en registrerad service.   |
|*Allvarlighetsgrad*     |     Allvarlighetsgrad för aviseringen.  |
|*state*     |    Tillstånd för avisering.   |
|*Rubrik*     |    Aviseringen rubrik.   |
|*impactedresourceid*     |     ID för påverkas resurs.    |
|*ImpactedresourceDisplayName*     |     Namnet på resursen påverkas.  |
|*closedByUserAlias*     |   Användaren som stängde aviseringen.      |

### <a name="get-resource-provider-health"></a>Hämta provider resurshälsa

**Förfrågan**

Begäran hämtar hälsostatus för alla registrerade providrar.


|Metod  |Förfrågans URI  |
|---------|---------|
|HÄMTA    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**Argument**


|Argument  |Beskrivning  |
|---------|---------|
|*armendpoint*     |    Resource Manager-slutpunkten för din miljö för Azure-stacken, i formatet https://adminmanagement. {RegionName}. {Externa FQDN}. Till exempel om den externa FQDN är azurestack.external och regionnamn är lokal Resource Manager-slutpunkten är https://adminmanagement.local.azurestack.external.     |
|*subid*     |     Prenumerations-ID för den användare som har att göra anropet. Du kan använda den här API för att fråga endast med en användare som har behörighet att providern standardabonnemang.    |
|*RegionName*     |     Regionnamn med Azure Stack-distributionen.    |
|*api-version*     |   Version av det protokoll som används för att göra denna begäran. Du måste använda 2016-05-01.      |


**Svar**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**Svarsinformation**


|Argumentet  |Beskrivning  |
|---------|---------|
|*Id*     |   Unikt ID för aviseringen.      |
|*Namn*     |  Internt namn för aviseringen.       |
|*Typ*     |  Resursdefinitionen.       |
|*Plats*     |  Namn på område.       |
|*tagg*     |     Resurstaggar.    |
|*registrationId*     |   Unik registrering för resursprovidern.      |
|*displayName*     |Visningsnamn för resurs-providern.        |
|*namnområde*     |   API-namnområdet resursprovidern implementerar.       |
|*routePrefix*     |    URI för att interagera med resursprovidern.     |
|*serviceLocation*     |   Den här resursprovidern har registrerats med region.      |
|*infraURI*     |   URI för resursprovidern listats som en infrastruktur-roll.      |
|*alertSummary*     |   Sammanfattning av kritiska meddelanden och varningsmeddelanden aviseringen som är associerade med resursprovidern.      |
|*HealthState*     |    Hälsotillståndet för resursprovidern.     |


### <a name="get-resource-health"></a>Hämta resurshälsa

Begäran hämtar hälsostatus för en specifik registrerad resursprovider.

**Förfrågan**

|Metod  |Förfrågans URI  |
|---------|---------|
|HÄMTA     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**Argument**

|Argument  |Beskrivning  |
|---------|---------|
|*armendpoint*     |    Resource Manager-slutpunkten för din miljö för Azure-stacken, i formatet https://adminmanagement. {RegionName}. {Externa FQDN}. Till exempel om den externa FQDN är azurestack.external och regionnamn är lokal Resource Manager-slutpunkten är https://adminmanagement.local.azurestack.external.     |
|*subid*     |Prenumerations-ID för den användare som har att göra anropet. Du kan använda den här API för att fråga endast med en användare som har behörighet att providern standardabonnemang.         |
|*RegionName*     |  Regionnamn med Azure Stack-distributionen.       |
|*api-version*     |  Version av det protokoll som används för att göra denna begäran. Du måste använda 2016-05-01.       |
|*RegistrationID* |Registrerings-ID för en viss resurs-provider. |

**Svar**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**Svarsinformation**

|Argumentet  |Beskrivning  |
|---------|---------|
|*Id*     |   Unikt ID för aviseringen.      |
|*Namn*     |  Internt namn för aviseringen.       |
|*Typ*     |  Resursdefinitionen.       |
|*Plats*     |  Namn på område.       |
|*tagg*     |     Resurstaggar.    |
|*registrationId*     |   Unik registrering för resursprovidern.      |
|*resourceType*     |Typ av resurs.        |
|*resourceName*     |   Resursnamnet.   |
|*usageMetrics*     |    Användning mått för resursen.     |
|*resourceLocation*     |   Namnet på regionen där distribueras.      |
|*resourceURI*     |   URI för resursen.   |
|*alertSummary*     |   Sammanfattning av kritiska och varningsaviseringar hälsostatus.     |

## <a name="learn-more"></a>Läs mer

Information om inbyggda hälsoövervakning finns [övervaka hälsotillstånd och aviseringar i Azure-stacken](azure-stack-monitor-health.md).


## <a name="next-steps"></a>Nästa steg

[Integrering av säkerhet](azure-stack-integrate-security.md)