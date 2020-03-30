---
title: Användbara resurser när du arbetar med Azure Sentinel| Microsoft-dokument
description: Det här dokumentet ger dig en lista över användbara resurser när du arbetar med Azure Sentinel.
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
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585279"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Användbara resurser för att arbeta med Azure Sentinel



I den här artikeln visas resurser som kan hjälpa dig att få mer information om hur du arbetar med Azure Sentinel.

Azure Logic Apps-kopplingar:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Revision och rapportering
Granskningsloggar för Azure Sentinel underhålls i [Azure Activity Logs](../azure-monitor/platform/platform-logs-overview.md).

Följande åtgärder som stöds kan granskas.

|Åtgärdsnamn|    Resurstyp|
|----|----|
|Skapa eller uppdatera arbetsbok  |Microsoft.Insights/arbetsböcker|
|Ta bort arbetsbok    |Microsoft.Insights/arbetsböcker|
|Ange arbetsflöde   |Microsoft.Logic/arbetsflöden|
|Ta bort arbetsflöde    |Microsoft.Logic/arbetsflöden|
|Skapa sparad sökning    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Ta bort sparad sökning    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Regler för uppdatering av aviseringar |Microsoft.SecurityInights/alertRules|
|Ta bort varningsregler |Microsoft.SecurityInights/alertRules|
|Svarsåtgärder för uppdatering av varningsregel |Microsoft.SecurityInights/alertRules/actions|
|Ta bort svarsåtgärder för varningsregel |Microsoft.SecurityInights/alertRules/actions|
|Uppdatera bokmärken   |Microsoft.SecurityInights/bokmärken|
|Ta bort bokmärken   |Microsoft.SecurityInights/bokmärken|
|Uppdatera ärenden   |Microsoft.SecurityInights/Ärenden|
|Uppdatera ärendeundersökning  |Microsoft.SecurityInights/Ärenden/utredningar|
|Skapa ärendekommentarer   |Microsoft.SecurityInsights/Ärenden/kommentarer|
|Uppdatera dataanslutningar |Microsoft.SecurityInights/dataConnectors|
|Ta bort datakopplingar |Microsoft.SecurityInights/dataConnectors|
|Uppdatera inställningarna    |Microsoft.SecurityInights/inställningar|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Visa gransknings- och rapporteringsdata i Azure Sentinel

Du kan visa dessa data genom att strömma dem från Azure Activity-loggen till Azure Sentinel där du sedan kan utföra forskning och analys på den.

1. Anslut [Azure Activity-datakällan.](connect-azure-activity.md) När du har gjort detta strömmas granskningshändelser till en ny tabell på skärmen **Loggar** som kallas AzureActivity.
2. Fråga sedan data med KQL, som du skulle göra någon annan tabell.



## <a name="vendor-documentation"></a>Dokumentation för leverantör

| **Leverantör**  | **Använd incident i Azure Sentinel** | **Länk**|
|----|----|----|
| GitHub| Används för att komma åt community-sidan| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto (på en)| Konfigurera FSE| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| Pluralsight (pluralsight) | Kusto Query Language-kurs| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Bloggar och forum

Skicka dina frågor om [TechCommunity-utrymmet](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) för Azure Sentinel.

Visa Azure Sentinel-blogginlägg från [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) och [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du en lista över resurser som är användbara när du arbetar med Azure Sentinel. Du hittar ytterligare information om Azures säkerhet och efterlevnad på [bloggen Microsoft Azure Security and Compliance](https://blogs.msdn.com/b/azuresecurity/).
