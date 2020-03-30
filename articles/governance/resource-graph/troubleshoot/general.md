---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem med de olika SDK:erna medan du frågar Azure-resurser med Azure Resource Graph.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303908"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Felsöka fel med Hjälp av Azure Resource Graph

Du kan stöta på fel när du frågar Azure-resurser med Azure Resource Graph. I den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta felinformation

De flesta fel är resultatet av ett problem när du kör en fråga med Azure Resource Graph. När en fråga misslyckas innehåller SDK information om den misslyckade frågan. Den här informationen anger problemet så att det kan åtgärdas och en senare fråga lyckas.

## <a name="general-errors"></a>Allmänna fel

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Scenario: För många prenumerationer

#### <a name="issue"></a>Problem

Kunder med åtkomst till mer än 1 000 prenumerationer, inklusive prenumerationer mellan innehavare med [Azure Lighthouse,](../../../lighthouse/overview.md)kan inte hämta data för alla prenumerationer i ett enda samtal till Azure Resource Graph.

#### <a name="cause"></a>Orsak

Azure CLI och PowerShell vidarebefordrar endast de första 1000 prenumerationerna till Azure Resource Graph. REST API för Azure Resource Graph accepterar ett maximalt antal prenumerationer för att utföra frågan på.

#### <a name="resolution"></a>Lösning

Batchbegäranden för frågan med en delmängd prenumerationer för att hålla sig under 1000-prenumerationsgränsen. Lösningen använder parametern **Prenumeration** i PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Scenario: REST-huvudet av innehållstyp som inte stöds

#### <a name="issue"></a>Problem

Kunder som frågar AZURE Resource Graph REST API får ett _500_ -svar (internt serverfel) returnerat.

#### <a name="cause"></a>Orsak

REST-API:et för `Content-Type` Azure Resource Graph stöder endast ett **program/json**. Vissa REST-verktyg eller agenter som standard är **text/oformaterad**, vilket inte stöds av REST API.

#### <a name="resolution"></a>Lösning

Verifiera att verktyget eller agenten som du använder för att `Content-Type` fråga Azure Resource Graph har REST API-huvudet konfigurerat för **program/json**.

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Scenario: Ingen läsbehörighet för alla prenumerationer i listan

#### <a name="issue"></a>Problem

Kunder som uttryckligen skickar en lista över prenumerationer med en Azure Resource Graph-fråga får ett _403_ -svar (förbjuden).

#### <a name="cause"></a>Orsak

Om kunden inte har läsbehörighet till alla angivna prenumerationer nekas begäran på grund av brist på lämpliga säkerhetsrättigheter.

#### <a name="resolution"></a>Lösning

Inkludera minst en prenumeration i prenumerationslistan som kunden som kör frågan har minst läsbehörighet till. Mer information finns [i Behörigheter i Azure Resource Graph](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure Forum](https://azure.microsoft.com/support/forums/).
- Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.