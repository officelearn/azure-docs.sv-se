---
title: "Azure logganalys logga Sök REST API | Microsoft Docs"
description: "Den här guiden innehåller en grundläggande genomgång som beskriver hur du kan använda Azure logganalys sökningen REST-API med exempel som visar hur du använder kommandon."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: b4e9ebe8-80f0-418e-a855-de7954668df7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 46c88f7cc250d4c35043039a6f0440aaac85b1c2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-log-search-rest-api"></a>Logganalys logga Sök REST API

> [!IMPORTANT]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), läser du bör den [dokumentationen för den nya versionen av loggen sökningen API](https://dev.loganalytics.io/).  Den här äldre API kan fortsätta att arbeta med en uppgraderad arbetsyta, men det kommer snart att depracated.  Du bör ändra några befintliga lösningar för att använda det nya API: T.

Den här guiden innehåller en grundläggande självstudier, inklusive exempel på hur du kan använda Log Analytics Sök REST API. 


## <a name="overview-of-the-log-search-rest-api"></a>Översikt över loggen sökningen REST-API
Log Analytics Sök REST API är RESTful och kan nås via Azure Resource Manager API. Den här artikeln innehåller exempel på åtkomst till API via [ARMClient](https://github.com/projectkudu/ARMClient), ett kommandoradsverktyg för öppen källkod som förenklar anropar API: et för Azure Resource Manager. Användning av ARMClient är en av många alternativ för att komma åt Log Analytics Sök-API. Ett annat alternativ är att använda Azure PowerShell-modulen för OperationalInsights som innehåller cmdlets för att komma åt sökningen. Med dessa verktyg kan du använda Azure Resource Manager API för att göra anrop till logganalys arbetsytor och utföra sökkommandon i dem. API: et matar ut sökresultat i JSON-format, så att du kan använda sökresultatet på många olika sätt programmässigt.

Azure Resource Manager kan användas en [-biblioteket för .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) och [REST API](https://msdn.microsoft.com/library/azure/mt163658.aspx). Granska de länkade webbsidorna om du vill veta mer.

> [!NOTE]
> Om du använder en aggregering som `|measure count()` eller `distinct`, varje anrop till söka kan returnera upp till 500 000 poster. Sökningar som inte inkluderar en aggregering kommandot returnerar upp till 5 000 poster.
>
>

## <a name="basic-log-analytics-search-rest-api-tutorial"></a>Grundläggande självstudierna för Log Analytics Sök REST API
### <a name="to-use-armclient"></a>Att använda ARMClient
1. Installera [Chocolatey](https://chocolatey.org/), vilket är en öppen källa Package Manager för Windows. Öppna ett kommandotolksfönster som administratör och kör sedan följande kommando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```
2. Installera ARMClient genom att köra följande kommando:

    ```
    choco install armclient
    ```

### <a name="to-perform-a-search-using-armclient"></a>Du utför en sökning med ARMClient
1. Logga in med ditt Microsoft-konto eller ditt arbets- eller skolkonto konto:

    ```
    armclient login
    ```

    En lyckad inloggning visar alla prenumerationer som är knutna till det angivna kontot:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```
2. Hämta Operations Management Suite-arbetsytor:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    En lyckad Get-anrop skulle utdata alla arbetsytor som är knutna till prenumerationen:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Skapa Sök-variabeln:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Sökningen med din nya Sök-variabeln:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## <a name="log-analytics-search-rest-api-reference-examples"></a>Logga Analytics Sök REST API-referens-exempel
I följande exempel visas hur du kan använda Sök-API.

### <a name="search---actionread"></a>Sök - åtgärd/läsning
**Exempel-Url:**

```
    /subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Begäran:**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
I följande tabell beskrivs de egenskaper som är tillgängliga.

| **Egenskap** | **Beskrivning** |
| --- | --- |
| Upp |Maximalt antal resultat ska returneras. |
| markera |Innehåller före och efter parametrar, används vanligtvis för syntaxmarkering matchande fält |
| före |Den angivna strängen till din matchade fält-prefix. |
| post |Lägger till den angivna strängen till din matchade fält. |
| DocumentDB |Frågan används för att samla in och returnerar resultat. |
| start |Början av tidsfönster som du vill att resultatet som ska returneras från. |
| slut |Slutet av tidsperioden som du vill att resultatet som ska returneras från. |

**Svar:**

```
    {
      "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "__metadata" : {
        "resultType" : "raw",
        "total" : 1455,
        "top" : 150,
        "StartTime" : "2015-02-11T21:09:07.0345815Z",
        "Status" : "Successful",
        "LastUpdated" : "2015-02-11T21:09:07.331463Z",
        "CoreResponses" : [],
        "sort" : [{
          "name" : "TimeGenerated",
          "order" : "desc"
        }],
        "requestTime" : 450
      },
      "value": [{
        "SourceSystem" : "OpsManager",
        "TimeGenerated" : "2015-02-07T14:07:33Z",
        "Source" : "SideBySide",
        "EventLog" : "Application",
        "Computer" : "BAMBAM",
        "EventCategory" : 0,
        "EventLevel" : 1,
        "EventLevelName" : "Error",
        "UserName" : "N/A",
        "EventID" : 78,
        "MG" : "00000000-0000-0000-0000-000000000001",
        "TimeCollected" : "2015-02-07T14:10:04.69Z",
        "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
        "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "Type" : "Event",
        "__metadata" : {
          "Type" : "Event",
          "TimeGenerated" : "2015-02-07T14:07:33Z",
          "highlighting" : {
          "EventLevelName" : ["{[hl]}Error{[/hl]}"]
        }
      }]
    ],
            "start" : "2015-02-04T21:03:29.231Z",
            "end" : "2015-02-11T21:03:29.231Z"
          }
        }
      }]
    }
```

### <a name="searchid---actionread"></a>Söka / {ID} - åtgärd/läsning
**Begär innehållet i en sparad sökning:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

> [!NOTE]
> Om sökningen returnerar med ”väntande” status avsökning uppdaterade resultat kan du göra detta API. Resultat av sökningen kommer att tas bort från cache efter 6 min, och HTTP-rest ska returneras. Om inledande sökbegäran returnerar status ”lyckades” omedelbart lägga resultatet inte till cachen orsakar detta API att returnera http-rest om efterfrågas. Innehållet i ett HTTP-200 resultat finns i samma format som inledande sökbegäran bara med de uppdaterade värdena.
>
>

### <a name="saved-searches"></a>Sparade sökningar
**Listan över sparade sökningar för begäran:**

```
    armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Metoder som stöds: GET PLACERA ta bort

Samlingen metoder som stöds: hämta

I följande tabell beskrivs de egenskaper som är tillgängliga.

| Egenskap | Beskrivning |
| --- | --- |
| Id |Den unika identifieraren. |
| ETag |**Krävs för korrigeringsfilen**. Uppdateras av servern vid varje skrivning. Värdet måste vara lika med det aktuella lagrade värdet eller ' *' att uppdatera. 409 returneras för gamla/ogiltiga värden. |
| properties.query |**Krävs**. Sökfråga. |
| properties.displayName |**Krävs**. Användardefinierade visningsnamnet för frågan. |
| Properties.category |**Krävs**. Den användardefinierade kategorin av frågan. |

> [!NOTE]
> Sök-API logganalys returnerar för närvarande användarskapade sparade sökningar när avsökas för sparade sökningar i en arbetsyta. API: N returnerar inte sparade sökningar som tillhandahålls av lösningar.
>
>

### <a name="create-saved-searches"></a>Skapa sparade sökningar
**Begäran:**

```
    $savedSearchParametersJson = "{'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisismyid?api-version=2015-03-20 $savedSearchParametersJson
```

> [!NOTE]
> Namnet på alla sparade sökningar, scheman och åtgärder som skapats med API: et för Log Analytics måste vara i gemener.

### <a name="delete-saved-searches"></a>Ta bort sparade sökningar
**Begäran:**

```
    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### <a name="update-saved-searches"></a>Uppdatera sparade sökningar
 **Begäran:**

```
    $savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### <a name="metadata---json-only"></a>Metadata - JSON endast
Här är ett sätt att visa fält för alla typer av loggen för de data som samlas in i din arbetsyta. Till exempel om du vill att du vet om vilken typ av händelse har ett fält med namnet på datorn, sedan den här frågan är ett sätt att kontrollera.

**Begäran för fält:**

```
    armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Svar:**

```
    {
      "__metadata" : {
        "schema" : {
          "name" : "Example Name",
          "version" : 2
        },
        "resultType" : "schema",
        "requestTime" : 35
      },
      "value" : [{
          "name" : "MG",
          "displayName" : "MG",
          "type" : "Guid",
          "facetable" : true,
          "display" : false,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
        }, {
          "name" : "ManagementGroupName",
          "displayName" : "ManagementGroupName",
          "type" : "String",
          "facetable" : true,
          "display" : true,
          "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
        }
      ]
    }
```

I följande tabell beskrivs de egenskaper som är tillgängliga.

| **Egenskap** | **Beskrivning** |
| --- | --- |
| namn |Fältnamn. |
| displayName |Visningsnamnet för fältet. |
| typ |Typ av fältvärdet. |
| facetable |Kombinationen av aktuella 'indexerad', ' lagras ' och 'aspekten' egenskaper. |
| Visa |Aktuella 'Visa-egenskapen. TRUE om fältet är synligt i sökningen. |
| ownerType |Minskas till endast typer som hör till publicerats så IP. |

## <a name="optional-parameters"></a>Valfria parametrar
Följande information beskriver valfria parametrar finns tillgängliga.

### <a name="highlighting"></a>Markering
Parametern ”Markera” är en valfri parameter som du kan använda för att begära undersystemet Sök innehåller en uppsättning markörer i sitt svar.

Dessa markörer anger början och sluta markerad text som matchar villkoren som angetts i sökfrågan.
Du kan ange start- och slutdatum markörerna som används av sökning för att omsluta markerade termen.

**Exempel sökfråga**

```
    $savedSearchParametersJson =
    {
      "top":150,
      "highlight":{
        "pre":"{[hl]}",
        "post":"{[/hl]}"
      },
      "query":"*",
      "start":"2015-02-04T21:03:29.231Z",
      "end":"2015-02-11T21:03:29.231Z"
    }
    armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Exempel resultat:**

```
    {
        "TimeGenerated":
        "2015-05-18T23:55:59Z", "Source":
        "EventLog": "Application",
        "Computer": "smokedturkey.net",
        "EventCategory": 0,
        "EventLevel":1,
        "EventLevelName":
        "Error"
        "Manager ", "__metadata":
        {
            "Type": "Event",
            "TimeGenerated": "2015-05-18T23:55:59Z",
            "highlighting": {
                "EventLevelName":
                ["{[hl]}Error{[/hl]}"]
            }
        }
    }
```

Observera att föregående resultatet innehåller ett felmeddelande som prefix och läggas till.

## <a name="computer-groups"></a>Datorgrupper
Datorgrupper är särskilda sparade sökningar som returnerar en uppsättning datorer.  Du kan använda en datorgrupp i andra frågor för att begränsa resultatet till datorerna i gruppen.  En datorgrupp implementeras som en sparad sökning med en grupp-tagg med ett värde på datorn.

Följande är ett exempelsvar för en datorgrupp.

```
    "etag": "W/\"datetime'2016-04-01T13%3A38%3A04.7763203Z'\"",
    "properties": {
        "Category": "My Computer Groups",
        "DisplayName": "My Computer Group",
        "Query": "srv* | Distinct Computer",
        "Tags": [{
            "Name": "Group",
            "Value": "Computer"
          }],
    "Version": 1
    }
```

### <a name="retrieving-computer-groups"></a>Hämtar datorgrupper
Använd Get-metoden för att hämta en datorgrupp med grupp-ID.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### <a name="creating-or-updating-a-computer-group"></a>Skapa eller uppdatera en datorgrupp
Använd Put-metoden för att skapa en datorgrupp med ett unikt sparad sökning-ID. Om du använder en befintlig dator-ID för gruppen har att en ändrats. När du skapar en datorgrupp i logganalys-portalen skapas ID från gruppen och namn.

Frågan används för att definiera gruppen måste returnera en uppsättning datorer för gruppen ska fungera korrekt.  Vi rekommenderar att du avslutar frågan med `| Distinct Computer` att säkerställa rätt data returneras.

Definitionen av den sparade sökningen måste innehålla en tagg med namnet grupp med ett värde för sökningen dator klassificeras som en datorgrupp.

```
    $etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
    $groupName="My Computer Group"
    $groupQuery = "Computer=srv* | Distinct Computer"
    $groupCategory="My Computer Groups"
    $groupID = "My Computer Groups | My Computer Group"

    $groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson
```

### <a name="deleting-computer-groups"></a>Ta bort datorgrupper
Om du vill ta bort en datorgrupp måste använda Delete-metoden med grupp-ID.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att skapa frågor med anpassade fält för villkoret.
