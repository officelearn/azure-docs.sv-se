---
title: Genomgång av Azure övervakning REST API | Microsoft Docs
description: Så här autentisera begäranden och använda Azure övervakaren REST API för att hämta tillgängliga mått definitioner och måttvärden.
author: mcollier
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.search.region: ''
ms.search.scope: ''
ms.search.validFrom: ''
ms.dyn365.ops.version: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: mcollier
ms.openlocfilehash: a87f60b04806fb337a9b4558a67ffa11da661ad5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Genomgång av Azure övervakning REST API
Den här artikeln visar hur du autentisera så att din kod kan använda den [Microsoft Azure övervakaren REST API-referens](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

Azure-Monitor API gör det möjligt att genom programmering hämta tillgängliga mått standarddefinitioner, granularitet och måttvärden. Du kan spara data i ett separat dataarkiv som Azure SQL Database, Azure Cosmos DB eller Azure Data Lake. Därifrån kan du utföra ytterligare analys efter behov.

Förutom att arbeta med olika mått datapunkter gör övervakaren API det också möjligt att visa Varningsregler, visa aktivitetsloggar och mycket mer. En fullständig lista över tillgängliga åtgärder finns i [Microsoft Azure övervakaren REST API-referens](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autentiserande Azure-Monitor-begäranden
Det första steget är att autentisera begäran.

Alla aktiviteter som körs mot Azure-Monitor API använder Azure Resource Manager autentisering modell. Därför måste alla förfrågningar autentiseras med Azure Active Directory (AD Azure). En metod för att autentisera klientprogrammet är att skapa en Azure AD huvudnamn för tjänsten och hämta token för autentisering (JWT). Följande exempelskript visar att skapa en Azure AD-tjänsten huvudnamn via PowerShell. För en mer detaljerad genomgång finns i dokumentationen på [med Azure PowerShell för att skapa ett huvudnamn för tjänsten att komma åt resurser](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Det är också möjligt att [skapa ett huvudnamn för tjänsten via Azure portal](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"
$secureStringPassword = ConvertTo-SecureString -String $pwd -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $secureStringPassword

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Om du vill fråga Azure-Monitor API bör klientprogrammet använder tidigare skapade tjänstens huvudnamn för att autentisera. I följande exempel PowerShell-skript visas en närmar sig, med hjälp av den [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) för att hämta JWT-token för autentisering. JWT-token har skickats som en del av en HTTP-auktorisering parameter i begäranden till Azure-Monitor REST-API.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $secureStringPassword)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Efter autentisering, kan du sedan köra frågor mot Azure övervakaren REST API. Det finns två användbara frågor:

1. Visa en lista med mått definitioner för en resurs
2. Hämta mått värden

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Hämta Måttdefinitioner (flerdimensionella API)

Använd den [mått för Azure-Monitor definitioner REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) att komma åt listan över mått som är tillgängliga för en tjänst.

**Metoden**: hämta

**Begärande-URI**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Till exempel för att hämta mått definitioner för ett Azure Storage-konto skulle begäran visas på följande sätt:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Använd ”2018-01-01” för att hämta måttdefinitioner med hjälp av mätvärden för flerdimensionella Azure-Monitor REST-API, som API-versionen.
>
>

Resulterande JSON svarstexten skulle vara liknar följande exempel: (Observera att andra mått har mått)

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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Hämta värden (flerdimensionella API)
När tillgängliga mått definitionerna är kända, kan det finnas vissa mått med dimensioner. Innan du frågar efter det mått som du kanske vill identifiera vilka värdeintervallet har en dimension. Baserat på dessa värden kan du sedan välja att filtrera eller segment mätvärdena baserat på värden vid sökningen efter mått.  Använd den [Azure övervakaren mått REST API](https://docs.microsoft.com/rest/api/monitor/metrics) att uppnå detta.

Använd det mått name 'value' (inte den ' localizedValue') för alla begäranden om filtrering. Om inga filter har angetts, returneras standardmått. Användningen av denna API kan bara en dimension har ett jokerteckenfiltret.

> [!NOTE]
> Använd ”2018-01-01” för att hämta värden med hjälp av Azure-Monitor REST-API som API-versionen.
>
>

**Metoden**: hämta

**Begärande-URI**: https://management.azure.com/subscriptions/ *{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/*{resurs-provider-namespace}* / *{resurstypen}*/*{resursnamn}*/providers/microsoft.insights/metrics?metric=*{mått}* & timespan =*{Starttid/Sluttid}*& $filter =*{filter}*& resultType = metadata & api-version =*{apiVersion}*

Till exempel för att hämta listan med värden som har orsakat för 'API-namnet dimensionen' för 'Transaktioner'-måttet där dimensionen GeoType = 'Primary' under det angivna tidsintervallet skulle begäran Se på följande sätt:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Resulterande JSON svarstexten är liknar följande exempel:

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
När de tillgängliga mått definitioner och möjliga värden är kända, har det möjligt att hämta relaterade måttvärden.  Använd den [Azure övervakaren mått REST API](https://docs.microsoft.com/rest/api/monitor/metrics) att uppnå detta.

Använd det mått name 'value' (inte den ' localizedValue') för alla begäranden om filtrering. Om inga dimensionsfilter anges returneras upplyfta aggregerade mått. Om en mått fråga returnerar flera timeseries, kan du använda frågeparametrar 'Upp' och 'OrderBy-för att returnera en begränsad sorterad lista över timeseries.

> [!NOTE]
> Om du vill hämta flerdimensionella måttvärden med hjälp av REST API för Azure-Monitor Använd ”2018-01-01” som API-versionen.
>
>

**Metoden**: hämta

**Begärande-URI**: https://management.azure.com/subscriptions/ *{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/*{resurs-provider-namespace}* / *{resurstypen}*/*{resursnamn}*/providers/microsoft.insights/metrics?metric=*{mått}* & timespan =*{Starttid/Sluttid}*& $filter =*{filter}*& intervall =*{Tidskorn}*& aggregering =*{ aggreation}*& api-version =*{apiVersion}*

Till exempel för att hämta upp 3 API: er, i fallande värdet, som antal transaktioner om du vid ett intervall på 5 minuter, där GeotType var primär, skulle begäran Se på följande sätt:

```PowerShell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Resulterande JSON svarstexten är liknar följande exempel:

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
Använd den [mått för Azure-Monitor definitioner REST API](https://msdn.microsoft.com/library/mt743621.aspx) att komma åt listan över mått som är tillgängliga för en tjänst.

**Metoden**: hämta

**Begärande-URI**: https://management.azure.com/subscriptions/ *{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}* / *{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Till exempel för att hämta mått definitioner för en Azure-Logikapp skulle begäran visas på följande sätt:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Om du vill hämta måttdefinitioner med hjälp av REST API för Azure-Monitor Använd ”2016-03-01” som API-versionen.
>
>

Resulterande JSON svarstexten är liknar följande exempel:
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

Mer information finns i [listan mått definitioner för en resurs i REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) dokumentation.

## <a name="retrieve-metric-values"></a>Hämta måttvärden
När tillgängliga mått definitionerna är kända, har det möjligt att hämta relaterade måttvärden. Använd det mått name 'value' (inte den ' localizedValue') för alla begäranden om filtrering (till exempel hämtar 'CpuTime' och 'Begär' mått datapunkter). Om inga filter har angetts, returneras standardmått.

> [!NOTE]
> Om du vill hämta mått värden med hjälp av REST API för Azure-Monitor Använd ”2016-09-01” som API-versionen.
>
>

**Metoden**: hämta

**Begärande-URI**: https://management.azure.com/subscriptions/ *{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/*{resurs-provider-namespace}* / *{resurstypen}*/*{resursnamn}*/providers/microsoft.insights/metrics?$filter=*{filter}*& api-version =*{apiVersion}*

Till exempel för att hämta RunsSucceeded mått datapunkter för det angivna tidsintervallet och för ett tidskorn 1 timme skulle begäran vara följande:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Resulterande JSON svarstexten är liknar följande exempel:

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

Om du vill hämta flera punkter för data eller aggregering, lägga till mått definition namn och typer för aggregering i filtret, som visas i följande exempel:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Resulterande JSON svarstexten är liknar följande exempel:

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

### <a name="use-armclient"></a>Använd ARMClient
En ytterligare metod är att använda [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. ARMClient hanterar automatiskt Azure AD-autentisering (och resulterande JWT-token). Följande steg beskriver användningen av ARMClient för att hämta mätvärden:

1. Installera [Chocolatey](https://chocolatey.org/) och [ARMClient](https://github.com/projectkudu/armclient).
2. Ange i ett terminalfönster *armclient.exe inloggning*. Gör det uppmanas du att logga in på Azure.
3. Typen *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Typen *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Till exempel för att hämta mått definitioner för en specifik Logikapp, kör du följande kommando:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Hämta resurs-ID
REST-API kan verkligen hjälp för att förstå tillgängliga mått definitioner och granularitet och relaterade värden. Att informationen är användbar när du använder den [Azure hanteringsmodul](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Föregående kod är resurs-ID för att använda den fullständiga sökvägen till den önskade Azure-resursen. Om du vill fråga mot en Azure-Webbapp, till exempel är resurs-ID:

*/subscriptions/{Subscription-ID}/resourceGroups/{Resource-group-name}/providers/Microsoft.Web/Sites/{Site-Name}/*

Följande lista innehåller några exempel på resurs-ID-formaten för olika Azure-resurser:

* **IoT-hubb** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.Devices/IotHubs/*{iot-hubb-name}*
* **Elastisk SQL-poolen** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL-databas (v12)** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.Sql/servers/*{servernamn}*/databases/*{databasnamnet}*
* **Service Bus** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.ServiceBus/*{namespace}* / *{servicebus-name}*
* **Skaluppsättningar för den virtuella datorn** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.Compute/virtualMachineScaleSets/ *{vm-name}*
* **Virtuella datorer** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Händelsehubbar** -/subscriptions/*{prenumerations-id}*/resourceGroups/*{resurs-gruppnamn}*/providers/Microsoft.EventHub/namespaces/*{ eventhub-namespace}*

Det finns alternativa metoder för hämtning av resurs-ID, inklusive användning av resursutforskaren i Azure, visa en resurs i Azure-portalen och via PowerShell eller Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
För att hitta resurs-ID för en resurs, en bra metod är att använda den [resursutforskaren Azure](https://resources.azure.com) verktyget. Navigera till en resurs och titta på det ID som visas i följande skärmbild:

![ALT ”Azure Resursläsaren”](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal
Resurs-ID kan också hämtas från Azure-portalen. Gör du genom att gå till en resurs och välj sedan Egenskaper. Resurs-ID visas i avsnittet egenskaper som visas i följande skärmbild:

![ALT ”resurs-ID som visas i bladet egenskaper i Azure portal”](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Resurs-ID kan hämtas med hjälp av Azure PowerShell-cmdlets. Till exempel för att hämta resurs-ID för en Azure-Logikapp, kör du cmdlet Get-AzureLogicApp, som i följande exempel:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
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
Om du vill hämta resurs-ID för ett Azure Storage-konto med hjälp av Azure CLI kör du kommandot 'az storage-konto visa' som visas i följande exempel:

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
> Med Azure Logikappar ännu inte tillgängliga via Azure CLI, alltså ett Azure Storage-konto visas i föregående exempel.
>
>

## <a name="retrieve-activity-log-data"></a>Hämta aktivitet loggdata
Förutom måttdefinitioner och relaterade värden är det också möjligt att använda Azure övervakaren REST API för att hämta ytterligare intressanta insikter relaterade till Azure-resurser. Exempelvis är det möjligt att fråga [aktivitetsloggen](https://msdn.microsoft.com/library/azure/dn931934.aspx) data. I följande exempel visas hur du använder Azure övervakaren REST API för att fråga aktivitet loggdata inom ett visst datumintervall för en Azure-prenumeration:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Nästa steg
* Granska de [översikt över övervakning](monitoring-overview.md).
* Visa den [stöds mått med Azure-Monitor](monitoring-supported-metrics.md).
* Granska de [övervaka REST API-referens för Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Granska de [Azure hanteringsmodul](https://msdn.microsoft.com/library/azure/mt417623.aspx).
