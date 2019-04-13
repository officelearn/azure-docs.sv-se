---
title: Azure Monitoring REST API-genomgång
description: Så här autentisera begäranden och använda Azure Monitor REST API för att hämta tillgängliga definitioner av mätvärden och måttvärden.
author: mcollier
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: mcollier
ms.subservice: ''
ms.openlocfilehash: ddbc00155c3c5c136f7d33861e9ad5177df77470
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528075"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Monitoring REST API-genomgång

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln visar hur du utför autentisering så att din kod kan använda den [Microsoft Azure Monitor REST API-referens](https://docs.microsoft.com/rest/api/monitor/).

Azure Monitor-API gör det möjligt att du kan hämta den tillgängliga standarddefinitioner av mätvärden och kornighet måttvärden. Data kan sparas i ett separat datalager som Azure SQL Database, Azure Cosmos DB eller Azure Data Lake. Därifrån kan du utföra ytterligare analys efter behov.

Förutom att arbeta med olika mått datapunkter, gör övervaka API: et det också möjligt att lista Varningsregler, visa aktivitetsloggar och mycket mer. En fullständig lista över tillgängliga åtgärder finns i den [Microsoft Azure Monitor REST API-referens](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Den autentiserande Azure Monitor-begäranden

Det första steget är att autentisera begäran.

Alla aktiviteter som körs mot Azure Monitor-API använder Azure Resource Manager-autentiseringsmodellen. Därför måste alla begäranden autentiseras med Azure Active Directory (AD Azure). En metod för att autentisera klientprogrammet är att skapa en Azure AD-tjänstens huvudnamn och hämta token för autentisering (JWT). Följande exempelskript visar skapar en Azure AD-tjänsten huvudnamn via PowerShell. En mer detaljerad genomgång finns i dokumentationen på [använder Azure PowerShell för att skapa ett huvudnamn för tjänsten för resursåtkomst](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Det går också att [skapa ett huvudnamn för tjänsten via Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md).

```powershell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Connect-AzAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Om du vill fråga API: et för Azure Monitor bör klientprogrammet använda tidigare skapade tjänstens huvudnamn för autentisering. I följande exempel PowerShell-skript visas en itu med den [Active Directory Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md) (ADAL) för att hämta JWT-token för autentisering. Detta JWT-token skickas som en del av en HTTP-auktorisering parameter i begäranden till REST-API i Azure Monitor.

```powershell
$azureAdApplication = Get-AzADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

När de har autentiserat, kan frågor sedan köras mot REST-API i Azure Monitor. Det finns två bra frågor:

1. Lista över måttdefinitioner för en resurs
2. Hämta mått värden

> [!NOTE]
> Mer information om autentisering med Azure REST-API och finns i den [Azure REST API-referens](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Hämta Måttdefinitioner (flerdimensionella API)

Använd den [REST API för Azure Monitor måttdefinitioner](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) att komma åt listan över mått som är tillgängliga för en tjänst.

**Metoden**: HÄMTA

**Begärande-URI**: https:\/\/management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{ apiVersion}*

Till exempel för att hämta måttdefinitioner för ett Azure Storage-konto, skulle begäran visas på följande sätt:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Använd ”2018-01-01” för att hämta definitioner av mått med hjälp av den flerdimensionella mätvärden för Azure Monitor REST API, som API-versionen.
>
>

Resulterande JSON svarstexten skulle vara liknar följande exempel: (Observera att andra mått har dimensioner)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "supportedAggregationTypes": [
                "Total",
                "Average",
                "Minimum",
                "Maximum"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage",
            "namespace": "Microsoft.Storage/storageAccounts",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "supportedAggregationTypes": [
                "Total"
            ],
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT5M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT15M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT30M",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT6H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "PT12H",
                    "retention": "P93D"
                },
                {
                    "timeGrain": "P1D",
                    "retention": "P93D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Hämta dimensionsvärden (flerdimensionella API)

När de tillgängliga definitionerna av mått är kända, kan det finnas vissa mått med dimensioner. Innan du frågar efter måttet kan du identifiera vilka värdeintervallet har en dimension. Baserat på dessa dimensionsvärden som du kan sedan välja att filtrera eller segment som mått som baseras på dimensionsvärden vid fråga till för mått.  Använd den [Azure Monitor Metrics REST API](https://docs.microsoft.com/rest/api/monitor/metrics) att uppnå detta.

Använd den måttet namnet 'value' (inte den ' localizedValue') för alla begäranden som filtrerande. Om inga filter har angetts, returneras Standardmåttet. Användningen av denna API tillåter endast en dimension har ett jokertecken-filtret.

> [!NOTE]
> Använd ”2018-01-01” för att hämta värden med hjälp av Azure Monitor REST API, som API-versionen.
>
>

**Metoden**: HÄMTA

**Begärande-URI**: https\://management.azure.com/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/*{ resurs-provider-namespace}*/*{resurstypen}*/*{resurs-name}*/providers/microsoft.insights/metrics? metricnames =*{mått}*& timespan =*{starttime/endtime}*& $filter =*{filter}*& resultType = metadata & api-version = *{apiVersion}*

Till exempel för att hämta listan över dimensionsvärden som har genererats för ”API-namn dimensionen' för måttet 'Transaktioner” där dimensionen GeoType = ”primär” under det angivna tidsintervallet begäran skulle vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Resulterande JSON svarstexten skulle vara liknar följande exempel:

```JSON
{
  "timespan": "2018-03-01T00:00:00Z/2018-03-02T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Hämta måttvärden (flerdimensionella API)

När tillgängliga definitioner av mätvärden och möjliga värden är kända, har det möjligt att hämta relaterad måttvärden.  Använd den [Azure Monitor Metrics REST API](https://docs.microsoft.com/rest/api/monitor/metrics) att uppnå detta.

Använd den måttet namnet 'value' (inte den ' localizedValue') för alla begäranden som filtrerande. Om inga dimensionsfilter har anges, returneras upplyfta sammansatta måttet. Om en måttfråga returnerar flera timeseries, kan du använda parametrarna ”längst upp' och 'OrderBy-fråga för att returnera en begränsad sorterad lista över timeseries.

> [!NOTE]
> Använd ”2018-01-01” för att hämta flerdimensionella mått värden med hjälp av Azure Monitor REST-API, som API-versionen.
>
>

**Metoden**: HÄMTA

**Begärande-URI**: https://management.azure.com/subscriptions/ *{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/*{resurs-provider-namespace}* / *{resurstypen}*/*{resurs-name}*/providers/microsoft.insights/metrics?metricnames=*{mått}*& timespan =*{starttime/endtime}*& $filter =*{filter}*& intervall =*{timeGrain}*& aggregering =*{ aggreation}*& api-version =*{apiVersion}*

Till exempel för att hämta de 3 främsta API: er, i fallande värde, av hur många transaktioner om du under flera 5 min där GeotType var primär, begäran skulle vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Resulterande JSON svarstexten skulle vara liknar följande exempel:

```JSON
{
  "cost": 0,
  "timespan": "2018-03-01T02:00:00Z/2018-03-01T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "total": 2
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "total": 1
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "total": 3
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "total": 7
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "total": 2
            }
          ]
        },
        ...
      ]
    }
  ],
  "namespace": "Microsoft.Storage/storageAccounts",
  "resourceregion": "eastus"
}
```

## <a name="retrieve-metric-definitions"></a>Hämta måttdefinitioner

Använd den [REST API för Azure Monitor måttdefinitioner](https://msdn.microsoft.com/library/mt743621.aspx) att komma åt listan över mått som är tillgängliga för en tjänst.

**Metoden**: HÄMTA

**Begärande-URI**: https:\/\/management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{ apiVersion}*

Till exempel för att hämta måttdefinitioner för en Azure Logic App skulle begäran visas på följande sätt:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Om du vill hämta definitioner av mått med hjälp av REST-API i Azure Monitor, använder du ”2016-03-01” som API-versionen.
>
>

Resulterande JSON svarstexten skulle vara liknar följande exempel:

```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Mer information finns i den [lista måttdefinitioner för en resurs i Azure Monitor REST API](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentation.

## <a name="retrieve-metric-values"></a>Hämta måttvärden

När de tillgängliga definitionerna av mått är kända har det möjligt att hämta relaterad måttvärden. Använda den måttet namnet 'value' (inte den ' localizedValue') för alla begäranden som filtrerande (till exempel hämta 'CpuTime' och 'Begär' mått datapunkter). Om inga filter har angetts, returneras Standardmåttet.

> [!NOTE]
> Använd ”2016-09-01” för att hämta mått värden med hjälp av Azure Monitor REST-API, som API-versionen.
>
>

**Metoden**: HÄMTA

**Begärande-URI**: https://management.azure.com/subscriptions/ *{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/*{resurs-provider-namespace}* / *{resurstypen}*/*{resurs-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*& api-version =*{apiVersion}*

Till exempel om du vill hämta RunsSucceeded mått datapunkter för det angivna tidsintervallet och för ett tidskorn 1 timme skulle begäran vara följande:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Resulterande JSON svarstexten skulle vara liknar följande exempel:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Om du vill hämta flera punkter för data eller aggregering, lägga till måttdefinition namn och aggregeringstyper filtret, som visas i följande exempel:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Resulterande JSON svarstexten skulle vara liknar följande exempel:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Använda ARMClient

En ytterligare metod är att använda [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. ARMClient hanterar automatiskt Azure AD-autentisering (och resulterande JWT-token). Följande steg beskriver användningen av ARMClient för att hämta måttdata:

1. Installera [Chocolatey](https://chocolatey.org/) och [ARMClient](https://github.com/projectkudu/armclient).
2. Skriv i ett terminalfönster *armclient.exe inloggning*. Då uppmanas du att logga in på Azure.
3. Type *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Type *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Till exempel för att hämta måttdefinitioner för en specifik Logikapp, kör du följande kommando:

```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Hämta resurs-ID

Med hjälp av REST-API kan verkligen tillgängliga definitioner av mätvärden, kornighet och de relaterade värden. Att informationen är användbar när du använder den [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

För den föregående koden är resurs-ID för att använda den fullständiga sökvägen till den önskade Azure-resursen. Till exempel om du vill fråga mot ett Azure Web Apps, skulle resurs-ID vara:

*/subscriptions/{Subscription-ID}/resourceGroups/{Resource-group-name}/providers/Microsoft.Web/Sites/{Site-Name}/*

I följande lista innehåller några exempel på resurs-ID-formaten för olika Azure-resurser:

* **IoT Hub** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **SQL-Databaspool** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL-databas (v12)** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{servernamnet}*/databases/*{databas-name}*
* **Service Bus** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namnområdet}* / *{servicebus-name}*
* **VM-skalningsuppsättningar** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{vm-name}*
* **Virtuella datorer** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Händelsehubbar** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{ eventhub-namespace}*

Det finns alternativa metoder för att hämta resurs-ID, inklusive användning av Azure Resource Explorer, visa önskad resurs i Azure-portalen och via PowerShell eller Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

För att hitta resurs-ID för en önskad resurs, en bra metod är att använda den [Azure Resource Explorer](https://resources.azure.com) verktyget. Navigera till önskad resurs och titta sedan på det ID som visas som i följande skärmbild:

![ALT ”Azure Resource Explorer”](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Resurs-ID kan också hämtas från Azure-portalen. Du gör detta genom att gå till önskad resurs och välj Egenskaper. Resurs-ID visas i avsnittet egenskaper som visas i följande skärmbild:

![ALT ”resurs-ID visas i bladet egenskaper i Azure portal”](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Resurs-ID kan hämtas med hjälp av Azure PowerShell-cmdlets. Till exempel för att hämta resurs-ID för ett Azure Logic Apps, kör du cmdleten Get-AzureLogicApp, som i följande exempel:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Resultatet bör likna följande exempel:

```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```

### <a name="azure-cli"></a>Azure CLI

Hämta resurs-ID för ett Azure Storage-konto med hjälp av Azure CLI genom att köra den `az storage account show` kommandot, som visas i följande exempel:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Resultatet bör likna följande exempel:

```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Med Azure Logic Apps ännu inte tillgänglig via Azure CLI, så ett Azure Storage-konto visas i föregående exempel.
>
>

## <a name="retrieve-activity-log-data"></a>Hämta aktivitetsloggdata

Förutom definitioner av mätvärden och relaterade värden är det också möjligt att använda Azure Monitor REST API för att hämta ytterligare intressanta insikter som är relaterade till Azure-resurser. Exempelvis är det möjligt att frågan [aktivitetsloggen](https://msdn.microsoft.com/library/azure/dn931934.aspx) data. I följande exempel visar hur du använder Azure Monitor REST API för att fråga aktivitetsloggdata inom ett visst datumintervall för en Azure-prenumeration:

```powershell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Nästa steg

* Granska den [översikt över övervakning](../../azure-monitor/overview.md).
* Visa den [stöds mått med Azure Monitor](metrics-supported.md).
* Granska den [övervaka REST API-referens för Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Granska den [bibliotek för Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).