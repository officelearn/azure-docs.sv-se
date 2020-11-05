---
title: Användbara resurser när du arbetar med Azure Sentinel | Microsoft Docs
description: I det här dokumentet får du en lista över användbara resurser när du arbetar med Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 50b88b4af6ea8479097b88b51fe7acfbea355ab8
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392324"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Användbara resurser för att arbeta med Azure Sentinel



Den här artikeln innehåller resurser som kan hjälpa dig att få mer information om hur du arbetar med Azure Sentinel.

Azure Logic Apps kopplingar: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Granskning och rapportering
Gransknings loggar för Azure Sentinel finns kvar i [Azures aktivitets loggar](../azure-monitor/platform/platform-logs-overview.md).

Följande åtgärder som stöds kan granskas.

|Åtgärdsnamn|    Resurstyp|
|----|----|
|Skapa eller uppdatera arbets bok  |Microsoft. Insights/arbets böcker|
|Ta bort arbets bok    |Microsoft. Insights/arbets böcker|
|Ange arbets flöde   |Microsoft. Logic/arbets flöden|
|Ta bort arbets flöde    |Microsoft. Logic/arbets flöden|
|Skapa Sparad sökning    |Microsoft. OperationalInsights/arbets ytor/savedSearches|
|Ta bort Sparad sökning    |Microsoft. OperationalInsights/arbets ytor/savedSearches|
|Uppdatera aviserings regler |Microsoft. SecurityInsights/alertRules|
|Ta bort aviserings regler |Microsoft. SecurityInsights/alertRules|
|Uppdatera svars åtgärder för aviserings regeln |Microsoft. SecurityInsights/alertRules/Actions|
|Ta bort svars åtgärder för aviserings regel |Microsoft. SecurityInsights/alertRules/Actions|
|Uppdatera bok märken   |Microsoft. SecurityInsights/bok märken|
|Ta bort bok märken   |Microsoft. SecurityInsights/bok märken|
|Uppdatera ärenden   |Microsoft. SecurityInsights/fall|
|Uppdatera ärende undersökning  |Microsoft. SecurityInsights/fall/undersökningar|
|Skapa ärende kommentarer   |Microsoft. SecurityInsights/Cases/comments|
|Uppdatera data anslutningar |Microsoft. SecurityInsights/dataConnectors|
|Ta bort data anslutningar |Microsoft. SecurityInsights/dataConnectors|
|Uppdatera inställningarna    |Microsoft. SecurityInsights/inställningar|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Visa gransknings-och rapporterings data i Azure Sentinel

Du kan visa dessa data genom att strömma den från Azures aktivitets logg till Azure Sentinel där du sedan kan utföra forskning och analys på den.

1. Anslut [Azure-aktivitetens](connect-azure-activity.md) data källa. När du har gjort det strömmas gransknings händelser till en ny tabell på skärmen **loggar** som kallas AzureActivity.

1. Sedan kan du köra frågor mot data med KQL, precis som med andra tabeller.

    Om du till exempel vill ta reda på vem som senast hade redigerat en viss analys regel använder du följande fråga (Ersätt `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` med regel-ID för regeln som du vill kontrol lera):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Leverantörs dokumentation

| **Leverantör**  | **Använd incident i Azure Sentinel** | **Operationsföljdslänkkod**|
|----|----|----|
| GitHub| Används för att få åtkomst till community-Sidan| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Konfigurera CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto Query Language-kurs| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Bloggar och forum

Publicera dina frågor på [TechCommunity-utrymmet](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) för Azure Sentinel.

Visa blogg inlägg från Azure Sentinel från [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) och [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du en lista över resurser som är användbara när du arbetar med Azure Sentinel. Du hittar mer information om Azure-säkerhet och efterlevnad på [bloggen för Microsoft Azure säkerhet och efterlevnad](https://docs.microsoft.com/archive/blogs/azuresecurity/).
