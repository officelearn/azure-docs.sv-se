---
title: Azure Övervakning REST API genomgång
description: Så här autentiserar du begäranden och använder AZURE Monitor REST API för att hämta tillgängliga måttdefinitioner och måttvärden.
ms.subservice: metrics
ms.topic: conceptual
ms.date: 03/19/2018
ms.openlocfilehash: 6b0e321747e0f84be5a75ab96749311ff0071e8d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687420"
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Azure Övervakning REST API genomgång

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln visar hur du utför autentisering så att koden kan använda [Microsoft Azure Monitor REST API Reference](https://docs.microsoft.com/rest/api/monitor/).

Azure Monitor API gör det möjligt att programmässigt hämta tillgängliga standardmåttdefinitioner, granularitet och måttvärden. Data kan sparas i ett separat datalager som Azure SQL Database, Azure Cosmos DB eller Azure Data Lake. Därifrån ytterligare analys kan utföras vid behov.

Förutom att arbeta med olika måttdatapunkter gör monitor-API:et det också möjligt att lista varningsregler, visa aktivitetsloggar och mycket mer. En fullständig lista över tillgängliga åtgärder finns i [Microsoft Azure Monitor REST API Reference](https://docs.microsoft.com/rest/api/monitor/).

## <a name="authenticating-azure-monitor-requests"></a>Autentisera Azure Monitor-begäranden

Det första steget är att autentisera begäran.

Alla uppgifter som körs mot Azure Monitor API använder Azure Resource Manager autentiseringsmodell. Därför måste alla begäranden autentiseras med Azure Active Directory (Azure AD). En metod för att autentisera klientprogrammet är att skapa ett Azure AD-tjänsthuvudnamn och hämta autentiseringstoken (JWT). Följande exempelskript visar att du skapar ett Huvudnamn för Azure AD-tjänsten via PowerShell. En mer detaljerad genomgång finns i dokumentationen om [hur du använder Azure PowerShell för att skapa ett tjänsthuvudnamn för åtkomst till resurser](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Det är också möjligt att [skapa ett huvudnamn för tjänsten via Azure-portalen](../../active-directory/develop/howto-create-service-principal-portal.md).

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

Om du vill fråga Azure Monitor API bör klientprogrammet använda det tidigare skapade tjänsthuvudhuvudet för att autentisera. I följande exempel visar PowerShell-skriptet en metod med hjälp av [Active Directory Authentication Library](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) för att hämta JWT-autentiseringstoken. JWT-token skickas som en del av en HTTP-auktoriseringsparameter i begäranden till AZURE Monitor REST API.

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

Efter autentisering kan frågor sedan köras mot AZURE Monitor REST API. Det finns två användbara frågor:

1. Lista måttdefinitionerna för en resurs
2. Hämta måttvärdena

> [!NOTE]
> Mer information om hur du autentiserar med Azure REST API finns i [Azure REST API Reference](https://docs.microsoft.com/rest/api/azure/).
>
>

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Hämta måttdefinitioner (flerdimensionellt API)

Använd [AZURE Monitor Metric definitions REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) för att komma åt listan över mått som är tillgängliga för en tjänst.

**Metod**: GET

**Begär URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/Definition metrics?api-version=*{apiVersion}*

Om du till exempel vill hämta måttdefinitionerna för ett Azure Storage-konto visas begäran på följande sätt:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```

> [!NOTE]
> Om du vill hämta måttdefinitioner med rest-API:erna för fleradimensionella Azure Monitor använder du "2018-01-01" som API-version.
>
>

Den resulterande JSON-svarstexten skulle likna följande exempel: (Observera att det andra måttet har dimensioner)

```json
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

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Hämta dimensionsvärden (flerdimensionellt API)

När de tillgängliga måttdefinitionerna är kända kan det finnas vissa mått som har dimensioner. Innan du frågar efter måttet kanske du vill ta reda på vilket värdeintervall en dimension har. Baserat på dessa dimensionsvärden kan du sedan välja att filtrera eller segmentera måtten baserat på dimensionsvärden medan du frågar efter mått.  Använd [REST-APIN för Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) för att uppnå detta.

Använd måttets namn "värde" (inte "localizedValue") för alla filtreringsbegäranden . Om inga filter anges returneras standardmåttet. Användningen av detta API tillåter bara en dimension att ha ett jokerteckenfilter.

> [!NOTE]
> Om du vill hämta dimensionsvärden med HJÄLP av AZURE Monitor REST API använder du "2018-01-01" som API-version.
>
>

**Metod**: GET

**Begär URI**\:: https //management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/mått?metricnames=*{metric}*&tidsintervall=*{starttid/sluttid}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

Om du till exempel vill hämta listan över dimensionsvärden som har avgetts för DIMENSIONEN "API-namn" för måttet Transaktioner, där GeoType-dimensionen = "Primär" under det angivna tidsintervallet, skulle begäran vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T00:00:00Z/2018-03-02T00:00:00Z&resultType=metadata&`$filter=${filter}&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```

Den resulterande JSON-svarsorganet skulle likna följande exempel:

```json
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

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Hämta måttvärden (flerdimensionellt API)

När de tillgängliga måttdefinitionerna och möjliga dimensionsvärden är kända är det då möjligt att hämta relaterade måttvärden.  Använd [REST-APIN för Azure Monitor Metrics](https://docs.microsoft.com/rest/api/monitor/metrics) för att uppnå detta.

Använd måttets namn "värde" (inte "localizedValue") för alla filtreringsbegäranden. Om inga dimensionsfilter anges returneras det samlade aggregerade måttet. Om en måttfråga returnerar flera tidsserier kan du använda frågeparametrarna "Överkant" och "OrderBy" för att returnera en begränsad ordnad lista över tidsserier.

> [!NOTE]
> Om du vill hämta flerdimensionella måttvärden med hjälp av AZURE Monitor REST API använder du "2018-01-01" som API-version.
>
>

**Metod**: GET

**Begär URI**:\/https: /management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metricnames=*{metric}*&tidsspann=*{starttid/sluttid}*&$filter=*{filter}*&intervall=*{timeGrain}*&aggregering=*{aggreation}*&api-version=*{apiVersion}*

Om du till exempel vill hämta de 3 översta API:erna, i fallande värde, efter antalet "Transaktioner" under ett 5 minintervall, där GeotType var "Primär", skulle begäran vara följande:

```powershell
$filter = "APIName eq '*' and GeoType eq 'Primary'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/ContosoStorage/providers/microsoft.insights/metrics?metricnames=Transactions&timespan=2018-03-01T02:00:00Z/2018-03-01T02:05:00Z&`$filter=${filter}&interval=PT1M&aggregation=Total&top=3&orderby=Total desc&api-version=2018-01-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```

Den resulterande JSON-svarsorganet skulle likna följande exempel:

```json
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

Använd [AZURE Monitor Metric definitions REST API](https://msdn.microsoft.com/library/mt743621.aspx) för att komma åt listan över mått som är tillgängliga för en tjänst.

**Metod**: GET

**Begär URI**:\/\/https: management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/Definition metrics?api-version=*{apiVersion}*

Om du till exempel vill hämta måttdefinitionerna för en Azure Logic App visas begäran på följande sätt:

```powershell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosotweets-metricdef-results.json" `
                  -Verbose
```

> [!NOTE]
> Om du vill hämta måttdefinitioner med HJÄLP av AZURE Monitor REST API använder du "2016-03-01" som API-version.
>
>

Den resulterande JSON-svarsorganet skulle likna följande exempel:

```json
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

Mer information finns i [lista måttdefinitionerna för en resurs i Azure Monitor REST API-dokumentation.](https://msdn.microsoft.com/library/azure/mt743621.aspx)

## <a name="retrieve-metric-values"></a>Hämta måttvärden

När de tillgängliga måttdefinitionerna är kända är det då möjligt att hämta relaterade måttvärden. Använd måttets namn "värde" (inte "localizedValue") för alla filtreringsbegäranden (till exempel hämta måttdatapunkterna "CpuTime" och "Begäranden"). Om inga filter anges returneras standardmåttet.

> [!NOTE]
> Om du vill hämta måttvärden med HJÄLP AV AZURE Monitor REST API använder du "2016-09-01" som API-version.
>
>

**Metod**: GET

**Begär URI:**`https:\//management.azure.com/subscriptions/\*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*`

Om du till exempel vill hämta datapunkterna RunsSucceeded för det angivna tidsintervallet och för en tidskorn på 1 timme, skulle begäran vara följande:

```powershell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-results.json" `
    -Verbose
```

Den resulterande JSON-svarsorganet skulle likna följande exempel:

```json
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

Om du vill hämta flera data eller aggregeringspunkter lägger du till måttdefinitionsnamnen och aggregeringstyperna i filtret, vilket visas i följande exempel:

```powershell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosotweets-metrics-multiple-results.json" `
    -Verbose
```

Den resulterande JSON-svarsorganet skulle likna följande exempel:

```json
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

En ytterligare metod är att använda [ARMClient](https://github.com/projectkudu/armclient) på din Windows-dator. ARMClient hanterar Azure AD-autentiseringen (och den resulterande JWT-token) automatiskt. I följande steg beskrivs användningen av ARMClient för att hämta måttdata:

1. Installera [Chocolatey](https://chocolatey.org/) och [ARMClient](https://github.com/projectkudu/armclient).
2. Skriv in inloggning på *armclient.exe*i ett terminalfönster . Om du gör det uppmanas du att logga in på Azure.
3. Skriv *armklient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Skriv *armklient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Om du till exempel vill hämta måttdefinitionerna för en viss logikapp utfärdar du följande kommando:

```console
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```

## <a name="retrieve-the-resource-id"></a>Hämta resurs-ID:et

Med REST API kan verkligen hjälpa till att förstå tillgängliga måttdefinitioner, granularitet och relaterade värden. Den informationen är användbar när du använder [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

För den föregående koden är det resurs-ID som ska användas den fullständiga sökvägen till önskad Azure-resurs. Om du till exempel vill fråga mot en Azure Web App skulle resurs-ID:a:For example, to query against an Azure Web App, the resource ID would be:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Följande lista innehåller några exempel på resurs-ID-format för olika Azure-resurser:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastisk SQL Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Servicebuss** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Skala uppsättningar för virtuella datorer** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **Virtuella datorer** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event hubbar** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Det finns alternativa metoder för att hämta resurs-ID: t, inklusive användning av Azure Resource Explorer, visa önskad resurs i Azure-portalen och via PowerShell eller Azure CLI.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Om du vill hitta resurs-ID:et för en önskad resurs är en användbar metod att använda Azure [Resource Explorer-verktyget.](https://resources.azure.com) Navigera till önskad resurs och titta sedan på ID som visas, som i följande skärmdump:

![Alt "Azure Resource Explorer"](./media/rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure Portal

Resurs-ID kan också erhållas från Azure-portalen. Det gör du genom att navigera till önskad resurs och sedan välja Egenskaper. Resurs-ID visas i avsnittet Egenskaper, vilket visas i följande skärmbild:

![Alt "Resurs-ID som visas i egenskapsbladet Egenskaper i Azure-portalen"](./media/rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell

Resurs-ID kan hämtas med Azure PowerShell-cmdlets också. Om du till exempel vill hämta resurs-ID:t för en Azure Logic App kör du cmdleten Get-AzureLogicApp, som i följande exempel:

```powershell
Get-AzLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Resultatet bör likna följande exempel:

```output
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

Om du vill hämta resurs-ID:t för ett `az storage account show` Azure Storage-konto med Hjälp av Azure CLI kör du kommandot, som visas i följande exempel:

```azurecli
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Resultatet bör likna följande exempel:

```json
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
> Azure Logic Apps är ännu inte tillgängliga via Azure CLI, vilket innebär att ett Azure Storage-konto visas i föregående exempel.
>
>

## <a name="retrieve-activity-log-data"></a>Hämta aktivitetsloggdata

Förutom måttdefinitioner och relaterade värden är det också möjligt att använda AZURE Monitor REST API för att hämta ytterligare intressanta insikter relaterade till Azure-resurser. Som ett exempel är det möjligt att fråga [aktivitetsloggdata.](https://msdn.microsoft.com/library/azure/dn931934.aspx) I följande exempel visas hur du använder REST-API:et för Azure Monitor för att fråga efter aktivitetsloggdata inom ett visst datumintervall för en Azure-prenumeration:

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

* Granska [översikten över övervakning](../../azure-monitor/overview.md).
* Visa [mått som stöds med Azure Monitor](metrics-supported.md).
* Granska [REST API-referensen](https://msdn.microsoft.com/library/azure/dn931943.aspx)för Microsoft Azure Monitor REST .
* Granska [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).
