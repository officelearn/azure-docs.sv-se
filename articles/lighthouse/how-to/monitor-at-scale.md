---
title: Övervaka delegerade resurser i stor skala
description: Lär dig hur du effektivt använder Azure Monitor-loggar på ett skalbart sätt i de kundklienter du hanterar.
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985209"
---
# <a name="monitor-delegated-resources-at-scale"></a>Övervaka delegerade resurser i stor skala

Som tjänsteleverantör kan du ha inbyggda flera kundklienter för Azure-delegerad resurshantering. [Azure Lighthouse](../overview.md) gör det möjligt för tjänsteleverantörer att utföra åtgärder i stor skala över flera klienter samtidigt, vilket gör hanteringsuppgifter mer effektiva.

Det här avsnittet visar hur du använder [Azure Monitor-loggar](../../azure-monitor/platform/data-platform-logs.md) på ett skalbart sätt i de kundklienter som du hanterar.

## <a name="create-log-analytics-workspaces"></a>Skapa logganalysarbetsytor

För att samla in data måste du skapa Log Analytics-arbetsytor. Dessa Log Analytics-arbetsytor är unika miljöer för data som samlas in av Azure Monitor. Varje arbetsyta har en egen datadatabas och konfiguration, och datakällor och lösningar är konfigurerade för att lagra sina data på en viss arbetsyta.

Vi rekommenderar att du skapar dessa arbetsytor direkt i kundens klienter. På så sätt finns deras data kvar i sina klienter i stället för att exporteras till dina. Detta möjliggör också centraliserad övervakning av alla resurser eller tjänster som stöds av Log Analytics, vilket ger dig större flexibilitet när det gäller vilka typer av data du övervakar.

Du kan skapa en Log Analytics-arbetsyta med hjälp av [Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md), med hjälp av [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)eller genom att använda [Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md).

## <a name="deploy-policies-that-log-data"></a>Distribuera principer som loggar data

När du har skapat dina Log Analytics-arbetsytor kan du distribuera [Azure Policy](../../governance/policy/index.yml) över dina kundhierarkier så att diagnostikdata skickas till rätt arbetsyta i varje klient. De exakta principer som du distribuerar kan variera beroende på vilka resurstyper du vill övervaka.

Mer information om hur du skapar principer finns i [Självstudiekurs: Skapa och hantera principer för att tillämpa efterlevnad](../../governance/policy/tutorials/create-and-manage.md). Det här [community-verktyget](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) innehåller ett skript som hjälper dig att skapa principer för att övervaka de specifika resurstyper som du väljer.

När du har bestämt vilka principer som ska distribueras kan du [distribuera dem till dina delegerade prenumerationer i stor skala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analysera insamlade data

När du har distribuerat dina principer loggas data i de logganalysarbetsytor som du har skapat i varje kundklientorganisation. För att få insikter över alla hanterade kunder kan du använda verktyg som [Azure Monitor-arbetsböcker](../../azure-monitor/platform/workbooks-overview.md) för att samla in och analysera information från flera datakällor.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor](../../azure-monitor/index.yml).
- Läs mer om [Azure Monitor-loggar](../../azure-monitor/platform/data-platform-logs.md).
- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
