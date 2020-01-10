---
title: Övervaka Azure Functions med Azure Monitor loggar
description: Lär dig hur du använder Azure Monitor loggar med Azure Functions för att övervaka funktions körningar.
author: ahmedelnably
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: aelnably
ms.openlocfilehash: f4af646569edc8a9274af752e7e4f2a36585ae4d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769106"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Övervaka Azure Functions med Azure Monitor loggar

Azure Functions erbjuder en integrering med [Azure Monitors loggar](../azure-monitor/platform/data-platform-logs.md) för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions att skicka systemgenererade och användarspecifika loggar till Azure Monitor loggar.

Azure Monitor loggar ger dig möjlighet att konsolidera loggar från olika resurser i samma arbets yta, där den kan analyseras med [frågor](../azure-monitor/log-query/log-query-overview.md) för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](../azure-monitor/log-query/portals.md) i Azure Portal och sedan antingen analysera data med hjälp av dessa verktyg eller spara frågor för användning med [visualiseringar](../azure-monitor/visualizations.md) eller [varnings regler](../azure-monitor/platform/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som används av Azure datautforskaren som är lämplig för enkla logg frågor, men även avancerade funktioner som agg regeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Integrering med Azure Monitor loggar finns för närvarande i en offentlig för hands version för Function-appar som körs på Windows förbruknings-, Premium-och dedikerade värd planer.

## <a name="setting-up"></a>Konfigurera

I avsnittet **övervakning** väljer du **diagnostikinställningar** och klickar sedan på **Lägg till diagnostisk inställning**.

![Lägg till en diagnostisk inställning](media/functions-monitor-log-analytics/diagnostic-settings-add.png)

På sidan **diagnostikinställningar** väljer du **Skicka till Log Analytics**och väljer sedan arbets ytan Log Analytics. Under **loggen** Välj **FunctionAppLogs**innehåller den här tabellen de önskade loggarna.

![Lägg till en diagnostisk inställning](media/functions-monitor-log-analytics/choose-table.png)

## <a name="user-generated-logs"></a>Loggar som skapats av användare

Om du vill skapa anpassade loggar kan du använda den specifika loggnings instruktionen, beroende på ditt språk, här visas exempel på kodfragment:


# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Skicka frågor till loggarna

Om du vill fråga de genererade loggarna går du till arbets ytan Log Analytics som du konfigurerade för att skicka funktions loggarna till och klickar på **loggar**.

![Frågefönstret i LA-arbetsytan](media/functions-monitor-log-analytics/querying.png)

Azure Functions skriver alla loggar till **FunctionAppLogs** -tabellen är några exempel frågor.

### <a name="all-logs"></a>Alla loggar

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="a-specific-function-logs"></a>En speciell funktions loggar

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Undantag

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Functions översikt](functions-overview.md)
- Läs mer om [Azure Monitor loggar](../azure-monitor/platform/data-platform-logs.md)
- Läs mer om [frågespråket](../azure-monitor/log-query/get-started-queries.md).
